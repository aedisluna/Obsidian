# String в Java
*Работа с неизменяемыми строками*

#java #strings #immutable #api #text-processing

---

## 📋 Содержание
- [[#Основы String]]
- [[#Создание строк]]
- [[#Основные методы]]
- [[#String Pool и интернирование]]
- [[#Unicode и кодировки]]
- [[#Производительность]]
- [[#Лучшие практики]]

---

## 🎯 Основы String

> [!info] String в Java - это неизменяемый (immutable) класс для работы с последовательностями символов. Все операции со строками создают новые объекты

### Неизменяемость строк
```java
public class StringImmutabilityDemo {
    
    public static void demonstrateImmutability() {
        System.out.println("=== Неизменяемость строк ===");
        
        String original = "Hello";
        String modified = original.concat(" World");
        
        System.out.println("Оригинальная строка: '" + original + "'");
        System.out.println("Модифицированная строка: '" + modified + "'");
        System.out.println("original == modified: " + (original == modified));
        System.out.println("original.equals(modified): " + original.equals(modified));
        
        // Строки не изменяются на месте
        String test = "Java";
        String originalReference = test;
        test = test.toUpperCase();
        
        System.out.println("После toUpperCase():");
        System.out.println("Исходная переменная: '" + originalReference + "'");
        System.out.println("Новое значение: '" + test + "'");
        System.out.println("Это разные объекты: " + (originalReference != test));
    }
    
    public static void demonstrateStringCreation() {
        System.out.println("\n=== Создание строк ===");
        
        // Разные способы создания
        String str1 = "Hello";              // String literal
        String str2 = new String("Hello");  // Через конструктор
        String str3 = "Hello";              // Еще один literal
        char[] chars = {'H', 'e', 'l', 'l', 'o'};
        String str4 = new String(chars);    // Из массива символов
        
        System.out.println("str1 == str2: " + (str1 == str2)); // false
        System.out.println("str1 == str3: " + (str1 == str3)); // true (String Pool)
        System.out.println("str1.equals(str2): " + str1.equals(str2)); // true
        System.out.println("str1.equals(str4): " + str1.equals(str4)); // true
        
        // Демонстрация String Pool
        demonstrateStringPool();
    }
    
    private static void demonstrateStringPool() {
        System.out.println("\n--- String Pool ---");
        
        String s1 = "Java";
        String s2 = "Java"; 
        String s3 = new String("Java");
        String s4 = s3.intern(); // Помещаем в пул
        
        System.out.println("s1 == s2 (literals): " + (s1 == s2));           // true
        System.out.println("s1 == s3 (constructor): " + (s1 == s3));        // false  
        System.out.println("s1 == s4 (interned): " + (s1 == s4));           // true
        System.out.println("s3 == s4 (before/after intern): " + (s3 == s4)); // false
        
        // Память и производительность
        System.out.println("System.identityHashCode(s1): " + System.identityHashCode(s1));
        System.out.println("System.identityHashCode(s2): " + System.identityHashCode(s2));
        System.out.println("System.identityHashCode(s3): " + System.identityHashCode(s3));
        System.out.println("System.identityHashCode(s4): " + System.identityHashCode(s4));
    }
    
    public static void main(String[] args) {
        demonstrateImmutability();
        demonstrateStringCreation();
    }
}
```

---

## 🏗️ Создание строк

### Конструкторы и методы создания
```java
public class StringCreationMethods {
    
    public static void demonstrateConstructors() {
        System.out.println("=== Конструкторы String ===");
        
        // 1. Пустая строка
        String empty1 = "";
        String empty2 = new String();
        System.out.println("Пустые строки равны: " + empty1.equals(empty2));
        
        // 2. Из другой строки (копирование)
        String original = "Hello World";
        String copy = new String(original);
        System.out.println("Original: " + original);
        System.out.println("Copy: " + copy);
        System.out.println("Разные объекты: " + (original != copy));
        
        // 3. Из массива символов
        char[] charArray = {'J', 'a', 'v', 'a', ' ', 'S', 't', 'r', 'i', 'n', 'g'};
        String fromChars = new String(charArray);
        System.out.println("Из char[]: " + fromChars);
        
        // 4. Из части массива символов
        String substring = new String(charArray, 5, 6); // "String"
        System.out.println("Подстрока из char[]: " + substring);
        
        // 5. Из массива байтов
        byte[] bytes = {72, 101, 108, 108, 111}; // "Hello" в ASCII
        String fromBytes = new String(bytes);
        System.out.println("Из byte[]: " + fromBytes);
        
        // 6. Из массива байтов с кодировкой
        try {
            byte[] utf8Bytes = "Привет мир! 🌍".getBytes("UTF-8");
            String fromUtf8 = new String(utf8Bytes, "UTF-8");
            System.out.println("Из UTF-8 байтов: " + fromUtf8);
            
            // Демонстрация проблем с кодировкой
            String fromDefaultCharset = new String(utf8Bytes, "ISO-8859-1");
            System.out.println("Неправильная кодировка: " + fromDefaultCharset);
            
        } catch (Exception e) {
            System.err.println("Ошибка кодировки: " + e.getMessage());
        }
        
        // 7. Из StringBuilder/StringBuffer
        StringBuilder sb = new StringBuilder("Built with StringBuilder");
        String fromBuilder = new String(sb);
        System.out.println("Из StringBuilder: " + fromBuilder);
    }
    
    public static void main(String[] args) {
        demonstrateConstructors();
    }
}
```

---

## 🛠️ Основные методы

### Методы получения информации
```java
public class StringInformationMethods {
    
    public static void demonstrateBasicInfo() {
        String text = "Hello World! 🌍 Java Programming";
        
        System.out.println("=== Основная информация о строке ===");
        System.out.println("Строка: '" + text + "'");
        System.out.println("Длина: " + text.length());
        System.out.println("Пустая: " + text.isEmpty());
        System.out.println("Пустая или только пробелы: " + text.isBlank()); // Java 11+
        
        // Доступ к символам
        System.out.println("\n--- Доступ к символам ---");
        System.out.println("Первый символ: '" + text.charAt(0) + "'");
        System.out.println("Последний символ: '" + text.charAt(text.length() - 1) + "'");
        
        // Безопасный доступ к символам
        int index = 50;
        if (index >= 0 && index < text.length()) {
            System.out.println("Символ на позиции " + index + ": '" + text.charAt(index) + "'");
        } else {
            System.out.println("Индекс " + index + " выходит за границы строки");
        }
        
        // Коды символов
        System.out.println("\n--- Unicode кодовые точки ---");
        System.out.println("Длина в символах: " + text.length());
        System.out.println("Количество кодовых точек: " + text.codePointCount(0, text.length()));
        
        // Демонстрация разницы между char и codePoint
        demoUnicodeHandling();
    }
    
    private static void demoUnicodeHandling() {
        String emoji = "Hello 👨‍💻 World";
        System.out.println("\nСтрока с эмодзи: " + emoji);
        System.out.println("Длина через .length(): " + emoji.length());
        System.out.println("Кодовых точек: " + emoji.codePointCount(0, emoji.length()));
        
        // Правильная итерация по кодовым точкам
        System.out.println("\nИтерация по кодовым точкам:");
        for (int i = 0; i < emoji.length();) {
            int codePoint = emoji.codePointAt(i);
            System.out.printf("Позиция %d: U+%04X ('%s')%n", 
                            i, codePoint, Character.toString(codePoint));
            i += Character.charCount(codePoint);
        }
    }
    
    public static void main(String[] args) {
        demonstrateBasicInfo();
    }
}
```

### Методы поиска и сравнения
```java
public class StringSearchAndComparison {
    
    public static void demonstrateSearchMethods() {
        String text = "Java Programming is fun. Java is powerful.";
        
        System.out.println("=== Методы поиска ===");
        System.out.println("Текст: " + text);
        
        // Поиск подстрок
        String searchWord = "Java";
        int firstIndex = text.indexOf(searchWord);
        int lastIndex = text.lastIndexOf(searchWord);
        
        System.out.println("Первое вхождение '" + searchWord + "': " + firstIndex);
        System.out.println("Последнее вхождение '" + searchWord + "': " + lastIndex);
        
        // Поиск с позиции
        int secondOccurrence = text.indexOf(searchWord, firstIndex + 1);
        System.out.println("Второе вхождение '" + searchWord + "': " + secondOccurrence);
        
        // Поиск символа
        char dotChar = '.';
        System.out.println("Первая точка: " + text.indexOf(dotChar));
        System.out.println("Последняя точка: " + text.lastIndexOf(dotChar));
        
        // Проверка содержимого
        System.out.println("\n--- Проверка содержимого ---");
        System.out.println("Содержит 'Programming': " + text.contains("Programming"));
        System.out.println("Начинается с 'Java': " + text.startsWith("Java"));
        System.out.println("Заканчивается на '.': " + text.endsWith("."));
        System.out.println("Начинается с 'Programming' с позиции 5: " + text.startsWith("Programming", 5));
        
        demonstrateComparisons();
    }
    
    private static void demonstrateComparisons() {
        System.out.println("\n=== Методы сравнения ===");
        
        String str1 = "Java";
        String str2 = "JAVA";
        String str3 = "Python";
        String str4 = "Java";
        
        // Точное сравнение
        System.out.println("'Java'.equals('JAVA'): " + str1.equals(str2));
        System.out.println("'Java'.equals('Java'): " + str1.equals(str4));
        
        // Сравнение без учета регистра
        System.out.println("'Java'.equalsIgnoreCase('JAVA'): " + str1.equalsIgnoreCase(str2));
        
        // Лексикографическое сравнение
        System.out.println("'Java'.compareTo('Python'): " + str1.compareTo(str3)); // < 0
        System.out.println("'Python'.compareTo('Java'): " + str3.compareTo(str1)); // > 0
        System.out.println("'Java'.compareTo('Java'): " + str1.compareTo(str4));   // = 0
        
        // Сравнение без учета регистра
        System.out.println("'Java'.compareToIgnoreCase('JAVA'): " + str1.compareToIgnoreCase(str2));
        
        // Regionmatches - сравнение регионов
        String text1 = "Hello World";
        String text2 = "Hi World!";
        boolean regionMatch = text1.regionMatches(6, text2, 3, 5); // "World" vs "World"
        System.out.println("Регионы совпадают: " + regionMatch);
        
        // Regionmatches с игнорированием регистра
        boolean regionMatchIgnoreCase = text1.regionMatches(true, 0, "HELLO world", 0, 5);
        System.out.println("Регионы совпадают (ignore case): " + regionMatchIgnoreCase);
        
        // Демонстрация проблем с null
        demonstrateNullHandling();
    }
    
    private static void demonstrateNullHandling() {
        System.out.println("\n--- Обработка null ---");
        
        String str = "Java";
        String nullStr = null;
        
        // ✅ Безопасные сравнения
        System.out.println("Objects.equals(str, nullStr): " + java.util.Objects.equals(str, nullStr));
        System.out.println("Objects.equals(nullStr, nullStr): " + java.util.Objects.equals(nullStr, nullStr));
        
        // ❌ Опасные операции с null
        try {
            System.out.println("nullStr.equals(str): " + nullStr.equals(str)); // NPE!
        } catch (NullPointerException e) {
            System.out.println("NullPointerException при вызове equals() на null");
        }
        
        // ✅ Правильный способ
        System.out.println("str.equals(nullStr): " + str.equals(nullStr)); // false, безопасно
        
        // Использование статических методов для безопасности
        System.out.println("String.valueOf(nullStr): '" + String.valueOf(nullStr) + "'");
    }
    
    public static void main(String[] args) {
        demonstrateSearchMethods();
    }
}
```

### Методы преобразования
```java
public class StringTransformationMethods {
    
    public static void demonstrateTransformations() {
        String original = "  Hello World! Programming in Java  ";
        
        System.out.println("=== Методы преобразования ===");
        System.out.println("Оригинал: '" + original + "'");
        
        // Изменение регистра
        System.out.println("toUpperCase(): '" + original.toUpperCase() + "'");
        System.out.println("toLowerCase(): '" + original.toLowerCase() + "'");
        
        // Удаление пробелов
        System.out.println("trim(): '" + original.trim() + "'");
        
        // Java 11+ методы для работы с пробелами
        String whitespaceString = "   \\t\\n Hello \\r\\n  ";
        System.out.println("\n--- Java 11+ методы (strip) ---");
        System.out.println("Оригинал: '" + whitespaceString + "'");
        System.out.println("strip(): '" + whitespaceString.strip() + "'");
        System.out.println("stripLeading(): '" + whitespaceString.stripLeading() + "'");
        System.out.println("stripTrailing(): '" + whitespaceString.stripTrailing() + "'");
        
        // Замена символов и подстрок
        String text = "Hello World! Hello Java!";
        System.out.println("\n--- Методы замены ---");
        System.out.println("Оригинал: '" + text + "'");
        System.out.println("replace('l', 'X'): '" + text.replace('l', 'X') + "'");
        System.out.println("replace('Hello', 'Hi'): '" + text.replace("Hello", "Hi") + "'");
        System.out.println("replaceFirst('Hello', 'Hi'): '" + text.replaceFirst("Hello", "Hi") + "'");
        System.out.println("replaceAll('Hello', 'Hi'): '" + text.replaceAll("Hello", "Hi") + "'");
        
        // Регулярные выражения в replaceAll
        String numberedText = "Item1, Item22, Item333";
        System.out.println("replaceAll с regex: '" + 
            numberedText.replaceAll("\\\\d+", "X") + "'");
        
        demonstrateSubstrings();
        demonstrateSplitting();
    }
    
    private static void demonstrateSubstrings() {
        String text = "Java Programming Language";
        
        System.out.println("\n--- Методы извлечения подстрок ---");
        System.out.println("Оригинал: '" + text + "'");
        
        // Подстроки
        System.out.println("substring(5): '" + text.substring(5) + "'");
        System.out.println("substring(5, 16): '" + text.substring(5, 16) + "'");
        
        // Безопасное извлечение подстрок
        int startIndex = 5;
        int endIndex = 100;
        String safeSub = safeSubstring(text, startIndex, endIndex);
        System.out.println("Безопасная подстрока (5, 100): '" + safeSub + "'");
        
        // subSequence (возвращает CharSequence)
        CharSequence charSeq = text.subSequence(0, 4);
        System.out.println("subSequence(0, 4): '" + charSeq + "'");
        System.out.println("Тип: " + charSeq.getClass().getSimpleName());
    }
    
    private static String safeSubstring(String str, int start, int end) {
        if (str == null) return "";
        
        int len = str.length();
        int safeStart = Math.max(0, Math.min(start, len));
        int safeEnd = Math.max(safeStart, Math.min(end, len));
        
        return str.substring(safeStart, safeEnd);
    }
    
    private static void demonstrateSplitting() {
        System.out.println("\n--- Методы разделения строк ---");
        
        // Простое разделение
        String csvData = "apple,banana,cherry,date";
        String[] fruits = csvData.split(",");
        System.out.println("CSV данные: " + csvData);
        System.out.println("Разделенные: " + java.util.Arrays.toString(fruits));
        
        // Разделение с лимитом
        String[] limitedSplit = csvData.split(",", 2);
        System.out.println("С лимитом 2: " + java.util.Arrays.toString(limitedSplit));
        
        // Разделение по регулярному выражению
        String mixedDelimiters = "word1,word2;word3:word4|word5";
        String[] words = mixedDelimiters.split("[,;:|]");
        System.out.println("Смешанные разделители: " + java.util.Arrays.toString(words));
        
        // Работа с пустыми строками
        String withEmpty = "a,,b,";
        String[] withEmpties = withEmpty.split(",");
        String[] withEmptiesKeep = withEmpty.split(",", -1);
        System.out.println("Обычное разделение: " + java.util.Arrays.toString(withEmpties));
        System.out.println("С сохранением пустых: " + java.util.Arrays.toString(withEmptiesKeep));
        
        // Java 11+ lines()
        String multiline = "Line 1\\nLine 2\\nLine 3";
        System.out.println("\\nМногострочный текст через lines():");
        multiline.lines().forEach(line -> System.out.println("  '" + line + "'"));
    }
    
    public static void main(String[] args) {
        demonstrateTransformations();
    }
}
```

---

## 🏊 String Pool и интернирование

### Механизм String Pool
```java
public class StringPoolDemo {
    
    public static void demonstrateStringPool() {
        System.out.println("=== String Pool демонстрация ===");
        
        // Литералы попадают в String Pool автоматически
        String str1 = "Hello";
        String str2 = "Hello";
        String str3 = "World";
        
        System.out.println("str1 == str2 (literals): " + (str1 == str2)); // true
        System.out.println("str1 == str3: " + (str1 == str3)); // false
        
        // Конструктор создает новый объект в heap
        String str4 = new String("Hello");
        String str5 = new String("Hello");
        
        System.out.println("str1 == str4 (literal vs constructor): " + (str1 == str4)); // false
        System.out.println("str4 == str5 (both constructors): " + (str4 == str5)); // false
        System.out.println("str1.equals(str4): " + str1.equals(str4)); // true
        
        // Интернирование
        String str6 = str4.intern();
        System.out.println("str1 == str6 (after intern): " + (str1 == str6)); // true
        
        demonstrateInternPerformance();
        demonstrateCompilerOptimizations();
    }
    
    private static void demonstrateInternPerformance() {
        System.out.println("\\n--- Производительность интернирования ---");
        
        int iterations = 100000;
        
        // Тест 1: Создание через конструктор
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            String str = new String("TestString" + (i % 1000));
        }
        long constructorTime = System.currentTimeMillis() - startTime;
        
        // Тест 2: Создание через конструктор + intern
        startTime = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            String str = new String("TestString" + (i % 1000)).intern();
        }
        long internTime = System.currentTimeMillis() - startTime;
        
        // Тест 3: Литералы
        startTime = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            String str = "TestString" + (i % 1000);
        }
        long literalTime = System.currentTimeMillis() - startTime;
        
        System.out.printf("Constructor: %d мс%n", constructorTime);
        System.out.printf("Constructor + intern: %d мс%n", internTime);
        System.out.printf("Literals: %d мс%n", literalTime);
        
        // Использование памяти
        demonstrateMemoryUsage();
    }
    
    private static void demonstrateMemoryUsage() {
        System.out.println("\\n--- Использование памяти ---");
        
        Runtime runtime = Runtime.getRuntime();
        
        // Принудительная очистка памяти
        System.gc();
        long memoryBefore = runtime.totalMemory() - runtime.freeMemory();
        
        // Создаем много строк без интернирования
        String[] strings = new String[10000];
        for (int i = 0; i < strings.length; i++) {
            strings[i] = new String("String" + (i % 100)); // 100 уникальных значений
        }
        
        long memoryAfterConstructor = runtime.totalMemory() - runtime.freeMemory();
        
        // Интернируем все строки
        for (int i = 0; i < strings.length; i++) {
            strings[i] = strings[i].intern();
        }
        
        System.gc();
        long memoryAfterIntern = runtime.totalMemory() - runtime.freeMemory();
        
        System.out.printf("Память до: %d KB%n", memoryBefore / 1024);
        System.out.printf("После создания: %d KB (прирост: %d KB)%n", 
                         memoryAfterConstructor / 1024, 
                         (memoryAfterConstructor - memoryBefore) / 1024);
        System.out.printf("После интернирования: %d KB (экономия: %d KB)%n", 
                         memoryAfterIntern / 1024, 
                         (memoryAfterConstructor - memoryAfterIntern) / 1024);
    }
    
    private static void demonstrateCompilerOptimizations() {
        System.out.println("\\n--- Оптимизации компилятора ---");
        
        // Компилятор объединяет литералы во время компиляции
        String s1 = "Hello" + "World";      // Компилируется в "HelloWorld"
        String s2 = "HelloWorld";
        System.out.println("s1 == s2 (compile-time concatenation): " + (s1 == s2)); // true
        
        // Но не объединяет переменные
        String hello = "Hello";
        String world = "World";
        String s3 = hello + world;           // Runtime конкатенация
        System.out.println("s2 == s3 (runtime concatenation): " + (s2 == s3)); // false
        System.out.println("s2.equals(s3): " + s2.equals(s3)); // true
        
        // Интернирование runtime конкатенации
        String s4 = (hello + world).intern();
        System.out.println("s2 == s4 (after intern): " + (s2 == s4)); // true
        
        // Константы final также оптимизируются
        final String CONST_HELLO = "Hello";
        final String CONST_WORLD = "World";
        String s5 = CONST_HELLO + CONST_WORLD; // Компилируется в "HelloWorld"
        System.out.println("s2 == s5 (final constants): " + (s2 == s5)); // true
    }
    
    public static void main(String[] args) {
        demonstrateStringPool();
    }
}
```

---

## 🌍 Unicode и кодировки

### Работа с Unicode
```java
public class StringUnicodeHandling {
    
    public static void demonstrateUnicodeSupport() {
        System.out.println("=== Unicode поддержка ===");
        
        // Различные способы представления Unicode символов
        String unicode1 = "Привет мир! 🌍";
        String unicode2 = "\\u041F\\u0440\\u0438\\u0432\\u0435\\u0442"; // "Привет" в Unicode escapes
        String unicode3 = "Java ☕ Programming 💻";
        
        System.out.println("Обычный текст: " + unicode1);
        System.out.println("Unicode escapes: " + unicode2);
        System.out.println("Текст с эмодзи: " + unicode3);
        
        // Анализ Unicode символов
        analyzeUnicodeString(unicode3);
        
        // Нормализация Unicode
        demonstrateNormalization();
        
        // Проблемы с кодировками
        demonstrateEncodingIssues();
    }
    
    private static void analyzeUnicodeString(String str) {
        System.out.println("\\n--- Анализ Unicode строки ---");
        System.out.println("Строка: " + str);
        System.out.println("Длина (char units): " + str.length());
        System.out.println("Кодовых точек: " + str.codePointCount(0, str.length()));
        
        System.out.println("\\nДетальный анализ:");
        for (int i = 0; i < str.length();) {
            int codePoint = str.codePointAt(i);
            int charCount = Character.charCount(codePoint);
            
            String character = new String(new int[]{codePoint}, 0, 1);
            String unicodeBlock = Character.UnicodeBlock.of(codePoint).toString();
            String category = getUnicodeCategory(Character.getType(codePoint));
            
            System.out.printf("Позиция %2d: U+%04X '%s' [%s] (%s) - %d char unit(s)%n",
                            i, codePoint, character, unicodeBlock, category, charCount);
            
            i += charCount;
        }
    }
    
    private static String getUnicodeCategory(int type) {
        switch (type) {
            case Character.UPPERCASE_LETTER: return "Uppercase Letter";
            case Character.LOWERCASE_LETTER: return "Lowercase Letter";
            case Character.DECIMAL_DIGIT_NUMBER: return "Decimal Number";
            case Character.SPACE_SEPARATOR: return "Space";
            case Character.OTHER_SYMBOL: return "Other Symbol";
            case Character.OTHER_PUNCTUATION: return "Punctuation";
            default: return "Type " + type;
        }
    }
    
    private static void demonstrateNormalization() {
        System.out.println("\\n--- Unicode нормализация ---");
        
        // Один символ можно представить разными способами
        String composed = "é";        // Один символ (U+00E9)
        String decomposed = "e\\u0301"; // e + combining acute accent (U+0065 + U+0301)
        
        System.out.println("Composed: '" + composed + "' (длина: " + composed.length() + ")");
        System.out.println("Decomposed: '" + decomposed + "' (длина: " + decomposed.length() + ")");
        System.out.println("Равны через equals: " + composed.equals(decomposed));
        System.out.println("Равны визуально: " + composed.equals(decomposed)); // false!
        
        // Нормализация для корректного сравнения
        String normalizedComposed = java.text.Normalizer.normalize(composed, java.text.Normalizer.Form.NFC);
        String normalizedDecomposed = java.text.Normalizer.normalize(decomposed, java.text.Normalizer.Form.NFC);
        
        System.out.println("После нормализации равны: " + normalizedComposed.equals(normalizedDecomposed));
    }
    
    private static void demonstrateEncodingIssues() {
        System.out.println("\\n--- Проблемы с кодировками ---");
        
        String originalText = "Тест кодировки: ñáéíóú 中文 🚀";
        System.out.println("Оригинальный текст: " + originalText);
        
        try {
            // Различные кодировки
            String[] encodings = {"UTF-8", "UTF-16", "ISO-8859-1", "Windows-1251"};
            
            for (String encoding : encodings) {
                try {
                    byte[] bytes = originalText.getBytes(encoding);
                    String restored = new String(bytes, encoding);
                    
                    System.out.printf("%-12s: %d байт, восстановлено: %s%n", 
                                    encoding, bytes.length, 
                                    restored.equals(originalText) ? "✓" : "✗ (" + restored + ")");
                } catch (Exception e) {
                    System.out.printf("%-12s: Ошибка - %s%n", encoding, e.getMessage());
                }
            }
            
            // Демонстрация проблемы с неправильной кодировкой
            byte[] utf8Bytes = originalText.getBytes("UTF-8");
            String wrongDecoding = new String(utf8Bytes, "ISO-8859-1");
            System.out.println("\\nПроблема декодирования:");
            System.out.println("UTF-8 байты, декодированные как ISO-8859-1: " + wrongDecoding);
            
            // Попытка исправить
            byte[] wrongBytes = wrongDecoding.getBytes("ISO-8859-1");
            String fixed = new String(wrongBytes, "UTF-8");
            System.out.println("Исправленный текст: " + fixed);
            
        } catch (Exception e) {
            System.err.println("Ошибка кодировки: " + e.getMessage());
        }
    }
    
    public static void main(String[] args) {
        demonstrateUnicodeSupport();
    }
}
```

---

## ⚡ Производительность

### Оптимизация работы со строками
```java
public class StringPerformanceOptimization {
    
    public static void performanceComparison() {
        System.out.println("=== Сравнение производительности ===");
        
        int iterations = 100000;
        
        // Тест конкатенации
        testConcatenationPerformance(iterations);
        
        // Тест поиска
        testSearchPerformance(iterations);
        
        // Тест создания строк
        testCreationPerformance(iterations);
    }
    
    private static void testConcatenationPerformance(int iterations) {
        System.out.println("\\n--- Тест конкатенации ---");
        
        // 1. Конкатенация через +
        long startTime = System.currentTimeMillis();
        String result1 = "";
        for (int i = 0; i < 1000; i++) { // Меньше итераций для String +
            result1 += "item" + i;
        }
        long stringPlusTime = System.currentTimeMillis() - startTime;
        
        // 2. StringBuilder
        startTime = System.currentTimeMillis();
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < iterations; i++) {
            sb.append("item").append(i);
        }
        String result2 = sb.toString();
        long sbTime = System.currentTimeMillis() - startTime;
        
        // 3. StringBuilder с предустановленной емкостью
        startTime = System.currentTimeMillis();
        StringBuilder sbOptimized = new StringBuilder(iterations * 10);
        for (int i = 0; i < iterations; i++) {
            sbOptimized.append("item").append(i);
        }
        String result3 = sbOptimized.toString();
        long sbOptimizedTime = System.currentTimeMillis() - startTime;
        
        // 4. String.format (медленно, но для сравнения)
        startTime = System.currentTimeMillis();
        StringBuilder formatResult = new StringBuilder();
        for (int i = 0; i < 1000; i++) { // Меньше итераций
            formatResult.append(String.format("item%d", i));
        }
        long formatTime = System.currentTimeMillis() - startTime;
        
        System.out.printf("String + (1000 итераций):      %d мс%n", stringPlusTime);
        System.out.printf("StringBuilder (%d итераций):  %d мс%n", iterations, sbTime);
        System.out.printf("StringBuilder оптимизированный: %d мс%n", sbOptimizedTime);
        System.out.printf("String.format (1000 итераций): %d мс%n", formatTime);
    }
    
    private static void testSearchPerformance(int iterations) {
        System.out.println("\\n--- Тест поиска ---");
        
        String longText = generateLongText(10000);
        String searchTerm = "target";
        
        // 1. indexOf
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            longText.indexOf(searchTerm);
        }
        long indexOfTime = System.currentTimeMillis() - startTime;
        
        // 2. contains
        startTime = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            longText.contains(searchTerm);
        }
        long containsTime = System.currentTimeMillis() - startTime;
        
        // 3. matches (регулярное выражение)
        String pattern = ".*" + searchTerm + ".*";
        startTime = System.currentTimeMillis();
        for (int i = 0; i < 1000; i++) { // Меньше итераций для regex
            longText.matches(pattern);
        }
        long matchesTime = System.currentTimeMillis() - startTime;
        
        // 4. Предкомпилированный Pattern
        java.util.regex.Pattern compiledPattern = java.util.regex.Pattern.compile(pattern);
        startTime = System.currentTimeMillis();
        for (int i = 0; i < 1000; i++) {
            compiledPattern.matcher(longText).matches();
        }
        long compiledPatternTime = System.currentTimeMillis() - startTime;
        
        System.out.printf("indexOf (%d итераций):          %d мс%n", iterations, indexOfTime);
        System.out.printf("contains (%d итераций):         %d мс%n", iterations, containsTime);
        System.out.printf("matches (1000 итераций):        %d мс%n", matchesTime);
        System.out.printf("Compiled Pattern (1000 итераций): %d мс%n", compiledPatternTime);
    }
    
    private static void testCreationPerformance(int iterations) {
        System.out.println("\\n--- Тест создания строк ---");
        
        // 1. Литералы
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            String s = "TestString";
        }
        long literalTime = System.currentTimeMillis() - startTime;
        
        // 2. Конструктор
        startTime = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            String s = new String("TestString");
        }
        long constructorTime = System.currentTimeMillis() - startTime;
        
        // 3. valueOf
        startTime = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            String s = String.valueOf(i);
        }
        long valueOfTime = System.currentTimeMillis() - startTime;
        
        // 4. toString
        startTime = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            String s = Integer.toString(i);
        }
        long toStringTime = System.currentTimeMillis() - startTime;
        
        System.out.printf("Literals:    %d мс%n", literalTime);
        System.out.printf("Constructor: %d мс%n", constructorTime);
        System.out.printf("valueOf:     %d мс%n", valueOfTime);
        System.out.printf("toString:    %d мс%n", toStringTime);
    }
    
    private static String generateLongText(int length) {
        StringBuilder sb = new StringBuilder(length);
        String[] words = {"word", "text", "string", "java", "performance", "test", "target", "search"};
        
        while (sb.length() < length) {
            sb.append(words[sb.length() % words.length]).append(" ");
        }
        
        return sb.toString();
    }
    
    public static void main(String[] args) {
        performanceComparison();
    }
}
```

---

## 🏆 Лучшие практики

### Рекомендации по использованию
```java
public class StringBestPractices {
    
    public static void demonstrateBestPractices() {
        System.out.println("=== Лучшие практики работы со String ===");
        
        goodPracticesExamples();
        badPracticesExamples();
        securityConsiderations();
        internationalizedStrings();
    }
    
    private static void goodPracticesExamples() {
        System.out.println("\\n✅ Хорошие практики:");
        
        // 1. Используйте equals() вместо == для сравнения содержимого
        String userInput = getUserInput(); // Эмуляция пользовательского ввода
        String expected = "admin";
        
        if (expected.equals(userInput)) { // ✅ Правильно - literal первым
            System.out.println("Доступ разрешен");
        }
        
        // 2. Null-безопасные операции
        String nullableString = getNullableString();
        
        // ✅ Безопасная проверка на null и пустоту
        if (nullableString != null && !nullableString.trim().isEmpty()) {
            System.out.println("Строка содержит данные: " + nullableString);
        }
        
        // Или еще лучше с Java 11+
        if (nullableString != null && !nullableString.isBlank()) {
            System.out.println("Строка не пуста: " + nullableString);
        }
        
        // 3. Используйте StringBuilder для множественных конкатенаций
        List<String> items = Arrays.asList("apple", "banana", "cherry");
        StringBuilder result = new StringBuilder();
        
        for (String item : items) {
            result.append(item).append(", ");
        }
        
        if (result.length() > 0) {
            result.setLength(result.length() - 2); // Удаляем последнюю запятую
        }
        
        System.out.println("Объединенные элементы: " + result);
        
        // 4. Используйте String.join() для простых случаев (Java 8+)
        String joined = String.join(", ", items);
        System.out.println("Через String.join(): " + joined);
        
        // 5. Правильная работа с регистром
        String userRole = "ADMIN";
        if ("admin".equalsIgnoreCase(userRole)) { // ✅ Правильно
            System.out.println("Пользователь является администратором");
        }
        
        // 6. Эффективный поиск и замена
        String text = "Hello World Hello Java";
        if (text.contains("Hello")) { // ✅ Быстрая проверка перед заменой
            String replaced = text.replace("Hello", "Hi");
            System.out.println("Замененный текст: " + replaced);
        }
        
        // 7. Кэширование часто используемых строк
        demonstrateStringCaching();
    }
    
    private static void badPracticesExamples() {
        System.out.println("\\n❌ Плохие практики (избегайте):");
        
        String userInput = "admin";
        
        // ❌ Плохо - может выбросить NPE
        try {
            String nullString = null;
            if (nullString.equals("test")) { // NPE!
                System.out.println("Никогда не выполнится");
            }
        } catch (NullPointerException e) {
            System.out.println("Получили NPE при сравнении с null");
        }
        
        // ❌ Плохо - использование == для сравнения содержимого
        String str1 = new String("test");
        String str2 = new String("test");
        if (str1 == str2) { // Почти всегда false
            System.out.println("Никогда не выполнится");
        } else {
            System.out.println("== сравнивает ссылки, а не содержимое");
        }
        
        // ❌ Плохо - неэффективная конкатенация в цикле
        String result = "";
        for (int i = 0; i < 5; i++) { // Только 5 для примера
            result += "item" + i + " "; // Каждая операция создает новый объект
        }
        System.out.println("Неэффективная конкатенация: " + result);
        
        // ❌ Плохо - излишнее использование intern()
        for (int i = 0; i < 5; i++) {
            String unnecessary = ("temp" + i).intern(); // Засоряет String Pool
        }
        
        // ❌ Плохо - игнорирование кодировок
        demonstrateBadEncodingPractice();
    }
    
    private static void demonstrateBadEncodingPractice() {
        try {
            String text = "Текст с русскими символами";
            
            // ❌ Плохо - использование platform default charset
            byte[] badBytes = text.getBytes(); // Может дать разные результаты на разных системах
            
            // ✅ Хорошо - явное указание кодировки
            byte[] goodBytes = text.getBytes("UTF-8");
            
            System.out.println("Размер с default charset: " + badBytes.length);
            System.out.println("Размер с UTF-8: " + goodBytes.length);
            
        } catch (Exception e) {
            System.err.println("Ошибка кодировки: " + e.getMessage());
        }
    }
    
    private static void demonstrateStringCaching() {
        System.out.println("\\n--- Кэширование строк ---");
        
        // Пример простого кэша для часто используемых строк
        Map<Integer, String> statusCache = new HashMap<>();
        statusCache.put(200, "OK");
        statusCache.put(404, "Not Found");
        statusCache.put(500, "Internal Server Error");
        
        // Использование кэша
        for (int code : Arrays.asList(200, 404, 500, 200, 404)) {
            String status = statusCache.get(code);
            System.out.println("Код " + code + ": " + status);
        }
    }
    
    private static void securityConsiderations() {
        System.out.println("\\n--- Вопросы безопасности ---");
        
        // 1. Избегайте хранения паролей в String
        System.out.println("❌ Пароли в String остаются в памяти:");
        String password = "secret123"; // Остается в String Pool до GC
        
        // ✅ Используйте char[] для паролей
        char[] passwordChars = {'s', 'e', 'c', 'r', 'e', 't', '1', '2', '3'};
        // Можно очистить после использования
        Arrays.fill(passwordChars, '\\0');
        
        // 2. Будьте осторожны с логированием
        String userInput = "user@example.com";
        String sensitiveData = "password=secret123&token=abc123";
        
        // ❌ Плохо - может попасть пароль в логи
        // log.info("Request: " + sensitiveData);
        
        // ✅ Хорошо - логируем только безопасную часть
        String safeLog = sanitizeForLogging(sensitiveData);
        System.out.println("Безопасный лог: " + safeLog);
        
        // 3. SQL Injection защита
        demonstrateSqlInjectionPrevention();
    }
    
    private static String sanitizeForLogging(String input) {
        return input.replaceAll("(password|token)=[^&]*", "$1=***");
    }
    
    private static void demonstrateSqlInjectionPrevention() {
        String userId = "1; DROP TABLE users; --"; // Malicious input
        
        // ❌ Плохо - SQL injection уязвимость
        String badQuery = "SELECT * FROM users WHERE id = " + userId;
        System.out.println("❌ Уязвимый запрос: " + badQuery);
        
        // ✅ Хорошо - используйте PreparedStatement
        String safeQuery = "SELECT * FROM users WHERE id = ?";
        System.out.println("✅ Безопасный запрос: " + safeQuery);
        System.out.println("   Параметр: " + userId);
    }
    
    private static void internationalizedStrings() {
        System.out.println("\\n--- Интернационализация ---");
        
        // Правильная работа с локалями
        String text = "Istanbul";
        
        // ❌ Может дать неожиданные результаты в турецкой локали
        String wrongUpper = text.toUpperCase();
        
        // ✅ Правильно - указываем локаль
        String correctUpper = text.toUpperCase(Locale.ENGLISH);
        String turkishUpper = text.toUpperCase(new Locale("tr", "TR"));
        
        System.out.println("Оригинал: " + text);
        System.out.println("Default locale: " + wrongUpper);
        System.out.println("English locale: " + correctUpper);
        System.out.println("Turkish locale: " + turkishUpper);
        
        // Сортировка с учетом локали
        List<String> names = Arrays.asList("Åke", "Anders", "Zebra", "Älg");
        
        // Сортировка по кодам символов
        names.sort(String::compareTo);
        System.out.println("По кодам: " + names);
        
        // Сортировка с учетом локали
        Collator swedishCollator = Collator.getInstance(new Locale("sv", "SE"));
        names.sort(swedishCollator);
        System.out.println("По шведской локали: " + names);
    }
    
    // Вспомогательные методы
    private static String getUserInput() {
        return new String("admin"); // Эмуляция ввода через конструктор
    }
    
    private static String getNullableString() {
        return Math.random() > 0.5 ? "  some data  " : null;
    }
    
    public static void main(String[] args) {
        demonstrateBestPractices();
    }
}
```

---

## 🔗 Связанные темы
- [[StringBuilder-Code|StringBuilder-Code]] - эффективная конкатенация строк
- [[Arrays-Code|Arrays-Code]] - работа с массивами символов
- [[Regex|Regular Expressions]] - паттерны для поиска и замены
- [[Exception-Handling-Code|Exception Handling]] - обработка ошибок при работе со строками