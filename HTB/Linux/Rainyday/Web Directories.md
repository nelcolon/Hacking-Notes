```bash
$ feroxbuster -u http://rainycloud.htb

 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher π€                 ver: 2.7.3
ββββββββββββββββββββββββββββ¬ββββββββββββββββββββββ
 π―  Target Url            β http://rainycloud.htb
 π  Threads               β 50
 π  Wordlist              β /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 π  Status Codes          β [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 π₯  Timeout (secs)        β 7
 π¦‘  User-Agent            β feroxbuster/2.7.3
 π  Config File           β /etc/feroxbuster/ferox-config.toml
 π  HTTP methods          β [GET]
 π  Recursion Depth       β 4
ββββββββββββββββββββββββββββ΄ββββββββββββββββββββββ
 π  Press [ENTER] to use the Scan Management Menuβ’
ββββββββββββββββββββββββββββββββββββββββββββββββββ
200      GET      110l      270w     4378c http://rainycloud.htb/
200      GET       68l      157w     3686c http://rainycloud.htb/register
302      GET        5l       22w      189c http://rainycloud.htb/logout => http://rainycloud.htb/
200      GET       63l      142w     3254c http://rainycloud.htb/login
308      GET        5l       22w      239c http://rainycloud.htb/api => http://rainycloud.htb/api/
302      GET        5l       22w      199c http://rainycloud.htb/new => http://rainycloud.htb/login
200      GET        1l        1w       59c http://rainycloud.htb/api/list
200      GET        1l        1w       29c http://rainycloud.htb/api/healthcheck
[####################] - 2m     60000/60000   0s      found:8       errors:0      
[####################] - 2m     30000/30000   216/s   http://rainycloud.htb/ 
[####################] - 2m     30000/30000   217/s   http://rainycloud.htb/api/ 
```