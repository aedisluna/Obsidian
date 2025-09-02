Давай ещё про аргументы в printf

Вот заметка для Obsidian по аргументам форматирования в printf для Java:

```markdown
# Форматирование вывода с printf в Java

## Основное

- Используй `System.out.printf("шаблон", аргументы...)` для форматированного вывода.
- В строке шаблона спецификаторы начинаются с `%` — они определяют, как отобразить аргумент.

## Важные спецификаторы

- `%d` — целое число (decimal)
- `%f` — число с плавающей точкой (float/double)
- `%s` — строка (String)
- `%c` — символ (char)
- `%x` — шестнадцатеричное целое (hex)
- `%o` — восьмеричное целое (octal)
- `%b` — булев тип (boolean)
- `%n` — перенос строки (аналог \n)
- `%%` — вывести символ процента

## Расширенные опции

- `.N` — количество знаков после запятой для чисел: `%.2f` — 2 знака
- Ширина: `%10d` — выводит число шириной 10 знаков, дополняет пробелами
- Заполнение нулями: `%05d` — число шириной 5, недостающие символы — нули
- Комбинирование: `%10.2f` — ширина 10, 2 знака после запятой для float
- Выравнивание по левому краю: `%-10s` — строка шириной 10, слева
- Разделитель тысяч: `%,d`, например — `%,d` для 12345 → 12,345

## Примеры

```
System.out.printf("Привет, %s! Вам %d лет.%n", "Денис", 23);
// Привет, Денис! Вам 23 лет.

System.out.printf("Число: %.2f%n", 3.14159);
// Число: 3.14

System.out.printf("%010d%n", 42);
// 0000000042

System.out.printf("%-10s%n", "Java");
// Java      
```

## Формат строки

`%[индекс$][флаги][ширина][.точность]тип`

- Пример: `%1$-10.2f` — первый аргумент, ширина 10, выравнивание влево, 2 знака после запятой, float

---

> Используй printf для аккуратного вывода таблиц, значений и любой информации прямо в консоль! [web:49][web:50][web:54][web:55]
```

Теперь все самые полезные спецификаторы и примеры — в одном месте, удобно для запоминания и постоянной практики!

Citations:
[1] image.jpg https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/images/72568650/74583774-19d8-43ad-81fd-12cf2db4e5b4/image.jpg
[2] Форматируем вывод чисел в Java - JavaRush https://javarush.com/groups/posts/1412-formatiruem-vihvod-chisel-v-java
[3] Форматирование с использованием printf в Java - LabEx https://labex.io/ru/tutorials/java-formatting-with-printf-117408
[4] printf Locale Formatter - большие числа с разделителями точкой ... https://objectone.ru/java/printf-locale-formatter-bolshie-chisla-s-razdelitelyami-tochkoj-ili-zapyatoj/
[5] PrintStream.Printf Метод (Java.IO) - Microsoft Learn https://learn.microsoft.com/ru-ru/dotnet/api/java.io.printstream.printf?view=net-android-34.0
[6] Как создать форматированную строку в Java: руководство для ... https://kedu.ru/press-center/articles/info-prog-kak-sozdat-formatirovannuyu-stroku-v-java-rukovodstvo-dlya-nachinayushchikh-i-opytnykh/
[7] Форматирование строк - Java программирование - ExamClouds https://www.examclouds.com/ru/java/java-core-russian/formatirovanie-strok
[8] Кофе-брейк #279. Руководство для начинающих по Output/Print в ... https://javarush.com/groups/posts/68415-kofe-breyk-279-rukovodstvo-dlja-nachinajujshikh-po-outputprint-v-java
[9] Как использовать флаги в форматировании с помощью printf() в ... https://labex.io/ru/tutorials/java-how-to-use-flags-in-printf-formatting-in-java-414157
[10] Формат вывода числа в System.out.printf - Stack Overflow на русском https://ru.stackoverflow.com/questions/1186509/%D0%A4%D0%BE%D1%80%D0%BC%D0%B0%D1%82-%D0%B2%D1%8B%D0%B2%D0%BE%D0%B4%D0%B0-%D1%87%D0%B8%D1%81%D0%BB%D0%B0-%D0%B2-system-out-printf
[11] Печать символа процента в printf Java: правильный escape-символ https://sky.pro/wiki/java/pechat-simvola-protsenta-v-printf-java-pravilniy-escape-simvol/
