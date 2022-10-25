## Імітаційне моделювання комп'ютерних систем
## СПм-21-2, **Іванов Іван Іванович**
### Лабораторная работа №**2**. Редактирование имитационных моделей в среде NetLogo

<br>

### Выбранная модель в среде NetLogo:
[Traffic Basic](http://www.netlogoweb.org/launch#http://www.netlogoweb.org/assets/modelslib/Sample%20Models/Social%20Science/Traffic%20Basic.nlogo)

<br>

### Вербальное описание модели:
Доступно в файле с первой лабораторной работы [Traffic Basic](..\Simulation_Lab1\example.md). *// иначе (если вторая лабораторная не продолжает первую), необходимо привести описание здесь, подготовив его по аналогии с тем, как описывалась модель в первой лабораторной работе.*

### Внесённые изменения в исходную логику модели:

**Исправление размещения активных агентов** на игровом поле при инициализации модели - исходно машины могли размещаться на одном и том же участке.
Вместо 
<pre>
set xcor abs random-xcor
</pre>
в процедуре setup-cars  используется вызов отдельной новой процедуры для размещения агентов: 
<pre>
to set-freeposition
  set xcor random-pxcor
  if any? other turtles-here [ set-freeposition ]
end
</pre>
Добавление функции для получения случайного числа в заданном диапазоне, для частого последующего использования в других изменениях логики модели
<pre>
to-report get-random-float [low high]
  report low + random-float high
end
</pre>
Например, использовалась при установке начального значения скорости активных агентов:
<pre>
set speed get-random-float 0.1 1
</pre>

**Установка лимита максимальной скорости для каждой машины производится индивидуально**, а не одна для всех машин:
<pre>
;; set speed-limit 1
set speed-limit get-random-float 1.1 0.9
</pre>
Используется цветовая дифференциация машин в зависимости от их скоростного лимита:
<pre>
if (speed-limit > 1.2) [
  set color green
]
if (speed-limit > 1.5) [
  set color orange
]

;; ask sample-car [ set color red ]
</pre>
Синие самые медленные, зелёные быстрее, оранжевые самые быстрые.
Окраска выбранной для отслеживания машины отменена.

**Изменение логики торможения и набора скорости** в зависимости от наличия препятствия перед машиной:
<pre>
  ask turtles [
    let car-ahead one-of turtles-on patch-ahead 1
    let car-ahead-2 one-of turtles-on patch-ahead 2
    ifelse (car-ahead = nobody) and (car-ahead-2 = nobody)
      [ speed-up-car ]
      [ slow-down-car car-ahead car-ahead-2 ]
    ;; don't slow down below speed minimum or speed up beyond speed limit
    if speed < speed-min [ set speed speed-min ]
    if speed > speed-limit [ set speed speed-limit ]
    fd speed
  ]
</pre>
Для этого также были внесены изменения в процедуру slow-down-car, срабатывающую при торможении:
<pre>
to slow-down-car [ car-ahead car-ahead-2 ] ;; turtle procedure
  if (car-ahead = nobody) and (car-ahead-2 != nobody) [
    let speed-car-ahead-2 [ speed ] of car-ahead-2
    let speed-difference-ahead-2 abs speed - speed-car-ahead-2
    set speed speed - speed-difference-ahead-2 / 2
  ]
  if (car-ahead != nobody) [
    let speed-car-ahead [ speed ] of car-ahead
      set speed speed-car-ahead - deceleration
  ]
end
</pre>

**Добавлена вероятность безответственности водителя, который, при случае, будет ездить по обочине**.
Вероятность устанавливается пользователем через интерфейс среды моделирования и используется при добавлении машин на поле:
<pre>
let prob random 100
ifelse (prob < bad-driver-probability) [
  set is-bad true
]
[
  set is-bad false
]

set bad-drivers-count 0
</pre>
Счётчик "плохих водителей" будет выводиться пользователю.  
Добавлена обочина, дорога сведена к действительно однополосной:
<pre>
to setup-road ;; patch procedure
  if pycor = -1 [ set pcolor yellow ]
  if pycor = 0 [ set pcolor white ]
end
</pre>

Выезд на обочину осуществляется, если перед машиной есть другая машина, водитель "плохой", находится на дороге (т.к. с обочины съезжать дальше некуда):
<pre>
if (car-ahead != nobody) [
  ifelse (is-bad = true) AND (pycor = 0) [
    if (is-roadside-free = true) [
      set heading 180
      fd 1
      set heading 90
      set bad-drivers-count bad-drivers-count + 1
    ]
  ]
  [ 
    ;; slow down so you are driving more slowly than the car ahead of you
    let speed-car-ahead [ speed ] of car-ahead
    set speed speed-car-ahead - deceleration
  ]
]
</pre>
Перед выездом на обочину, она просматривается водителем, свободно ли там, с помощью функции is-roadside-free:
<pre>
to-report is-roadside-free
  set heading 180
  let car-roadside one-of turtles-on patch-ahead 1
  ifelse (car-roadside = nobody) [
    set heading 90
    report true
  ]
  [
    set heading 90
    report false
  ]
end
</pre>

На каждом ходу вполняется проверка, находится ли машина на дороге. Если машина едет по обочине, то будет пытаться вернуться на дорогу:
<pre>
if (pycor != 0) [
  if (is-road-main-free = true) [
    set heading 0
    fd 1
    set heading 90
    set bad-drivers-count bad-drivers-count - 1
  ]
]
</pre>
Функция проверки, свободна ли дорога в окрестностях машины, чтобы на неё можно было вернуться:
<pre>
to-report is-road-main-free  
  let cars-near-amount count turtles-on neighbors
  if (cars-near-amount = 0) [
    report true
  ]
  report false
end
</pre>

![Скриншот модели в процессе симуляции](example-model.png)

Итоговый код модели и её интерфейс доступны по [ссылке](example-model.nlogo). *// если вносили изменения в интерфейс среды моделирования - то экспорт нужен в формате nlogo, как здесь. Иначе, если менялся только код логики модели, достаточно выложить только его - как [здесь](example-model-code.html), если экспортирован из десктопной версии NetLogo, или отдельным текстовым файлом, путём копипаста с веб-версии*.