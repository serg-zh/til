# Mender for Raspberry Pi 3

## Установка утилиты Repo

Для работы с Mender необходимо установить утилиту Repo. Для этого выполняем следующие команды:

```shell
mkdir ${HOME}/bin
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ${HOME}/bin/repo
chmod a+x ${HOME}/bin/repo
echo "PATH=${PATH}:~/bin" >> ${HOME}/.bashrc
```

## Настройка Yocto

Подготовка исходных кодов с помощью `repo`

```shell
export BRANCH="warrior"
mkdir mender-raspberrypi && cd mender-raspberrypi
repo init -u https://github.com/mendersoftware/meta-mender-community \
           -m meta-mender-raspberrypi/scripts/manifest-raspberrypi.xml \
           -b ${BRANCH}
repo sync
```

## Сборка образа

``` shell
source setup-environment raspberrypi
```

Для ускорения сборки обязательно отредактировать local.conf в части переменных

``` shell
DL_DIR = "/yocto/downloads"
SSTATE_DIR = "/yocto/sstate-cache"
```

Сборка образа

``` shell
MACHINE=raspberrypi3 bitbake core-image-base
```

## Настройка Raspberry для загрузки

Артефакты после сборки

* tmp/deploy/images/raspberrypi3/core-image-base-raspberrypi3.sdimg
* tmp/deploy/images/raspberrypi3/core-image-base-raspberrypi3.mender

Первичная инициализация образа с поддержкой Mender

```shell
lsblk -p             # находим нужный диск, если он смонтирован, то отмонтировать (наверное это нужно)
umount /dev/mmcblk0* # отмонтируем, если диск смонтирован
# sdimg - образ для первоначальной записи
sudo dd bs=4M if=core-image-base-raspberrypi3.sdimg of=/dev/mmcblk0 conv=fsync status=progress
```

Во время первой загрузки система расширяет раздел data на оставшееся свободное место.

## Автономное Standalone обновление

Для выполнения автономного обновления нам понадобится https сервер. Руководство по запуску простого https сервера - [Простейший https сервер, только для тестирования](../mender/simplehttps.md)

После того, как сервер https запущен, для автономного обновления выполняем на Raspberry Pi3 следующую команду:

```shell
mender -rootfs https:/<simplehttpsserver:port>/core-image-base-raspberrypi3.mender
```

Mender загрузит обновление, установит его и сконфигурирует загрузчик для загрузки нового образа. Для применения нового образа необходимо перезагрузить систему командой `reboot`.

Если результат обновления удовлетворительный, можно зафиксировать его командой

```shell
mender -commit
```

Без выполнения данной команды при последующей перезагрузке будет использован предыдущий образ системы.

* [Raspberry Pi 3 Model B/B+](https://hub.mender.io/t/raspberry-pi-3-model-b-b/57)
* [Google Repo](https://hub.mender.io/t/google-repo/58)
* [Updating Raspberry Pi boot firmware files using Yocto Project and Mender](https://hub.mender.io/t/updating-raspberry-pi-boot-firmware-files-using-yocto-project-and-mender/1719)
* [Standalone deployments](https://docs.mender.io/2.3/architecture/standalone-deployments)
