---
# Front matter
lang: ru-RU
title: "Информационная безопасность"
subtitle: "Л.8. Элементы криптографии. Шифрование (кодирование) различных исходных текстов одним ключом"
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

Освоить на практике применение режима однократного гаммирования на примере кодирования различных исходных текстов одним ключом.

# Теория

## Однократное гаммирование одним ключом

Режим шифрования однократного гаммирования одним ключом двухвидов открытого текста реализуется в соответствии со схемой (рис. -@fig:001)

![Общая схема шифрования двух различных текстов одним ключом](image/1.png){ #fig:001 width=70% }

## Шифротексты телеграмм

Шифротексты обеих телеграмм можно получить по формулам режима однократного гаммирования:

$$C_{1} = P_{1} ⊕ K_{i}$$
$$C_{2} = P_{2} ⊕ K_{i}$$

Открытый текст можно найти в соответствии с (рис. -@fig:001), зная шифротекст двух телеграмм, зашифрованных одним ключом.

## Следствие свойства операции XOR

Для это оба равенства складываются по модулю 2. Тогда получаем:

$$C_{1} ⊕ C_{2} = P_{1} ⊕ K ⊕ P_{2} ⊕ K = P_{1} ⊕ P_{2}$$

Предположим, что одна из телеграмм является шаблоном — т.е. имеет текст фиксированный формат, в который вписываются значения полей.

## Получение второго открытого текста по первому 

Допустим, что злоумышленнику этот формат известен. Тогда он получает достаточно много пар $C_{1} ⊕ C_{2}$ (известен вид обеих шифровок).

Тогда зная *P1*, имеем: 

$$C_{1} ⊕ C_{2} ⊕ P_{1} = P_{1} ⊕ P_{2} ⊕ P_{1} = P_{2}$$

Таким образом, злоумышленник получает возможность определить те символы сообщения *P2*, которые находятся на позициях известного шаблона сообщения *P1*. 

В соответствии с логикой сообщения *P2*, злоумышленник имеет реальный шанс узнать ещё некоторое количество символов сообщения *P2*. 

Затем вновь используется описанное свойство с подстановкой вместо *P1* полученных на предыдущем шаге новых символов сообщения *P2*. И так далее.

Действуя подобным образом, злоумышленник даже если не прочитает оба сообщения, то значительно уменьшит пространство их поиска.

# Ход работы

## Исходные данные

Рассмотрим две телеграммы Центра:

- *P1* = НаВашисходящийот1204
- *P2* = ВСеверныйфилиалБанка

Ключ Центра длиной 20 байт:

*K* = 05 0C 17 7F 0E 4E 37 D2 94 10 09 2E 22 57 FF C8 0B B2 70 54

## Код исходных данных

Установим данные значения в соответствующие поля (рис. -@fig:002), используя программный код, реализованный в ходе предыдущий лабораторной, и получим шифротексты.

![Исходные данные](image/2.png){ #fig:002 width=100% }

## Функция tripl()

Реализуем функцию (рис. -@fig:003), принимающую три строки, и возвращающую их совместное наложение операцией *XOR*, согласно описанному раннее свойству.

![Функция tripl()](image/3.png){ #fig:003 width=100% }

## Последовательные вызовы tripl()

Предположим, что злоумышленник знает начало первого сообщения *"НаВаш"*. 

Пользуясь *tripl()* (рис. -@fig:004) пробуем расшифровать имеющиеся сообщения последовательной подстановкой в функцию открытых участков то первого, то второго сообщения, постепенно подбирая (рис. -@fig:005) продолжения уже имеющихся участков, тем самым увеличивая длину расшифрованных последовательностей.

Таким образом удалось полность расшифровать оба сообщения.

![Последовательные вызовы функции *tripl()* с новыми данными](image/4.png){ #fig:004 width=100% }

## Последовательные открытия участков

![Последовательные открытия участков с новыми данными](image/5.png){ #fig:005 width=100% }

# Вывод

Освоили на практике применение режима однократного гаммирования на примере кодирования различных исходных текстов одним ключом.




