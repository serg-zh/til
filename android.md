# Сборка и модификация Android

* [Android Build System - очень хорошее руководство](https://elinux.org/Android_Build_System)
* [Creating a Android/AOSP Build machine on Ubuntu 20.04](https://back2basics.io/2020/05/creating-a-android-aosp-build-machine-on-ubuntu-20-04)

## Наблюдение за используемым ccache

docker exec -ti -u root [android] watch -n1 -d ccache -s

## Android emulator docker

https://github.com/antonienko/android-emulator

## Версия SDK

Файл build.gradle, переменная compileSdkVersion 28

## Ключи платформы для подписи пакетов

android_build/build/make/target/product/security

## Как подписывать свои сборки

https://source.android.com/devices/tech/ota/sign_builds.html

## При изменении приложения

* удалить apk из product/generic_x86_64/vendor/priv-app/myapplication
* удалить все (?) *.img из product/generic_x86_64
* выполнить ```m```

