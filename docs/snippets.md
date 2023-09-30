# Useful code snippets and scripts


##### Enable paste on websites that block it

``` js title="Don't F&_k with Paste"
// Prevents websites from blocking copy/paste
var allowPaste = function(e){
  e.stopImmediatePropagation();
  return true;
};
document.addEventListener('paste', allowPaste, true);
```


##### Get IPV4

``` sh title="Get the current IPv4 address"
curl -s https://api.ipify.org
```

##### Get IPV6

``` sh title="Get the current IPv6 address"
curl -s https://api6.ipify.org
```


##### List versions of PyPI package

``` sh title="List the available versions of a PyPi package"
curl -s https://pypi.org/pypi/<package>/json | jq '.releases | keys | .[]'

```

##### Git: Sign commits

```sh title="Sign all commits from the commit id"
git rebase --exec 'git commit --amend --no-edit -n -S' -i <commit id>
```

```sh title="Resign all commits in a repository"
git rebase --exec 'git commit --amend --no-edit --no-verify -S' -i --root
```

```sh title="Keep the commited date when signing using above commands"
git rebase --committer-date-is-author-date -i --root
```


## Powershell 

Restart windows into BIOS

```powershell
shutdown /r /fw /t 0
```

Restart windows into Safe Mode
```powershell
shutdown /r /o /t 0
```

Restart windows into Normal Mode
```powershell
shutdown /r /t 0
```

Shutdown windows
```powershell
shutdown /s /t 0
```

Get WIFI Passwords on windows
```powershell
(netsh wlan show profiles) | Select-String "\:(.+)$" | %{$name=$_.Matches.Groups[1].Value.Trim(); $_} | %{(netsh wlan show profile name="$name" key=clear)} | Select-String "Key Content\W+\:(.+)$" | %{$pass=$_.Matches.Groups[1].Value.Trim(); $_} | %{[PSCustomObject]@{ PROFILE_NAME=$name;PASSWORD=$pass }} | Format-Table –Wrap
```