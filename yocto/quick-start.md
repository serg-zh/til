# Сборка базового дистрибутива Poky

## Выгрузка исходных кодов poky

Первым делом нам необходимо загрузить исходные коды проекта Yocto с дистрибутивом Poky. Данную команду можно выполнить как под управлением хоста, так и в работающем контейнере. Проследите чтобы вы находилсь в рабочем каталоге:

```shell
git clone git://git.yoctoproject.org/poky
cd poky
git fetch --tags
git tag | grep 3.0.2
git checkout tags/yocto-3.0.2 -b my-yocto-3.0.2
```

Мы используем ревизию, для которой есть [sstate кеш](http://sstate.yoctoproject.org). При конфигурировании проекта мы укажем соответствующую нашей версии директорию  на данном сервер в качестве sstate кеш.  Это позволит значительно уменьшить время сборки дистрибутива.

## Подготовка к сборке

Следущим шагом является инициализация каталога build и окружения для сборки:

```shell
$ cd ~/poky
$ source oe-init-build-env
You had no conf/local.conf file. This configuration file has therefore been
created for you with some default values. You may wish to edit it to, for
example, select a different MACHINE (target hardware). See conf/local.conf
for more information as common configuration options are commented.
You had no conf/bblayers.conf file. This configuration file has therefore been
created for you with some default values. To add additional metadata layers
into your configuration please add entries to conf/bblayers.conf.
The Yocto Project has extensive documentation about OE including a reference
manual which can be found at:
    http://yoctoproject.org/documentation
For more information about OpenEmbedded see their website:
    http://www.openembedded.org/
### Shell environment set up for builds. ###
You can now run 'bitbake <target>'
Common targets are:
    core-image-minimal
    core-image-sato
    meta-toolchain
    meta-ide-support
You can also run generated qemu images with a command like 'runqemu qemux86-64'
```

После выполнения указанной команды в директории Poky появится каталог build, который станет для нас текущим. Далее нам небходимо внести изменения в файл build/local.conf. Укажем пути к каталогам, в которые будут сохраняться скачанные исходные коды пакетов, каталогам с локальным sstate кешем и сетевым sstate кешем.

```shell
DL_DIR = "/yocto/downloads"
SSTATE_DIR = "/yocto/sstate-cache"
SSTATE_MIRRORS = "file://.* http://sstate.yoctoproject.org/3.0.2/PATH;downloadfilename=PATH"
```

Указанные локальные каталоги рекомендуется сделать общими для всех проектов Yocto, это позволить уменьшить использование сети интернет и будет способствовать ускорению сборки дистрибутивов.

## Сборка дистрибутива

Теперь мы можем выполнить сборку дистрибутива. В работающем контейнере выполните указанную ниже команду. Убедитесь что находитесь в каталоге build, который был создан на предыдущем шаге:

```shell
bitbake core-image-minimal
```

На компьютере с 8-ми процессором **Intel Core i7-7700HQ CPU @ 2.80GHz** сборка заняла около 4х часов.

## Запуск построенного образа под quemu

По окончании сборки мы можем запустить собранный образ на выполнение под управлением эмулятора qemu:

``` shell
runqemu core-image-minimal slirp nographic
```

Дополнительные параметры ```slirp nographic``` позволяют нам запускать эмулятор из работающего конейнера.
