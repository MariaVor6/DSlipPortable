# vcpkg в проектах CMake
## Постановка проблемы 
В чём проблема? Проблема в жёстком указании путей, что в дальнейшем не позволит использовать даный код на других устройствах (несовпадение путей обеспечит 
отсутствие соединения с нужными файлами)
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

Наши шаги поэтапно:

1. Создаём файл конфигурации ```CMakePresets.json``` (создаем его в одной директории с CMakeList.txt). Внутренности:
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
где ``` "$env{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake"``` указание пути расположения ```vcpkg.cmake```. Данная часть является изменяемой взависмости от расположения.
Соответственно в блоке кода представленного выше (if (WIN32)....endif (WIN32)) необходимости нет, его можно убрать.

2. Настраиваем переменную окружения VCPKG_ROOT (один раз на компьютере)
   ```
   set VCPKG_ROOT="C:/Program Files/vcpkg"
   ```
   ! Для сохранения переменной навсегда добавляем её в переменное окружение Windows. (дописать как)
3. Важный шаг, который забывать не надо это группирование проекта (сконфигурация) 
   ```
   cmake --preset default
   ```
   Данная команда запускает конфигурацию CMake с использованием предустановленных настроек из файла CMakePresets.json. 



[Подробная документация здесь] (https://learn.microsoft.com/ru-ru/vcpkg/users/buildsystems/cmake-integration) 
