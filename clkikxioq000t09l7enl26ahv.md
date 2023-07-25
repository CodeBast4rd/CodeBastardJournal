---
title: "WSL2 and WireGuard on Host"
seoTitle: "WSL2 WireGuard Host Integration"
seoDescription: "Optimize WSL2 & WireGuard VPN on Windows: Guide to share VPN, ensure connectivity, and troubleshoot for smooth web development"
datePublished: Tue Jul 25 2023 17:37:20 GMT+0000 (Coordinated Universal Time)
cuid: clkikxioq000t09l7enl26ahv
slug: wsl2-and-wireguard-on-host
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/T3XUccTgw7s/upload/a6f40f36c7c3e15f873a47abea0b653d.jpeg
tags: windows, networking, wsl, vpn, wireguard

---

I spend multiple hours fighting the bad fight.

The problem:

Since WSL2 the host network is no longer shared between the host and WSL through a separate network adapter. Great for security and bad for funnelling your traffic through your host's VPN. And for this use case is no straight way to go.

Like every good company, my current client keeps a wire guard VPN to manage secure outside connections and secure the network for remote work.

As a web developer on Windows of course need WSL it is just not sane to fight with the powershell and binary hell for Windows for all the different possible stacks.

My solution was:

1. **Share the VPN Connection with WSL:**
    
    You have to change the network settings on your Windows machine to allow the WSL instance to share the VPN connection. Here's how you do it:
    
    * Open `Control Panel` -&gt; `Network and Internet` -&gt; `Network and Sharing Center` -&gt; `Change adapter settings`.
        
    * Right-click on the WireGuard Tunnel adapter, and click on `Properties`.
        
    * Go to the `Sharing` tab and check the `Allow other network users to connect through this computer's Internet connection` box.
        
    * In the dropdown menu, select the adapter that corresponds to the WSL instance. It might be labelled as `vEthernet (WSL)` or `vEthernet (Default Switch)` or similar. Click `OK`.
        
2. **Confirm Connectivity Inside WSL:**
    
    Open your WSL instance and check if you can connect through the VPN by running commands such as `ping`, `curl`, or `wget` to confirm you're connecting through the VPN.
    

This worked for `ping`, `dig` and co but `ssh` and `git` was still not usable.

This [Holly-Comment](https://github.com/microsoft/WSL/issues/4585#issuecomment-610061194) fixed at least the traffic flow fully with this command in your PowerShell as Admin.

```powershell
New-NetFirewallRule -DisplayName "WSL" -Direction Inbound  -InterfaceAlias "vEthernet (WSL)"  -Action Allow
```

Afterwards, I also increased the MTU (Maximum Transmission Unit) as recommended. Without this change `ssh` and `git` still was hanging on execution.

```bash
sudo ifconfig eth0 mtu 1350
```

For other souls visiting this circle of windows hell, they can try their hand on

[https://github.com/sakai135/wsl-vpnkit](https://github.com/sakai135/wsl-vpnkit)

And for the masochist troubleshooting with Microsoft:

[https://learn.microsoft.com/en-us/windows/wsl/troubleshooting#wsl-has-no-network-connectivity-once-connected-to-a-vpn](https://learn.microsoft.com/en-us/windows/wsl/troubleshooting#wsl-has-no-network-connectivity-once-connected-to-a-vpn)

I like my daily dose of pain like any other developer but for today I did not have the nerve and endurance for it anymore.