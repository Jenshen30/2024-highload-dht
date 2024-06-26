# Отчёт Stage 4

Т. к. задание в срок выполнено не было, профилировался и нагружался
фактически референс. Это затрудняет сравнение с предыдущим этапом.

Анализ проводился на 3 инстансах со стандартными значениями ack и from.

[get cpu profile](./asprof/get_cpu.html)

[put cpu profile](./asprof/put_cpu.html)

[get alloc profile](./asprof/get_alloc.html)

[put alloc profile](./asprof/put_alloc.html)

[get lock profile](./asprof/get_lock.html)

[put lock profile](./asprof/put_lock.html)

### GET

8k rps

| Percentile | time    |
|------------|---------|
| 50.000%    | 1.30ms  |
| 75.000%    | 1.73ms  |
| 90.000%    | 2.15ms  |
| 99.000%    | 6.18ms  |
| 99.900%    | 14.13ms |
| 99.990%    | 22.35ms |
| 99.999%    | 26.75ms |
| 100.000%   | 28.82ms |

### PUT

8k rps

| Percentile | time    |
|------------|---------|
| 50.000%    | 1.44ms  |
| 75.000%    | 1.98ms  |
| 90.000%    | 2.77ms  |
| 99.000%    | 13.39ms |
| 99.900%    | 40.22ms |
| 99.990%    | 61.18ms |
| 99.999%    | 68.74ms |
| 100.000%   | 72.57ms |

Уже на 10к RPS при продолжительном тестировании сервер умирает,
поэтому профилировал на 8к RPS. с чем связано такое падение?
По большей части - накладные расходы, связанные с репликацией.
У нас теперь 3 инстанса, а не 2 - больше работы с сетью.

В референсе используется HttpClient из java.net.http, но количество
сэмплов на lock профиле по сравнению с one.nio'вским стало только больше,
то есть узким местом он быть не перестал.

В целом, значительная часть ресурсов теперь 
уходит на поддержание согласованности данных.