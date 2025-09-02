# Java Arrays — памятка

> [!info] Что внутри
> Объявление/инициализация, ключевые методы java.util.Arrays, частые паттерны, примитивы vs объекты, подводные камни, Big‑O и полезные сниппеты в формате «скопировать и вставить».

## Быстрый старт

- Объявление и литералы массивов в Java выполняются через тип с квадратными скобками и могут быть сразу инициализированы литералом new или фигурными скобками.

```java
int[] a; 
String[] names; 
var arr = new int[]{1,2,3};
```

- Создание и инициализация задают размер и начальные значения: примитивы обнуляются, ссылки становятся null.

```java
int[] a = new int[^13];        // [0,0,0]
int[] b = {1,2,3};           // литерал массива
String[] s = new String[^14];  // [null,null]
```

- Длина и индексация работают через свойство length и нулевую базу индексации.

```java
int n = a.length;
int x = b;
b = 42;
```

- Многомерные массивы бывают прямоугольные и «рваные» (jagged), где внутренние размеры задаются отдельно.

```java
int[][] m = new int[^14][^13];    // прямоугольный
int[][] jag = new int[^14][];   // «рваный»
jag = new int[]{1,2};
jag = new int[]{3,4,5};
```


## Arrays (java.util.Arrays)

- Класс Arrays содержит статические утилиты для вывода, сравнения, копирования, сортировки, поиска, заполнения и потоковой обработки массивов.
- toString / deepToString печатают одномерные и вложенные структуры соответственно.

```java
int[] a = {1,2,3};
System.out.println(Arrays.toString(a));         // [1, 2, 3]
Object[] nest = { new int[]{1,2}, new int[]{3,4} };
System.out.println(Arrays.deepToString(nest));  // [[1, 2], [3, 4]]
```

- equals / deepEquals сравнивают содержимое одномерных и вложенных массивов.

```java
int[] x = {1,2}; int[] y = {1,2};
boolean eq = Arrays.equals(x, y);               // true
Object[] o1 = { new int[]{1,2} };
Object[] o2 = { new int[]{1,2} };
boolean de = Arrays.deepEquals(o1, o2);         // true
```

- copyOf / copyOfRange копируют массивы с изменением длины или по полуинтервалу [from, to).

```java
int[] a = {1,2,3};
int[] c1 = Arrays.copyOf(a, 5);        // [1,2,3,0,0]
int[] c2 = Arrays.copyOfRange(a, 1, 3); // [2,3]
```

- fill заполняет весь массив или диапазон указанным значением.

```java
int[] a = new int;
Arrays.fill(a, 9);               // [9,9,9,9,9]
Arrays.fill(a, 1, 3, 7);         // [9,7,7,9,9]
```

- sort сортирует по естественному порядку, перегрузка с компаратором — только для ссылочных типов.

```java
int[] a = {5,1,4,2};
Arrays.sort(a);                   // [1,2,4,5]
String[] s = {"b","aa","c"};
Arrays.sort(s);                   // ["aa","b","c"]
Arrays.sort(s, Comparator.comparingInt(String::length).reversed()); // ["b","c","aa"]
```

- parallelSort применяет параллельную сортировку для больших массивов.

```java
int[] big = {...};
Arrays.parallelSort(big);
```

- binarySearch работает на отсортированных массивах и возвращает индекс или –insertionPoint–1.

```java
int[] a = {1,3,5,7};
int i = Arrays.binarySearch(a, 5);   // 2
int j = Arrays.binarySearch(a, 4);   // -3
```

- mismatch возвращает первую позицию различия или –1 при полном совпадении (Java 9+).

```java
int[] a = {1,2,3};
int[] b = {1,4,3};
int k = Arrays.mismatch(a, b);       // 1
```

- setAll / parallelSetAll наполняют массив выражением от индекса.

```java
int[] a = new int;
Arrays.setAll(a, i -> i*i);          // [0,1,4,9,16]
Arrays.parallelSetAll(a, i -> i+1);  // [1,2,3,4,5]
```

- stream для примитивов доступен через специализированные стримы Arrays.stream(int[]/long[]/double[]).

```java
int[] a = {1,2,3};
int sum = Arrays.stream(a).sum();    
long cnt = Arrays.stream(a).filter(x -> x%2==1).count();
```

- asList создаёт фиксированный список‑вид на массив; модификация структуры запрещена.

```java
String[] arr = {"a","b","c"};
List<String> list = Arrays.asList(arr); // изменение arr отражается в list
```

- compare/compareUnsigned и hashCode/deepHashCode доступны для лексикографического сравнения и хешей.

```java
int r = Arrays.compare(new int[]{1,2}, new int[]{1,3}); // < 0
int h = Arrays.hashCode(new int[]{1,2,3});
int dh = Arrays.deepHashCode(new Object[]{ new int[]{1,2} });
```


