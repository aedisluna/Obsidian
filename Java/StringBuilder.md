# StringBuilder — памятка

> [!info] Когда использовать
> - Частые конкатенации в циклах или поэтапная сборка строк — использовать StringBuilder для минимизации аллокаций и копирований.
> - StringBuilder изменяемый и не синхронизирован, быстрее в одном потоке; для многопоточности — StringBuffer или внешняя синхронизация.

## Конструкторы

```java
var sb = new StringBuilder(32);   // стартовая емкость 32 символа (capacity), длина 0
sb.append(' ')                    // append(char) — добавит пробел в конец и вернёт тот же sb
  .append(42)                     // append(int) — число конвертируется в текст
  .append((Object) null)          // append(Object) — String.valueOf(obj) => "null" для null
  .append(true)                   // append(boolean) — "true"/"false"
  .append(3.14)                   // append(double) — "3.14" (без локализации)
  .append(" OK");                 // append(String) — добавит строку как есть
String result = sb.toString();    // финализируем в неизменяемый String
```

- Все append возвращают тот же объект (удобно чейнить); insert — вставляет в указанную позицию.
- Перегрузки append есть под char, int, long, float, double, boolean, char[], CharSequence, String, Object и диапазоны подстрок; для Object используется String.valueOf(obj) (без NPE для null).


## Capacity и рост буфера

> [!tip] Планируй ёмкость
> capacity — размер внутреннего массива символов; пока length ≤ capacity, реаллокаций и копирования нет. При переполнении буфер расширяется и копируется в новый массив.

- Начальная ёмкость:
    - new StringBuilder() — 16 символов.
    - new StringBuilder(String s) — s.length() + 16.
    - new StringBuilder(int cap) — ровно заданная емкость.
- Рост ёмкости: типично до newCap = oldCap*2 + 2 (правило может отличаться между версиями JDK). Лучше заранее оценивать размер и задавать запас через конструктор или ensureCapacity.

```java
var sb = new StringBuilder();
sb.ensureCapacity(200);    // гарантировать минимум 200; если текущая < 200 — увеличит
int cap = sb.capacity();   // текущая емкость
sb.setLength(0);           // очистить содержимое, емкость сохраняется
```

- ensureCapacity увеличит до max(minimumCapacity, old*2+2); если minimum ≤ текущей — ничего не делает.


## Практический совет

- В одном выражении компилятор сам использует StringBuilder для "a" + b + c, но не «сквозь» итерации; при наращивании строки в цикле держать один общий StringBuilder и по возможности задать capacity с запасом.


## Базовые операции

```java
var sb = new StringBuilder();
sb.append("Hello");         // -> "Hello"
sb.append(' ').append(42);  // перегрузки под примитивы и Object (toString)
String s = sb.toString();   // финализировать в String
int n = sb.length();        // текущая длина
int cap = sb.capacity();    // текущая ёмкость буфера
sb.ensureCapacity(200);     // гарантировать ёмкость без лишних реаллокаций
sb.setLength(3);            // обрезать/дополнить \u0000
```

- setLength может как обрезать, так и «дополнить» нулевыми символами \u0000 при увеличении длины.


## Вставка, замены, удаление

```java
sb.insert(0, ">> ");        // вставка в позицию
sb.replace(0, 2, "**");     // заменить подстроку [start, end)
sb.delete(5, 10);           // удалить диапазон [start, end)
sb.deleteCharAt(0);         // удалить символ по индексу
sb.reverse();               // развернуть содержимое
```

- Индексы полузакрытые [start, end); deleteCharAt удаляет ровно один символ.


## Доступ по индексам

```java
char ch = sb.charAt(1);              // чтение
sb.setCharAt(1, 'i');                // запись
CharSequence sub = sb.subSequence(2, 5); // «вид» подстроки
String subStr = sb.substring(2, 5);      // новая строка
```

- subSequence возвращает «вид» (CharSequence), изменяемый при мутациях буфера; substring — отдельный String.


## Поиск

```java
int i1 = sb.indexOf("foo");         // с начала
int i2 = sb.indexOf("foo", from);   // с позиции
int j1 = sb.lastIndexOf("bar");     // с конца
int j2 = sb.lastIndexOf("bar", fromIndexBack);
```

- Методы работают с подстроками, второй параметр задаёт смещение поиска.


## Частые паттерны

- Конкатенация в цикле:

