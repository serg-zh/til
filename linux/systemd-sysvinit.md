# Systemd vs Sysvinit

## Service Related Commands

 comments                            | sysvinit                  | systemd
-------------------------------------|---------------------------|------------
start a service                      | service dummy start       | systemctl start dummy.service
stop a service                       | service dummy stop        | systemctl stop dummy.service
restart a service                    | service dummy restart     | systemctl restart dummy.service
reload a service                     | service dummy reload      | systemctl reload dummy.service
service status                       | service dummy status      | systemctl status dummy.service
restart a service if already running | service dummy condrestart | systemctl condrestart dummy.service
enable service at startup            | chkconfig dummy on        | systemctl enable dummy.service
disable service at startup           | chkconfig dummy off       | systemctl disable dummy.service
check if a service is enabled at startup| chkconfig dummy        | systemctl is-enabled dummy.service
create a new service file or modify conf| chkconfig dummy --add  | ----

## Runlevels

 comments                            | sysvinit                  | systemd
-------------------------------------|---------------------------|------------
system halt                          | 0                         | runlevel0.target, poweroff.target
single user mode                     | 1, s, single              | runlevel1.target, rescue.target
multi user                           | 2                         | runlevel2.target, multi-user.target
multi user with network              | 3                         | runlevel3.target, multi-user.target
experimental                         | 4                         | runlevel4.target, multi-user.target
multi user, with network, graphical  | 5                         | runlevel5.target, graphical.target
reboot                               | 6                         | runlevel6.target, reboot.target
emergency shell                      | emergency                 | emergency.target
change to multi user runlevel/target | telinit 3                 | systemctl isolate multiuser.target (or systemctl isolate runlevel3.target)
set multiuser target on next boot    | sed s/^id:.*:initdefault:/ id:3:initdefault:/ | systemctl set-default multi-user.target
check current runlevel               | runlevel                  | systemctl get-default
change default runlevel              | sed s/^id:.*:initdefault:/id:3:initdefault:/  | systemctl set-default multi-user.target

## Miscellaneous Commands

 comments                            | sysvinit                  | systemd
-------------------------------------|---------------------------|------------
system halt                          | halt                      | systemctl halt
power off the system                 | poweroff                  | systemctl poweroff
restart the system                   | reboot                    | systemctl reboot
suspend the system                   | pm-suspend                | systemctl suspend
hibernate                            | pm-hibernate              | systemctl hibernate
follow the system log file           | tail -f /var/log/messages | journalctl -f

## Systemd New Commands

 comments                                                   |  systemd
------------------------------------------------------------|--------------
execute a systemd command on remote host                    | systemctl dummy.service start -H user@host
Check boot time                                             | systemd-analyze or systemd-analyze time
Kill all processes related to a service                     | systemctl kill dummy
Get logs for events for today                               | journalctl --since=today
Hostname and other host related information                 | hostnamectl
Date and time of system with timezone and other information | timedatectl
