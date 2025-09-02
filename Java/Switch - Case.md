
# Java switch/case — памятка

## Быстрый старт

- Базовый оператор switch:

```java
int day = 3;
String name;
switch (day) {
  case 1: name = "Mon"; break;
  case 2: name = "Tue"; break;
  case 3: name = "Wed"; break;
  default: name = "Unknown";
}
System.out.println(name);
```

- Современное switch-выражение (Java 14+):

```java
int day = 3;
String name = switch (day) {
  case 1 -> "Mon";
  case 2 -> "Tue";
  case 3 -> "Wed";
  default -> "Unknown";
};
System.out.println(name);
```

- Несколько меток в одном case:

```java
String type = switch (day) {
  case 1, 7 -> "Weekend-ish?";
  case 2, 3, 4, 5, 6 -> "Workday";
  default -> "N/A";
};
```


## Типы и правила

- Разрешённые типы выражения в switch: `byte`, `short`, `char`, `int`, их обёртки, `enum`, `String` (с Java 7), а также `var` если выводится к одному из этих типов.
- Сравнение идёт по равенству; не поддерживаются `>`, `<`, `>=`, `<=`.
- Метки `case` — константные выражения совместимого типа; для `enum` — имена констант без квалификатора.


## Оператор vs выражение

- Оператор switch не возвращает значение сам по себе, обычно требует `break` для предотвращения fall-through:

```java
switch (n) {
  case 1:
    log("one");
    break; // иначе «провалимся» дальше
  case 2:
    log("two");
    break;
  default:
    log("other");
}
```

- Switch-выражение возвращает значение и не использует fall-through; вместо `break` применяются:
    - Стрелки `->` с единственным результатом или блоком.
    - `yield` внутри блока для возврата значения.


## Стрелочный синтаксис (рекомендуется)

- Короткая форма:

```java
String gradeDesc = switch (grade) {
  case 'A' -> "Excellent";
  case 'B' -> "Good";
  case 'C' -> "Average";
  case 'D', 'E' -> "Below average";
  default -> "Fail";
};
```

- Блоки с побочными эффектами:

```java
String msg = switch (status) {
  case "NEW" -> {
    audit("new");
    yield "ToDo";
  }
  case "DONE" -> "Closed";
  default -> {
    warn("unknown: " + status);
    yield "Unknown";
  }
};
```


## Fall-through и как им управлять

- Классический двоеточечный стиль допускает «проваливание» без `break`:

```java
switch (x) {
  case 1:
    log("one");
    // без break — пойдём дальше
  case 2:
    log("two");
    break;
  default:
    log("other");
}
```

- В стрелочном стиле fall-through отсутствует; метки можно перечислять через запятую для объединения:

```java
case 1, 2, 3 -> "Group";
```


## Strings и enum

- Строки в switch:

```java
String fruit = "Apple";
String color = switch (fruit) {
  case "Apple" -> "Red";
  case "Banana" -> "Yellow";
  case "Orange" -> "Orange";
  default -> "Unknown";
};
```

- Перечисления:

```java
enum State { NEW, PROCESSING, DONE, FAILED }

String action = switch (State.PROCESSING) {
  case NEW -> "start";
  case PROCESSING -> "wait";
  case DONE -> "archive";
  case FAILED -> "retry";
};
```


## yield внутри switch-выражений

- Используй `yield` для возврата значения из блока:

```java
int score = 77;
String level = switch (score / 10) {
  case 10, 9 -> "A";
  case 8 -> "B";
  case 7 -> {
    log("borderline");
    yield "C";
  }
  case 6 -> "D";
  default -> "F";
};
```


## Паттерн-матчинг (вводная)

- В новых версиях Java развивается pattern matching для `switch` (по типам с `sealed`/`record` и `null`-обработкой). Для повседневной практики запомни базовый принцип: можно матчить по типу и распаковывать компоненты, использовать `when`-гварды. Проверяй версию JDK проекта в IntelliJ IDEA и флаг превью-фич.

Пример (включается как превью в соответствующей версии JDK):

```java
static String formatShape(Object s) {
  return switch (s) {
    case Circle c -> "Circle r=" + c.radius();
    case Rectangle r when r.w() == r.h() -> "Square " + r.w();
    case Rectangle r -> "Rect " + r.w() + "x" + r.h();
    case null -> "null";
    default -> "Unknown";
  };
}
```


