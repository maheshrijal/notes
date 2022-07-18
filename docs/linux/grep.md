## GREP
##### Count the number of matches
```bash
grep -c 'error' /var/log/syslog
```

##### Case insensitive search
```bash
grep -i 'Alias' /.bashrc
```
##### Recursive search within sub-dirs
```bash
grep -r 'error' /var/log   
```

##### Returns file without matches
```bash
grep -L 'Alias' /home/mhs*
```