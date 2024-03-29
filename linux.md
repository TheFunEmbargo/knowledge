# Linux


## VMs

Connect `ssh -v <username>@<hostname> -i ~/.ssh/key.pem`

Task Manager `top`

UNIX system report, view Kernal events `dmesg -T`

Search file `grep -i "oom" /var/log/syslog`


## Systemd
Manage services


### Create

Define a systemd service
`cat /etc/systemd/system/foo.service`

```
[Unit]

Description=the service for foo
After=network.target  # servers should start after network manager is up


[Service]

User= # whoever this service should be run by (see User)
WorkingDirectory=/var/foo
ExecStart=/var/foo/run.sh
EnvironmentFile=/var/foo/.service.env
Restart=always


[Install]

WantedBy=multi-user.target
```


Define commands to be executed when this service is started (specified with ExecStart= above)
`cat /var/foo/run.sh` 

```
#!/bin/bash -l

set -e

cd /var/foo  # location of the service files

# set up environment & launch 
pyenv install --skip-existing
poetry install

poetry run python foo.py
```

### Manage

#### Start & Stop
`sudo service start foo`

`sudo service stop foo`

`sudo service restart foo`

`sudo systemctl status foo`

#### Logs
`sudo journalctl -u foo.service`


### User

Systemd shouldn't be run by root for security reasons.  Create a user:

Have Jenkins CICDing service? Use jenkins user.

`sudo adduser servicemanager`

Grant ownership of the service & Grant sudo permissions & Enable service management sans password

`sudo chown servicemanager:servicemanager /etc/systemd/system/foo.service`

`sudo visudo -f /etc/sudoers.d/servicemanager`

`servicemanager ALL=(ALL) NOPASSWD: /bin/systemctl restart foo.service, /bin/systemctl status foo.service, /bin/systemctl enable foo.service, /bin/systemctl disable foo.service`

`sudo systemctl restart sudo` (restart sudoers file for permission changes to take effect)


## Notes

A service could be a docker container

