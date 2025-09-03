# Regex в Java — шпаргалка

> Библиотека: java.util.regex — Pattern (компилирует шаблон), Matcher (выполняет поиск/совпадения), PatternSyntaxException (ошибки синтаксиса).

# [[Regular Expressions|Синтаксис Regexp]]


## Meta

- Библиотека: java.util.regex — Pattern (компилирует шаблон), Matcher (выполняет поиск), PatternSyntaxException (синтаксические ошибки).
- Рекомендации по оформлению в Obsidian: используем Markdown, callouts вида > [!info], блок‑коды с тройными бэктиками, сворачиваемые callouts при необходимости.

> [!info] Когда использовать что
> - Pattern.compile(...) для переиспользуемых шаблонов и производительности, особенно в циклах и сервисах.
> - String.matches/replaceAll/split — для простых одноразовых случаев, но без кэширования regex.
> - Callouts в Obsidian помогают быстро выделять сноски, предупреждения и лучшие практики.

## Быстрый старт

```java
import java.util.regex.*;

Pattern pattern = Pattern.compile("\\b\\w+@\\w+\\.\\w+\\b"); // упрощённый email
Matcher matcher = pattern.matcher("mail: a@b.io, bad@, x@y.z");

while (matcher.find()) {
    System.out.println(matcher.group()); // печатает найденные email’ы
}
```

- Pattern.compile(String regex) — компиляция шаблона в Pattern.
- pattern.matcher(CharSequence) — создание Matcher поверх входной строки.
- matcher.find()/group() — итеративный поиск и доступ к текущему совпадению.


## Ключевые методы

- Проверка целой строки:

```java
boolean ok = Pattern.matches("\\d{4}-\\d{2}-\\d{2}", "2025-09-02"); // true
```

- Поиск подстрок:

```java
Matcher m = Pattern.compile("\\d+").matcher("id=42 code=7");
while (m.find()) System.out.println(m.group()); // 42, 7
```

- Замены:

```java
String src = "foo bar baz";
String out = src.replaceAll("\\bba", "BA"); // "foo BAr BAz"
```

- Первая замена:

```java
String out = src.replaceFirst("\\s+", " "); // сжать первый пробельный блок
```

- Разбиение:

```java
String[] parts = "a,b; c".split("[,;]\\s*"); // ["a","b","c"]
```


## Флаги компиляции

```java
Pattern p = Pattern.compile("price:\\s*(\\d+)", Pattern.CASE_INSENSITIVE | Pattern.MULTILINE);
```

- CASE_INSENSITIVE — регистронезависимый матчинг.
- MULTILINE — якоря ^ и \$\$\$ работают построчно.
- DOTALL — точка матчит перевод строки.
- UNICODE_CASE — регистронезависимость с учётом Unicode.
- COMMENTS — пробелы и комментарии в шаблоне игнорируются, удобно для многострочных regex.

Пример с COMMENTS:

```java
String re = "(?x)        # включаем режим комментариев\n" +
            "^           # начало строки\n" +
            "(\\+7|8)?   # код страны, опционально\n" +
            "\\s*\\(?\\d{3}\\)?\\s*  # код\n" +
            "[\\d\\s-]{7,10}        # номер\n" +
            "$";
Pattern p = Pattern.compile(re);
```


## Группы и ссылки

- Захватывающие группы: (...) — доступ через m.group(1), m.group(2).
- Незахватывающие: (?:...) — структурируют без нумерации.
- В строках замены используются \$1, \$2... — для подстановки групп.

Примеры:

```java
// "Фамилия Имя" -> "Имя Фамилия"
String out = "Иванов Иван".replaceAll("^(\\p{L}+)\\s+(\\p{L}+)$", "$2 $1");

// Извлечение домена
Matcher m = Pattern.compile("https?://(+)").matcher("https://example.com/x");
if (m.find()) System.out.println(m.group(1)); // example.com
```


## Жадность и нежадность

- Жадные квантификаторы: *, +, {n,m} — стремятся расширить матч.
- Нежадные: *?, +?, {n,m}? — берут минимум, полезно для разметок.

```java
String s = "<a>1</a><a>2</a>";
System.out.println(s.replaceAll("<a>.*</a>", "#"));   // "#"
System.out.println(s.replaceAll("<a>.*?</a>", "#")); // "##"
```


## Границы слова и якоря

- ^ — начало строки, \$\$\$ — конец строки.
- \b — граница слова, \B — не‑граница.

```java
"котик кот котёл".replaceAll("\\bкот\\b", "CAT"); // "котик CAT котёл"
```

С MULTILINE:

```java
Pattern.compile("^ERROR: .*", Pattern.MULTILINE)
       .matcher("ok\nERROR: fail\nok")
       .replaceAll("[masked]"); // маскирует строки ошибок
```


## Unicode и классы символов

- \p{L} — буква (все алфавиты), \p{N} — цифра, \p{Zs} — пробельные разделители.
- Пример для слов длиной ≥3 в любых алфавитах:

```java
Pattern p = Pattern.compile("\\b\\p{L}{3,}\\b", Pattern.UNICODE_CASE);
```


## Типичные паттерны

- Дата ISO:

```regex
\d{4}-\d{2}-\d{2}
```

- Упрощённый email:

