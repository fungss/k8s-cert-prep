# Service Management with SYSTEMD

## SYSTEMD Service
To auto restart svc when system boots

Create a service in /etc/systemd/system/project-mercury.service
```
[Unit]
Description=Python Django for Project Mercury
Documentation=http://.....
After=postgresql.service

[Service]
ExecStart= /bin/bash /usr/bin/project-mercury.sh
User=project_mercury # <- for loading configured svc account
Restart=on-failure
RestartSec=10

[Install]
WantedBy graphical.target # <- load when booting into specified runlevel/systemd target
```
then ```systemctl start project-mercury.service``` to start the svc. 

To check status
```
systemctl status project-mercury.service
```
also, systemctl stop/daemon-reload

## systemctl and journalctl
```
journalctl -u docker.service
```