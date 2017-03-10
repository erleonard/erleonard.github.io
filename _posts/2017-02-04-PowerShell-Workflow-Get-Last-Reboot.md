---
layout: single
title:  "PowerShell Workflow: Get-LastReboot"
date:   2017-03-09 12:47:12 -0500
categories: blog
---

Recently I had to check hundreds of servers to check their uptime and identify which servers had rebooted.  I had my own script to check a single server but needed to turbo charge my script to check many servers in a short amount of time.

This script will utilize a PowerShell workflow to check multiple servers in parallel.

<script src="https://gist.github.com/erleonard/cfdec8c9080702178ea1d3c95c1054e3.js"></script>

Eric.
