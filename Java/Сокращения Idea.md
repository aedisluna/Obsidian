# Live Templates — Java

## Быстрые выводы и main

- sout — System.out.println();
- soutm — печать имени текущего класса и метода.
- soutp — печать параметров метода (имена=значения).
- soutv — печать значения последней переменной.
- serr — System.err.println();
- psvm или main — public static void main(String[] args) { }
- psfs — public static final String
- psf — public static final


## Циклы и условия

- fori — классический for (int i = 0; i < ...; i++).
- iter — foreach по массиву/коллекции.
- itar — перебор массива индексом.
- itco — перебор коллекции через итератор.
- ifn — if (var == null) { }
- inn — if (var != null) { }


## Исключения и логгирование

- try — блок try/catch с Exception.
- tryf — try/finally.
- tryw — try-with-resources.
- logi — логирование info через популярные логгеры (если включены соответствующие шаблоны).


## Полезные служебные

- psfi — public static final int
- psfb — public static final boolean
- thr — throw new
- prsf — private static final
- pr — private
- pu — public
- soutf — System.out.printf("");


## Как посмотреть и развернуть

- Ctrl+J — показать доступные шаблоны в текущем контексте.
- Разворачивать шаблон: по Tab (по умолчанию), затем Tab/Shift+Tab — переходы по полям.
- Ctrl+Alt+J — обернуть выделенный код выбранным шаблоном (surround).


## Свои шаблоны

- Настройки → Editor → Live Templates — создать/редактировать/копировать.
- Укажи:
    - Abbreviation (сокращение), Description (описание), Template text (тело).
    - Context — где доступен (Java: statement/declaration/comments и т.д.).
    - Variables — переменные и функции (напр., suggestIndexName(), complete(), date()).
- Пример шаблона syso:
    - Abbreviation: syso; Template text: System.out.println($END$); Context: Java → statement.


## Советы по эффективности

- Освой базовые: psvm, fori, iter, sout, ifn/inn — они покрывают 80% рутины.
- Пользуйся индексацией табами внутри шаблона, чтобы не тянуться стрелками.
- Добавь свои: тестовые методы JUnit, логгеры, шаблоны DTO/record.

Хочешь — подготовлю импортируемый XML с набором кастомных Java‑шаблонов (JUnit 5, Logger, Builder) и раскладкой под твои хоткеи.
<span style="display:none">[^10]</span>

<div style="text-align: center">⁂</div>

: https://www.jetbrains.com/help/idea/using-live-templates.html

: https://www.jetbrains.com/guide/java/tips/live-templates/

: https://stackoverflow.com/questions/38992114/system-out-println-shortcut-on-intellij-idea

: http://www.radcortez.com/getting-started-with-intellij-idea-live-templates/

: https://www.helenjoscott.com/2020/10/25/live-templates-in-intellij-idea/

: https://mahendranv.github.io/posts/live-template/

: https://www.jetbrains.com/help/idea/settings-live-templates.html

: https://www.jetbrains.com/help/idea/template-variables.html

: https://tutorials7.com/qa/java/shortcuts-and-live-templates-in-intellij-idea/

[^10]: https://javarush.com/quests/lectures/questsyntaxpro.level20.lecture03

