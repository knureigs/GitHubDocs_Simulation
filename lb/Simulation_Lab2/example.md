## Імітаційне моделювання комп'ютерних систем
## СПм-21-2, **Іванов Іван Іванович**
### Лабораторна робота №**2**. Редагування імітаційних моделей у середовищі NetLogo

<br>

### Вибрана модель у середовищі NetLogo:
[Traffic Basic](http://www.netlogoweb.org/launch#http://www.netlogoweb.org/assets/modelslib/Sample%20Models/Social%20Science/Traffic%20Basic.nlogo)

<br>

### Вербальний опис моделі:
Доступно у файлі з першої лабораторної роботы [Traffic Basic](..\Simulation_Lab1_ua\example.md). *// інакше (якщо друга лабораторна не продовжує першу), необхідно навести опис тут, підготувавши його за аналогією з тим, як описувалася модель у першій лабораторній роботі.*

### Внесені зміни у вихідну логіку моделі:

**Виправлення розміщення активних агентів** на ігровому полі при ініціалізації моделі - спочатку машини могли розміщуватися на тому самому ділянці.  
Замість
<pre>
set xcor abs random-xcor
</pre>
у процедурі setup-cars використовується виклик окремої нової процедури розміщення агентів:
<pre>
to set-freeposition
  set xcor random-pxcor
  if any? other turtles-here [ set-freeposition ]
end
</pre>
Додавання функції для отримання випадкового числа в заданому діапазоні для частого подальшого використання в інших змінах логіки моделі
<pre>
to-report get-random-float [low high]
  report low + random-float high
end
</pre>
Наприклад, використовувалася при встановленні початкового значення швидкості активних агентів:
<pre>
set speed get-random-float 0.1 1
</pre>

**Встановлення ліміту максимальної швидкості для кожної машини здійснюється індивідуально**, а не однаково для всіх машин:
<pre>
;; set speed-limit 1
set speed-limit get-random-float 1.1 0.9
</pre>
Використовується кольорова диференціація машин залежно від їхнього швидкісного ліміту:
<pre>
if (speed-limit > 1.2) [
  set color green
]
if (speed-limit > 1.5) [
  set color orange
]

;; ask sample-car [ set color red ]
</pre>
Сині найповільніші, зелені швидше, помаранчеві найшвидші.  
Забарвлення обраної для відстеження машини скасовано.

**Зміна логіки гальмування та набору швидкості** залежно від наявності перешкоди перед машиною:
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
Для цього також були внесені зміни до процедури slow-down-car, яка спрацьовує при гальмуванні:
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

**Додано ймовірність безвідповідальності водія, який, при нагоді, їздитиме узбіччям**.
Імовірність встановлюється користувачем через інтерфейс середовища моделювання та використовується при додаванні машин на поле:
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
Лічильник "поганих водіїв" виводитиметься користувачеві.  
Додано узбіччя, дорога зведена до повноційної односмугової:
<pre>
to setup-road ;; patch procedure
  if pycor = -1 [ set pcolor yellow ]
  if pycor = 0 [ set pcolor white ]
end
</pre>

Виїзд на узбіччя здійснюється, якщо перед машиною є інша машина, водій "поганий", знаходиться на дорозі (бо з узбіччя з'їжджати далі нікуди):
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
Перед виїздом на узбіччя, воне проглядається водієм, чи вільно там, за допомогою функції is-roadside-free:
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

На кожному ході виконується перевірка, чи знаходиться машина на дорозі. Якщо машина їде узбіччям, то намагатиметься повернутися на дорогу:
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
Функція перевірки, чи вільна дорога поблизу машини, щоб можна було повернутися з узбіччя:
<pre>
to-report is-road-main-free  
  let cars-near-amount count turtles-on neighbors
  if (cars-near-amount = 0) [
    report true
  ]
  report false
end
</pre>

![Скріншот моделі в процесі симуляції](example-model.png)

Фінальний код моделі та її інтерфейс доступні за [посиланням](example-model.nlogo). *// якщо вносили зміни до інтерфейсу середовища моделювання - то експорт потрібен у форматі nlogo, як тут. Інакше, якщо змінювався лише код логіки моделі, достатньо викласти лише його, як [тут](example-model-code.html),якщо експортовано з десктопної версії NetLogo, або окремим текстовим файлом, шляхом копіпасту з веб-версії*.