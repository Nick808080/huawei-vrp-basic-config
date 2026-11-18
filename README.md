# Базовая инициализация и настройка маршрутизатора Huawei (VRP)

## Описание проекта
В данном проекте продемонстрированы базовые навыки работы с интерфейсом командной строки (CLI) операционной системы Huawei VRP (Versatile Routing Platform). Работа выполнялась на маршрутизаторе серии **AR3260** (VRP 5.130). 

Проект иллюстрирует практическое применение команд навигации, конфигурирования интерфейсов и встроенной системы помощи, что является основой для администрирования сетей на базе оборудования Huawei (уровень HCIA-Datacom).

## Выполненные задачи
1. **Базовая проверка системы:** Анализ версии VRP, аппаратной платформы и аптайма устройства с помощью `display version`.
2. **Навигация по режимам:** Переход из пользовательского режима (User View) в системный (System View) и смена имени устройства (hostname).
3. **Работа со встроенной справкой:** Использование контекстной справки (`?`) для изучения доступных команд интерфейса и аргументов группы `ip`.
4. **Конфигурирование интерфейсов и траблшутинг:**
   * Назначение IP-адреса на интерфейс.
   * Использование команды `display this` для проверки примененных настроек в текущем контексте.
   * Демонстрация безопасного отката конфигурации: обработка опечатки, удаление ошибочно назначенного IP-адреса с помощью `undo ip address` и перенос настроек на правильный интерфейс.

## Ход работы (Консольный лог)

### 1. Проверка версии ОС и смена имени
Устройство работает под управлением VRP Release 5.130 (AR3200 V200R003C00). Имя по умолчанию изменено на `Datacom-Router` для удобства идентификации.

```text
<Huawei> display version
Huawei Versatile Routing Platform Software
VRP (R) software, Version 5.130 (AR3200 V200R003C00)
Copyright (C) 2011-2012 HUAWEI TECH CO., LTD
Huawei AR3260 Router uptime is 0 week, 0 day, 0 hour, 0 minute
BKP 0 version information:
1. PCB      Version : AR01BAK3A VER.NC
2. If Supporting PoE : No
3. Board    Type    : AR3260
4. MPU Slot Quantity : 2
5. LPU Slot Quantity : 10

<Huawei> system-view
Enter system view, return user view with Ctrl+Z.
[Huawei] sysname Datacom-Router
[Datacom-Router]
```

### 2. Использование системы контекстной справки
В режиме конфигурирования интерфейса (`GigabitEthernet 0/0/1`) была вызвана справка `?` для просмотра доступных команд, а затем `ip ?` для уточнения параметров IP-адресации.

```text
[Datacom-Router-GigabitEthernet0/0/1] ip ?
  accounting           <Group> accounting command group
  address              <Group> address command group
  binding              Enable binding of an interface with a VPN instance
...
[Datacom-Router-GigabitEthernet0/0/1] ip address 192.168.1.1 24 ?
  sub      Indicate a subordinate address
  <cr>     Please press ENTER to execute command
```

### 3. Настройка IP, обработка ошибок и проверка конфигурации
Адрес `192.168.1.1/24` был изначально назначен на интерфейс `G0/0/1`. Настройки были проверены командой `display this`. 
Затем конфигурация была отменена с помощью `undo ip address` (при вводе была допущена синтаксическая ошибка `udno`, которая была корректно обработана CLI) и перенесена на нужный интерфейс `G0/0/2`.

```text
[Datacom-Router-GigabitEthernet0/0/1] ip address 192.168.1.1 24
[Datacom-Router-GigabitEthernet0/0/1] display this
[V200R003C00]
#
interface GigabitEthernet0/0/1
 ip address 192.168.1.1 255.255.255.0
#
return

[Datacom-Router-GigabitEthernet0/0/1] udno ip address
                                           ^
Error: Unrecognized command found at '^' position.
[Datacom-Router-GigabitEthernet0/0/1] undo ip address
[Datacom-Router-GigabitEthernet0/0/1] quit

[Datacom-Router] interface GigabitEthernet 0/0/2
[Datacom-Router-GigabitEthernet0/0/2] ip address 192.168.1.1 24
[Datacom-Router-GigabitEthernet0/0/2] display this
[V200R003C00]
#
interface GigabitEthernet0/0/2
 ip address 192.168.1.1 255.255.255.0
#
return
```