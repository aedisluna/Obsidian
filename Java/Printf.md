# Java printf — памятка

> [!info] Что внутри
> Синтаксис, спецификаторы, флаги, ширина/точность, индексация аргументов, локаль, таблицы/колонки, частые паттерны, подводные камни и готовые сниппеты.

## Быстрый старт

```java
System.out.printf("Hello, %s! You have %d msgs.%n", name, count);
// как строка: 
String msg = String.format("Hello, %s! You have %d msgs.%n", name, count);
```

- Формат: %[arg_index\$][flags][width][.precision]conversion — классический синтаксис Formatter.


## Часто используемые спецификаторы

- Строки и символы:
    - %s — строка; %S — в верхнем регистре.
    - %c — символ; %C — символ в верхнем регистре.
- Целые:
    - %d — десятичное; %o — восьмеричное; %x/%X — шестнадцатеричное.
- Плавающая точка:
    - %f — десятичная; %.2f — 2 знака после точки.
    - %e/%E — экспоненциальная; %g/%G — компактная.
    - %a/%A — hex‑представление double.
- Булевы и объекты:
    - %b — логическое; для null печатает false.
    - %h/%H — hashCode в hex; %s вызывает toString().
- Дата/время (через Formatter для Date/Calendar/TemporalAccessor):
    - %tF — ISO дата yyyy-MM-dd; %tT — HH:mm:ss; %tR — HH:mm.
    - %tc — локализованный полный штамп; %tY, %tm, %td — части.

Примеры:

```java
System.out.printf("User: %-10s | Score: %06d | Rate: %.2f%%%n", name, score, rate);
System.out.printf("Hex: 0x%08X, Octal: %o%n", value, value);
System.out.printf("Date: %tF %tT%n", date, date);
```


## Флаги выравнивания и форматирования

- \- — выравнивание влево: `%\-10s` (строка в поле шириной 10, дополнение пробелами справа).
- 0 — нули слева для чисел: `%010d` (паддинг нулями до ширины; знак выводится перед нулями).
- \+ — явный знак для чисел: `%\+d`, `%\+8.2f` (плюс у положительных, минус у отрицательных).
- пробел — пробел как знак для положительных: `%\ d` (для отрицательных всё равно минус).
- \$ — отрицательные в скобках: `%$d` или `%$$10.2f` (заменяет ведущий минус на скобки).
- , — группировка разрядов по локали: `%,d`, `%,.2f` (тысячные разделители зависят от Locale).
- \# — альтернативная форма:
    - для `%o` добавляет ведущий `0`: `%#o`.
    - для `%x/%X` добавляет `0x/0X`: `%#x`, `%#X`.
    - для `%f` всегда печатает десятичную точку, даже если дробная часть нулевая: `%#f`.

Нюансы применения:

- Флаг `0` игнорируется при указании `-` или точности для целых; для отрицательных чисел нули идут после знака. 
- Флаги `+` и пробел взаимоисключаемы; при совместном указании побеждает `+`. 
- Флаг `(` применим к числовым конверсиям и не влияет на положительные значения. 

Примеры:

```java
System.out.printf("|%\-10s|%10s|%n", "left", "right");   // выравнивание строк 
System.out.printf("%010d%n", -42);                       // -000000042 (знак перед нулями)
System.out.printf("%\+8.2f%n", 12.0);                    //    +12.00
System.out.printf("% d%n", 12);                          //  12 (ведущий пробел)
System.out.printf("%$$10.2f%n", -1234.5);                //   (1234.50)
System.out.printf("%,d%n", 1_234_567);                   // 1,234,567 (en_US)
System.out.printf("%#x%n", 48879);                       // 0xbeef
System.out.printf("%#f%n", 10.0);                        // 10.000000 (с точкой)
```



## Ширина и точность

- Ширина — минимальное число символов: %8d, %08d.
- Точность:
    - Для %f/%e/%g — число знаков после точки: %.3f.
    - Для %s — максимум символов, обрезает строку: %.5s.
    - Для %d/%x/%o — точность не применяется; нужны ширина/флаги.

Примеры:

