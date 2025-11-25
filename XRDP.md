# XRDP on Linux
Date: 2025/11/25

Was using X11VNC, it seems it's about time to review & upgrade the Remote Desktop application

_Fedora_ has XRDP native supported, switch from X11VNC to **XRDP + X11rdp** backend on _Xubuntu_ running the **XFCE** environment.

## Required packages
```code
xfce4 xfce4-goodies
xrdp  xorgxrdp
net-tools
```
Note

- `xrdp` calls `netstat`

## Configuration
### Start XFCE
Either modify /etc/xrdp/startwm.sh to launch **_startxfce4_** (or _xfce4-session_), OR set ``UserWindowManager=startxfce4 (or xfce4-session)`` in the /etc/xrdp/sesman.ini file.


```bash
# Create or edit the user-level .xsession file
echo "xfce4-session" > ~/.xsession
# or system-wide (recommended if multiple users)
sudo bash -c 'echo "xfce4-session" > /etc/skel/.xsession'
sudo cp ~/.xsession /etc/xrdp/startwm.sh   # fallback script
```

```
/etc/xrdp/
|-- startwm.sh  # Launch Windows Manager
|-- sesman.ini  # New environment or Shadow/Console
`-- xrdp.ini    # Configuration
```

### startwm.sh
```code
...
#test -x /etc/X11/Xsession && exec /etc/X11/Xsession
#exec /bin/sh /etc/X11/Xsession
startxfce4
```

### Misc
#### Change port number
File: /etc/xrdp/xrdp.ini

For example: Change from 3389 (default) to 3390.
```bash
sudo sed -i 's/3389/3390/g' /etc/xrdp/xrdp.ini
```
## Start XRDP server
```bash
$ sudo systemctl [status | start | enable | restart] xrdp
```
Enable firewall (example)
```bash
$ sudo ufw allow from 192.168.1.1/31 to any port 3389 proto tcp
```
## Check
```bash
$ sudo systemctl status xrdp
$ netstat -tulnp | grep 3389  # may only see tcp6
```

***
## Connect from the Client
### SSH Tunnel (via Jump server)

```bash
# Manual test
# From local port 3381 jump via gateway.alamy to xrdp.alamy
$ ssh -J gateway.alamy -L 3381:localhost:3389 xrdp.alamy
```
Note

* Domain: "alamy"
* Jump server: gateway.alamy
* XRDP server: xrdp.alamy (port 3389)
* Configure ~/.ssh/config properly

### Remmina
Protocol: **RDP - Remote Desktop Protocol**

Server: **localhost:3381**

Username & Password (Optional)

***

## License
[GNU GPLv3](https://choosealicense.com/licenses/gpl-3.0/)
