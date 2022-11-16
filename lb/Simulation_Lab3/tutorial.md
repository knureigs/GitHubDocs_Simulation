# Імітаційне моделювання комп'ютерних систем
## Лабораторная работа №3. Использование средств вычислительного интеллекта для оптимизации имитационных моделей

<br>

### 3.1 Цель работы
Изучить применение генетических алгоритмов и случайного поиска для настройки параметров имитационных моделей, используя среду BehaviorSearch.

<br>

### 3.2 Описание лабораторной установки
Для выполнения лабораторной работы потребуется:
- свободно распространяемый пакет **BehaviorSearch**, который входит в состав установки среды мультиагентного моделирование **NetLogo**. Потребуется для настройки алгоритмов поиска значений параметров имитационной модели.
- для выбора использовауемой модели может использоваться как десктопная, так и веб-версия среды мультиагентного моделирование **NetLogo**. Но для работы **BehaviorSearch** в любом случае будет нужна установка полноценной десктопной версии.
- для описания работы используется файл в формате **md**, размещаемый в составе репозитория лабораторной на сервисе GitHub. *Для удобства локальной работы с md-разметкой можно использовать редакторы кода, такие как MS Visual Studio Code или любой другой, знакомый пользователю. В случае локальной работы по подготовке md-файла, потребуется установленная СКВ Git.*
- для работы с сервисом GitHub потребуется доступ к сети Internet.

<br>

### 3.3 Порядок выполнения работы
Выбрать для индивидуальной работы готовую имитационную модель среди предлагаемых средой NetLogo. Воизбежание одновременного выбора одного и того же варианта несколькими студентами, свой выбор **обязательно зафиксируйте** в [гугл-документе с общим доступом](https://docs.google.com/spreadsheets/d/1qpwpdv9j_9LmdFnWsEiEt_Qec-cPVk6LviXC2GWig-Y/edit).  
Параметры выбранной вами модели в будущем будут настраиваться с помощью генетического алгоритма и метода случайного поиска, использую среду BehaviorSearch, поэтому в процессе выбора обратите внимание на то, какой из показателей работы выбранной модели можно будет использовать для целевой функции (функции приспособленности). В ряде моделей нет удобного для исследования показателя эффективности. *Допускается внесение изменения в логику модели, если имеющихся показателей вам недостаточно, а сама модель заинтересовала и другую выбирать не хотите, но это явное усложнение работы.*   
Возможно использование модели, рассматриваемой в одной из прошлых лабораторных работ. Если выбранная модель не использовалась ранее, **необходимо её описание** её описание (аналогично тому, как делали в лабораторной, посвященной [описанию имитационных моделей](..\Simulation_Lab1\tutorial.md)).  
Настроить среду BehaviorSearch:
1. Описать управляющие **параметры модели**, диапазоны и шаг изменений их возможных значений.
2. Выбрать показатель эффективности модели и **настроить целевую функцию**. 
3. Настроить параметры работы **алгоритма поиска**. Использовать случайный поиск и простой генетический алгоритм. *При настройке работы генетического алгоритма достаточно добиться того, "чтобы работало", а о качестве получаемого результата можно будет поговорить на защите.*
4. Запустить исследование и сравнить с результаты, полученные при использовании генетического алгоритма и метода случайного поиска.  

Убедиться в корректной работе имитационной модели (используя NetLogo) при найденных в BehaviorSearch значениях параметров.

<br>

### 3.4 Содержание отчета
Наличие отчета не является необходимым. Его функции выполняет **md-описание** работы (которое и так необходимо для отработки), подготовленное и выложеное на сервисе GitHub. Для ориентировки можно использовать [пример](example.md).
<br>

### 3.5 Контрольные вопросы
1. Для чего нужна оптимизационная модель?
1. Какие этапы использования среды BehaviorSearch?
1. Что такое целевая функция (функция приспособленности)?
1. Как вы понимаете понятие "пространства поиска"?
1. Какие алгоритмы поиска доступны в среде BehaviorSearch?
1. В чём заключается метод случайного поиска?
1. Какие могут использоваться критерии остановки поиска?
2. Какие основные этапы работы генетического алгоритма?
2. Какие генетические операторы вам известны?
2. Что такое кроссовер? Как происходит отбор особей для него?
2. Для чего нужен оператор мутации?
2. Какие есть способы кодирования вариантов решений (особей) в генетическом алгоритме?
2. Недостатки и преимущества использования генетических алгоритмов.
2. В чём заключается алгоритм имитации отжига?
2. В чём заключается метод поиска восхождением к вершине?

<br>

### СПИСОК ЛИТЕРАТУРЫ
1. Среда [BehaviorSearch](http://www.behaviorsearch.org/).
1. Подробное [описание процесса использования](https://www.behaviorsearch.org/documentation/tutorial.html) BehaviorSearch, на примере.
1. Популярное [описание работы генетических алгоритмов](http://algolist.ru/ai/ga/).
1. [Introduction to Genetic Algorithms](https://towardsdatascience.com/introduction-to-genetic-algorithms-including-example-code-e396e98d8bf3).
1. [Генетический алгоритм. Просто о сложном](https://habr.com/ru/post/128704/). *Пример использования для решения Диофантового уравнения и масса ссылок на другие неформальные статьи по этой теме*.
1. Руководства по разметке Markdown: [1](https://gist.github.com/Jekins/2bf2d0638163f1294637), [2](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet), [3](https://www.markdownguide.org/basic-syntax/).
<!-- 
Если не пугает углубление в науку (оно для лабы не является необходимым, базовых источников и рассказанного на лекциях хватит), есть хорошие статьи про использование методов поиска, указанные в самом проекте BehaviorSearch, по ссылке http://www.behaviorsearch.org/papers.html
-->