# Common tips

`lsblk -p` список блочных устройств с выводом партиций, подключенных файловых систем,  размеров и т.д.

Журнал системных событий, общий или для конкретного сервиса:

```shell
journalctl -b                         # current boot
journalctl -u service-name.service    # for the given service
journalctl -u service-name.service -b # the service and the current boot
```
