
### Тюнинг видеокарты для майнинга эфира (Ethereum) пошаговая инструкция

#### Что имеем

- Ubuntu 20.04 ([Как установить Ubuntu](./Install-Ubuntu.md))
- Nvidia драйвер and CUDA установлены ([Как установить Nvidia Cuda и драйвер](./Install-Ubuntu-Nvidia-cuda-and-driver.md))
- Видеокарта Nvidia GeForce GTX 1660 Ti
- GPUTuner приложение запущено с root правами
- Возможность overclocking-а для выбранной видеокарты включена

<img src="../images/terminal-launch-gpu-tuner.png"/>

#### Профилирование

Перед нами скрин из GPUTuner со всей информацией

<img src="../images/app-launch.png" width="80%"/>

Как можно видеть в данный момент карта находится в простое, никакого майнинга не происходит:

- текущая частота ядра (current core clock) равна 375 MHz
- текущая частота памяти (current memory clock) равна 405 MHz
- температура ядра (core temperature) показывает 45 C
- текущее потребление карты (power draw) всего лишь 19.56 W

Выберем `ethash` алгоритм (алгоритм эфира) и `lolMiner` в качестве майнера и запустим майнинг через `Start mining` кнопку. Через некоторое время информация на экране GPUTuner изменится и станет приблизительно как показано ниже

<img src="../images/eth-01.png" width="80%"/> 

- частота ядра подросла до 1875 MHz
- частота памяти до 5750 MHz
- температура увеличилась до 63 C и быстро растет
- потребление стало 105.70 W

По этим данным мы можем предположить, что частота ядра 1875 MHz является слишком большим значением для данной видеокарты на данном алгоритме и видеокарта начинает сильно нагреваться. Чтобы частота ядра видеокарты не поднималась до такого большого значения, мы можем это явно ограничить используя фиксацию частоты ядра на одном более низком значении или понизить значение максимально доступного уровня потребления питания (power limit). Я предпочитаю первый подход (хотя понижения power limit также способствует понижению частоты ядра) и просто зафиксирую частоту ядра на более низком значении, например, для данной видеокарты мне кажется, что 1530 MHz будет достаточно. Найдите `Overclocking` секцию, найдите там `Core clock current` подсекцию и выберите там `fixed` режим, затем из списка предложенных частот выберите 1530 MHz и нажмите `Apply` (применить) кнопку. Через несколько секунд значение `Core clock current` должно стать 1530 MHz.

<img src="../images/eth-02.png" width="80%"/>

Спустя некоторое время (1 минуту) мы видим, что температура ядра всё еще остается довольно высокой `61 C`, но мне бы хотелось получить температуру меньше 60 C. Для этого я снова меняю частоту ядра с 1530 MHz на 1200 MHz и наблюдаю еще минуту.

<img src="../images/eth-03.png" width="80%"/>

Теперь температура ядра стала менее 60 C как мы и хотели. Останавливаем майнинг через `Stop mining` кнопку. Теперь мы готовы к профилированию.

#### Профилирование офсета по памяти (memory transfer rate offset)

Сначала мы будем профилировать офсет по памяти. Найдите `Profiling` секцию, выберите/установите `Memory transfer rate offset range` чекбокс (галку) и установите там

- Start = -2000 MHz (с какой частоты начать профилирование)
- End = 2000 Hz (на какой частоте закончить профилирование)
- Step = 50 MHz (шаг профилирования)

И также можете установить `Save profiling results to the file` чтобы результат сохранился в файл и посмотреть его позже.
Почему мы установили `End` в 2000 MHz хотя, как можно видеть, там доступны значения до `5000 MHz` ? Это всё потому, что наша видеокарта `1660 Ti` не держит офсет больше чем 2000 MHz, иначе происходит зависание системы. Каждая видеокарта имеет своё индивидуальное максимальное значение для офсета, для вашей видеокарты это значение нужно подобрать самим.
После того как всё выставили, запускаем профилирование нажав `Start profiling`.

<img src="../images/eth-04.png" width="80%"/>

Профилирование начнется в отдельном окне. Когда профилирование закончится, перед нами будет результат в подобном виде

<img src="../images/eth-05.png" width="80%"/>

По графикам мы можем отметить, что:

- Чем выше офсет по памяти, тем выше потребление (power draw)
- Чем выше офсет по памяти, тем выше hashrate

Это значит, что `ethash` алгоритм для тестируемой видеокарты имеет максимальный hashrate при максимальном значении офсета по памяти. Если вы выбирали сохранение результатов в файл, то откройте сохраненный файл в браузере (результаты сохранены в html файле), и там можно детально рассмотреть значение hashrate на каждом шаге профилирования.

<img src="../images/eth-06.png" width="80%"/>

Мы нашли наилучшее значение для офсета по памяти для нашей видеокарты и это есть 2000 MHz. Заканчиваем профилирование по офсету памяти нажав `Stop profiling`.

