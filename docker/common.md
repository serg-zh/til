# Common tips

[Challenges of using Containers to Run Graphical Embedded Systems](https://www.youtube.com/watch?time_continue=223&v=LHXpv2oee84&feature=emb_logo)

[Контроль ресурсов](https://goldmann.pl/blog/2014/09/11/resource-management-in-docker/#_cpu)

## Tips

### Статистика по использованию ресурсов (память, процессор, сеть, диск)

``` shell
docker stats <docker>
```

[Intel® VTune™ Profiler Performance](https://software.intel.com/en-us/vtune-cookbook-profiling-in-docker-container)

### set used cpu numbers for a working containter

``` shell
docker update --cpus 4 <containter>
```

### запуск qemu в контейнере требует параметра slirp и nographic

``` shell
runqemu core-image-minimal slirp nographic
```

### Присоединиться к контейнеру пользователем  root

``` shell
docker exec -u root -t -i container_id /bin/bash
```

### Запуск с графическим приложением 

docker run --rm -it --env="DISPLAY" --net=host \
        -v${HOME}:/home/user \
        -v${HOME}/.Xauthority:/root/.Xauthority \
        -v${HOME}/.Xauthority:/home/user/.Xauthority  \
        -v/dev/dri/card0:/dev/dri/card0 \
        application
