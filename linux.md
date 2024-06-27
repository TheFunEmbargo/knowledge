# Linux


## VMs

Connect `ssh -v <username>@<hostname> -i ~/.ssh/key.pem`

Task Manager `top`

UNIX system report, view Kernal events `dmesg -T`

Search file `grep -i "oom" /var/log/syslog`

Why is my disk full?  `du -sh ~/`

fetch something off a vm? `scp -i <path_to_pem_file> <username>@<hostname>:<remote_file_path> <local_file_path>`

## SSH Tunnel

Open `ssh -fNM -S <whateveryouwishtocallthissocket> -i ~/.ssh/key.pem -L <localport>:<desthost>:<destport> <username>@<hostname>`

Close `ssh -S <whateveryouwishtocallthissocket> -O exit <hostname>`

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


## Ubuntu

Get path in file explorer `ctrl+l`

Kernel events (debug external devices) `dmesg`

`~/.bash_aliases`

## Notes

A service could be a docker container





