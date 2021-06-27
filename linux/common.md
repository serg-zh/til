# Common tips

`lsblk -p` список блочных устройств с выводом партиций, подключенных файловых систем,  размеров и т.д.

Журнал системных событий, общий или для конкретного сервиса:

```shell
journalctl -b                         # current boot
journalctl -u service-name.service    # for the given service
journalctl -u service-name.service -b # the service and the current boot
journalctl --rotate --vacuum-time=1month # оставлять сообщения не старше месяца

```

## Подключение двух logitech устройств к одному unifying устройству

В компьютере оставить включенным донгл, к которому подключена клавиатура. Мышку выключить. Выполнить команды show и pair. Иногда они выполняются с ошибкой, просто повторить их выполнение. Когда появится надпись `Pairing: turn your new device on (timing out in 20 seconds).` включить питание на мышке.

```shell
sudo aptitude install solaar
solaar-cli show
solaar-cli pair
```

Так же появляется приложение с интерфейсом, которое показывает устройства logitech. Приложение надоедает, так как каждый раз при загрузке показывает свое окно. При этом неправильно показывает состояние батарейки на мышке.
