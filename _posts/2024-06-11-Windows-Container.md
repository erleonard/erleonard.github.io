---
layout: single
title:  "Windows Container Local Development with BuildKit"
date:   2024-06-10 12:00:00 -0500
toc: true
toc_icon: "cloud"
categories:
  - Azure
tags:
  - Azure
  - WindowsContainers
---


## What is BuildKit?
BuildKit is a toolkit for converting source code into build artifacts, such as container images, in an efficient, expressive, and repeatable manner. It has been the default build engine on Linux since Docker Engine 23.0.0. With BuildKit, you can parallelize building independent build stages, incrementally transfer only changed files, and use Dockerfile frontend implementations with new features.

## Experimental Windows Containers Support
The exciting news is that the latest BuildKit release, v0.13.0, includes experimental Windows Containers support1. Until now, Buildx (the BuildKit client) on Windows was primarily used for building Linux images and limited Windows images through cross-compilation. However, with this release, Windows Containers are now supported directly in BuildKit.

Hyper-V
```bash
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V, Containers -All
```

ContainerD
```bash
# Download and extract desired containerd Windows binaries
$UserRepo = "containerd/containerd"
$Response = Invoke-RestMethod -Uri "https://api.github.com/repos/$UserRepo/releases/latest"
$Version = $Response.tag_name
$Version = $Version.Replace("v", "")
$Arch = "amd64"	# arm64 also available
curl.exe -LO https://github.com/containerd/containerd/releases/download/v$Version/containerd-$Version-windows-$Arch.tar.gz
tar.exe xvf .\containerd-$Version-windows-amd64.tar.gz

# Copy
Copy-Item -Path .\bin -Destination $Env:ProgramFiles\containerd -Recurse -Force

# add the binaries (containerd.exe, ctr.exe) in $env:Path
$Path = [Environment]::GetEnvironmentVariable("PATH", "Machine") + [IO.Path]::PathSeparator + "$Env:ProgramFiles\containerd"
[Environment]::SetEnvironmentVariable( "Path", $Path, "Machine")
# reload path, so you don't have to open a new PS terminal later if needed
$Env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")

# configure
containerd.exe config default | Out-File $Env:ProgramFiles\containerd\config.toml -Encoding ascii
# Review the configuration. Depending on setup you may want to adjust:
# - the sandbox_image (Kubernetes pause image)
# - cni bin_dir and conf_dir locations
Get-Content $Env:ProgramFiles\containerd\config.toml

# Register and start service
containerd.exe --register-service
Start-Service containerd

# Validate
containerd.exe --version
```

CNI
```bash
$networkName = 'nat'
# Get-HnsNetwork is available once you have enabled the 'Hyper-V Host Compute Service' feature
# which must have been done at the Quick setup above
# Enable-WindowsOptionalFeature -Online -FeatureName containers -All
# Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All -All
# the default one named `nat` should be available
# source: https://github.com/MOONLAPSED/cognosis/blob/816f1d126f727370e7cf7f75483572d8330c0215/source/.windows/containerd_windows.ps1
$natInfo = Get-HnsNetwork -ErrorAction Ignore | Where-Object { $_.Name -eq $networkName }
if ($null -eq $natInfo) {
    $gateway = "192.168.100.1"
    $subnet = "192.168.100.0/24"
    New-HNSNetwork -Type Nat -AddressPrefix $subnet -Gateway $gateway -Name "nat"
    $natInfo = Get-HnsNetwork -ErrorAction Ignore | Where-Object { $_.Name -eq $networkName }
}

$gateway = $natInfo.Subnets[0].GatewayAddress
$subnet = $natInfo.Subnets[0].AddressPrefix

$cniConfPath = "$env:ProgramFiles\containerd\cni\conf\0-containerd-nat.conf"
$cniBinDir = "$env:ProgramFiles\containerd\cni\bin"
$cniVersion = "0.3.0"

$UserRepo = "microsoft/windows-container-networking"
$Response = Invoke-RestMethod -Uri "https://api.github.com/repos/$UserRepo/releases/latest"
$Version = $Response.tag_name
$Version = $Version.Replace("v", "")
$Arch = "amd64"	# arm64 also available
curl.exe -LO https://github.com/microsoft/windows-container-networking/releases/download/v$Version/windows-container-networking-cni-$Arch-v$Version.zip
tar xvf windows-container-networking-cni-$Arch-v$Version.zip -C $cniBinDir

# get the CNI plugins (binaries)
New-Item -Path $cniBinDir -ItemType Directory -Force
curl.exe -LO https://github.com/microsoft/windows-container-networking/releases/download/v$cniVersion/windows-container-networking-cni-amd64-v$cniVersion.zip
tar xvf windows-container-networking-cni-amd64-v$cniVersion.zip -C $cniBinDir

$natConfig = @"
{
    "cniVersion": "$cniVersion",
    "name": "nat",
    "type": "nat",
    "master": "Ethernet",
    "ipam": {
        "subnet": "$subnet",
        "routes": [
            {
                "gateway": "$gateway"
            }
        ]
    },
    "capabilities": {
        "portMappings": true,
        "dns": true
    }
}
"@
Set-Content -Path $cniConfPath -Value $natConfig -Force
```

