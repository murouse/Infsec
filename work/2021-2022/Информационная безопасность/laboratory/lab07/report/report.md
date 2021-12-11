---
# Front matter
lang: ru-RU
title: "Информационная безопасность"
subtitle: "Л.7. Элементы криптографии. Однократное гаммирование"
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

Освоить на практике применение режима однократного гаммирования

# Задание

Нужно подобрать ключ, чтобы получить сообщение «С Новым Годом, друзья!». Требуется разработать приложение, позволяющее шифровать и дешифровать данные в режиме однократного гаммирования. Приложение должно:  

1. Определить вид шифротекста при известном ключе и известном открытом тексте.  
2. Определить ключ, с помощью которого шифротекст может быть преобразован в некоторый фрагмент текста, представляющий собой один из возможных вариантов прочтения открытого текста.

# Теория

## Гаммирование

Гаммирование представляет собой наложение (снятие) на открытые (зашифрованные) данные последовательности элементов других данных, полученной с помощью некоторого криптографического алгоритма, для получения зашифрованных (открытых) данных. Иными словами, наложение гаммы — это сложение её элементов с элементами открытого (закрытого) текста по некоторому фиксированному модулю, значение которого представляет собой известную часть алгоритма шифрования.

## Как применять 

В соответствии с теорией криптоанализа, если в методе шифрования используется однократная вероятностная гамма (однократное гаммирование) той же длины, что и подлежащий сокрытию текст, то текст нельзя раскрыть. Даже при раскрытии части последовательности гаммы нельзя получить информацию о всём скрываемом тексте. Наложение гаммы по сути представляет собой выполнение операции сложения по модулю 2 между элементами гаммы и элементами подлежащего сокрытию текста.

## Нахождение шифротекста

Если известны ключ и открытый текст, то задача нахождения шифротекста заключается в применении к каждому символу открытого текста следующего правила:
$$C_{i} = P_{i} ⊕ K_{i}$$
где $C_{i}$ — i-й символ получившегося зашифрованного послания, $P_{i}$ — i-й символ открытого текста, $K_{i}$ — i-й символ ключа. Размерности открытого текста и ключа должны совпадать, и полученный шифротекст будет такой же длины.

## Нахождение ключа

Если известны шифротекст и открытый текст, то, чтобы найти ключ, обе части равенства необходимо сложить по модулю 2 с $P_{i}$:
$$C_{i} ⊕ P_{i} = P_{i} ⊕ K_{i} ⊕ P_{i} = K_{i},$$
$$K_{i} = C_{i} ⊕ P_{i}$$

## Стойкость шифра

К. Шеннон доказал абсолютную стойкость шифра в случае, когда однократно используемый ключ, длиной, равной длине исходного сообщения, является фрагментом истинно случайной двоичной последовательности с равномерным законом распределения. Криптоалгоритм не даёт никакой информации об открытом тексте: при известном зашифрованном сообщении C все различные ключевые последовательности K возможны и равновероятны, а значит, возможны и любые сообщения P.

## Условия стойкости шифра

Необходимые и достаточные условия абсолютной стойкости шифра:  

- полная случайность ключа;  
- равенство длин ключа и открытого текста;  
- однократное использование ключа.  

# Ход работы

## Класс Gumming

Для разработки приложения был описан класс *Gumming* (рис. -@fig:001), описывающий интересующие нас поля (открытый текст, закрытый текст, ключ шифрования), а также методы для работы с ними:

![Класс Gumming](image/1.png){ #fig:001 width=70% }

## Метод keygen()

Метод *keygen()* (рис. -@fig:002) позволяет сгенерировать псевдослучайный ключ такой же длины, как и открытый текст:

![Метод keygen()](image/2.png){ #fig:002 width=70% }

## Метод key()

Метод *key()* (рис. -@fig:003) позволяет получать ключ, зная открытый и закрытый текст:

![Метод key()](image/3.png){ #fig:003 width=70% }

## Метод encrypt()

Метод *encrypt()* (рис. -@fig:004) позволяет зашифровывать текст (получать закрытый текст), зная открытый текст и ключ:

![Метод encrypt()](image/4.png){ #fig:004 width=70% }

## Метод decipher()

Метод *decipher()* (рис. -@fig:005) позволяет расшифровывать текст (получать открытый текст), зная закрытый текст и ключ:

![Метод decipher()](image/5.png){ #fig:005 width=70% }

## Вывод информации

Также были реализованы методы (рис. -@fig:006), служащие для вывода информации на экран в различных представлениях (текстовое, десятичное, шестнадцатеричное):

![Вывод информации](image/6.png){ #fig:006 width=70% }

## Главная программа

В главной программе подключили кириллические символы в консоль (рис. -@fig:007), сгенерировали псевдослучайную последовательность, создали объект класса, установили открытый текст *"С Новым Годом, друзья!"*:

![Главная программа (1)](image/7.png){ #fig:007 width=70% }

Затем вызвали методы для генерации псевдослучайного ключа и зашифровки текста (рис. -@fig:008). Всю информацию поэтапно выводим на экран:

![Главная программа (2)](image/8.png){ #fig:008 width=70% }

После этого проверили работу методов для расшифровки и нахождения ключа (рис. -@fig:009), убедились в корректности работы программы по информации, получанной в консоли (рис. -@fig:010):

![Главная программа (3)](image/9.png){ #fig:009 width=70% }

## Вывод программы

![Вывод программы](image/10.png){ #fig:010 width=100% }

# Вывод

Освоили на практике применение режима однократного гаммирования