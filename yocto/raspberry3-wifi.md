# Настройка сети WiFI после загрузки

Для подключения к сети WiFi при работе с базовым образом Yocto, необходимо отредактировать файл `/etc/network/interfaces`

```shell
iface wlan0 inet dhcp
  wireless_mode managed
  wireless_essid any
  wpa-driver wext
  wpa-conf /etc/wpa_supplicant.conf
```

А также файл `/etc/wpa_supplicant.conf`

```shell
network={
  ssid="wlan-name"    # название сети
  psk="keyHex"        # пароль к сети
  proto=RSN
  key_mgmt=WPA-PSK
  pairwise=CCMP
  auth_alg=OPEN
}
```

Запуск сети выполняется командой `ifup wlan0`

[настройка wlan](https://raspinterest.wordpress.com/2017/02/28/configure-wlan0-and-bluetooth-in-yocto-raspberry-pi-3/)