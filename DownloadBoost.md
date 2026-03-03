# DSlipPortable
# Установка Boost используя vcpkg
Команда для поиска расположения vcpkg
```
Get-Command vcpkg -ErrorAction SilentlyContinue | Select-Object Source
```
Данная команда нам выведет путь распложения vcpkg, после чего по этому пути надо перейти
```
cd "C:\Program Files\vcpkg" 
```
После перехода по пути при наличии пакетов boost (в моём случае попытки установки предпринимались ранее) следует удалить ошибочные файлы
```
.\vcpkg remove boost-filesystem boost-system boost-program-options --recurse
```
Также во избежание проблем следует очистить кеш сборки
```
Remove-Item -Recurse -Force C:\Program Files\vcpkg\buildtrees\boost-* -ErrorAction SilentlyContinue 
```
А теперь переходим к самой установки boost со всеми зависимостями. В нашем случае необходимы следующие библиотеки ```date_time system program_options```
В любом случае если чего то не будет хватать, то это можно спокойно доустановить.

```.\vcpkg install boost-system:x64-windows boost-filesystem:x64-windows boost-program-options:x64-windows boost-date-time:x64-windows --recurse
```
> Команда --recurse нужна для очистки уже сущесвующих файлов библиотек что бы не было дублирования, соответственно если установка идёт впервые, то данную команду необходимо убрать
