# Обновление ПО

The Software Factory (SWF) concept is intended to be used as a structured way to set up a platform, and to be a handbook for the work involved in the development of that platform. Part of this concept is to enable a project to start creating and populating its own SWF - a handbook specific to the needs of the project.[Концептуальное описание всех путей разработки ПО](https://pelux.io/software-factory/PELUX-3.0/index.html)

[Хорошее краткое описание разных систем](https://pelux.io/software-factory/PELUX-3.0/swf-blueprint/docs/articles/architecture/vert-config-SOTA.html)

## OTA Solutions SWUpdate + Hawkbit

* [FullMetallUpdate](https://www.fullmetalupdate.io/docs/documentation/)
* [Update Factory](https://docs.updatefactory.io/)

## Системы обновления

Требования  к системе обновления

* атомарность обновления;
* штатная возможность восстановления работоспособности системы в случае если ОС не загружается;
* возможность обновить всё ПО модуля из одного окна;

### Mender

Готовое решение, клиент + сервер. Написан на языке Go, что может вызвать проблемы с портированием на платформы, для которых нет поддержки Go.

Компактное решение, на целевой системе исполняемый файл занимает около 16Мб. Однако очень ограниченный функционал. Все что позволяет сделать - обновлять корневую ФС с сохранением предыдущей версии A/B. **Заявлено расширение функций с помощью дополнительный модулей, однако в настоящий момент таких модулей нет** (?).

### swupd

Разрабатывается в рамках ClearOS, заявлена возможность интеграции с Yocto, но данное направление выглядит заброшенным.

### OSTree

Не подходит в связи с тем, что нет защиты от поломок файловой системы. 

### SWUpdate


