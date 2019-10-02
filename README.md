# python-systemd
Tutorial to run Python script via systemd

---
## Systemd Service file location and management
 - Write Service file
 - place your service files inside ** /etc/systemd/system/ ** folder
 - reload services using ```systemctl daemon-reload```
 - after that you are able to perform operations such as
    - systemctl start name.service
    - systemctl status name.service
    - systemctl stop name.service
    - systemctl restart name.service


