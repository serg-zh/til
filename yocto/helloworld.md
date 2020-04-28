# Модификация базового проекта Yocto для сборки пользовательского приложения

Добавим приложение пользователя как слой мета. Создадим директории для нашего приложения.  На одном уровне с директориями build и poky, выполним команду:

```shell
mkdir -p meta-hello/conf meta-hello/recipes meta-hello/recipes/files
```

Состав файла `meta-hello/conf/layer.conf`

```python
# We have a conf and classes directory, append to BBPATH
BBPATH .= ":${LAYERDIR}"

# We have a recipes directory containing .bb and .bbappend files, add to BBFILES
BBFILES += "${LAYERDIR}/recipes*/*.bb \
            ${LAYERDIR}/recipes*/*.bbappend"

BBFILE_COLLECTIONS += "myhello"
BBFILE_PATTERN_myhello := "^${LAYERDIR}/"
BBFILE_PRIORITY_myhello = "10"

IMAGE_INSTALL_append = " myhello"
```

Состав файла `meta-hello/recipes/myhello.bb`

```python
DESCRIPTION = "Simple helloworld application"
SECTION = "examples"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302"

SRC_URI = "file://helloworld.c"

S = "${WORKDIR}"

TARGET_CC_ARCH += "${LDFLAGS}"

do_compile() {
        ${CC} helloworld.c -o helloworld
}

do_install() {
        install -d ${D}${bindir}
        install -m 0755 helloworld ${D}${bindir}
}
```

Состав файла `meta-hello/recipes/files/helloworld.c`

```C
#include <stdio.h>

int main(void)
{
        printf("Hello world!\n");

        return 0;
}
```

В файле `build\conf\bblayers.conf` включим поддержку нашего приложения:\

```python
BBLAYERS ?= " \
  /yocto/yocto-quick/poky/meta \
  /yocto/yocto-quick/poky/meta-poky \
  /yocto/yocto-quick/poky/meta-yocto-bsp \
  /yocto/yocto-quick/meta-myhello \
  "
```

Перейдем в каталог build и выполним команду

``` shell
MACHINE=raspberrypi3 bitbake core-image-base
```

По окончании сборки мы получим новый образ системы, в который будет включено наше приложение. После установки и загрузки нового образа системы можно выполнить команду `helloworld` и убедиться в том что она выполняется.
