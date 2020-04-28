# Common tips

[Контроль ресурсов](https://goldmann.pl/blog/2014/09/11/resource-management-in-docker/#_cpu)

Статистика по использованию ресурсов (память, процессор, сеть, диск)

``` shell
docker stats <docker>
```

[Intel® VTune™ Profiler Performance](https://software.intel.com/en-us/vtune-cookbook-profiling-in-docker-container)

set used cpu numbers for a working containter

``` shell
docker update --cpus 4 <containter>
```

запуск qemu в контейнере требует параметра slirp и nographic

``` shell
runqemu core-image-minimal slirp nographic
```