```java
System.out.printf("|%8s|%-8s|%.3s|%n", "hi", "left", "truncate");
System.out.printf("|%10.4f|%010.2f|%n", Math.PI, Math.E);
```


## Индексация аргументов и повтор

- Позиции: %2\$s — взять второй аргумент как строку.
- Повтор предыдущего аргумента: %<tH:%<tM.

Примеры:

```java
System.out.printf("%2$s, %1$s!%n", "мир", "Привет");  // Привет, мир!
System.out.printf("%tF %<tT%n", new Date());          // повтор даты
```


## Локализация

- Локаль влияет на разделители, названия месяцев и группировку разрядов.
- Используйте перегрузки с Locale:

```java
System.out.printf(Locale.GERMANY, "%,.2f%n", 1234.56);  // 1.234,56
String s = String.format(Locale.FRANCE, "%,d", 1_000_000);
```


## Таблицы и выравнивание колонок

```java
System.out.printf("%-12s %10s %12s%n", "Item", "Qty", "Price");
System.out.printf("%-12s %10d %12.2f%n", "Apples", 7, 3.5);
System.out.printf("%-12s %10d %12.2f%n", "Bananas", 12, 1.25);
```

- Комбинируйте ширину, выравнивание и точность для аккуратных колонок.


## Частые паттерны

- Проценты:

```java
System.out.printf("Completion: %.1f%%%n", 99.95);
```

- Денежные суммы с разрядами:

```java
System.out.printf("%,.2f RUB%n", 1_250_000.0);
```

- Ведущие нули:

```java
System.out.printf("Order-%06d%n", id); // Order-000123
```

- HEX дамп:

```java
for (byte b : data) System.out.printf("%02X ", b);
System.out.println();
```

- Комбинация ширины и обрезки строки:

```java
System.out.printf("|%10.10s|%-10.10s|%n", longName, longName);
```


## Ошибки и подводные камни

> [!warning] Будь внимателен
> - IllegalFormatConversionException при несоответствии типа и спецификатора.
> - Для числовых типов BigDecimal/BigInteger используйте совместимые спецификаторы или форматируйте через java.text / java.time API по задаче.
> - Для доменной логики дат отдавайте приоритет java.time и DateTimeFormatter; printf — для консольного вывода.

## Сниппеты для Obsidian/IDEA

- Функция‑помощник для строк отчёта:

```java
static void row(String name, int qty, double price) {
  System.out.printf("%-20s %,6d %,12.2f%n", name, qty, price);
}
```

- Локализованный отчёт:

```java
static void report(Locale loc, String user, double sum, ZonedDateTime dateTime) {
  System.out.printf(loc, "User: %-10s | Sum: %,.2f | Date: %1$td.%1$tm.%1$tY %1$tT%n",
      user, sum, Date.from(dateTime.toInstant()));
}
```

- Через String.format:

```java
String msg = String.format("File %s: %,d bytes", file, size);
log.info(msg);
```


## Формат Obsidian

> [!tip]+ Полезно для заметок
> - Callouts создаются как блок‑цитаты с [!info]/[!tip]/[!warning]; можно делать сворачиваемыми через +/‑.
> - Базовый и расширенный синтаксис Obsidian поддерживает заголовки, списки, таблицы, кодовые блоки с подсветкой.

Готово: вставить в Obsidian, дополнять раздел «Частые паттерны» своими форматами чеков, логов и отчётов.
<span style="display:none">[^10][^11]</span>

<div style="text-align: center">⁂</div>

: https://help.obsidian.md/callouts

: https://help.obsidian.md/syntax

: https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html

: https://help.obsidian.md/advanced-syntax

: Printf.md

: https://forum.obsidian.md/t/all-callout-styles-for-reference/36102

: https://www.reddit.com/r/ObsidianMD/comments/1jdxcwq/pro_tip_use_callouts/

: https://www.obsidianstats.com/posts/2025-06-10-custom-callouts-in-obsidian

: https://springframework.guru/java-output-printf-method/

[^10]: https://blog.obsibrain.com/readme/obsidian-cheatsheet-basic-text-formatting

[^11]: https://mszturc.github.io/obsidian-advanced-slides/basic-syntax/callouts/

