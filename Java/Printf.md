# Printf в Java
*Форматированный вывод данных*

#java #printf #formatting #output #string-formatting

---

## 📋 Содержание
- [[#Основы Printf]]
- [[#Часто используемые спецификаторы]]
- [[#Ширина и точность]]
- [[#Индексация аргументов и повтор]]
- [[#Локализация]]
- [[#Таблицы и выравнивание колонок]]
- [[#Частые паттерны]]
- [[#Ошибки и подводные камни]]
- [[#Сниппеты для Obsidian/IDEA]]
- [[#Примеры от нейросетки]]

---
## Основы Printf

### Основные методы форматирования
- `System.out.printf()` - вывод в консоль
- `String.format()` - создание отформатированной строки
- `PrintStream.printf()` - форматированный вывод в поток
- `Formatter` - низкоуровневый класс для форматирования

```java
System.out.printf("Hello, %s! You have %d msgs.%n", name, count);
// как строка: 
String msg = String.format("Hello, %s! You have %d msgs.%n", name, count);
```

- Формат: %[arg_index\$][flags][width][.precision]conversion — классический синтаксис Formatter.


## Часто используемые спецификаторы

- Строки и символы:
    - `%s` — строка; `%S` — в верхнем регистре.
    - `%c` — символ; `%C` — символ в верхнем регистре.
- Целые:
    - `%d` — десятичное; `%o` — восьмеричное; `%x/%X` — шестнадцатеричное.
- Плавающая точка:
    - `%f` — десятичная; `%.2f` — 2 знака после точки.
    - `%e/%E` — экспоненциальная; `%g/%G` — компактная.
    - `%a/%A` — hex‑представление double.
- Булевы и объекты:
    - `%b` — логическое; для null печатает false.
    - `%h/%H` — hashCode в hex; `%s` вызывает toString().
- Дата/время (через Formatter для Date/Calendar/TemporalAccessor):
    - `%tF` — ISO дата yyyy-MM-dd; `%tT` — HH:mm:ss; `%tR` — HH:mm.
    - `%tc` — локализованный полный штамп;` %tY, %tm, %td` — части.

### Примеры:

```java
public class PrintfBasicsDemo {
    
    public static void demonstratePrintfBasics() {
        System.out.println("=== Основы Printf ===");
        
        // Основные способы форматирования
        String name = "Alice";
        int age = 30;
        double salary = 75000.50;
        
        // 1. System.out.printf - прямой вывод
        System.out.printf("Сотрудник: %s, возраст: %d, зарплата: %.2f%n", name, age, salary);
        
        // 2. String.format - создание строки
        String formatted = String.format("Информация: %s (%d лет) - $%.2f", name, age, salary);
        System.out.println(formatted);
        
        // 3. PrintWriter.printf - в файл или другой поток
        java.io.StringWriter writer = new java.io.StringWriter();
        java.io.PrintWriter printWriter = new java.io.PrintWriter(writer);
        printWriter.printf("Отчет: %s зарабатывает $%,.2f в год%n", name, salary);
        System.out.println("В поток: " + writer.toString());
        
        // 4. Formatter - низкоуровневый контроль
        java.util.Formatter formatter = new java.util.Formatter();
        formatter.format("Детали: %-10s | %5d | %8.2f%n", name, age, salary);
        System.out.println("Formatter: " + formatter.toString());
        formatter.close();
        
        demonstrateBasicFormatSpecifiers();
    }
    
    private static void demonstrateBasicFormatSpecifiers() {
        System.out.println("\\n--- Базовые спецификаторы ---");
        
        // Основные типы данных
        String text = "Java";
        char character = 'J';
        boolean flag = true;
        int number = 42;
        double decimal = 3.14159;
        
        System.out.println("=== Основные спецификаторы ===");
        System.out.printf("Строка: '%s'%n", text);
        System.out.printf("Символ: '%c'%n", character);
        System.out.printf("Булево: '%b'%n", flag);
        System.out.printf("Целое: '%d'%n", number);
        System.out.printf("Вещественное: '%f'%n", decimal);
        System.out.printf("Экспоненциальное: '%e'%n", decimal);
        System.out.printf("Общий формат: '%g'%n", decimal);
        System.out.printf("Шестнадцатеричное: '%x'%n", number);
        System.out.printf("Восьмеричное: '%o'%n", number);
        
        // Специальные символы
        System.out.println("\\n=== Специальные символы ===");
        System.out.printf("Новая строка: '%n'");
        System.out.printf("Табуляция: '%t\\t'<-табуляция%n", "");
        System.out.printf("Процент: '%%'%n");
        System.out.printf("Кавычки: \"%s\"%n", "в кавычках");
    }
    
    public static void main(String[] args) {
        demonstratePrintfBasics();
    }
}
```
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
    - Для `%f/%e/%g` — число знаков после точки: %.3f.
    - Для `%s` — максимум символов, обрезает строку: %.5s.
    - Для `%d/%x/%o` — точность не применяется; нужны ширина/флаги.

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


# Примеры от нейросетки

## 🔧 Спецификаторы формата

### Синтаксис спецификаторов
```java
public class FormatSpecifiersDemo {
    
    public static void demonstrateFormatSpecifierSyntax() {
        System.out.println("=== Синтаксис спецификаторов ===");
        
        // Полный синтаксис: %[аргумент][флаги][ширина][.точность]спецификатор
        demonstrateArgumentIndex();
        demonstrateFlags();
        demonstrateWidthAndPrecision();
        demonstrateConversionTypes();
    }
    
    private static void demonstrateArgumentIndex() {
        System.out.println("\\n--- Индексы аргументов ---");
        
        String firstName = "John";
        String lastName = "Doe";
        int age = 30;
        
        // Обычный порядок
        System.out.printf("Обычный: %s %s (%d лет)%n", firstName, lastName, age);
        
        // Изменение порядка с индексами (1-based)
        System.out.printf("Переставлено: %2$s, %1$s (%3$d лет)%n", firstName, lastName, age);
        
        // Повторное использование аргументов
        System.out.printf("Повтор: %1$s %2$s, а еще раз: %1$s %2$s%n", firstName, lastName);
        
        // Относительная индексация
        System.out.printf("Текущий: %s, предыдущий: %<s, следующий: %s%n", "A", "B");
    }
    
    private static void demonstrateFlags() {
        System.out.println("\\n--- Флаги форматирования ---");
        
        int positive = 123;
        int negative = -123;
        double decimal = 3.14;
        
        System.out.println("=== Флаги ===");
        
        // Флаг '-': выравнивание по левому краю
        System.out.printf("Обычное: '%5d'%n", positive);
        System.out.printf("Влево: '%-5d'%n", positive);
        
        // Флаг '+': всегда показывать знак
        System.out.printf("Обычное: '%d' и '%d'%n", positive, negative);
        System.out.printf("Со знаком: '%+d' и '%+d'%n", positive, negative);
        
        // Флаг ' ' (пробел): пробел для положительных чисел
        System.out.printf("С пробелом: '% d' и '% d'%n", positive, negative);
        
        // Флаг '0': заполнение нулями
        System.out.printf("Нули: '%05d'%n", positive);
        
        // Флаг '#': альтернативный формат
        System.out.printf("Hex обычный: '%x'%n", 255);
        System.out.printf("Hex альтернативный: '%#x'%n", 255);
        System.out.printf("Octal альтернативный: '%#o'%n", 64);
        
        // Флаг ',': групповые разделители
        System.out.printf("Большое число: '%,d'%n", 1234567);
        System.out.printf("Десятичное с разделителями: '%,.2f'%n", 1234567.89);
        
        // Флаг '(': отрицательные числа в скобках
        System.out.printf("В скобках: '%(d' и '%(d'%n", positive, negative);
    }
    
    private static void demonstrateWidthAndPrecision() {
        System.out.println("\\n--- Ширина и точность ---");
        
        String text = "Hello";
        double number = 123.456789;
        
        System.out.println("=== Ширина ===");
        System.out.printf("Обычное: '%s'%n", text);
        System.out.printf("Ширина 10: '%10s'%n", text);
        System.out.printf("Ширина 10 влево: '%-10s'%n", text);
        
        System.out.println("\\n=== Точность ===");
        System.out.printf("Обычное: '%f'%n", number);
        System.out.printf("2 знака: '%.2f'%n", number);
        System.out.printf("0 знаков: '%.0f'%n", number);
        
        // Точность для строк - ограничение длины
        String longText = "Very long text here";
        System.out.printf("Полная строка: '%s'%n", longText);
        System.out.printf("Обрезанная (10 символов): '%.10s'%n", longText);
        
        System.out.println("\\n=== Ширина + точность ===");
        System.out.printf("Число: '%10.2f'%n", number);
        System.out.printf("Число влево: '%-10.2f'%n", number);
        System.out.printf("С нулями: '%010.2f'%n", number);
        
        // Динамическая ширина и точность
        int width = 8;
        int precision = 3;
        System.out.printf("Динамические параметры: '%*.*f'%n", width, precision, number);
    }
    
    private static void demonstrateConversionTypes() {
        System.out.println("\\n--- Типы конверсии ---");
        
        int intValue = 42;
        double doubleValue = 123.456;
        java.util.Date date = new java.util.Date();
        
        System.out.println("=== Целые числа ===");
        System.out.printf("Десятичное: %d%n", intValue);
        System.out.printf("Восьмеричное: %o%n", intValue);
        System.out.printf("Шестнадцатеричное: %x%n", intValue);
        System.out.printf("Шестнадцатеричное (заглавные): %X%n", intValue);
        
        System.out.println("\\n=== Вещественные числа ===");
        System.out.printf("Обычное: %f%n", doubleValue);
        System.out.printf("Экспоненциальное: %e%n", doubleValue);
        System.out.printf("Экспоненциальное (заглавное): %E%n", doubleValue);
        System.out.printf("Общий формат: %g%n", doubleValue);
        System.out.printf("Общий формат (заглавный): %G%n", doubleValue);
        System.out.printf("Шестнадцатеричное float: %a%n", doubleValue);
        
        System.out.println("\\n=== Дата и время ===");
        System.out.printf("Полная дата: %tc%n", date);
        System.out.printf("Время: %tT%n", date);
        System.out.printf("Дата: %tF%n", date);
        
        System.out.println("\\n=== Символы и строки ===");
        System.out.printf("Символ: %c%n", 65); // 'A'
        System.out.printf("Строка: %s%n", "Hello World");
        System.out.printf("Строка (заглавные): %S%n", "hello world");
        System.out.printf("Булево: %b%n", true);
        System.out.printf("Булево: %B%n", false);
        
        System.out.println("\\n=== Специальные ===");
        System.out.printf("Хэш-код: %h%n", "Hello");
        System.out.printf("Хэш-код (заглавные): %H%n", "Hello");
        System.out.printf("Процент: %%n");
        System.out.printf("Новая строка: %n");
    }
    
    public static void main(String[] args) {
        demonstrateFormatSpecifierSyntax();
    }
}
```

---

## 🔢 Форматирование чисел

### Целые и вещественные числа
```java
public class NumberFormattingDemo {
    
    public static void demonstrateNumberFormatting() {
        System.out.println("=== Форматирование чисел ===");
        
        // Различные числовые типы
        byte byteValue = 127;
        short shortValue = 32767;
        int intValue = 2147483647;
        long longValue = 9223372036854775807L;
        float floatValue = 3.14159f;
        double doubleValue = 2.718281828459045;
        
        demonstrateIntegerFormatting(intValue, longValue);
        demonstrateFloatingPointFormatting(floatValue, doubleValue);
        demonstrateSpecialNumbers();
        demonstratePaddingAndAlignment();
        demonstrateGroupingSeparators();
    }
    
    private static void demonstrateIntegerFormatting(int intVal, long longVal) {
        System.out.println("\\n--- Форматирование целых чисел ---");
        
        System.out.println("=== Различные системы счисления ===");
        int number = 255;
        System.out.printf("Десятичное: %d%n", number);
        System.out.printf("Двоичное: %s%n", Integer.toBinaryString(number)); // Printf не поддерживает двоичное
        System.out.printf("Восьмеричное: %o%n", number);
        System.out.printf("Шестнадцатеричное: %x%n", number);
        System.out.printf("Шестнадцатеричное (заглавные): %X%n", number);
        
        // С префиксами
        System.out.println("\\n=== С префиксами (флаг #) ===");
        System.out.printf("Восьмеричное с префиксом: %#o%n", number);
        System.out.printf("Hex с префиксом: %#x%n", number);
        System.out.printf("HEX с префиксом: %#X%n", number);
        
        System.out.println("\\n=== Знаки ===");
        int positive = 42;
        int negative = -42;
        System.out.printf("Обычное: %d, %d%n", positive, negative);
        System.out.printf("Всегда со знаком: %+d, %+d%n", positive, negative);
        System.out.printf("Пробел для положительных: % d, % d%n", positive, negative);
        System.out.printf("Отрицательные в скобках: %(d, %(d%n", positive, negative);
        
        System.out.println("\\n=== Большие числа ===");
        long bigNumber = 1234567890L;
        System.out.printf("Большое число: %d%n", bigNumber);
        System.out.printf("С разделителями: %,d%n", bigNumber);
        System.out.printf("В научной нотации: %e%n", (double)bigNumber);
    }
    
    private static void demonstrateFloatingPointFormatting(float floatVal, double doubleVal) {
        System.out.println("\\n--- Форматирование вещественных чисел ---");
        
        double number = 1234.5678901234567;
        
        System.out.println("=== Основные форматы ===");
        System.out.printf("Обычный (%%f): %f%n", number);
        System.out.printf("Экспоненциальный (%%e): %e%n", number);
        System.out.printf("Экспоненциальный (%%E): %E%n", number);
        System.out.printf("Общий (%%g): %g%n", number);
        System.out.printf("Общий (%%G): %G%n", number);
        System.out.printf("Hex float (%%a): %a%n", number);
        
        System.out.println("\\n=== Точность ===");
        System.out.printf("По умолчанию: %f%n", number);
        System.out.printf("0 знаков: %.0f%n", number);
        System.out.printf("2 знака: %.2f%n", number);
        System.out.printf("6 знаков: %.6f%n", number);
        System.out.printf("10 знаков: %.10f%n", number);
        
        System.out.println("\\n=== Специальные случаи ===");
        double[] specialNumbers = {
            0.0, -0.0,
            Double.POSITIVE_INFINITY, Double.NEGATIVE_INFINITY,
            Double.NaN,
            1e-10, 1e10,
            0.000001, 0.00000001
        };
        
        for (double special : specialNumbers) {
            System.out.printf("Число %g: %%f=%.6f, %%e=%.2e, %%g=%.6g%n", 
                            special, special, special, special);
        }
    }
    
    private static void demonstrateSpecialNumbers() {
        System.out.println("\\n--- Специальные числа ---");
        
        // Очень большие и очень маленькие числа
        double veryBig = 1.23456789e20;
        double verySmall = 1.23456789e-20;
        
        System.out.println("=== Автоматический выбор формата (%%g) ===");
        System.out.printf("Очень большое %%g: %g%n", veryBig);
        System.out.printf("Очень большое %%f: %f%n", veryBig);
        System.out.printf("Очень большое %%e: %e%n", veryBig);
        
        System.out.printf("Очень маленькое %%g: %g%n", verySmall);
        System.out.printf("Очень маленькое %%f: %f%n", verySmall);
        System.out.printf("Очень маленькое %%e: %e%n", verySmall);
        
        // Денежные суммы
        System.out.println("\\n=== Форматирование денег ===");
        double[] amounts = {0.99, 1234.56, 1000000.00, 0.001, 999999999.99};
        
        for (double amount : amounts) {
            System.out.printf("Сумма: $%,.2f (научная нотация: %.2e)%n", amount, amount);
        }
    }
    
    private static void demonstratePaddingAndAlignment() {
        System.out.println("\\n--- Выравнивание и заполнение ---");
        
        double[] numbers = {1.23, 12.34, 123.45, 1234.56};
        
        System.out.println("=== Выравнивание по правому краю (по умолчанию) ===");
        for (double num : numbers) {
            System.out.printf("'%8.2f'%n", num);
        }
        
        System.out.println("\\n=== Выравнивание по левому краю (флаг -) ===");
        for (double num : numbers) {
            System.out.printf("'%-8.2f'%n", num);
        }
        
        System.out.println("\\n=== Заполнение нулями (флаг 0) ===");
        for (double num : numbers) {
            System.out.printf("'%08.2f'%n", num);
        }
        
        System.out.println("\\n=== Комбинированное форматирование ===");
        for (double num : numbers) {
            System.out.printf("Право: '%8.2f' | Лево: '%-8.2f' | Нули: '%08.2f'%n", num, num, num);
        }
        
        // Целые числа с заполнением
        System.out.println("\\n=== Целые числа с заполнением ===");
        int[] ints = {1, 12, 123, 1234};
        
        for (int num : ints) {
            System.out.printf("'%5d' | '%-5d' | '%05d'%n", num, num, num);
        }
    }
    
    private static void demonstrateGroupingSeparators() {
        System.out.println("\\n--- Групповые разделители ---");
        
        long[] bigNumbers = {
            1234L,
            12345L, 
            123456L,
            1234567L,
            12345678L,
            123456789L,
            1234567890123L
        };
        
        System.out.println("=== Разделители тысяч ===");
        for (long num : bigNumbers) {
            System.out.printf("Без разделителей: %12d | С разделителями: %,12d%n", num, num);
        }
        
        System.out.println("\\n=== Десятичные числа с разделителями ===");
        double[] decimals = {1234.56, 123456.789, 1234567890.123};
        
        for (double num : decimals) {
            System.out.printf("%.3f -> %,.3f%n", num, num);
        }
        
        // Комбинирование с другими флагами
        System.out.println("\\n=== Комбинирование флагов ===");
        double amount = 1234567.89;
        System.out.printf("Базовый: %.2f%n", amount);
        System.out.printf("С разделителями: %,.2f%n", amount);
        System.out.printf("Со знаком: %+,.2f%n", amount);
        System.out.printf("В поле шириной 15: '%15.2f'%n", amount);
        System.out.printf("Все флаги: '%+,15.2f'%n", amount);
        
        // Отрицательные числа
        double negativeAmount = -amount;
        System.out.printf("Отрицательное обычное: %,.2f%n", negativeAmount);
        System.out.printf("Отрицательное в скобках: %(,.2f%n", negativeAmount);
    }
    
    public static void main(String[] args) {
        demonstrateNumberFormatting();
    }
}
```

---

## 📝 Форматирование строк

### Строки и символы
```java
public class StringFormattingDemo {
    
    public static void demonstrateStringFormatting() {
        System.out.println("=== Форматирование строк ===");
        
        demonstrateBasicStringFormatting();
        demonstrateStringWidthAndAlignment();
        demonstrateStringPrecision();
        demonstrateCharacterFormatting();
        demonstrateBooleanFormatting();
        demonstrateAdvancedStringFormatting();
    }
    
    private static void demonstrateBasicStringFormatting() {
        System.out.println("\\n--- Основное форматирование строк ---");
        
        String name = "Alice";
        String surname = "Johnson";
        String email = "alice.johnson@example.com";
        
        System.out.println("=== Простые строки ===");
        System.out.printf("Имя: %s%n", name);
        System.out.printf("Полное имя: %s %s%n", name, surname);
        System.out.printf("Email: %s%n", email);
        
        // Заглавные буквы
        System.out.println("\\n=== Преобразование регистра ===");
        System.out.printf("Обычное: %s%n", name);
        System.out.printf("Заглавные: %S%n", name);
        
        // Null значения
        String nullString = null;
        System.out.println("\\n=== Null значения ===");
        System.out.printf("Null строка: '%s'%n", nullString);
        System.out.printf("Null с шириной: '%10s'%n", nullString);
        
        // Числа как строки
        System.out.println("\\n=== Числа как строки ===");
        int number = 42;
        System.out.printf("Число как int: %d%n", number);
        System.out.printf("Число как строка: %s%n", number);
        
        Object[] objects = {"текст", 123, 3.14, true, null};
        System.out.println("\\n=== Различные объекты как строки ===");
        for (int i = 0; i < objects.length; i++) {
            System.out.printf("Объект %d: '%s' (тип: %s)%n", 
                            i, objects[i], 
                            objects[i] != null ? objects[i].getClass().getSimpleName() : "null");
        }
    }
    
    private static void demonstrateStringWidthAndAlignment() {
        System.out.println("\\n--- Ширина и выравнивание строк ---");
        
        String[] names = {"Jo", "Alice", "Alexander", "Kim"};
        
        System.out.println("=== Выравнивание по правому краю (по умолчанию) ===");
        for (String name : names) {
            System.out.printf("'%10s' - длина: %d%n", name, name.length());
        }
        
        System.out.println("\\n=== Выравнивание по левому краю (флаг -) ===");
        for (String name : names) {
            System.out.printf("'%-10s' - длина: %d%n", name, name.length());
        }
        
        System.out.println("\\n=== Динамическая ширина ===");
        int width = 15;
        for (String name : names) {
            System.out.printf("'%*s' | '%-*s'%n", width, name, width, name);
        }
        
        // Создание таблицы
        System.out.println("\\n=== Табличное представление ===");
        String[][] users = {
            {"ID", "Имя", "Email", "Возраст"},
            {"1", "Анна", "anna@example.com", "25"},
            {"2", "Борис", "boris@company.ru", "34"},
            {"3", "Вера", "vera.s@mail.org", "29"},
            {"123", "Александр", "alexander.petrov@very-long-domain.com", "45"}
        };
        
        // Вычисляем максимальную ширину для каждого столбца
        int[] columnWidths = new int[users[0].length];
        for (String[] row : users) {
            for (int i = 0; i < row.length; i++) {
                columnWidths[i] = Math.max(columnWidths[i], row[i].length());
            }
        }
        
        // Выводим таблицу
        for (String[] row : users) {
            System.out.printf("| %-*s | %-*s | %-*s | %*s |%n",
                            columnWidths[0], row[0],
                            columnWidths[1], row[1],
                            columnWidths[2], row[2],
                            columnWidths[3], row[3]);
        }
    }
    
    private static void demonstrateStringPrecision() {
        System.out.println("\\n--- Точность строк (обрезание) ---");
        
        String longText = "Это очень длинная строка для демонстрации обрезания";
        
        System.out.println("=== Ограничение длины строки ===");
        System.out.printf("Полная строка: '%s'%n", longText);
        System.out.printf("10 символов: '%.10s'%n", longText);
        System.out.printf("20 символов: '%.20s'%n", longText);
        System.out.printf("5 символов: '%.5s'%n", longText);
        
        System.out.println("\\n=== Ширина + точность ===");
        System.out.printf("Ширина 30, точность 15: '%30.15s'%n", longText);
        System.out.printf("Влево, ширина 30, точность 15: '%-30.15s'%n", longText);
        
        // Применение к различным данным
        System.out.println("\\n=== Обрезание разных типов данных ===");
        Object[] data = {
            "короткая строка",
            "очень длинная строка которая будет обрезана",
            12345678901234567890L,
            3.14159265359,
            new java.util.Date()
        };
        
        for (Object item : data) {
            System.out.printf("Полное: '%-40s' | Обрезанное: '%.15s'%n", item, item);
        }
    }
    
    private static void demonstrateCharacterFormatting() {
        System.out.println("\\n--- Форматирование символов ---");
        
        System.out.println("=== ASCII символы ===");
        for (int i = 65; i <= 90; i++) { // A-Z
            if (i % 10 == 65) System.out.println(); // Новая строка каждые 10 символов
            System.out.printf("%c", i);
        }
        System.out.println();
        
        System.out.println("\\n=== Символы с кодами ===");
        char[] chars = {'A', 'Я', '€', '中', '🚀'};
        for (char ch : chars) {
            System.out.printf("Символ: '%c' (код: %d, hex: %#x)%n", ch, (int)ch, (int)ch);
        }
        
        System.out.println("\\n=== Специальные символы ===");
        System.out.printf("Табуляция: 'before%ctafter'%n", '\\t');
        System.out.printf("Новая строка: 'before%cafter'%n", '\\n');
        System.out.printf("Возврат каретки: 'before%cafter'%n", '\\r');
        
        // Символы из чисел
        System.out.println("\\n=== Числа как символы ===");
        int[] codes = {48, 65, 97, 8364, 128640}; // 0, A, a, €, 🚀
        for (int code : codes) {
            System.out.printf("Код %d -> символ '%c'%n", code, code);
        }
    }
    
    private static void demonstrateBooleanFormatting() {
        System.out.println("\\n--- Форматирование булевых значений ---");
        
        boolean trueValue = true;
        boolean falseValue = false;
        
        System.out.println("=== Основное форматирование ===");
        System.out.printf("True: %b%n", trueValue);
        System.out.printf("False: %b%n", falseValue);
        System.out.printf("True (заглавными): %B%n", trueValue);
        System.out.printf("False (заглавными): %B%n", falseValue);
        
        // Различные значения как булевы
        System.out.println("\\n=== Различные значения как булевы ===");
        Object[] values = {
            true, false,
            "true", "false", "yes", "no", "",
            1, 0, -1,
            new Object(), null,
            new int[0], new int[]{1, 2, 3}
        };
        
        for (Object value : values) {
            System.out.printf("Значение: %-15s -> %%b: %-5b, %%B: %-5B%n", 
                            value, value, value);
        }
        
        // С шириной поля
        System.out.println("\\n=== Булевы значения с выравниванием ===");
        System.out.printf("Право: '%10b' | Лево: '%-10b'%n", trueValue, trueValue);
        System.out.printf("Право: '%10b' | Лево: '%-10b'%n", falseValue, falseValue);
    }
    
    private static void demonstrateAdvancedStringFormatting() {
        System.out.println("\\n--- Продвинутое форматирование строк ---");
        
        // Многострочный текст
        String multilineText = "Первая строка\\nВторая строка\\nТретья строка";
        System.out.println("=== Многострочный текст ===");
        System.out.printf("Обычный: %s%n", multilineText);
        System.out.printf("Ограниченный: %.20s...%n", multilineText);
        
        // Эскейпинг специальных символов
        System.out.println("\\n=== Специальные символы в строках ===");
        String withSpecials = "Строка с \\\"кавычками\\\" и \\\\слешами\\\\";
        System.out.printf("Со спецсимволами: %s%n", withSpecials);
        
        // Форматирование URL и путей
        System.out.println("\\n=== URL и пути ===");
        String protocol = "https";
        String domain = "example.com";
        String path = "/api/users";
        int port = 8080;
        
        System.out.printf("URL: %s://%s:%d%s%n", protocol, domain, port, path);
        
        // Шаблоны сообщений
        System.out.println("\\n=== Шаблоны сообщений ===");
        String template = "Пользователь '%s' (ID: %d) выполнил действие '%s' в %tc";
        String username = "admin";
        int userId = 12345;
        String action = "login";
        java.util.Date timestamp = new java.util.Date();
        
        System.out.printf(template + "%n", username, userId, action, timestamp);
        
        // Условное форматирование
        System.out.println("\\n=== Условное форматирование ===");
        String[] statuses = {"active", "inactive", "pending", "blocked"};
        for (String status : statuses) {
            String formatted = switch (status) {
                case "active" -> String.format("✅ Статус: %-10s (готов к работе)", status);
                case "inactive" -> String.format("⚪ Статус: %-10s (неактивен)", status);
                case "pending" -> String.format("⏳ Статус: %-10s (ожидание)", status);
                case "blocked" -> String.format("❌ Статус: %-10s (заблокирован)", status);
                default -> String.format("❓ Статус: %-10s (неизвестен)", status);
            };
            System.out.println(formatted);
        }
    }
    
    public static void main(String[] args) {
        demonstrateStringFormatting();
    }
}
```

---

## 🕐 Дата и время

### Форматирование даты и времени
```java
public class DateTimeFormattingDemo {
    
    public static void demonstrateDateTimeFormatting() {
        System.out.println("=== Форматирование даты и времени ===");
        
        // Создаем тестовые даты
        java.util.Date date = new java.util.Date();
        java.util.Calendar calendar = java.util.Calendar.getInstance();
        calendar.set(2024, java.util.Calendar.DECEMBER, 25, 14, 30, 45);
        java.util.Date christmasDate = calendar.getTime();
        
        demonstrateBasicDateFormatting(date, christmasDate);
        demonstrateTimeFormatting(date, christmasDate);
        demonstrateDateComponents(date);
        demonstrateCustomDateFormatting();
        demonstrateModernDateTimeAPI();
    }
    
    private static void demonstrateBasicDateFormatting(java.util.Date currentDate, java.util.Date fixedDate) {
        System.out.println("\\n--- Основное форматирование даты ---");
        
        System.out.println("=== Полные форматы ===");
        System.out.printf("Полная дата и время (%%tc): %tc%n", currentDate);
        System.out.printf("Полная дата (%%tF): %tF%n", currentDate);
        System.out.printf("Полное время (%%tT): %tT%n", currentDate);
        System.out.printf("Время 12-часовое (%%tr): %tr%n", currentDate);
        System.out.printf("Время 24-часовое (%%tR): %tR%n", currentDate);
        
        System.out.println("\\n=== Фиксированная дата для демонстрации ===");
        System.out.printf("Рождество 2024: %tc%n", fixedDate);
        System.out.printf("Дата: %tF%n", fixedDate);
        System.out.printf("Время: %tT%n", fixedDate);
    }
    
    private static void demonstrateTimeFormatting(java.util.Date currentDate, java.util.Date fixedDate) {
        System.out.println("\\n--- Форматирование времени ---");
        
        System.out.println("=== Компоненты времени ===");
        System.out.printf("Час (24): %tH%n", fixedDate);
        System.out.printf("Час (12): %tI%n", fixedDate);
        System.out.printf("Минуты: %tM%n", fixedDate);
        System.out.printf("Секунды: %tS%n", fixedDate);
        System.out.printf("Миллисекунды: %tL%n", fixedDate);
        System.out.printf("Наносекунды: %tN%n", fixedDate);
        System.out.printf("AM/PM: %tp%n", fixedDate);
        System.out.printf("Часовой пояс: %tZ%n", fixedDate);
        System.out.printf("Смещение часового пояса: %tz%n", fixedDate);
        
        System.out.println("\\n=== Комбинированные форматы времени ===");
        System.out.printf("12-часовое с AM/PM: %tI:%tM:%tS %tp%n", fixedDate, fixedDate, fixedDate, fixedDate);
        System.out.printf("24-часовое: %tH:%tM:%tS%n", fixedDate, fixedDate, fixedDate);
        System.out.printf("Время с миллисекундами: %tT.%tL%n", fixedDate, fixedDate);
        
        // Использование индекса для повторного использования одного аргумента
        System.out.println("\\n=== Повторное использование аргумента ===");
        System.out.printf("Эффективно: %1$tH:%1$tM:%1$tS (24ч) | %1$tI:%1$tM:%1$tS %1$tp (12ч)%n", fixedDate);
    }
    
    private static void demonstrateDateComponents(java.util.Date date) {
        System.out.println("\\n--- Компоненты даты ---");
        
        System.out.println("=== Основные компоненты ===");
        System.out.printf("Год (4 цифры): %tY%n", date);
        System.out.printf("Год (2 цифры): %ty%n", date);
        System.out.printf("Месяц (число): %tm%n", date);
        System.out.printf("Месяц (полное название): %tB%n", date);
        System.out.printf("Месяц (сокращенное): %tb%n", date);
        System.out.printf("День месяца: %td%n", date);
        System.out.printf("День месяца (без ведущего нуля): %te%n", date);
        
        System.out.println("\\n=== Дни недели ===");
        System.out.printf("День недели (полное название): %tA%n", date);
        System.out.printf("День недели (сокращенное): %ta%n", date);
        System.out.printf("День недели (число): %tu%n", date); // 1=понедельник, 7=воскресенье
        System.out.printf("День недели (число US): %tw%n", date); // 0=воскресенье, 6=суббота
        
        System.out.println("\\n=== Дополнительные компоненты ===");
        System.out.printf("День года: %tj%n", date);
        System.out.printf("Неделя года: %tU%n", date); // Неделя начинается с воскресенья
        System.out.printf("Неделя года (ISO): %tV%n", date); // ISO неделя
        System.out.printf("Неделя года (понедельник): %tW%n", date); // Неделя начинается с понедельника
        
        System.out.println("\\n=== Эпоха ===");
        System.out.printf("Секунды с эпохи: %ts%n", date);
        System.out.printf("Миллисекунды с эпохи: %tQ%n", date);
    }
    
    private static void demonstrateCustomDateFormatting() {
        System.out.println("\\n--- Пользовательские форматы даты ---");
        
        java.util.Date date = new java.util.Date();
        
        System.out.println("=== Различные стили дат ===");
        // Международные форматы
        System.out.printf("ISO 8601: %tF %tT%n", date, date);
        System.out.printf("Европейский: %1$td.%1$tm.%1$tY%n", date);
        System.out.printf("Американский: %1$tm/%1$td/%1$tY%n", date);
        System.out.printf("Читаемый: %1$td %1$tB %1$tY%n", date);
        System.out.printf("Сокращенный: %1$td %1$tb %1$ty%n", date);
        
        System.out.println("\\n=== Деловые форматы ===");
        System.out.printf("Формальный: %1$tA, %1$td %1$tB %1$tY года%n", date);
        System.out.printf("Краткий деловой: %1$td.%1$tm.%1$ty %1$tH:%1$tM%n", date);
        System.out.printf("Файловая метка: %1$tY%1$tm%1$td_%1$tH%1$tM%1$tS%n", date);
        System.out.printf("Лог формат: [%1$tY-%1$tm-%1$td %1$tH:%1$tM:%1$tS]%n", date);
        
        System.out.println("\\n=== Специальные случаи ===");
        // Начало и конец дня
        java.util.Calendar cal = java.util.Calendar.getInstance();
        cal.set(java.util.Calendar.HOUR_OF_DAY, 0);
        cal.set(java.util.Calendar.MINUTE, 0);
        cal.set(java.util.Calendar.SECOND, 0);
        cal.set(java.util.Calendar.MILLISECOND, 0);
        java.util.Date startOfDay = cal.getTime();
        
        cal.set(java.util.Calendar.HOUR_OF_DAY, 23);
        cal.set(java.util.Calendar.MINUTE, 59);
        cal.set(java.util.Calendar.SECOND, 59);
        cal.set(java.util.Calendar.MILLISECOND, 999);
        java.util.Date endOfDay = cal.getTime();
        
        System.out.printf("Начало дня: %tc%n", startOfDay);
        System.out.printf("Конец дня: %tc%n", endOfDay);
        
        // Временной интервал
        System.out.printf("Рабочий день: %1$tH:%1$tM - %2$tH:%2$tM%n", startOfDay, endOfDay);
    }
    
    private static void demonstrateModernDateTimeAPI() {
        System.out.println("\\n--- Современный API даты и времени (Java 8+) ---");
        
        // Современные классы
        java.time.LocalDateTime localDateTime = java.time.LocalDateTime.now();
        java.time.ZonedDateTime zonedDateTime = java.time.ZonedDateTime.now();
        java.time.LocalDate localDate = java.time.LocalDate.now();
        java.time.LocalTime localTime = java.time.LocalTime.now();
        
        System.out.println("=== Современные классы с printf ===");
        // Примечание: printf поддерживает только классы, реализующие Temporal
        System.out.printf("LocalDateTime: %tc%n", java.sql.Timestamp.valueOf(localDateTime));
        System.out.printf("LocalDate: %tF%n", java.sql.Date.valueOf(localDate));
        
        // Для современного API лучше использовать DateTimeFormatter
        System.out.println("\\n=== Рекомендуемый подход для Java 8+ ===");
        java.time.format.DateTimeFormatter customFormatter = 
            java.time.format.DateTimeFormatter.ofPattern("dd MMMM yyyy, HH:mm:ss");
        
        System.out.println("LocalDateTime: " + localDateTime.format(customFormatter));
        System.out.println("LocalDate: " + localDate.format(java.time.format.DateTimeFormatter.ofPattern("dd.MM.yyyy")));
        System.out.println("LocalTime: " + localTime.format(java.time.format.DateTimeFormatter.ofPattern("HH:mm:ss")));
        
        // Сравнение старого и нового подходов
        System.out.println("\\n=== Сравнение подходов ===");
        java.util.Date oldDate = new java.util.Date();
        System.out.printf("Старый API (printf): %1$td.%1$tm.%1$tY %1$tH:%1$tM:%1$tS%n", oldDate);
        System.out.println("Новый API (formatter): " + localDateTime.format(
            java.time.format.DateTimeFormatter.ofPattern("dd.MM.yyyy HH:mm:ss")));
        
        // Работа с часовыми поясами
        System.out.println("\\n=== Часовые пояса ===");
        java.time.ZonedDateTime utc = java.time.ZonedDateTime.now(java.time.ZoneId.of("UTC"));
        java.time.ZonedDateTime moscow = java.time.ZonedDateTime.now(java.time.ZoneId.of("Europe/Moscow"));
        java.time.ZonedDateTime tokyo = java.time.ZonedDateTime.now(java.time.ZoneId.of("Asia/Tokyo"));
        
        // Конвертируем в старый Date для printf
        System.out.printf("UTC: %tc%n", java.util.Date.from(utc.toInstant()));
        System.out.printf("Moscow: %tc%n", java.util.Date.from(moscow.toInstant()));
        System.out.printf("Tokyo: %tc%n", java.util.Date.from(tokyo.toInstant()));
    }
    
    public static void main(String[] args) {
        demonstrateDateTimeFormatting();
    }
}
```

---

## 🌍 Локализация

### Форматирование с учетом локали
```java
public class LocalizedFormattingDemo {
    
    public static void demonstrateLocalizedFormatting() {
        System.out.println("=== Локализованное форматирование ===");
        
        // Различные локали для тестирования
        java.util.Locale[] locales = {
            java.util.Locale.US,                    // Английский (США)
            java.util.Locale.UK,                    // Английский (Великобритания)
            new java.util.Locale("ru", "RU"),       // Русский
            java.util.Locale.GERMANY,               // Немецкий
            java.util.Locale.FRANCE,                // Французский
            java.util.Locale.JAPAN,                 // Японский
            new java.util.Locale("ar", "SA"),       // Арабский
            new java.util.Locale("hi", "IN")        // Хинди
        };
        
        demonstrateNumberLocalization(locales);
        demonstrateDateLocalization(locales);
        demonstrateCurrencyFormatting(locales);
        demonstrateCustomLocalization();
    }
    
    private static void demonstrateNumberLocalization(java.util.Locale[] locales) {
        System.out.println("\\n--- Локализация чисел ---");
        
        double number = 1234567.89;
        
        System.out.println("=== Форматирование больших чисел ===");
        for (java.util.Locale locale : locales) {
            // Сохраняем текущую локаль
            java.util.Locale originalLocale = java.util.Locale.getDefault();
            
            try {
                // Устанавливаем локаль для форматирования
                java.util.Locale.setDefault(locale);
                
                String formatted = String.format(locale, "%,.2f", number);
                System.out.printf("%-20s: %s%n", locale.getDisplayName(java.util.Locale.ENGLISH), formatted);
                
            } finally {
                // Восстанавливаем оригинальную локаль
                java.util.Locale.setDefault(originalLocale);
            }
        }
        
        // Процентное форматирование
        System.out.println("\\n=== Процентное форматирование ===");
        double percentage = 0.1234;
        
        for (java.util.Locale locale : new java.util.Locale[]{java.util.Locale.US, java.util.Locale.GERMANY, new java.util.Locale("ru", "RU")}) {
            java.text.NumberFormat percentFormat = java.text.NumberFormat.getPercentInstance(locale);
            System.out.printf("%-20s: %s%n", locale.getDisplayName(), percentFormat.format(percentage));
        }
    }
    
    private static void demonstrateDateLocalization(java.util.Locale[] locales) {
        System.out.println("\\n--- Локализация дат ---");
        
        java.util.Date date = new java.util.Date();
        
        System.out.println("=== Названия месяцев ===");
        for (java.util.Locale locale : locales) {
            java.util.Locale originalLocale = java.util.Locale.getDefault();
            
            try {
                java.util.Locale.setDefault(locale);
                String formatted = String.format(locale, "%tB %tY", date, date);
                System.out.printf("%-20s: %s%n", locale.getDisplayName(java.util.Locale.ENGLISH), formatted);
            } finally {
                java.util.Locale.setDefault(originalLocale);
            }
        }
        
        System.out.println("\\n=== Названия дней недели ===");
        for (java.util.Locale locale : new java.util.Locale[]{java.util.Locale.US, new java.util.Locale("ru", "RU"), java.util.Locale.FRANCE, java.util.Locale.GERMANY}) {
            java.util.Locale originalLocale = java.util.Locale.getDefault();
            
            try {
                java.util.Locale.setDefault(locale);
                String formatted = String.format(locale, "%tA, %td %tB %tY", date, date, date, date);
                System.out.printf("%-20s: %s%n", locale.getDisplayName(), formatted);
            } finally {
                java.util.Locale.setDefault(originalLocale);
            }
        }
        
        // Использование DateFormat для лучшей локализации
        System.out.println("\\n=== Полное форматирование даты через DateFormat ===");
        for (java.util.Locale locale : new java.util.Locale[]{java.util.Locale.US, new java.util.Locale("ru", "RU"), java.util.Locale.JAPAN}) {
            java.text.DateFormat fullFormat = java.text.DateFormat.getDateTimeInstance(
                java.text.DateFormat.FULL, java.text.DateFormat.FULL, locale);
            System.out.printf("%-20s: %s%n", locale.getDisplayName(), fullFormat.format(date));
        }
    }
    
    private static void demonstrateCurrencyFormatting(java.util.Locale[] locales) {
        System.out.println("\\n--- Локализация валют ---");
        
        double amount = 1234.56;
        
        System.out.println("=== Форматирование валют ===");
        java.util.Locale[] currencyLocales = {
            java.util.Locale.US,                    // USD
            new java.util.Locale("ru", "RU"),       // RUB
            java.util.Locale.GERMANY,               // EUR (Германия)
            java.util.Locale.UK,                    // GBP
            java.util.Locale.JAPAN,                 // JPY
            new java.util.Locale("zh", "CN")        // CNY
        };
        
        for (java.util.Locale locale : currencyLocales) {
            java.text.NumberFormat currencyFormat = java.text.NumberFormat.getCurrencyInstance(locale);
            String formatted = currencyFormat.format(amount);
            java.util.Currency currency = currencyFormat.getCurrency();
            
            System.out.printf("%-20s: %-15s (%s)%n", 
                            locale.getDisplayName(), 
                            formatted, 
                            currency.getDisplayName(locale));
        }
        
        // Конвертация валют (демонстрационные курсы)
        System.out.println("\\n=== Конвертация валют ===");
        java.util.Map<String, Double> exchangeRates = java.util.Map.of(
            "USD", 1.0,
            "EUR", 0.85,
            "RUB", 75.0,
            "JPY", 110.0
        );
        
        double usdAmount = 100.0;
        System.out.printf("Базовая сумма: $%.2f%n", usdAmount);
        
        for (java.util.Map.Entry<String, Double> entry : exchangeRates.entrySet()) {
            String currencyCode = entry.getKey();
            double rate = entry.getValue();
            double convertedAmount = usdAmount * rate;
            
            // Находим подходящую локаль для валюты
            java.util.Locale locale = findLocaleForCurrency(currencyCode);
            java.text.NumberFormat format = java.text.NumberFormat.getCurrencyInstance(locale);
            
            System.out.printf("%s: %s (курс: %.4f)%n", 
                            currencyCode, 
                            format.format(convertedAmount), 
                            rate);
        }
    }
    
    private static java.util.Locale findLocaleForCurrency(String currencyCode) {
        return switch (currencyCode) {
            case "USD" -> java.util.Locale.US;
            case "EUR" -> java.util.Locale.GERMANY;
            case "RUB" -> new java.util.Locale("ru", "RU");
            case "JPY" -> java.util.Locale.JAPAN;
            default -> java.util.Locale.getDefault();
        };
    }
    
    private static void demonstrateCustomLocalization() {
        System.out.println("\\n--- Пользовательская локализация ---");
        
        // Создание отчета с учетом локали
        generateLocalizedReport();
        
        // Форматирование для различных регионов
        demonstrateRegionalFormatting();
        
        // Работа с RTL (Right-to-Left) языками
        demonstrateRTLFormatting();
    }
    
    private static void generateLocalizedReport() {
        System.out.println("\\n=== Локализованный отчет ===");
        
        java.util.Locale[] reportLocales = {
            java.util.Locale.US,
            new java.util.Locale("ru", "RU"),
            java.util.Locale.GERMANY
        };
        
        // Данные для отчета
        String companyName = "TechCorp Inc.";
        java.util.Date reportDate = new java.util.Date();
        double revenue = 1234567.89;
        double profit = 234567.12;
        double profitMargin = profit / revenue;
        
        for (java.util.Locale locale : reportLocales) {
            System.out.printf("\\n--- Отчет (%s) ---%n", locale.getDisplayName());
            
            java.text.NumberFormat currencyFormat = java.text.NumberFormat.getCurrencyInstance(locale);
            java.text.NumberFormat percentFormat = java.text.NumberFormat.getPercentInstance(locale);
            java.text.DateFormat dateFormat = java.text.DateFormat.getDateInstance(java.text.DateFormat.LONG, locale);
            
            System.out.printf("Компания: %s%n", companyName);
            System.out.printf("Дата отчета: %s%n", dateFormat.format(reportDate));
            System.out.printf("Выручка: %s%n", currencyFormat.format(revenue));
            System.out.printf("Прибыль: %s%n", currencyFormat.format(profit));
            System.out.printf("Маржа: %s%n", percentFormat.format(profitMargin));
        }
    }
    
    private static void demonstrateRegionalFormatting() {
        System.out.println("\\n=== Региональные различия ===");
        
        double temperature = 23.5;
        double distance = 1609.34; // метры
        double weight = 2.5; // кг
        
        System.out.println("=== США (имперские единицы) ===");
        System.out.printf("Температура: %.1f°F%n", temperature * 9/5 + 32);
        System.out.printf("Расстояние: %.2f миль%n", distance * 0.000621371);
        System.out.printf("Вес: %.2f фунтов%n", weight * 2.20462);
        
        System.out.println("\\n=== Метрическая система ===");
        System.out.printf("Температура: %.1f°C%n", temperature);
        System.out.printf("Расстояние: %.0f м%n", distance);
        System.out.printf("Вес: %.1f кг%n", weight);
        
        // Форматы адресов
        System.out.println("\\n=== Форматы адресов ===");
        String street = "Main Street";
        String building = "123";
        String city = "New York";
        String state = "NY";
        String zip = "10001";
        String country = "USA";
        
        System.out.printf("США: %s %s, %s, %s %s, %s%n", building, street, city, state, zip, country);
        System.out.printf("Европа: %s %s, %s-%s %s, %s%n", street, building, zip, city, state, country);
    }
    
    private static void demonstrateRTLFormatting() {
        System.out.println("\\n=== RTL (справа налево) форматирование ===");
        
        // Арабская локаль
        java.util.Locale arabicLocale = new java.util.Locale("ar", "SA");
        
        double amount = 1234.56;
        java.util.Date date = new java.util.Date();
        
        System.out.println("=== Арабский (RTL) ===");
        java.text.NumberFormat arabicNumberFormat = java.text.NumberFormat.getNumberInstance(arabicLocale);
        java.text.NumberFormat arabicCurrencyFormat = java.text.NumberFormat.getCurrencyInstance(arabicLocale);
        java.text.DateFormat arabicDateFormat = java.text.DateFormat.getDateInstance(java.text.DateFormat.FULL, arabicLocale);
        
        System.out.printf("Число: %s%n", arabicNumberFormat.format(amount));
        System.out.printf("Валюта: %s%n", arabicCurrencyFormat.format(amount));
        System.out.printf("Дата: %s%n", arabicDateFormat.format(date));
        
        // Смешанный текст (LTR + RTL)
        System.out.println("\\n=== Смешанный текст ===");
        String englishText = "Hello";
        String arabicText = "مرحبا"; // "Привет" на арабском
        
        System.out.printf("Английский + Арабский: %s %s%n", englishText, arabicText);
        System.out.printf("Арабский + Английский: %s %s%n", arabicText, englishText);
    }
    
    public static void main(String[] args) {
        // Сохраняем оригинальную локаль
        java.util.Locale originalLocale = java.util.Locale.getDefault();
        
        try {
            demonstrateLocalizedFormatting();
        } finally {
            // Восстанавливаем оригинальную локаль
            java.util.Locale.setDefault(originalLocale);
        }
    }
}
```

---

## 🏆 Лучшие практики

### Рекомендации по использованию
```java
public class PrintfBestPractices {
    
    public static void demonstrateBestPractices() {
        System.out.println("=== Лучшие практики Printf ===");
        
        goodPractices();
        badPractices();
        performanceConsiderations();
        securityConsiderations();
        debuggingAndLogging();
    }
    
    private static void goodPractices() {
        System.out.println("\\n✅ Хорошие практики:");
        
        // 1. Использование константных форматов
        demonstrateConstantFormats();
        
        // 2. Валидация входных данных
        demonstrateInputValidation();
        
        // 3. Правильное использование локали
        demonstrateProperLocale();
        
        // 4. Читаемые форматы
        demonstrateReadableFormats();
        
        // 5. Переиспользование форматтеров
        demonstrateFormatterReuse();
    }
    
    private static void demonstrateConstantFormats() {
        System.out.println("\\n--- 1. Константные форматы ---");
        
        // ✅ Хорошо: константы для часто используемых форматов
        final String CURRENCY_FORMAT = "$%,.2f";
        final String DATE_FORMAT = "%tF %tT";
        final String USER_INFO_FORMAT = "Пользователь: %-20s | ID: %08d | Статус: %s";
        final String ERROR_FORMAT = "[ERROR] %tc - %s: %s%n";
        
        // Использование константных форматов
        double price = 1234.56;
        java.util.Date timestamp = new java.util.Date();
        String username = "admin";
        int userId = 12345;
        String status = "active";
        
        System.out.printf("Цена: " + CURRENCY_FORMAT + "%n", price);
        System.out.printf("Время: " + DATE_FORMAT + "%n", timestamp, timestamp);
        System.out.printf(USER_INFO_FORMAT + "%n", username, userId, status);
        
        // Для логирования ошибок
        String errorType = "ValidationError";
        String errorMessage = "Invalid input data";
        System.out.printf(ERROR_FORMAT, new java.util.Date(), errorType, errorMessage);
        
        System.out.println("💡 Константы упрощают поддержку и обеспечивают консистентность");
    }
    
    private static void demonstrateInputValidation() {
        System.out.println("\\n--- 2. Валидация входных данных ---");
        
        // ✅ Безопасное форматирование с проверками
        String[] userInputs = {"John Doe", null, "", "Very long name that might break formatting"};
        
        for (String input : userInputs) {
            String safeFormatted = formatUserName(input);
            System.out.println("Безопасно отформатировано: " + safeFormatted);
        }
        
        // ✅ Валидация числовых данных
        Double[] amounts = {100.50, null, Double.POSITIVE_INFINITY, Double.NaN, -50.25};
        
        for (Double amount : amounts) {
            String formatted = formatCurrency(amount);
            System.out.println("Валюта: " + formatted);
        }
    }
    
    private static String formatUserName(String name) {
        if (name == null) {
            return "[Без имени]";
        }
        
        if (name.trim().isEmpty()) {
            return "[Пустое имя]";
        }
        
        // Ограничиваем длину для безопасного форматирования
        String safeName = name.length() > 20 ? name.substring(0, 17) + "..." : name;
        return String.format("Пользователь: %-20s", safeName);
    }
    
    private static String formatCurrency(Double amount) {
        if (amount == null) {
            return "N/A";
        }
        
        if (Double.isInfinite(amount)) {
            return amount > 0 ? "+∞" : "-∞";
        }
        
        if (Double.isNaN(amount)) {
            return "Некорректная сумма";
        }
        
        return String.format("$%,.2f", amount);
    }
    
    private static void demonstrateProperLocale() {
        System.out.println("\\n--- 3. Правильное использование локали ---");
        
        double amount = 1234.56;
        java.util.Date date = new java.util.Date();
        
        // ✅ Явное указание локали для интернациональных приложений
        java.util.Locale[] locales = {
            java.util.Locale.US,
            new java.util.Locale("ru", "RU"),
            java.util.Locale.GERMANY
        };
        
        System.out.println("=== Локализованное форматирование ===");
        for (java.util.Locale locale : locales) {
            String localizedAmount = String.format(locale, "%,.2f", amount);
            String localizedDate = String.format(locale, "%tF", date);
            
            System.out.printf("%-15s: %10s | %s%n", 
                            locale.getDisplayName(), 
                            localizedAmount, 
                            localizedDate);
        }
        
        // ✅ Создание локализованного форматтера
        LocalizedFormatter formatter = new LocalizedFormatter(new java.util.Locale("ru", "RU"));
        System.out.println("\\nЛокализованный форматтер:");
        System.out.println(formatter.formatReport("TechCorp", amount, date));
    }
    
    static class LocalizedFormatter {
        private final java.util.Locale locale;
        private final java.text.NumberFormat currencyFormat;
        private final java.text.DateFormat dateFormat;
        
        public LocalizedFormatter(java.util.Locale locale) {
            this.locale = locale;
            this.currencyFormat = java.text.NumberFormat.getCurrencyInstance(locale);
            this.dateFormat = java.text.DateFormat.getDateTimeInstance(
                java.text.DateFormat.MEDIUM, java.text.DateFormat.MEDIUM, locale);
        }
        
        public String formatReport(String company, double revenue, java.util.Date date) {
            return String.format(locale,
                "Отчет компании %s%nВыручка: %s%nДата: %s%n",
                company,
                currencyFormat.format(revenue),
                dateFormat.format(date)
            );
        }
    }
    
    private static void demonstrateReadableFormats() {
        System.out.println("\\n--- 4. Читаемые форматы ---");
        
        // ✅ Хорошо: понятные имена переменных и форматы
        String customerName = "John Smith";
        String orderId = "ORD-12345";
        double orderTotal = 299.99;
        int itemCount = 3;
        java.util.Date orderDate = new java.util.Date();
        
        // ✅ Многострочный формат для сложных структур
        String orderSummary = String.format(
            "╔══════════════════════════════════════╗%n" +
            "║              ЗАКАЗ                   ║%n" +
            "╠══════════════════════════════════════╣%n" +
            "║ ID заказа: %-25s ║%n" +
            "║ Клиент:    %-25s ║%n" +
            "║ Дата:      %-25tc ║%n" +
            "║ Товаров:   %-25d ║%n" +
            "║ Итого:     %-25.2f$ ║%n" +
            "╚══════════════════════════════════════╝",
            orderId, customerName, orderDate, itemCount, orderTotal
        );
        
        System.out.println(orderSummary);
        
        // ✅ Табличный формат для данных
        demonstrateTableFormat();
    }
    
    private static void demonstrateTableFormat() {
        System.out.println("\\n=== Табличный формат ===");
        
        Object[][] salesData = {
            {"Продукт", "Количество", "Цена", "Итого"},
            {"Laptop Pro", 2, 1299.99, 2599.98},
            {"Mouse Wireless", 5, 29.99, 149.95},
            {"Keyboard RGB", 3, 89.50, 268.50}
        };
        
        // Рассчитываем ширину столбцов
        int[] columnWidths = {15, 10, 10, 10};
        
        // Заголовок
        System.out.printf("┌%s┬%s┬%s┬%s┐%n",
            "─".repeat(columnWidths[0]),
            "─".repeat(columnWidths[1]),
            "─".repeat(columnWidths[2]),
            "─".repeat(columnWidths[3]));
        
        for (int row = 0; row < salesData.length; row++) {
            System.out.printf("│%-*s│%*s│%*s│%*s│%n",
                columnWidths[0], salesData[row][0],
                columnWidths[1], salesData[row][1],
                columnWidths[2], salesData[row][2],
                columnWidths[3], salesData[row][3]);
            
            if (row == 0) { // Разделитель после заголовка
                System.out.printf("├%s┼%s┼%s┼%s┤%n",
                    "─".repeat(columnWidths[0]),
                    "─".repeat(columnWidths[1]),
                    "─".repeat(columnWidths[2]),
                    "─".repeat(columnWidths[3]));
            }
        }
        
        // Нижняя граница
        System.out.printf("└%s┴%s┴%s┴%s┘%n",
            "─".repeat(columnWidths[0]),
            "─".repeat(columnWidths[1]),
            "─".repeat(columnWidths[2]),
            "─".repeat(columnWidths[3]));
    }
    
    private static void demonstrateFormatterReuse() {
        System.out.println("\\n--- 5. Переиспользование форматтеров ---");
        
        // ✅ Создаем переиспользуемый форматтер
        ReusableFormatter formatter = new ReusableFormatter();
        
        String[] usernames = {"alice", "bob", "charlie_very_long_name"};
        int[] userIds = {1, 42, 12345};
        double[] balances = {100.50, 0.0, 9999.99};
        
        System.out.println("=== Отчет пользователей ===");
        for (int i = 0; i < usernames.length; i++) {
            String report = formatter.formatUserReport(usernames[i], userIds[i], balances[i]);
            System.out.println(report);
        }
        
        formatter.close(); // Освобождаем ресурсы
    }
    
    static class ReusableFormatter implements AutoCloseable {
        private final java.util.Formatter formatter;
        private final java.io.StringWriter writer;
        
        public ReusableFormatter() {
            this.writer = new java.io.StringWriter();
            this.formatter = new java.util.Formatter(writer);
        }
        
        public String formatUserReport(String username, int userId, double balance) {
            // Очищаем буфер
            writer.getBuffer().setLength(0);
            
            formatter.format("👤 Пользователь: %-15s | ID: %05d | Баланс: $%8.2f",
                username, userId, balance);
            
            return writer.toString();
        }
        
        @Override
        public void close() {
            formatter.close();
        }
    }
    
    private static void badPractices() {
        System.out.println("\\n❌ Плохие практики (избегайте):");
        
        // Эти примеры показывают что НЕ нужно делать
        demonstrateBadFormatting();
        demonstrateSecurityIssues();
        demonstratePerformanceProblems();
    }
    
    private static void demonstrateBadFormatting() {
        System.out.println("\\n--- ❌ Плохое форматирование ---");
        
        // ❌ Плохо: магические числа и неясные форматы
        double price = 1234.567890123;
        System.out.printf("❌ Плохо: %.10f%n", price); // Слишком много знаков
        System.out.printf("❌ Плохо: %20.2f%n", price); // Непонятная ширина
        
        // ❌ Плохо: неконсистентное форматирование
        String[] names = {"John", "Alice", "Bob"};
        for (int i = 0; i < names.length; i++) {
            if (i == 0) {
                System.out.printf("Name: %s%n", names[i]);
            } else if (i == 1) {
                System.out.printf("User: %-10s%n", names[i]);  
            } else {
                System.out.printf("Person: %S%n", names[i]);
            }
        }
        
        System.out.println("💡 Используйте консистентные форматы для однотипных данных");
    }
    
    private static void demonstrateSecurityIssues() {
        System.out.println("\\n--- ❌ Проблемы безопасности ---");
        
        // ❌ Потенциальная уязвимость: пользовательский ввод как формат
        String userInput = "%s %s %d %d"; // Потенциально опасно
        
        System.out.println("❌ Никогда не используйте пользовательский ввод как формат строки:");
        System.out.println("// printf(userInput, args); // ОПАСНО!");
        
        // ✅ Безопасный подход
        System.out.printf("✅ Безопасно: Пользователь ввел: %s%n", userInput);
        
        // ❌ Логирование конфиденциальных данных
        String password = "secret123";
        System.out.println("❌ Плохо: логирование паролей");
        // System.out.printf("User password: %s%n", password); // НЕ ДЕЛАЙТЕ ТАК!
        
        // ✅ Безопасное логирование
        System.out.printf("✅ Хорошо: Password set: %b%n", password != null && !password.isEmpty());
    }
    
    private static void demonstratePerformanceProblems() {
        System.out.println("\\n--- ❌ Проблемы производительности ---");
        
        // ❌ Создание форматтеров в циклах
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < 1000; i++) {
            // ❌ Плохо: создаем новый форматтер каждый раз
            java.util.Formatter badFormatter = new java.util.Formatter();
            badFormatter.format("Item %d", i);
            badFormatter.close();
        }
        long badTime = System.currentTimeMillis() - startTime;
        
        // ✅ Переиспользование форматтера
        startTime = System.currentTimeMillis();
        try (java.util.Formatter goodFormatter = new java.util.Formatter()) {
            for (int i = 0; i < 1000; i++) {
                goodFormatter.format("Item %d%n", i);
            }
        }
        long goodTime = System.currentTimeMillis() - startTime;
        
        System.out.printf("❌ Плохой подход: %d мс%n", badTime);
        System.out.printf("✅ Хороший подход: %d мс%n", goodTime);
        System.out.printf("Улучшение: %.1fx%n", (double)badTime / Math.max(goodTime, 1));
    }
    
    private static void performanceConsiderations() {
        System.out.println("\\n=== Соображения производительности ===");
        
        // Сравнение различных подходов к форматированию
        int iterations = 100000;
        String name = "John";
        int age = 30;
        
        // 1. String concatenation
        long startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            String result = "Name: " + name + ", Age: " + age;
        }
        long concatTime = System.nanoTime() - startTime;
        
        // 2. String.format
        startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            String result = String.format("Name: %s, Age: %d", name, age);
        }
        long formatTime = System.nanoTime() - startTime;
        
        // 3. StringBuilder
        startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            StringBuilder sb = new StringBuilder();
            sb.append("Name: ").append(name).append(", Age: ").append(age);
            String result = sb.toString();
        }
        long sbTime = System.nanoTime() - startTime;
        
        // 4. Reused StringBuilder
        StringBuilder reusedSb = new StringBuilder(50);
        startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            reusedSb.setLength(0);
            reusedSb.append("Name: ").append(name).append(", Age: ").append(age);
            String result = reusedSb.toString();
        }
        long reusedSbTime = System.nanoTime() - startTime;
        
        System.out.printf("Результаты для %d итераций:%n", iterations);
        System.out.printf("Конкатенация:     %.2f мс%n", concatTime / 1_000_000.0);
        System.out.printf("String.format:    %.2f мс (%.1fx медленнее)%n", 
                         formatTime / 1_000_000.0, 
                         (double)formatTime / concatTime);
        System.out.printf("StringBuilder:    %.2f мс (%.1fx медленнее)%n", 
                         sbTime / 1_000_000.0, 
                         (double)sbTime / concatTime);
        System.out.printf("Reused SB:        %.2f мс (%.1fx медленнее)%n", 
                         reusedSbTime / 1_000_000.0, 
                         (double)reusedSbTime / concatTime);
    }
    
    private static void securityConsiderations() {
        System.out.println("\\n=== Соображения безопасности ===");
        
        System.out.println("🔒 Правила безопасности для printf:");
        System.out.println("1. Никогда не используйте пользовательский ввод как формат");
        System.out.println("2. Валидируйте все входные данные");
        System.out.println("3. Не логируйте конфиденциальную информацию");
        System.out.println("4. Используйте параметризованные запросы для SQL");
        System.out.println("5. Экранируйте специальные символы в веб-контексте");
        
        // Пример безопасного логирования
        demonstrateSecureLogging();
    }
    
    private static void demonstrateSecureLogging() {
        System.out.println("\\n--- Безопасное логирование ---");
        
        // Данные пользователя
        String username = "admin";
        String email = "admin@example.com";
        String sessionId = "abc123xyz789";
        String ipAddress = "192.168.1.100";
        
        // ✅ Логируем только необходимую информацию
        System.out.printf("[INFO] %tc - Пользователь '%s' вошел в систему%n", 
                         new java.util.Date(), username);
        
        // ✅ Маскируем чувствительные данные
        String maskedEmail = maskEmail(email);
        String maskedSessionId = sessionId.substring(0, 4) + "****";
        
        System.out.printf("[DEBUG] %tc - Session: %s, Email: %s, IP: %s%n",
                         new java.util.Date(), maskedSessionId, maskedEmail, ipAddress);
    }
    
    private static String maskEmail(String email) {
        if (email == null || !email.contains("@")) {
            return "***";
        }
        
        String[] parts = email.split("@");
        String localPart = parts[0];
        String domain = parts[1];
        
        if (localPart.length() <= 2) {
            return "***@" + domain;
        }
        
        return localPart.charAt(0) + "*".repeat(localPart.length() - 2) + 
               localPart.charAt(localPart.length() - 1) + "@" + domain;
    }
    
    private static void debuggingAndLogging() {
        System.out.println("\\n=== Отладка и логирование ===");
        
        // Создание структурированных логов
        LogFormatter logFormatter = new LogFormatter();
        
        // Различные уровни логирования
        logFormatter.debug("Начало обработки заказа", "orderId", "12345");
        logFormatter.info("Заказ обработан успешно", "orderId", "12345", "amount", 299.99);
        logFormatter.warn("Низкий остаток товара", "productId", "P001", "quantity", 5);
        logFormatter.error("Ошибка платежа", "orderId", "12345", "error", "Card declined");
        
        logFormatter.close();
    }
    
    static class LogFormatter implements AutoCloseable {
        private final java.util.Formatter formatter;
        private final java.io.StringWriter writer;
        
        public LogFormatter() {
            this.writer = new java.io.StringWriter();
            this.formatter = new java.util.Formatter(writer);
        }
        
        public void log(String level, String message, Object... params) {
            writer.getBuffer().setLength(0);
            
            formatter.format("[%s] %tc - %s", level, new java.util.Date(), message);
            
            if (params.length > 0) {
                formatter.format(" |");
                for (int i = 0; i < params.length; i += 2) {
                    if (i + 1 < params.length) {
                        formatter.format(" %s=%s", params[i], params[i + 1]);
                    }
                }
            }
            
            System.out.println(writer.toString());
        }
        
        public void debug(String message, Object... params) { log("DEBUG", message, params); }
        public void info(String message, Object... params) { log("INFO", message, params); }
        public void warn(String message, Object... params) { log("WARN", message, params); }
        public void error(String message, Object... params) { log("ERROR", message, params); }
        
        @Override
        public void close() {
            formatter.close();
        }
    }
    
    public static void main(String[] args) {
        demonstrateBestPractices();
    }
}
```

---

