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
WorkingDirectory=/path/to/working/dir/
ExecStart=/usr/bin/python /python/file/path/demo.py
Restart=on-failure
StandardOutput=syslog
StandardError=syslog

[Install]
WantedBy=multi-user.target
```

## Example
```
[Unit]
Description=Python Demo
After=syslog.target

[Service]
Type=simple
User=nivratti
Group=nivratti
WorkingDirectory=/programming/python/projects/
ExecStart=/usr/bin/python /programming/python/projects/python-demo.py
StandardOutput=syslog
StandardError=syslog

[Install]
WantedBy=multi-user.target
```
---

# Imp -- Specify python interpreter
### Specify Python interpreter 
 - In unit(Service File)
   __/usr/bin/python__ or any other path
   ```
   ExecStart=/usr/bin/python /file/path/python_demo_service.py
   ```
   
  - or at first line of python file
  ```
  #!/usr/bin/python -u
  ```
---

# Service file configuration details
### ADD Description
```
[Unit]
Description=Python Demo Service
```
### Script Location
```
WorkingDirectory=**Enter working dir path**
ExecStart=**Enter python file path**
```

### Python Output -- STDOUT and STDERR
You might have noticed that the output of our script's print calls did not show up on your terminal. This is because systemd detached the service process from that terminal and also redirected the process's STDOUT and STDERR streams.
```
[Service]
Environment=PYTHONUNBUFFERED=1
```
As always when you change your unit file you need to tell systemd to reload its configuration, and (if your service is currently running), restart the service:
```
$ systemctl daemon-reload
$ systemctl restart python_demo_service
```
The output from our script should now show up in systemd's logs, which by default are redirected to syslog:
```
$ grep 'Python Demo Service' /var/log/syslog
Dec 30 18:05:34 leibniz python[26218]: Hello from the Python Demo Service
```
Another way to display your service's output is via
```
$ journalctl --user-unit python_demo_service
```

### Automatically Starting the Service during Boot
Many services are intended to be started automatically when the system boots. This is easy to achieve using systemd. First we need to attach our service to a suitable target: targets are special systemd units that are used for grouping other units and for synchronization during startup. See [systemd.target](https://www.freedesktop.org/software/systemd/man/systemd.target.html) for details about targets in general and [systemd.special](https://www.freedesktop.org/software/systemd/man/systemd.special.html) for a list of built-in targets.

For user services, the __default.target__ is usually a good choice. Add the following to your unit file:
```
[Install]
WantedBy=default.target
```
Our service is now ready to be started automatically, but for that to actually happen we have to __enable__ the service first:
```
$ systemctl --user enable python_demo_service
```

####To disable autostart, simply disable your service:
```
$ systemctl --user disable python_demo_service
```
To check whether your service is enabled, use
```
$ systemctl --user list-unit-files | grep python_demo_service
```

### Automatically Restarting the Service after Failure
As with any other software, your service might crash. In that case, systemd can automatically try to restart it. By default, systemd will not do that, so you have to enable this functionality in your unit file.
```
[Service]
Restart=on-failure
```
---
# Moving the Python Script
If you moved script file we also need to change the script's location in our unit file: update the __ExecStart=...__ line to
```
ExecStart=/usr/bin/python /new/path/python_demo_service.py
```

# Service Management
Manage Systemd Services and Units.
--
## Starting and Stopping Services
To __start__ a systemd service, executing instructions in the service’s unit file, use the __start__ command. 
```
sudo systemctl start application_name
```

To stop a currently running service, you can use the __stop__ command instead:
```
sudo systemctl stop application_name
```
## Restarting
To restart a running service, you can use the __restart__ command:
```
sudo systemctl restart application_name
```

## Enabling and Disabling Services
To tell __systemd__ to start services __automatically at boot__, you must enable them.
To start a service at boot, use the enable command:
```
sudo systemctl enable application_name
```
To disable the service from starting automatically, you can type:
```
sudo systemctl disable application_name
```

## Checking the Status of Services
To check the status of a service on your system, you can use the status command
```
systemctl status application_name
```
