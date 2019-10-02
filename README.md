# python-systemd
Tutorial to run Python script via systemd

---
## Systemd Service file location and management
 - Write Service file
 - place your service files inside ```/etc/systemd/system/``` folder
 - reload services using ```systemctl daemon-reload```
 - after that you are able to perform operations such as
    - systemctl start name.service
    - systemctl status name.service
    - systemctl stop name.service
    - systemctl restart name.service

---
# Creating Service file
## Syntax 
```
[Unit]
Description=**Enter Service Description**
After=syslog.target

[Service]
Type=simple
User=**Enter username**
Group=**Enter groupname**
WorkingDirectory=**Enter working dir path**
ExecStart=**Enter python file path**
StandardOutput=syslog
StandardError=syslog

[Install]
WantedBy=multi-user.target
```

## Example
```
[Unit]
Description=Twitter News Service
After=syslog.target

[Service]
Type=simple
User=nivratti
Group=nivratti
WorkingDirectory=/programming/python/projects/
ExecStart=/programming/python/projects/python-demo.py
StandardOutput=syslog
StandardError=syslog

[Install]
WantedBy=multi-user.target
```
---

# Imp -- Specify python interpreter
Specify Python interpreter at first line of python file
```
#!/usr/bin/python -u
```
