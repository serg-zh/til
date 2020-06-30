# Сборка mender для nvidia-jetson

Процесс включает себя установку кросс компилятора, вытягивание исходных кодов и сборку.

``` script
sudo aptitude install gcc-aarch64-linux-gnu
go get github.com/mendersoftware/mender
git checkout 2.2.0
cd $GOPATH/src/github.com/mendersoftware/mender
CGO_ENABLED=1 CC=aarch64-linux-gnu-gcc GOOS=linux GOARCHdf=arm64 make TAGS=nolzma  build
```

Ссылки на статьи

* https://habr.com/ru/post/249449/
* https://docs.mender.io/2.3/client-configuration/cross-compiling
