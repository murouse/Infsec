---
# Front matter
lang: ru-RU
title: "Информационная безопасность"
subtitle: "Л.4. Дискреционное разграничение прав в Linux. Расширенные атрибуты"
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

Целью данной лабораторной работы является получение практических навыков работы в консоли с расширенными атрибутами файлов

# Ход работы 

## Определение расширенных атрибутов

От имени пользователя guest определили расширенные атрибуты файла ```/home/guest/dir1/file1``` командой ```lsattr /home/guest/dir1/file1```

![Расширенные атрибуты файла](image/1.png){ #fig:001 width=70% }

## Изменение прав на файл

Установили командой ```chmod 600 file1``` на файл ```file1``` права, разрешающие чтение и запись для владельца файла

![Изменение прав на файл](image/2.png){ #fig:002 width=70% }

## Расширенный атрибут a

Попробовали установить на файл ```/home/guest/dir1/file1``` расширенный атрибут ```a``` от имени пользователя guest:

```chattr +a /home/guest/dir1/file1```

В ответ получили отказ от выполнения операции. Дело в том, что расширенные атрибуты можно установить от имени администратора с root правами.

![Попытка установить расширенный атрибут a](image/3.png){ #fig:003 width=70% }

Зашли на вторую консоль с правами администратора. Попробовали установить расширенный атрибут ```a``` на файл ```/home/guest/dir1/file1``` от имени суперпользователя:

```chattr +a /home/guest/dir1/file1```

От пользователя guest проверили правильность установления атрибута: 

```lsattr /home/guest/dir1/file1```

![Вход с правами администратора](image/4.png){ #fig:004 width=70% }

![Установка и проверка расширенного атрибута a](image/5.png){ #fig:005 width=70% }

## Работа с файлом с атрибутом а

Выполнили дозапись в файл ```file1``` слова «test» командой ```echo "test" >> /home/guest/dir1/file1```

После этого выполнили чтение файла ```file1``` командой ```cat /home/guest/dir1/file1```

Убедились, что слово test было успешно записано в ```file1```.

![Дозапись в файл с расширенным атрибутом a](image/6.png){ #fig:006 width=70% }

Попробовали стереть имеющуюся в файле информацию командой ```echo "abcd" > /home/guest/dirl/file1```

Попробовали переименовать файл командой ```mv file1 file2```

Попробовали с помощью команды ```chmod 000 file1```  установить на файл права, например, запрещающие чтение и запись для владельца файла

Все попытки не увенчались успехом потому, что расширенный атрибут ```a``` позволяет только добавление информации к файлу.

![Попытка изменить файл с расширенным атрибутом a](image/7.png){ #fig:007 width=70% }

## Работа с файлом без атрибута а

Сняли расширенный атрибут a с файла ```/home/guest/dirl/file1``` от имени суперпользователя командой ```chattr -a /home/guest/dir1/file1```

Повторили операции, которые ранее не удавалось выполнить. Они были успешно выполнены, потому что мы предварительно сняли ограничение "только дозапись"

![Попытка изменить файл без расширенного атрибута a](image/8.png){ #fig:008 width=70% }

## Работа с файлом с атрибутом i

Повторили прошлые действия по шагам, заменив атрибут «a» атрибутом «i».

Расширенный атрибут «i» говорит о том, что на файл накладывается ограничение неизменяемости. 

Поэтому произвести прошлые действия с файлом не удастся.

![Установка расширенного атрибута i](image/9.png){ #fig:009 width=70% }

![Попытка изменить файл с расширенным атрибутом i](image/10.png){ #fig:010 width=70% }

# Вывод

В результате выполнения работы повысили свои навыки использования интерфейса командой строки (CLI), познакомились на примерах с тем, как используются основные и расширенные атрибуты при разграничении доступа. 

Имели возможность связать теорию дискреционного разделения доступа (дискреционная политика безопасности) с её реализацией на практике в ОС Linux. 

Составили наглядные таблицы, поясняющие какие операции возможны при тех или иных установленных правах. 

Опробовали действие на практике расширенных атрибутов «а» и «i».