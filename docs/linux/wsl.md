# WSL2 CLI

## Enable Networking on VPN

WSL2 with Cisco AnyConnect

!!! tip

    Use [wsl-vpnkit](https://github.com/sakai135/wsl-vpnkit)
```
wsl.exe -d wsl-vpnkit service wsl-vpnkit start
```
## Shutdown Distros
Shutdown WSL distro
```
wsl -t DISTRO-NAME
```

Shutdown all WSL Distros
```
wsl --shutdown
```

## K8s on WSL2

### Rancher Desktop

SSH to VM (Powershell)
```
wsl -d rancher-desktop
```
or
```
rdctl shell
```

Run a Command
```
rdctl shell $CMD
```

### Rancher Desktop (Linux/Mac)
```
limactl shell rancher-desktop
```