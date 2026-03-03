# vcpkg в проектах CMake
## Постановка проблемы

При использовании жёсткого указания путей, в дальнейшем даный код не будет возможно использовать на других устройствах (несовпадение путей обеспечит 
отсутствие соединения с нужными файлами)

``` CMakeList.txt```
```
if (WIN32)
	# Boost размещается не стандартным образом, в не стандартных местах расположения на жёстком диске и
	# функция find_package найти его не сможет. По этой причине, CMake нуждается в подсказке
	set( BOOST_ROOT "C:/Program Files/vcpkg/installed/x64-windows" )
	set( BOOST_INCLUDEDIR "C:/Program Files/vcpkg/installed/x64-windows/include/boost" )
	set( BOOST_LIBRARYDIR "C:/Program Files/vcpkg/installed/x64-windows/lib" )    
endif (WIN32)
```
**Вариант решения:** vcpkg предлагает простую интеграцию с CMake,
чтобы сделать установленные пакеты доступными в проектах автоматически.
Механизм интеграции vcpkg заключается в предоставлении файла цепочки инструментов CMake.

**Наши шаги поэтапно:**

1. Создаём файл конфигурации ```CMakePresets.json``` (создаем его в одной директории с CMakeList.txt), который будет ссылаться на файл цепочки ```toolchain``` . Внутренности:
```
{
    "version": 3,
    "configurePresets": [
        {
            "name": "default",
            "binaryDir": "${sourceDir}/build",
            "cacheVariables": {
                "CMAKE_TOOLCHAIN_FILE": "$env{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake"
            }
        }
    ]
}
```
где ``` "$env{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake"``` указание пути расположения ```vcpkg.cmake```. Данная часть является изменяемой взависимости от расположения.
Соответственно в блоке кода представленного выше (if (WIN32)....endif (WIN32)) необходимости нет, его можно убрать.

> Файл конфигурации должен находиться рядом с CMakeList.txt

2. Настраиваем переменную окружения VCPKG_ROOT (один раз на компьютере). 
   
```
set "VCPKG_ROOT=C:\Program Files\vcpkg"
   или без кавычек
set VCPKG_ROOT=C:\Program Files\vcpkg
```

 Это нужно для того, что бы не вводит каждый раз полный путь.
   
```
cmake -B build -S . -DCMAKE_TOOLCHAIN_FILE="C:/Program Files/vcpkg/scripts/buildsystems/vcpkg.cmake"
```
   
> Обратите внимание что переменная ```VCPKG_ROOT``` содержится в файле ```CMakePresets.json```

 **!** Для сохранения переменной навсегда добавляем её в переменное окружение Windows.
   
   
4. Важный шаг, который забывать не надо это группирование проекта (конфигурация) 
   ```
   cmake --preset default
   ```
   Данная команда запускает конфигурацию CMake с использованием предустановленных настроек из файла CMakePresets.json. Её необходимо использовать до основного ```cmake ..```

   > Когда вы установите переменную окружения в переменное окружение Window всё равно есть вероятность ошибки выполнения команды конфигурации, в таком случае рекомендуется в окне терминала повторно настроить  переменную окружения VCPKG_ROOT
   
6. Далее идёт стандартный набор команд
   
``` 
mkdir build
cd build
cmake ..
cmake --build .
```
   
7. [Ссылка на установку Boost посредсвом vcpkg] (https://github.com/MariaVor6/DSlipPortable/blob/main/DownloadBoost%26splog.md)


[Подробная документация здесь об взаимосвязи CMake & vcpkg] (https://learn.microsoft.com/ru-ru/vcpkg/users/buildsystems/cmake-integration) 