## Частые паттерны

- Печать и диагностика массивов удобнее через Arrays.toString/deepToString вместо Arrays.toString на примитивных вложенных напрямую.

```java
System.out.println(Arrays.toString(a));
System.out.println(Arrays.deepToString(matrix));
```

- Клонирование одномерного массива создаёт поверхностную копию.

```java
int[] b = a.clone();
```

- Расширение массива выполняется через copyOf с новой длиной.

```java
a = Arrays.copyOf(a, a.length * 2);
```

- Срез берётся через copyOfRange c полуинтервалом [from, to).

```java
int[] sub = Arrays.copyOfRange(a, from, to);
```

- Равенство содержимого проверяется Arrays.equals/ deepEquals.

```java
boolean ok = Arrays.equals(a, b);
```

- Поиск элемента на отсортированном массиве выполняется binarySearch.

```java
Arrays.sort(a);
int idx = Arrays.binarySearch(a, key);
```


## Примитивы vs объекты

- Примитивные массивы (int[], long[], double[]) компактнее и быстрее, но сортируются только по естественному порядку без компараторов.
- Массивы ссылок (String[], Integer[]) поддерживают компараторы, но имеют накладные расходы на объекты и автоупаковку/распаковку.


## Подводные камни

> [!warning] Важные нюансы
> - Arrays.asList создаёт список фиксированного размера; для модификаций оборачивайте в new ArrayList<>(...).
> - binarySearch требует сортировки тем же порядком, что и поиск; иначе результат неопределён.
> - deepToString / deepEquals работают корректно для вложенных структур через Object[]; не путать с прямым toString на примитивном массиве.

## Полезные сниппеты

- Обратный порядок для Integer[] с готовым компаратором.

```java
Integer[] a = {3,1,2};
Arrays.sort(a, Comparator.reverseOrder());
```

- Сортировка по составному ключу через Comparator.comparing + thenComparing.

```java
record Person(String name, int age){}

Person[] ps = {
  new Person("Ann", 30),
  new Person("Bob", 25),
  new Person("Bob", 20)
};

Arrays.sort(ps, Comparator
    .comparing(Person::name)
    .thenComparingInt(Person::age));
```

- Быстрое заполнение с вычислением от индекса с setAll.

```java
int[] a = new int[^10];
Arrays.setAll(a, i -> i + 1); // 1..10
```

- Слияние двух отсортированных массивов двухуказательным проходом.

```java
int[] a = {1,3,5};
int[] b = {2,4,6};
int[] c = new int[a.length + b.length];
int i=0,j=0,k=0;
while (i<a.length && j<b.length) {
  c[k++] = (a[i] <= b[j]) ? a[i++] : b[j++];
}
while (i<a.length) c[k++] = a[i++];
while (j<b.length) c[k++] = b[j++];
```


## Шпаргалка по Big‑O

- Доступ по индексу: O(1) для чтения/записи в массив.
- Arrays.sort: примитивы используют Dual‑Pivot Quicksort, амортизированно O(n log n).
- binarySearch: O(log n) на отсортированном массиве.
- fill/copyOf: линейные по длине массива O(n).


## Формат Obsidian

> [!tip]+ Как использовать в заметках
> - Заголовки \#, списки -, блок‑коды ```java и подсветка кода поддерживаются из коробки. 
> - Callouts вида > [!note]/[!warning]/[!tip] и сворачиваемые варианты с +/‑ улучшают читабельность. [^10]
> - Таблицы и чеклисты доступны в Advanced/Flavored Markdown Obsidian. 

Готово: вставить в Obsidian, расширять разделы «Полезные сниппеты» и «Подводные камни» под проектные кейсы (парсинг логов, большие сортировки, бинарный поиск с собственным порядком). [^11]
<span style="display:none">[^12]</span>

<div style="text-align: center">⁂</div>

: https://publish.obsidian.md/help-ru/Руководства/Форматирование+заметок

: https://help.obsidian.md/obsidian-flavored-markdown

: https://ryan.himmelwright.net/post/obsidian-callouts/

: https://help.obsidian.md/advanced-syntax

: https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html

: https://beginnersbook.com/2024/06/java-arrays-methods/

: Arrays.md

: https://help.obsidian.md/syntax

: https://www.reddit.com/r/ObsidianMD/comments/1btytnh/obsidian_text_showing_md_syntax_not_formatted_new/

[^10]: https://gist.github.com/saharshbhansali/5da604f1731c7d5ea07b2bd91552d48c

[^11]: https://github.com/milk717/obsidian-callouts-markdown

[^12]: https://www.youtube.com/watch?v=d8fXEhWy_rY

