---
# Front matter
lang: ru-RU
title: "Информационная безопасность"
subtitle: "Л.5. Дискреционное разграничение прав в Linux. Исследование влияния дополнительных атрибутов"
author: "Греков Максим Сергеевич"
institute: RUDN University, Moscow, Russian Federation
date: 2021

# Formatting
toc-title: "Содержание"
toc: true # Table of contents
toc_depth: 2
lof: true # List of figures
lot: false # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a4paper
documentclass: scrreprt
polyglossia-lang: russian
polyglossia-otherlangs: english
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Serif
monofont: PT Serif
mainfontoptions: Ligatures=TeX
romanfontoptions: Ligatures=TeX
sansfontoptions: Ligatures=TeX,Scale=MatchLowercase
monofontoptions: Scale=MatchLowercase
indent: true
pdf-engine: lualatex
header-includes:
  - \linepenalty=10 # the penalty added to the badness of each line within a paragraph (no associated penalty node) Increasing the value makes tex try to have fewer lines in the paragraph.
  - \interlinepenalty=0 # value of the penalty (node) added after each line of a paragraph.
  - \hyphenpenalty=50 # the penalty for line breaking at an automatically inserted hyphen
  - \exhyphenpenalty=50 # the penalty for line breaking at an explicit hyphen
  - \binoppenalty=700 # the penalty for breaking a line at a binary operator
  - \relpenalty=500 # the penalty for breaking a line at a relation
  - \clubpenalty=150 # extra penalty for breaking after first line of a paragraph
  - \widowpenalty=150 # extra penalty for breaking before last line of a paragraph
  - \displaywidowpenalty=50 # extra penalty for breaking before last line before a display math
  - \brokenpenalty=100 # extra penalty for page breaking after a hyphenated line
  - \predisplaypenalty=10000 # penalty for breaking before a display
  - \postdisplaypenalty=0 # penalty for breaking after a display
  - \floatingpenalty = 20000 # penalty for splitting an insertion (can only be split footnote in standard LaTeX)
  - \raggedbottom # or \flushbottom
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Изучение механизмов изменения идентификаторов, применения SetUID- и Sticky-битов. 

Получение практических навыков работы в консоли с дополнительными атрибутами. 

Рассмотрение работы механизма смены идентификатора процессов пользователей, а также влияние бита Sticky на запись и удаление файлов.

# Подготовка лабораторного стенда

## Компилятор gcc

При подготовке стенда убедились, что в системе установлен компилятор gcc (для этого ввели команду gcc -v):

