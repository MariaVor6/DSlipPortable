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
А теперь переходим к самой установки boost со всеми зависимостями.
```
.\vcpkg install boost-filesystem:x64-windows boost-system:x64-windows boost-program-options:x64-windows --recurse