#### Профилирование офсета по ядру (core clock offset)

Теперь проделаем то же самое, только для офсета по ядру. Выберите `Core clock offset range` чекбокс и установите

- Start = -1000 MHz
- End = 50 Hz
- Step = 50 MHz

Мы используем 50 MHz для `End`, потому что тестируемая видеокарта при большем значении офсета приводит к зависанию системы. 

<img src="../images/eth-07.png" width="80%"/>

И начинаем профилирование, нажав `Start profiling`. После профилирования мы видим такие результаты

<img src="../images/eth-08.png" width="80%"/>

На этих графиках мы видим, что максимальный hashrate когда офсет по ядру установлен в минимум, то есть в `-1000 MHz`. Но посмотрите на потребление, оно неадекватно большое, 105 W для обычной 1660 Ti и сильно греет видеокарту. Согласно графику потребления питания, для нас оптимальный вариант будет при минимальном потреблении, в данном случае нам идеально подходит `0 MHz`, потому что разница в хэше с `-1000 MHz` всего лишь 0.6 MH/s (2 %), но по потреблению питания у нас разница 30 Вт (28 %).

После профилирования офсетов по ядру и памяти мы нашли максимальный hashrate для тестовой видеокарты как `29.1 MH/s` при офсете по ядру `0 MHz` и при офсете по памяти `+2000 MHz`.

Но можем ли мы получить лучше результат ? Что еще можно поменять, чтобы увеличить hashrate ? И ответ утвердительный, осталась настройка, которую мы еще не профилировали достаточно, и это частота ядра (и частота памяти, если поддерживается видеокартой, регулировка частоты памяти только работает на 3-ей серии Nvidia, поэтому тут мы не будем ее описывать, но работать аналогично как с частотой по ядру).

#### Профилирование частоты ядра (core clock)

Перед началом профилирования давайте выставим значения офсетов для максимального hashrate

- Офсет по ядру, найдите `Core clock offset current` секцию, выберите чекбокс, напишите `0` и нажмите `Apply` чтобы применить
- Офсет по памяти, найдите `Memory transfer rate offset current` секцию, выберите чекбокс, напишите `2000` и нажмите `Apply` чтобы применить
- И можно обратно отключить чекбоксы чтобы случайно не изменить значения.

<img src="../images/eth-09.png" width="80%"/>

И просто запустите майнинг через `Start mining`. В отдельном окне в самом низу мы должны увидеть наш максимальный hashrate как 29.1 MH/s.

<img src="../images/eth-10.png" width="80%"/>

Теперь давайте изменим (залочим) частоту ядра на другой частоте, найдите `Overclocking` секцию, найдите там `Core clock current` подсекцию и выберите `fixed` режим. И выберем, например, 900 MHz и применим через `Apply`.

<img src="../images/eth-11.png" width="80%"/>

После этого наш hashrate изменился и стал 29,48 MH/s

<img src="../images/eth-12.png" width="80%"/>

Затем перебрали соседние частоты и получили такие результаты

| Hashrate MH/s | Core clock |
|---------------|------------|
| 29,47         | 900        |
| 29,42         | 810        |
| 29,45         | 855        |
| 29.46         | 870        |
| 29.47         | 885        |
| 29,48         | 900        |
| 29,48         | 915        |
| 29,49         | 930        |
| 29,49         | 945        |
| 29,50         | 960        |
| 29,41         | 975        |

Из таблицы результатов выше, мы можем сделать вывод, что наивысший hashrate мы получили когда залочили частоту ядра на 960 MHz и получили 29,50 MHz. Подведем итоги, для получения максимального hashrate для нашей тестируемой видеокарты нужно выставить следующие значения

- Установить офсет по ядру в 0 (это значение по умолчанию)
- Установить офсет по памяти в 2000 MHz
- И зафиксировать частоту ядра на 960 MHz

Также отметим, что после выключения монитора компьютера hashrate увеличился до 30.60 MH/s.

#### Профилирование лимитом потребления видеокарты (power limit)

Я это не использую, но возможно кому-то будет полезна краткая инструкция как это сделать.
Так как вместо лока частоты ядра мы будем ограничивать лимит на потребление, то для начала выключим лок частоты, в секции `Core clock lock` переключите на режим `auto`. Затем начните майнинг через `Start mining`. Если обнаружите, что видеокарта начинает сильно нагреваться (а она будет, потому что мы еще не ограничили потребление и она может в себя брать на сколько максимально рассчитана) затем просто шагами понижайте потребление.

- Переключите `Power limit current` режим из `default` в `fixed`
- Установите например 100 W (если у вас 1660 Ti, иначе устанавливайте лимит подходящий для вашей видеокарты)
- И примените изменение через `Apply`

И изменяя значение лимита потребления найдите наиболее подходящий для вашей видеокарты, ориентируясь на температуру и hashrate.