Buildkit
```bash
$UserRepo = "moby/buildkit"
$Response = Invoke-RestMethod -Uri "https://api.github.com/repos/$UserRepo/releases/latest"
$Version = $Response.tag_name
$arch = "amd64" # arm64 binary available too

curl.exe -LO https://github.com/moby/buildkit/releases/download/$Version/buildkit-$Version.windows-$arch.tar.gz
tar.exe xvf .\buildkit-$version.windows-$arch.tar.gz

# after the binaries are extracted in the bin directory
# move them to an appropriate path in your $Env:PATH directories or:
Copy-Item -Path ".\bin" -Destination "$Env:ProgramFiles\buildkit" -Recurse -Force
# add `buildkitd.exe` and `buildctl.exe` binaries in the $Env:PATH
$Path = [Environment]::GetEnvironmentVariable("PATH", "Machine") + `
    [IO.Path]::PathSeparator + "$Env:ProgramFiles\buildkit"
[Environment]::SetEnvironmentVariable( "Path", $Path, "Machine")
$Env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + `
    [System.Environment]::GetEnvironmentVariable("Path","User")

#Start deamon process
buildkitd.exe

buildctl debug info

# windows service
buildkitd `
    --register-service `
    --service-name buildkitd `
    --containerd-cni-config-path="C:\Program Files\containerd\cni\conf\0-containerd-nat.conf" `
    --containerd-cni-binary-dir="C:\Program Files\containerd\cni\bin" `
    --debug `
    --log-file="C:\Windows\Temp\buildkitd.log"

Start-Service buildkitd
sc.exe config buildkitd depend= containerd
Set-Service -StartupType Automatic buildkitd
```

NerdCTL
```bash
# Download and extract desired containerd Windows binaries
$UserRepo = "containerd/nerdctl"
$Response = Invoke-RestMethod -Uri "https://api.github.com/repos/$UserRepo/releases/latest"
$Version = $Response.tag_name
$Version = $Version.Replace("v", "")
$Arch = "amd64"	# arm64 also available
curl.exe -LO https://github.com/$UserRepo/releases/download/v$Version/nerdctl-$Version-windows-$Arch.tar.gz
https://github.com/containerd/nerdctl/releases/download/v1.7.6/nerdctl-1.7.6-windows-amd64.tar.gz
tar.exe xvf .\nerdctl-$Version-windows-$Arch.tar.gz

New-Item -Path "$Env:ProgramFiles\nerdctl\" -ItemType Directory
Copy-Item -Path .\nerdctl.exe -Destination "$Env:ProgramFiles\nerdctl\" -Force

# add `nerdctl.exe` binary in the $Env:PATH
$Path = [Environment]::GetEnvironmentVariable("PATH", "Machine") + `
    [IO.Path]::PathSeparator + "$Env:ProgramFiles\nerdctl"
[Environment]::SetEnvironmentVariable( "Path", $Path, "Machine")
$Env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + `
    [System.Environment]::GetEnvironmentVariable("Path","User")
```