# String в Java — памятка

> [!info] Ключевая идея
> String — неизменяемая последовательность символов; все операции создают новые объекты, поэтому для частых изменений предпочтительнее StringBuilder/StringBuffer.

## Создание строк

```java
String s1 = "Hello";                  // строковый литерал
String s2 = new String("World");      // конструктор (обычно не нужен)
char[] chars = {'J','a','v','a'};
String s3 = new String(chars);        // из массива символов
```

- Литералы помещаются в пул строк (String Pool); одинаковые литералы разделяют экземпляр.
- Интернирование через intern() возвращает канонический экземпляр из пула.


## Основные методы

- length(), charAt(i), substring(begin,end) — базовые операции доступа/среза.
- equals/equalsIgnoreCase — сравнение с учётом/без учёта регистра.
- contains, startsWith, endsWith, indexOf/lastIndexOf — поиск подстрок/префиксов/суффиксов.
- toLowerCase/toUpperCase, trim/strip (Java 11+), replace/replaceAll — нормализация и замены.
- split(regex), concat, isEmpty, isBlank (Java 11+).

```java
String s = " Hello World! ";
System.out.println(s.length());             // 13
System.out.println(s.trim());               // "Hello World!"
System.out.println(s.toUpperCase());        // " HELLO WORLD! "
System.out.println(s.substring(1, 6));      // "Hello"
System.out.println(s.contains("World"));    // true
System.out.println(s.replace('l','x'));     // " Hexxo Worxd! "
```


## Текстовые блоки (Java 15+)

- Многострочные строки с тройными кавычками """ ... """; сохраняют переносы, убирают общий отступ (stripIndent).

```java
String html = """
  <html>
    <body>
      <p>Hello</p>
    </body>
  </html>
  """;
```

- Удобно для HTML/JSON/SQL; можно использовать .formatted(...) или String.format для подстановок.

```java
String json = """
  { "id": 1, "name": "%s" }
  """.formatted(name);
```


## Unicode и кодпоинты

- Некоторые символы занимают два char (суррогатная пара); итерируйтесь по кодпоинтам через codePointAt и Character.charCount.

```java
for (int i = 0; i < s.length(); ) {
  int cp = s.codePointAt(i);
  // обработка cp
  i += Character.charCount(cp);
}
```

- Для подсчёта реальных символов используйте codePointCount(begin,end).


## Частые паттерны

- Безопасная вырезка подстроки:

```java
int from = Math.max(0, start);
int to = Math.min(s.length(), end);
String sub = s.substring(from, to);
```

- Очистка пробелов и нормализация:

```java
String norm = s.strip().replaceAll("\\s+", " ");
```

- Регистронезависимый префикс/суффикс (фиксированная локаль):

```java
boolean has = s.toLowerCase(Locale.ROOT).startsWith(prefix.toLowerCase(Locale.ROOT));
```

- Лимит split и trim полей:

```java
String[] cols = line.split("\\|", -1);
for (int i = 0; i < cols.length; i++) cols[i] = cols[i].trim();
```


## Производительность

> [!tip] Избегайте лишних аллокаций
> - В циклах для конкатенаций используйте StringBuilder; одно выражение a + b компилятор сам превращает в StringBuilder, но не между итерациями.
> - Кэшируйте повторно используемые результаты (например, заранее собранные фрагменты), избегайте лишних split/replaceAll на горячих путях.

## Что важно про equals/сравнение

- equals сравнивает содержимое; оператор == сравнивает ссылки (одинаков ли объект/литерал в пуле).
- compareTo/compareToIgnoreCase — лексикографический порядок; учитывайте локали при пользовательском сравнении через Collator.


## Полезные API рядом

- StringBuilder/StringBuffer — изменяемые буферы для сборки строк.
- StringJoiner и Collectors.joining для сборки из коллекций/стримов.

```java
String res = list.stream()
  .filter(t -> !t.isBlank())
  .collect(java.util.stream.Collectors.joining(", "));
```


---

> String — основной инструмент работы с текстом в Java; владение методами и понимание неизменяемости критичны для производительности и корректности в проектах. 
<span style="display:none"></span>

<div style="text-align: center">⁂</div>

: https://publish.obsidian.md/help-ru/Руководства/Форматирование+заметок

: https://help.obsidian.md/syntax

: https://docs.oracle.com/en/java/javase/15/text-blocks/index.html

: https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html

: https://stackoverflow.com/questions/61432347/how-to-have-placeholder-for-variable-value-in-java-text-block

: String.md

: https://www.reddit.com/r/ObsidianMD/comments/17m82xc/how_to_format_code_snippets_on_obsidian/?tl=ru

: https://help.obsidian.md/advanced-syntax

: https://forum.obsidian.md/t/convert-markdown-text-styles-to-html-tags-with-regex/104297

: https://ryan.himmelwright.net/post/obsidian-callouts/

: https://www.reddit.com/r/ObsidianMD/comments/1ii9rxn/any_tips_tutorials_for_an_obsidian_newbie/?tl=ru

: https://pdworkman.com/obsidian-callouts/
