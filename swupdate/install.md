# SWUpdate install and configure

## Сборка

## Установка зависимостей (Ubuntu 18.04 server)

``` bash
sudo aptitude install liblua5.2-0 libzmq5 libcurl4 libconfig9 libarchive13
```

## Запуск сервера

Интерфейс для веб в исходниках: swupdate/examples/www/v2

Для использования swupdate/web-app смотреть [документацию](https://sbabic.github.io/swupdate/mongoose.html#customize)

```bash
#!/bin/bash

root=`df / | grep -Eo '/dev/[^ ]+'`
echo "Current root partition $root"

# Write to another partition
[[ "$root" == "/dev/sda2" ]] && version=sda2 
[[ "$root" == "/dev/sda3" ]] && version=sda3

# Check if we found right partition
[[ -z $version ]] && { echo "ERROR, wrong root partition" ; exit -1; }

swupdate -M --select stable,$version -H mupd:18.04 -v -w "-r www/v2/"
```

## Работа с образом диска virtualbox

Скрипт для создания упакованной копии раздела с виртуального диска.

```bash
vbox-img convert --srcfilename ubuntu_swupdate.vdi --dstfilename disk.img --dstformat raw
kpartx -av disk.img
dd if=/dev/mapper/loop0p2 of=root.img status=progress
kpartx -d disk.img
gzip root.img
```

Дополнительные полезные команды.

* ```tune2fs -U $(uuidgen) /dev/sda3``` - создать новый uuid для файловой системы
* ```partprobe``` - сообщить системе об изменения в таблице разделов
* ```blkid``` - список разделов с uuid и label

### Работа с разделами

* sfdisk
* cgdisk
* cfdisk
* kpartx

Конфигурация grub сохранятеся в общий раздел efi, который монтируется в /boot/grub.
Пример fstab:

```bash
/dev/sda2      /          ext4 defaults 0 0
UUID=E80A-0328 /boot/efi  vfat defaults 0 0
UUID=E80A-0328 /boot/grub vfat defaults 0 0
```

Вывод mount:

``` bash
/dev/sda2 on / type ext4 (rw,relatime,data=ordered)
/dev/sda1 on /boot/grub type vfat (rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro)
/dev/sda1 on /boot/efi type vfat (rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro)
```

Конфигурация grub для загрузки с разных разделов. Сохраняется в /boot/grub/custom.cfg

``` bash
menuentry 'Ubuntu /dev/sda2' --id sda2 {
        set root='hd1,gpt2'
        linux /boot/vmlinuz-4.15.0-106-generic root=/dev/sda2 ro nomodeset
        initrd /boot/initrd.img-4.15.0-106-generic
    }

menuentry 'Ubuntu /dev/sda3' --id sda3 {
        set root='hd1,gpt3'
        linux /boot/vmlinuz-4.15.0-106-generic root=/dev/sda3 ro nomodeset
        initrd  /boot/initrd.img-4.15.0-106-generic
    }
```

* ```sudo grub-set-default``` -  to fix boot entry
* ```sudo grub-reboot``` - to try boot entry
