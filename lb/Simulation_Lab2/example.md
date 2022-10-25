## Імітаційне моделювання комп'ютерних систем
## СПм-21-2, **Іванов Іван Іванович**
### Лабораторная работа №**2**. Редактирование имитационных моделей в среде NetLogo

<br>

### Выбранная модель в среде NetLogo:
[Traffic Basic](http://www.netlogoweb.org/launch#http://www.netlogoweb.org/assets/modelslib/Sample%20Models/Social%20Science/Traffic%20Basic.nlogo)

<br>

### Вербальное описание модели:
Доступно в файле с первой лабораторной работы [Traffic Basic](..\Simulation_Lab1\example.md)

### Внесённые изменения в логику модели:

Исправление размещения активных агентов на игровом поле при инициализации модели - машины могли размещаться на одном и том же участке.
Добавлена процедура 
<pre>
to set-freeposition
  set xcor random-pxcor
  if any? other turtles-here [ set-freeposition ]
end
</pre>
вместо 
<pre>
set xcor abs random-xcor
</pre>
в процедуре setup-cars.

Добавление функции для получения рандомного числа, для частого последующего использования
<pre>
to-report get-random-float [low high]
  report low + random-float high
end
</pre>
Например, использовалась при установке начального значения скорости активных агентов:
<pre>
set speed get-random-float 0.1 1
</pre>

Установка лимита скорости для каждой машины индивидуально, а не общего для всех машин:
<pre>
;; set speed-limit 1
set speed-limit get-random-float 1.1 0.9
</pre>
И выделение цветом машин в зависимости от их скоростного лимита:
<pre>
if (speed-limit > 1.2) [
    set color green
  ]
  if (speed-limit > 1.5) [
    set color orange
  ]
</pre>
Отменена окраска выбранной для отслеживания машины:
<pre>
;; ask sample-car [ set color red ]
</pre>

Изменение логики торможения и набора скорости в зависимости от наличия препятствия перед машиной
<pre>
to go
  ;; if there is a car right ahead of you, match its speed then slow down
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
  tick
end
</pre>
и были внесены изменения в процедуру slow-down-car, срабатывающую при торможении:
<pre>
to slow-down-car [ car-ahead car-ahead-2 ] ;; turtle procedure
  if (car-ahead = nobody) and (car-ahead-2 != nobody) [
    let speed-car-ahead-2 [ speed ] of car-ahead-2
    let speed-difference-ahead-2 abs speed - speed-car-ahead-2
    set speed speed - speed-difference-ahead-2 / 2
  ]
  if (car-ahead != nobody) [
    ifelse (car-ahead-2 != nobody) [ 
      set heading 180
      fd 1
      set heading 90
    ]
    [ 
      ;; slow down so you are driving more slowly than the car ahead of you
      let speed-car-ahead [ speed ] of car-ahead
      set speed speed-car-ahead - deceleration
    ]
  ]
end
</pre>

Добавлена вероятность того, что добавляемая на поле машина - с недобросовестным водителем, который, при случае, будет ездить по обочине.
Вероятность устанавливается польвателем через интерфейс среды моделирвоание.

<pre>
let prob random 100
ifelse (prob < bad-driver-probability) [
  set is-bad true
]
[
  set is-bad false
]
</pre>

Добавлена обочина
<pre>
to setup-road ;; patch procedure
  if pycor = -1 [ set pcolor yellow ]
  if pycor = 0 [ set pcolor white ]
end
</pre>