```java
var sb = new StringBuilder(items.size() * 8);
for (String it : items) sb.append(it).append(',');
if (sb.length() > 0) sb.setLength(sb.length() - 1);
return sb.toString();
```

- CSV с экранированием кавычек:

```java
static void appendCsvCell(StringBuilder sb, String cell) {
  sb.append('"');
  for (int k = 0; k < cell.length(); k++) {
    char c = cell.charAt(k);
    if (c == '"') sb.append('"'); // удвоить
    sb.append(c);
  }
  sb.append('"');
}
```

- Префикс и суффикс батчево:

```java
var sb = new StringBuilder("content");
sb.insert(0, "[");
sb.append("]");
```

- Быстрое обрезание до последнего разделителя:

```java
int p = sb.lastIndexOf(", ");
if (p >= 0) sb.setLength(p);
```

- Генерация JSON:

```java
var sb = new StringBuilder(256);
sb.append("{\"id\":").append(id).append(",\"name\":\"")
  .append(name.replace("\"","\\\"")).append("\"}");
```


## Производительность и память

> [!tip] Эффективность
> - new StringBuilder(estimatedChars) снижает количество расширений и копирований буфера.
> - Повторные + в одном выражении компилятор соберёт в StringBuilder, но в циклах — нет; явно используйте sb.
> - setLength не освобождает память; чтобы уменьшить footprint, создайте новый StringBuilder при необходимости.

## Потокобезопасность

- StringBuilder не потокобезопасен; общий доступ из нескольких потоков требует внешней синхронизации или StringBuffer.


## Примеры задач

- join без Stream API:

```java
static String join(Iterable<String> src, String sep) {
  var sb = new StringBuilder();
  var first = true;
  for (String s : src) {
    if (!first) sb.append(sep);
    sb.append(s);
    first = false;
  }
  return sb.toString();
}
```

- Инверсия строки по кодпоинтам (корректно для суррогатов):

```java
static String reverseByCodePoints(String s) {
  var sb = new StringBuilder(s.length());
  for (int i = s.length(); i > 0; ) {
    int cp = s.codePointBefore(i);
    i -= Character.charCount(cp);
    sb.appendCodePoint(cp);
  }
  return sb.toString();
}
```

- Построение табличного вывода:

```java
static String tableRow(String a, int b, double c) {
  var sb = new StringBuilder(64);
  return sb.append(String.format("%-12s", a))
           .append(String.format("%8d", b))
           .append(String.format("%12.2f", c))
           .toString();
}
```


## Антипаттерны и подводные камни

> [!warning] Обрати внимание
> - Не вызывайте toString() на каждом шаге — делайте один раз в конце.
> - Не смешивайте активную мутацию и «вьюхи»: после subSequence дальнейшие insert/delete изменяют индексацию ожиданий.
> - substring возвращает String, subSequence — «вид»; для долгоживущих фрагментов лучше сразу substring.
> - Вставки в начало insert(0, ...) — O(n); при множественных prepend рассмотрите Deque + join или сборку в обратном порядке и reverse().

## Мини‑чеклист

- Частые изменения? StringBuilder.
- Известный размер? capacity заранее.
- Нужна потокобезопасность? StringBuffer или синхронизация.
- Финализируй строку одним toString() в конце.
- Осторожно с индексами после модификаций.

<div style="text-align: center">⁂</div>

: https://docs.oracle.com/javase/8/docs/api/java/lang/StringBuilder.html

: https://help.obsidian.md/callouts

: https://help.obsidian.md/syntax

: https://stackoverflow.com/questions/45094521/java-stringbuilderstringbuffers-ensurecapacity-why-is-it-doubled-and-incre

: https://www.geeksforgeeks.org/java/stringbuilder-ensurecapacity-in-java-with-examples/

: https://www.tutorialspoint.com/java/lang/stringbuilder_ensurecapacity.htm

: StringBuilder.md

: https://docs.oracle.com/cd/E24001_01/apirefs.1111/e17503/oracle/adfnmc/java/lang/StringBuilder.html

: https://www.youtube.com/watch?v=TqYQ0kA1yAo

: https://javatutorialhq.com/java/lang/stringbuilder-class-tutorial/ensurecapacity-method-example/

: https://stackoverflow.com/questions/3184244/stringbuilder-capacity

: https://forum.obsidian.md/t/obsidian-callouts/36600

: https://www.obsidianstats.com/posts/2025-06-13-callout-plugins

