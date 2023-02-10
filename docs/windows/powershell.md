---
title: Windows Powershell commands
---

# Powershell Commands

## Shutdown & Restarts

### Restart into Bios
```powershell
shutdown /r /fw /t 0
```

### Restart into Safe Mode
```powershell
shutdown /r /o /t 0
```

### Restart into Normal Mode
```powershell
shutdown /r /t 0
```

### Shutdown
```powershell
shutdown /s /t 0
```

## Wireless
### Wifi Passwords
```powershell
(netsh wlan show profiles) | Select-String "\:(.+)$" | %{$name=$_.Matches.Groups[1].Value.Trim(); $_} | %{(netsh wlan show profile name="$name" key=clear)} | Select-String "Key Content\W+\:(.+)$" | %{$pass=$_.Matches.Groups[1].Value.Trim(); $_} | %{[PSCustomObject]@{ PROFILE_NAME=$name;PASSWORD=$pass }} | Format-Table –Wrap
```

### Wifi Profiles
```powershell
netsh wlan show profiles
```

### Wifi Scan
```powershell
netsh wlan show networks mode=bssid
```