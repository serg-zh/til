# Common tips

## Полезные ссылки

[Yocto Super Fast Build](https://medium.com/@zertsekel/yocto-super-fast-build-d3a8f7a3680)

[Yocto Builds without Internet](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/60129817/Xilinx+Yocto+Builds+without+an+Internet+Connection)

[Yocto Quick Build](https://www.yoctoproject.org/docs/current/brief-yoctoprojectqs/brief-yoctoprojectqs.html#releases)

[Использование Docker для построения Yocto](https://www.embeddeduse.com/2019/02/11/using-docker-containers-for-yocto-builds/)

[Использование CROPS контейнеров для построения Yocto](https://www.embeddeduse.com/2019/05/06/yocto-builds-with-crops-containers/)

[Poky Container repo](https://github.com/crops/poky-container)

[5 Steps to Compile Yocto Using Docker Containers](https://www.witekio.com/blog/5-steps-compile-yocto-docker-container/)

[Building your own Linux distribution for Raspberry Pi](https://github.com/oscr/raspberrypi_linux)

## Переменные для рассмотрения

```shell
DISTRO_FEATURES_append
IMAGE_INSTALL_append
```

## Полезные команды

```shell
bitbake -c listtasks core-image-minimal
bitbake -c fetch core-image-minimal

bitbake-layers show-recipes
bitbake-layers show-recipes "<image_name>"
bitbake-layers show-recipes "*-image-*"

bitbake-layers show-layers

bitbake -e imagename | grep "^DISTRO_FEATURES"
bitbake -e imagename | grep "FSTYPES"
```

## Ошибка *QA Issue: No GNU_HASH in the ELF binary*

При построении приложения с помощью bitbake появляется указанная ошибка.
Необходимо добавить в рецепт приложения *.bb следующую строку

```shell
TARGET_CC_ARCH += "${LDFLAGS}"
```
