# Использование Docker для сборки образов Yocto

Сложность настройки и установки Yocto, а также большое количетво зависимостей, навело на мысль о том, чтобы использовать контейнеры Docker изоляции всех необходимых для работы с Yocto инструментов.

Данный документ рассказывает как создать образ Docker с необходимыми для работы с Yocto инструментами.

## Установка Docker

Первым делом нужно удалить версии Docker, поставляемые с дистрибутивом. Они сильно отстают от актуальных версий.

```shell
sudo apt-get remove docker docker-engine docker.io containerd runc
```

После того, как мы удалили старые версии, необходимо установить пакеты, которые помогут нам получить свежую версию.

```shell
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl \
    gnupg-agent software-properties-common
```

Добавлям GPG ключ.

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add
```

Добавляем репозиторий Docker, подходящий к нашему дистрибутиву

```shell
sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable"
```

Устанавливаем клиент и сервер Docker CE.

```shell
sudo apt-get update
sudo apt-get -y install docker-ce
```

Последняя команда устанавливает клиент и сервер Docker CE, а также создает группу docker. Добавим пользователя в группу docker, чтобы мы могли запускать Docker без прав root.

```shell
sudo usermod -a -G docker "$(whoami)"
```

Необходимо перелогиниться активировать членство в группе docker.
Теперь можно протестировать работу Docker.

```shell
$ docker run --rm -ti ubuntu:latest /bin/bash
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
6cf436f81810: Pull complete
987088a85b96: Pull complete
b4624b3efe06: Pull complete
d42beb8ded59: Pull complete
Digest: sha256:7a47ccc3bbe8a451b500d2b53104868b46d60ee8f5b35a24b41a86077c650210
Status: Downloaded newer image for ubuntu:latest
root@ae2d3196342d:/#
```

Опция -rm автоматически удаляет контейнер и его данные по завершинии работы. Опция -i подключает текущий стандартный ввод, чтобы мы могли вводить текст в командной строке. Опция -t предоставляет терминал для взаимодействия с контейнером.

Команда docker run пытается найти указанный образ Ubuntu на запускаемой системе. Так как такого образа еще нет, она загружает последний образ c Docker hub, репозитория образов Docker. После того, как образ контейнера загружен, он запускается и в его контексте выполняется команда Linux /bin/bash.

[Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

## Создание образа Docker для работы с Yocto

Образ Docker это окружение, в котором запускается приложение. В нашем случае приложением является Yocto. Создадим образ основанный на Ubuntu 18.04 и установим пакеты, необходимые для запуска Yocto и bitbake.

Состав и конфигурация образа Docker определяется файлом Dockefile. Данный файл содержит все шаги по созданию и настройке образа для работы  с Yocto.

### Конфигурационный файл Dockerfile

Наша конфигурация начинается с определения образа, который станет базовым:

```shell
FROM ubuntu:18.04
```

После создания базового образа установливаем инструменты, от которых зависит работа Yocto:

```shell
RUN apt-get update && apt-get -y install gawk wget git-core diffstat unzip texinfo gcc-multilib \
     build-essential chrpath socat cpio python python3 python3-pip python3-pexpect \
     xz-utils debianutils iputils-ping libsdl1.2-dev xterm tar locales
```

Ubuntu по умолчанию использует интерпретатор команд dash. Для правльной работы Yocto нам необходим интерпретатор bash. Переключим систему на его использование:

```shell
RUN rm /bin/sh && ln -s bash /bin/sh
```

Укажем переменные окружения, которые понадобятся нам позже, для организации записи на файловую систему хоста, в которой запускается образ докера:

```shell
ENV USER_NAME yocto
ENV PROJECT yocto
```

Создаваемые при работе с Yocto артефакты будут записываться в директорию хоста, вне контейнера. Для того, чтобы такая запись была возможна, нам нужен пользователь, ID которого будет совпадать с ID владельца каталога на хосте, в который будет производиться запись. Имя пользователя значения не имеет, важен только ID.

Мы будем передавать ID пользователя в контейнер при его запуске.

```shell
ARG host_uid=1000
ARG host_gid=1000
RUN groupadd -g $host_gid $USER_NAME && \
    useradd -g $host_gid -m -s /bin/bash -u $host_uid $USER_NAME
```

Следующая команда переключает пользователя в контейнере с root на $USER_NAME, делает домашний каталог пользователя рабочим и запускает оболочку shell:

```shell
USER $USER_NAME
WORKDIR /home/yoctouser
CMD /bin/bash
```

### Создание образа Docker

После того, как мы определили нужную нам конфигурацию в Dockerfile, мы можем создать образ:

```shell
docker build --no-cache --build-arg "host_uid=$(id -u)" \
  --build-arg "host_gid=$(id -g)" --tag "yocto:latest" .
```

Мы можем запустить контейнер, как только образ будет создан.

## Запуск контейнера

Запустим контейнер, используя созданный нами образ:

```shell
docker run -it --rm -v $PWD:/home/yocto yocto:latest
```

После запуска контейнера, нам доступна командная строка, в которой мы можем выполнять любые работы с проектом Yocto.

[Using Docker Containers for Yocto Builds](https://www.embeddeduse.com/2019/02/11/using-docker-containers-for-yocto-builds/)