![Компилятор gcc](image/1.png){ #fig:001 width=70% }

## Система запретов

Отключили систему запретов до очередной перезагрузки системы командой ```setenforce 0```

После этого команда ```getenforce``` вывела Permissive. 

![Система запретов](image/2.png){ #fig:002 width=70% }

# Ход работы 

## Программа simpleid.c

Вошли в систему от имени пользователя guest и создали программу simpleid.c:

![Программа simpleid.c](image/3.png){ #fig:003 width=70% }

## Компиляция simpleid.c

Скомплилировали программу и убедились, что файл программы создан:

```gcc simpleid.c -o simpleid```

![Компиляция simpleid.c](image/4.png){ #fig:004 width=70% }

## Выполнение simpleid.c и id

Выполнили программу simpleid: ```./simpleid```, а также системную программу id:

![Выполнение simpleid.c и id](image/5.png){ #fig:005 width=70% }

Получили идентичные результаты рассматриваемых параметров

## Программа simpleid2.c

Усложнили программу, добавив вывод действительных идентификаторов, и назвали её simpleid2.c:

![Программа simpleid2.c](image/6.png){ #fig:006 width=70% }

## Компиляция и выполнение simpleid2.c

Скомпилировали и запустили программу simpleid2.c:

![Компиляция и выполнение simpleid2.c](image/7.png){ #fig:007 width=70% }

Теперь видим не только текущих группу и пользователя, но и владельца файла.

## Смена владельца и атрибут s

От имени суперпользователя выполнили команды:

```chown root:guest /home/guest/simpleid2```

```chmod u+s /home/guest/simpleid2```

Тем самым, сменили владельца файла и добавили ему дополнительный атрибут (SetUID).

Затем выполнили проверку правильности установки новых атрибутов и смены владельца файла simpleid2:

```ls -l simpleid2```

![Смена владельца и атрибут s](image/8.png){ #fig:008 width=70% }

## Запуск simpleid2 с SetUID

Запустили simpleid2 и id, вновь получили идентичные результаты.

Убедились в принадлежности файла пальзователю root.

![Запуск simpleid2 с SetUID](image/9.png){ #fig:009 width=70% }

## Запуск simpleid2 с SetGID

Проделали то же самое относительно SetGID-бита:

![Запуск simpleid2 с SetGID](image/10.png){ #fig:010 width=70% }

## Программа readfile.c

Создали программу readfile.c:

![Программа readfile.c](image/11.png){ #fig:011 width=70% }

## Компиляция и изменение readfile.c

Откомпилировали программу и изменили владельца у файла readfile.c и права так, чтобы только суперпользователь (root) мог прочитать его, a guest не мог, убедились в правильности, получив отказ в доступе:

![Компиляция и изменение readfile.c](image/12.png){ #fig:012 width=70% }

## Изменение владельца readfile с SetUID

Сменили у программы readfile владельца и установили SetUID-бит:

![Изменение владельца readfile с SetUID](image/13.png){ #fig:013 width=70% }

## Попытка прочтения

Проверили, может ли программа readfile прочитать файлы readfile.c и /etc/shadow, в обоих случаях это не удалось, потому что владелец файла программы guest2:

![Попытка прочтения](image/14.png){ #fig:014 width=70% }

## Атрибут Sticky на /tmp

Выяснили, что на директории /tmp установлен атрибут Sticky:

```ls -l / | grep tmp```

![Атрибут Sticky на /tmp](image/15.png){ #fig:015 width=70% }

## Создание file01.txt

От имени пользователя guest создали файл file01.txt в директории /tmp со словом test:

```echo "test" > /tmp/file01.txt```

![Создание file01.txt](image/16.png){ #fig:016 width=70% }

## Атрибуты file01.txt

Просмотрели атрибуты у только что созданного файла и разрешили чтение и запись для категории пользователей «все остальные»:

```chmod o+rw /tmp/file01.txt```

```ls -l /tmp/file01.txt```

![Атрибуты file01.txt](image/17.png){ #fig:017 width=70% }

## Работа с файлом file01.txt в директории с t

От пользователя guest2 (не являющегося владельцем) попробовали:

1. прочитать файл /tmp/file01.txt: ```cat /tmp/file01.txt```

2. дозаписать в файл /tmp/file01.txt слово test2: ```echo "test2" >> /tmp/file01.txt```

3. проверить содержимое файла: ```cat /tmp/file01.txt```

4. записать в файл /tmp/file01.txt слово test3, стерев при этом всю имеющуюся в файле информацию: ```echo "test3" > /tmp/file01.txt```

5. проверить содержимое файла: ```cat /tmp/file01.txt```

6. удалить файл /tmp/file01.txt: ```rm /tmp/fileOl.txt```

Удалось дозаписать информацию в файл, перезаписать, прочитать, но не удалось удалить файл:

![Работа с файлом file01.txt в директории с t](image/18.png){ #fig:018 width=70% }

## Снятие атрибута t

Повысили свои права до суперпользователя командой ```su -```

Выполнили после этого команду, снимающую атрибут t (Sticky-бит) с директории /tmp: ```chmod -t /tmp```.

Покинули режим суперпользователя командой ```exit```

От пользователя guest2 проверили, что атрибута t у директории /tmp нет: ```ls -l / | grep tmp```

![Снятие атрибута t](image/19.png){ #fig:019 width=70% }

## Работа с файлом file01.txt в директории без t

Повторили все действия и, в отличие от предыдущего раза, теперь уже нам удалось удалить файл:

![Работа с файлом file01.txt в директории без t](image/20.png){ #fig:020 width=70% }

## Возврат атрибута t

Повысили свои права с помощью su, вернули директории /tmp атрибут t:

![Возврат атрибута t](image/21.png){ #fig:021 width=70% }

# Вывод

Изучили механизмы изменения идентификаторов, применения SetUID- и Sticky-битов. 

Получили практические навыки работы в консоли с дополнительными атрибутами. 

Рассмотрели работы механизма смены идентификатора процессов пользователей, а также влияние бита Sticky на запись и удаление файлов.