## Частые паттерны

- Группировка значений:

```java
String quarter = switch (month) {
  case 1, 2, 3 -> "Q1";
  case 4, 5, 6 -> "Q2";
  case 7, 8, 9 -> "Q3";
  case 10, 11, 12 -> "Q4";
  default -> "N/A";
};
```

- Маппинг кода статуса:

```java
int code = 404;
String text = switch (code) {
  case 200 -> "OK";
  case 201 -> "Created";
  case 400 -> "Bad Request";
  case 401, 403 -> "Auth Error";
  case 404 -> "Not Found";
  case 500, 502, 503 -> "Server Error";
  default -> "Unknown";
};
```

- Перенос логики из if-else:

```java
String title = switch (role) {
  case "ADMIN" -> "Administrator";
  case "MANAGER" -> "Manager";
  case "USER", "GUEST" -> "Viewer";
  default -> "Unknown";
};
```


## Ошибки и подводные камни

- Отсутствующий `break` в старом стиле вызывает неожиданный fall-through; в сомнении — используй стрелочный стиль.
- Метки `case` должны быть константами компиляции; нельзя использовать переменные и неконстантные выражения.
- Тип `switch` и типы `case` должны совпадать; следи за автоупаковкой/распаковкой.
- В switch-выражении все ветки обязаны возвращать значение (через `->` или `yield`) либо бросать исключение.


## Производительность

- Компилятор может оптимизировать switch через таблицу переходов или lookupswitch; для больших плотных диапазонов целых значений switch часто быстрее цепочки if-else.
- Разница заметна в горячих участках; в обычном коде выбирай читабельность, а не микроптимизацию.


## Инструменты и подсказки IntelliJ IDEA

- Инспекции предлагают конвертацию if-else в switch и старой формы switch в стрелочный стиль.
- Быстрые фиксы помогают добавить недостающие `default`/ветки, вернуть значение из выражения, заменить fall-through на перечисление меток.
- Включай уровень языка в настройках проекта (Project language level) для доступа к switch-выражениям и паттерн-матчингу.


## Мини-чеклист

- Нужно значение? Используй switch-выражение.
- Несколько одинаковых действий? Перечисли метки через запятую.
- Нужны побочные эффекты и возврат? Блок + `yield`.
- Старый стиль? Не забывай `break`.
- Обрабатывай `default`, даже если «не должен случиться».


## Быстрые сниппеты

- Классика:

```java
switch (c) {
  case 'y':
  case 'Y':
    return true;
  case 'n':
  case 'N':
    return false;
  default:
    throw new IllegalArgumentException("Unknown: " + c);
}
```

- Выражение с `yield`:

```java
String msg = switch (state) {
  case RUNNING -> "Go";
  case STOPPED -> "Stop";
  default -> {
    log("unexpected " + state);
    yield "Unknown";
  }
};
```

- Enum с полным покрытием:

```java
enum Op { ADD, SUB, MUL, DIV }

int apply(Op op, int a, int b) {
  return switch (op) {
    case ADD -> a + b;
    case SUB -> a - b;
    case MUL -> a * b;
    case DIV -> a / b;
  };
}
```


<span style="display:none">[^1][^10][^2][^3][^4][^5][^6][^7][^8][^9]</span>

<div style="text-align: center">⁂</div>

[^1]: https://javarush.com/groups/posts/operator-switch-v-java

[^2]: https://skillbox.ru/media/code/operator-switch-v-java-ponyatie-evolyutsiya-i-primery-ispolzovaniya/

[^3]: https://www.examclouds.com/ru/java/java-core-russian/switch-operator-russian

[^4]: https://topjava.ru/blog/polnoye-rukovodstvo-po-switch-v-java

[^5]: https://learn.javascript.ru/switch

[^6]: https://habr.com/ru/articles/838890/

[^7]: https://sky.pro/wiki/java/operator-switch-v-java/

[^8]: https://proglang.su/java/operator-switch-case

[^9]: https://code-basics.com/ru/languages/java/lessons/switch

[^10]: https://javarush.com/quests/lectures/questsyntaxpro.level13.lecture06