```regex
[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}
```

- Российский телефон (приближённо):

```regex
^((8|\+7)[\- ]?)?(\(?\d{3}\)?[\- ]?)?[\d\- ]{7,10}$
```

- UUID v4 (приближённый):

```regex
\b[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-4[0-9a-fA-F]{3}-[89abAB][0-9a-fA-F]{3}-[0-9a-fA-F]{12}\b
```


## Производительность

- Компилировать и переиспользовать Pattern; разделять логику паттерна и матчера, избегая повторной компиляции.
- Избегать катастрофического бэктрекинга: не использовать (.+)+ и родственные конструкции без ограничений.
- Сужать контекст поиска — якоря, точные диапазоны, жёсткие классы символов.

```java
// Хорошо — кэширование шаблона
private static final Pattern PRICE = Pattern.compile("\\bprice\\s*:\\s*(\\d+)\\b", Pattern.CASE_INSENSITIVE);

Matcher m = PRICE.matcher(text);
while (m.find()) {
    // обработка
}
```

> [!tip]+ Быстро проверять «горячие» regex
> - На стенде/в тестах используйте заранее скомпилированные шаблоны и фиксируйте SLA матчинга на выборке длинных строк.
> - Если нужен DOTALL, включайте ровно там, где требуется; лишний охват повышает стоимость матча.

## Отладка и тестирование

- Собирать шаблон итеративно; включать COMMENTS или (?x) и давать пояснения в многострочных паттернах.
- Писать unit‑тесты на крайние случаи: пустые строки, большие входы, Unicode, суррогатные пары.

> [!example]+ Приём: «минимальный репрод»
> - Выделить минимальную строку, где regex ведёт себя неверно, и уменьшать паттерн до участка, вызывающего бэктрекинг.
> - Фиксировать флаги и окружение, чтобы избежать «невоспроизводимости» из‑за различий в режимах.

## Частые ошибки

- Экранирование: в Java‑строках бэкслеш удваивается, иначе \d превратится в d, \b — в backspace.
- Различать matches() и find(): первое — о полной строке, второе — о подстроках.
- Точка без DOTALL не матчит перевод строки; включать DOTALL осознанно.
- В replaceAll/replaceFirst подстановки только через \$1, \$2..., а не \1.


## Мини‑шаблоны

- Ключ=значение:

```java
Pattern p = Pattern.compile("\\b(\\w+)=([^\\s;]+)");
Matcher m = p.matcher("a=1; b=hello c=42");
while (m.find()) {
    System.out.println(m.group(1) + " -> " + m.group(2));
}
```

- Сжать множественные пробелы:

```java
text = text.replaceAll("\\s{2,}", " ").trim();
```

- Удалить простые HTML‑теги:

```java
text = text.replaceAll("<+>", "");
```


## Памятка по экранированию (Java‑строки)

- Внутри строк "..." бэкслеш — служебный символ Java, поэтому слеши удваиваются.
- Примеры:
    - regex: \d  → строка: "\\d"
    - regex: \b  → строка: "\\b"
    - regex: \s+ → строка: "\\s+"
    - regex: (\\d+) как буквальный «\» перед \d: "(\\\\d+)"



## Дополнительно: lesser‑known методы Matcher

- lookingAt() — проверка префиксного совпадения от начала входа, полезно для парсеров.
- region(start,end) — ограничение области матчинга без нарезки строки.
- usePattern(newPattern) — переиспользование Matcher с новым Pattern при сохранении входа.

```java
Matcher m = Pattern.compile("\\d+").matcher("123abc");
System.out.println(m.lookingAt()); // true
m.region(3, 6);
System.out.println(m.find()); // на "abc" — false
```


## Рекомендации по структуре заметок

- Держать «Типичные паттерны» как накапливаемый раздел под конкретные домены: артикулы, форматы логов, внутр. ID.
- Вставлять рядом минимальные примеры входа/выхода и «красные флаги» производительности в callout‑блоках.

> [!warning] Красные флаги regex
> - Нелокализованные .*, особенно перед жадными группами; добавьте якоря/границы/ограничители.
> - Вложенные квантификаторы без ограничений: применяйте нежадные варианты или точечные классы.

———

Ссылки на оформление и API: Obsidian syntax и callouts, базовый Markdown и сворачиваемые callouts; Java Regex API и приёмы компиляции/флагов/Matcher.
<span style="display:none">[^10][^11]</span>

<div style="text-align: center">⁂</div>

: https://help.obsidian.md/callouts

: https://help.obsidian.md/syntax

: https://www.baeldung.com/regular-expressions-java

: https://ryan.himmelwright.net/post/obsidian-callouts/

: https://www.browserstack.com/guide/pattern-compile-java

: https://nicolevanderhoeven.com/blog/20220330-new-in-obsidian-obsidian-callouts/

: sdelai-zametku-pro-rabotu-s-regex-v-java.md

: https://www.reddit.com/r/ObsidianMD/comments/1jdxcwq/pro_tip_use_callouts/

: https://forum.obsidian.md/t/best-way-to-create-expandable-big-blocks-of-code/51514

[^10]: https://github.com/glushchenko/fsnotes/issues/1720

[^11]: https://www.youtube.com/watch?v=TqYQ0kA1yAo

