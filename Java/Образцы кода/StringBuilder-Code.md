# StringBuilder в Java
*Эффективная работа с изменяемыми строками*

#java #strings #stringbuilder #performance #mutable

---

## 📋 Содержание
- [[#Основы StringBuilder]]
- [[#Создание и инициализация]]
- [[#Основные методы]]
- [[#Управление capacity]]
- [[#Практические примеры]]
- [[#Производительность]]
- [[#Лучшие практики]]

---

## 🎯 Основы StringBuilder

> [!info] StringBuilder - это изменяемая последовательность символов, которая позволяет эффективно конкатенировать строки без создания промежуточных объектов

### Проблема со String
```java
public class StringConcatenationProblem {
    
    public static void demonstrateStringProblem() {
        System.out.println("=== Проблема конкатенации String ===");
        
        // ❌ Неэффективный способ
        String result = "";
        long startTime = System.currentTimeMillis();
        
        for (int i = 0; i < 10000; i++) {
            result += "item" + i + ", "; // Каждая операция создает новый объект!
        }
        
        long stringTime = System.currentTimeMillis() - startTime;
        System.out.println("Конкатенация String: " + stringTime + " мс");
        
        // ✅ Эффективный способ с StringBuilder
        StringBuilder sb = new StringBuilder();
        startTime = System.currentTimeMillis();
        
        for (int i = 0; i < 10000; i++) {
            sb.append("item").append(i).append(", ");
        }
        String sbResult = sb.toString();
        
        long sbTime = System.currentTimeMillis() - startTime;
        System.out.println("Конкатенация StringBuilder: " + sbTime + " мс");
        System.out.println("StringBuilder быстрее в " + (stringTime / (double)sbTime) + " раз");
    }
    
    public static void main(String[] args) {
        demonstrateStringProblem();
    }
}
```

### Почему StringBuilder эффективен?
- **Внутренний буфер**: Использует изменяемый массив символов
- **Динамическое расширение**: Увеличивает размер буфера по мере необходимости  
- **Минимум объектов**: Создает новый String только при вызове toString()
- **Цепочка вызовов**: Поддерживает fluent interface

---

## 🏗️ Создание и инициализация

### Конструкторы StringBuilder
```java
public class StringBuilderConstructors {
    
    public static void demonstrateConstructors() {
        System.out.println("=== Способы создания StringBuilder ===");
        
        // 1. По умолчанию (capacity = 16)
        StringBuilder sb1 = new StringBuilder();
        System.out.println("sb1 capacity: " + sb1.capacity() + ", length: " + sb1.length());
        
        // 2. С заданной начальной емкостью
        StringBuilder sb2 = new StringBuilder(100);
        System.out.println("sb2 capacity: " + sb2.capacity() + ", length: " + sb2.length());
        
        // 3. Из существующей строки
        StringBuilder sb3 = new StringBuilder("Hello World");
        System.out.println("sb3 capacity: " + sb3.capacity() + ", length: " + sb3.length());
        System.out.println("sb3 content: " + sb3);
        
        // 4. Из CharSequence
        StringBuilder sb4 = new StringBuilder(new StringBuffer("From StringBuffer"));
        System.out.println("sb4 capacity: " + sb4.capacity() + ", length: " + sb4.length());
        System.out.println("sb4 content: " + sb4);
        
        // Демонстрация автоматического расширения
        StringBuilder growing = new StringBuilder(5); // Маленький capacity
        System.out.println("\nДемонстрация автоматического расширения:");
        System.out.println("Начальный capacity: " + growing.capacity());
        
        growing.append("123456789012345678901234567890"); // Больше чем capacity
        System.out.println("После добавления длинной строки:");
        System.out.println("Новый capacity: " + growing.capacity());
        System.out.println("Length: " + growing.length());
    }
    
    public static void main(String[] args) {
        demonstrateConstructors();
    }
}
```

---

## 🛠️ Основные методы

### Методы добавления (append)
```java
public class StringBuilderAppendMethods {
    
    public static void demonstrateAppendMethods() {
        StringBuilder sb = new StringBuilder("Start: ");
        
        System.out.println("=== Методы append ===");
        
        // Различные типы данных
        sb.append(42)                    // int
          .append(", ")
          .append(3.14159)              // double  
          .append(", ")
          .append(true)                 // boolean
          .append(", ")
          .append('X')                  // char
          .append(", ")
          .append("String")             // String
          .append(", ")
          .append(new char[]{'a','r','r','a','y'}) // char[]
          .append(", ")
          .append((Object) null);       // Object (null)
        
        System.out.println("Результат append: " + sb);
        
        // Append с массивом символов (offset, length)
        char[] chars = {'H', 'e', 'l', 'l', 'o', ' ', 'W', 'o', 'r', 'l', 'd'};
        StringBuilder sb2 = new StringBuilder();
        sb2.append(chars, 6, 5); // "World" (offset=6, length=5)
        System.out.println("Append char[] with offset: " + sb2);
        
        // Append подстроки
        StringBuilder sb3 = new StringBuilder();
        sb3.append("Hello World", 6, 11); // "World"
        System.out.println("Append substring: " + sb3);
        
        // StringBuilder в качестве StringBuffer альтернативы
        StringBuffer buffer = new StringBuffer("Buffer content");
        StringBuilder sb4 = new StringBuilder();
        sb4.append(buffer); // Принимает любой CharSequence
        System.out.println("Append StringBuffer: " + sb4);
    }
    
    public static void main(String[] args) {
        demonstrateAppendMethods();
    }
}
```

### Методы модификации
```java
public class StringBuilderModificationMethods {
    
    public static void demonstrateModificationMethods() {
        System.out.println("=== Методы модификации ===");
        
        // Insert - вставка в определенную позицию
        StringBuilder sb1 = new StringBuilder("Hello World!");
        sb1.insert(6, "Beautiful ");
        System.out.println("После insert: " + sb1);
        
        // Replace - замена части строки
        StringBuilder sb2 = new StringBuilder("Hello World!");
        sb2.replace(6, 11, "Java");
        System.out.println("После replace: " + sb2);
        
        // Delete - удаление части строки
        StringBuilder sb3 = new StringBuilder("Hello Beautiful World!");
        sb3.delete(6, 16); // Удаляем "Beautiful "
        System.out.println("После delete: " + sb3);
        
        // DeleteCharAt - удаление одного символа
        StringBuilder sb4 = new StringBuilder("Hello World!");
        sb4.deleteCharAt(sb4.length() - 1); // Удаляем последний символ
        System.out.println("После deleteCharAt: " + sb4);
        
        // Reverse - переворот строки
        StringBuilder sb5 = new StringBuilder("Hello World!");
        sb5.reverse();
        System.out.println("После reverse: " + sb5);
        
        // SetCharAt - изменение символа по индексу
        StringBuilder sb6 = new StringBuilder("Hello World!");
        sb6.setCharAt(0, 'h'); // Заменяем 'H' на 'h'
        System.out.println("После setCharAt: " + sb6);
        
        // SetLength - изменение длины
        StringBuilder sb7 = new StringBuilder("Hello World!");
        System.out.println("Исходная длина: " + sb7.length());
        
        sb7.setLength(5); // Обрезаем до "Hello"
        System.out.println("После setLength(5): '" + sb7 + "'");
        
        sb7.setLength(10); // Расширяем, заполняя null символами
        System.out.println("После setLength(10): '" + sb7 + "'");
        System.out.println("Длина: " + sb7.length());
    }
    
    public static void main(String[] args) {
        demonstrateModificationMethods();
    }
}
```

### Методы поиска и доступа
```java
public class StringBuilderAccessMethods {
    
    public static void demonstrateAccessMethods() {
        StringBuilder sb = new StringBuilder("Hello World! Hello Java!");
        
        System.out.println("=== Методы поиска и доступа ===");
        System.out.println("Строка: " + sb);
        
        // Доступ к символам
        char firstChar = sb.charAt(0);
        char lastChar = sb.charAt(sb.length() - 1);
        System.out.println("Первый символ: " + firstChar);
        System.out.println("Последний символ: " + lastChar);
        
        // Поиск подстроки
        int firstHello = sb.indexOf("Hello");
        int lastHello = sb.lastIndexOf("Hello");
        System.out.println("Первое вхождение 'Hello': " + firstHello);
        System.out.println("Последнее вхождение 'Hello': " + lastHello);
        
        // Поиск с начальной позиции
        int worldFrom5 = sb.indexOf("World", 5);
        System.out.println("'World' начиная с позиции 5: " + worldFrom5);
        
        // Получение подстроки
        String substring = sb.substring(6, 11); // "World"
        System.out.println("Подстрока (6, 11): " + substring);
        
        // Получение CharSequence (не создает новую строку)
        CharSequence subSequence = sb.subSequence(6, 11);
        System.out.println("SubSequence (6, 11): " + subSequence);
        
        // Проверка кодовых точек Unicode
        System.out.println("\n=== Unicode кодовые точки ===");
        StringBuilder emoji = new StringBuilder("Hello 🌍 World 🚀!");
        System.out.println("Строка с emoji: " + emoji);
        System.out.println("Длина в символах: " + emoji.length());
        System.out.println("Длина в кодовых точках: " + emoji.codePointCount(0, emoji.length()));
        
        // Итерация по кодовым точкам
        for (int i = 0; i < emoji.length();) {
            int codePoint = emoji.codePointAt(i);
            System.out.printf("Позиция %d: U+%04X ('%s')%n", 
                            i, codePoint, Character.toString(codePoint));
            i += Character.charCount(codePoint);
        }
    }
    
    public static void main(String[] args) {
        demonstrateAccessMethods();
    }
}
```

---

## ⚙️ Управление capacity

### Понимание capacity и length
```java
public class StringBuilderCapacityManagement {
    
    public static void demonstrateCapacityBasics() {
        System.out.println("=== Основы capacity ===");
        
        StringBuilder sb = new StringBuilder();
        System.out.println("Начальное состояние:");
        printCapacityInfo(sb);
        
        // Добавляем символы
        sb.append("Hello");
        System.out.println("После 'Hello':");
        printCapacityInfo(sb);
        
        // Добавляем больше символов
        sb.append(" World! This is a longer string");
        System.out.println("После добавления длинной строки:");
        printCapacityInfo(sb);
        
        // Явное увеличение capacity
        sb.ensureCapacity(100);
        System.out.println("После ensureCapacity(100):");
        printCapacityInfo(sb);
        
        // Уменьшение до минимального размера
        sb.trimToSize();
        System.out.println("После trimToSize():");
        printCapacityInfo(sb);
    }
    
    public static void demonstrateCapacityGrowth() {
        System.out.println("\n=== Алгоритм роста capacity ===");
        
        StringBuilder sb = new StringBuilder(5); // Маленький начальный размер
        System.out.println("Начальный capacity: " + sb.capacity());
        
        // Добавляем символы и смотрим как растет capacity
        for (int i = 1; i <= 50; i += 5) {
            sb.append("12345"); // Добавляем 5 символов за раз
            System.out.printf("Длина: %2d, Capacity: %2d (рост: %s)%n", 
                            sb.length(), sb.capacity(),
                            i == 1 ? "начальный" : "x" + String.format("%.1f", sb.capacity() / 5.0));
        }
        
        System.out.println("\n💡 Алгоритм роста: newCapacity = (oldCapacity * 2) + 2");
    }
    
    public static void optimizeCapacityUsage() {
        System.out.println("\n=== Оптимизация использования capacity ===");
        
        // ❌ Плохо - частые расширения
        long startTime = System.currentTimeMillis();
        StringBuilder badSb = new StringBuilder(); // capacity = 16
        for (int i = 0; i < 10000; i++) {
            badSb.append("item").append(i).append(", ");
        }
        long badTime = System.currentTimeMillis() - startTime;
        System.out.println("Без предварительного размера: " + badTime + " мс");
        System.out.println("Итоговый capacity: " + badSb.capacity());
        
        // ✅ Хорошо - заранее заданный размер
        startTime = System.currentTimeMillis();
        StringBuilder goodSb = new StringBuilder(100000); // Достаточный capacity
        for (int i = 0; i < 10000; i++) {
            goodSb.append("item").append(i).append(", ");
        }
        long goodTime = System.currentTimeMillis() - startTime;
        System.out.println("С предварительным размером: " + goodTime + " мс");
        System.out.println("Итоговый capacity: " + goodSb.capacity());
        
        System.out.println("Улучшение производительности: " + 
                          String.format("%.1f%%", ((badTime - goodTime) / (double)badTime) * 100));
    }
    
    private static void printCapacityInfo(StringBuilder sb) {
        System.out.printf("  Length: %d, Capacity: %d, Содержимое: '%s'%n", 
                         sb.length(), sb.capacity(), 
                         sb.length() > 20 ? sb.substring(0, 17) + "..." : sb.toString());
    }
    
    public static void main(String[] args) {
        demonstrateCapacityBasics();
        demonstrateCapacityGrowth();
        optimizeCapacityUsage();
    }
}
```

---

## 💼 Практические примеры

### Построение SQL запросов
```java
public class StringBuilderSQLBuilder {
    
    public static void buildDynamicQuery() {
        System.out.println("=== Построение SQL запросов ===");
        
        // Данные для фильтров
        String tableName = "users";
        String name = "John";
        Integer minAge = 25;
        Integer maxAge = null;
        String city = "Moscow";
        boolean isActive = true;
        
        StringBuilder query = new StringBuilder(200);
        query.append("SELECT id, name, age, city, email FROM ").append(tableName);
        query.append(" WHERE 1=1"); // Удобный трюк для динамических условий
        
        // Динамически добавляем условия
        if (name != null && !name.trim().isEmpty()) {
            query.append(" AND name LIKE '%").append(escapeSql(name)).append("%'");
        }
        
        if (minAge != null) {
            query.append(" AND age >= ").append(minAge);
        }
        
        if (maxAge != null) {
            query.append(" AND age <= ").append(maxAge);
        }
        
        if (city != null && !city.trim().isEmpty()) {
            query.append(" AND city = '").append(escapeSql(city)).append("'");
        }
        
        if (isActive) {
            query.append(" AND status = 'ACTIVE'");
        }
        
        query.append(" ORDER BY name, age");
        query.append(" LIMIT 100");
        
        System.out.println("Построенный запрос:");
        System.out.println(query.toString());
        
        // Пример с JOIN
        buildComplexQuery();
    }
    
    private static void buildComplexQuery() {
        StringBuilder complexQuery = new StringBuilder(500);
        
        complexQuery.append("SELECT u.id, u.name, u.email, p.phone, a.street, a.city")
                   .append(" FROM users u")
                   .append(" LEFT JOIN phones p ON u.id = p.user_id")
                   .append(" LEFT JOIN addresses a ON u.id = a.user_id")
                   .append(" WHERE u.status = 'ACTIVE'")
                   .append(" AND u.created_date >= '2023-01-01'")
                   .append(" GROUP BY u.id")
                   .append(" HAVING COUNT(p.id) > 0")
                   .append(" ORDER BY u.name");
        
        System.out.println("\nСложный запрос с JOIN:");
        System.out.println(complexQuery.toString());
    }
    
    private static String escapeSql(String input) {
        // Простое экранирование одинарных кавычек
        return input.replace("'", "''");
    }
    
    public static void main(String[] args) {
        buildDynamicQuery();
    }
}
```

### Генерация HTML
```java
public class StringBuilderHTMLGenerator {
    
    public static void generateHTML() {
        System.out.println("=== Генерация HTML ===");
        
        // Данные для таблицы
        String[][] users = {
            {"1", "Иван Петров", "ivan@example.com", "Moscow"},
            {"2", "Maria Smith", "maria@example.com", "New York"}, 
            {"3", "王小明", "wang@example.com", "Beijing"}
        };
        
        StringBuilder html = new StringBuilder(1000);
        
        // HTML каркас
        html.append("<!DOCTYPE html>\n")
            .append("<html lang=\"ru\">\n")
            .append("<head>\n")
            .append("    <meta charset=\"UTF-8\">\n")
            .append("    <title>Список пользователей</title>\n")
            .append("    <style>\n")
            .append("        table { border-collapse: collapse; width: 100%; }\n")
            .append("        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }\n")
            .append("        th { background-color: #f2f2f2; }\n")
            .append("        .even { background-color: #f9f9f9; }\n")
            .append("    </style>\n")
            .append("</head>\n")
            .append("<body>\n")
            .append("    <h1>Список пользователей</h1>\n")
            .append("    <table>\n")
            .append("        <thead>\n")
            .append("            <tr><th>ID</th><th>Имя</th><th>Email</th><th>Город</th></tr>\n")
            .append("        </thead>\n")
            .append("        <tbody>\n");
        
        // Генерируем строки таблицы
        for (int i = 0; i < users.length; i++) {
            String[] user = users[i];
            html.append("            <tr");
            
            if (i % 2 == 1) {
                html.append(" class=\"even\"");
            }
            
            html.append(">\n");
            
            for (String field : user) {
                html.append("                <td>").append(escapeHtml(field)).append("</td>\n");
            }
            
            html.append("            </tr>\n");
        }
        
        html.append("        </tbody>\n")
            .append("    </table>\n")
            .append("    <p>Всего пользователей: ").append(users.length).append("</p>\n")
            .append("</body>\n")
            .append("</html>");
        
        System.out.println("Сгенерированный HTML:");
        System.out.println(html.toString());
        
        // Сохранение в файл (в реальном приложении)
        System.out.println("\nРазмер сгенерированного HTML: " + html.length() + " символов");
    }
    
    private static String escapeHtml(String input) {
        return input.replace("&", "&amp;")
                   .replace("<", "&lt;")
                   .replace(">", "&gt;")
                   .replace("\"", "&quot;")
                   .replace("'", "&#x27;");
    }
    
    public static void main(String[] args) {
        generateHTML();
    }
}
```

### Работа с CSV
```java
public class StringBuilderCSVProcessor {
    
    public static void generateCSV() {
        System.out.println("=== Генерация CSV ===");
        
        // Данные для экспорта
        Object[][] data = {
            {"ID", "Name", "Age", "Salary", "Department"},
            {1, "John Doe", 30, 75000.50, "Engineering"},
            {2, "Jane, Smith", 28, 65000.00, "Marketing"}, // Запятая в имени
            {3, "Bob \"The Builder\"", 35, 80000.75, "Construction"}, // Кавычки в имени
            {4, "Alice\nWonderland", 25, 55000.00, "HR"} // Перенос строки в имени
        };
        
        StringBuilder csv = new StringBuilder(500);
        
        for (int i = 0; i < data.length; i++) {
            Object[] row = data[i];
            
            for (int j = 0; j < row.length; j++) {
                if (j > 0) {
                    csv.append(",");
                }
                
                String field = String.valueOf(row[j]);
                csv.append(escapeCsvField(field));
            }
            
            csv.append("\n");
        }
        
        System.out.println("Сгенерированный CSV:");
        System.out.println(csv.toString());
    }
    
    private static String escapeCsvField(String field) {
        // Если поле содержит запятую, кавычки или перенос строки - заключаем в кавычки
        if (field.contains(",") || field.contains("\"") || field.contains("\n") || field.contains("\r")) {
            StringBuilder escaped = new StringBuilder(field.length() + 10);
            escaped.append("\"");
            
            // Экранируем кавычки удвоением
            for (int i = 0; i < field.length(); i++) {
                char c = field.charAt(i);
                if (c == '"') {
                    escaped.append("\"\"");
                } else {
                    escaped.append(c);
                }
            }
            
            escaped.append("\"");
            return escaped.toString();
        }
        
        return field;
    }
    
    public static void parseCSV() {
        System.out.println("\n=== Парсинг CSV ===");
        
        String csvData = "Name,Age,\"City, Country\"\n" +
                        "John,30,\"New York, USA\"\n" +
                        "\"Jane, Doe\",25,\"Moscow, Russia\"\n" +
                        "Bob,35,\"London, UK\"";
        
        String[] lines = csvData.split("\n");
        
        for (int i = 0; i < lines.length; i++) {
            System.out.println("Строка " + i + ": " + parseCsvLine(lines[i]));
        }
    }
    
    private static String parseCsvLine(String line) {
        StringBuilder result = new StringBuilder();
        result.append("[");
        
        boolean inQuotes = false;
        StringBuilder field = new StringBuilder();
        
        for (int i = 0; i < line.length(); i++) {
            char c = line.charAt(i);
            
            if (c == '"') {
                if (inQuotes && i + 1 < line.length() && line.charAt(i + 1) == '"') {
                    // Двойная кавычка - это экранированная кавычка
                    field.append('"');
                    i++; // Пропускаем следующую кавычку
                } else {
                    // Переключаем состояние "в кавычках"
                    inQuotes = !inQuotes;
                }
            } else if (c == ',' && !inQuotes) {
                // Конец поля
                if (result.length() > 1) {
                    result.append(", ");
                }
                result.append("'").append(field.toString()).append("'");
                field.setLength(0);
            } else {
                field.append(c);
            }
        }
        
        // Добавляем последнее поле
        if (result.length() > 1) {
            result.append(", ");
        }
        result.append("'").append(field.toString()).append("'");
        result.append("]");
        
        return result.toString();
    }
    
    public static void main(String[] args) {
        generateCSV();
        parseCSV();
    }
}
```

---

## ⚡ Производительность

### Бенчмарки и сравнения
```java
public class StringBuilderPerformance {
    
    public static void performBenchmarks() {
        System.out.println("=== Бенчмарки производительности ===");
        
        int iterations = 50000;
        String[] testStrings = {"Hello", " ", "World", "!", " ", "Java", " ", "Programming"};
        
        // 1. Конкатенация через String +
        long startTime = System.currentTimeMillis();
        String result1 = "";
        for (int i = 0; i < iterations; i++) {
            for (String s : testStrings) {
                result1 += s;
            }
            result1 = ""; // Сброс для следующей итерации
        }
        long stringConcatTime = System.currentTimeMillis() - startTime;
        
        // 2. StringBuilder
        startTime = System.currentTimeMillis();
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < iterations; i++) {
            sb.setLength(0); // Сброс
            for (String s : testStrings) {
                sb.append(s);
            }
            String result2 = sb.toString();
        }
        long sbTime = System.currentTimeMillis() - startTime;
        
        // 3. StringBuilder с предустановленной емкостью
        startTime = System.currentTimeMillis();
        StringBuilder sbOptimized = new StringBuilder(50); // Предустановленная емкость
        for (int i = 0; i < iterations; i++) {
            sbOptimized.setLength(0);
            for (String s : testStrings) {
                sbOptimized.append(s);
            }
            String result3 = sbOptimized.toString();
        }
        long sbOptimizedTime = System.currentTimeMillis() - startTime;
        
        // 4. StringJoiner (Java 8+)
        startTime = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            StringJoiner joiner = new StringJoiner("");
            for (String s : testStrings) {
                joiner.add(s);
            }
            String result4 = joiner.toString();
        }
        long stringJoinerTime = System.currentTimeMillis() - startTime;
        
        // 5. String.join (Java 8+)
        startTime = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            String result5 = String.join("", testStrings);
        }
        long stringJoinTime = System.currentTimeMillis() - startTime;
        
        // Результаты
        System.out.printf("Результаты для %d итераций:%n", iterations);
        System.out.printf("String конкатенация (+):     %d мс%n", stringConcatTime);
        System.out.printf("StringBuilder:               %d мс (в %.1fx быстрее)%n", 
                         sbTime, (double)stringConcatTime / sbTime);
        System.out.printf("StringBuilder оптимизирован: %d мс (в %.1fx быстрее)%n", 
                         sbOptimizedTime, (double)stringConcatTime / sbOptimizedTime);
        System.out.printf("StringJoiner:                %d мс (в %.1fx быстрее)%n", 
                         stringJoinerTime, (double)stringConcatTime / stringJoinerTime);
        System.out.printf("String.join:                 %d мс (в %.1fx быстрее)%n", 
                         stringJoinTime, (double)stringConcatTime / stringJoinTime);
    }
    
    public static void memoryUsageDemo() {
        System.out.println("\n=== Использование памяти ===");
        
        Runtime runtime = Runtime.getRuntime();
        
        // Измеряем память до
        runtime.gc(); // Принудительная сборка мусора
        long memoryBefore = runtime.totalMemory() - runtime.freeMemory();
        
        // Создаем много StringBuilder объектов
        StringBuilder[] builders = new StringBuilder[10000];
        for (int i = 0; i < builders.length; i++) {
            builders[i] = new StringBuilder(100);
            builders[i].append("Test string ").append(i);
        }
        
        long memoryAfter = runtime.totalMemory() - runtime.freeMemory();
        long memoryUsed = memoryAfter - memoryBefore;
        
        System.out.printf("Память до: %d KB%n", memoryBefore / 1024);
        System.out.printf("Память после: %d KB%n", memoryAfter / 1024);
        System.out.printf("Использовано: %d KB%n", memoryUsed / 1024);
        System.out.printf("В среднем на объект: %.2f байт%n", (double)memoryUsed / builders.length);
        
        // Освобождаем память
        builders = null;
        runtime.gc();
    }
    
    public static void main(String[] args) {
        performBenchmarks();
        memoryUsageDemo();
    }
}
```

---

## 🏆 Лучшие практики

### Рекомендации по использованию
```java
public class StringBuilderBestPractices {
    
    // ✅ Хорошие практики
    public static void goodPractices() {
        System.out.println("=== Лучшие практики StringBuilder ===");
        
        // 1. Предварительно устанавливайте capacity для известного размера
        List<String> items = Arrays.asList("item1", "item2", "item3", "item4", "item5");
        int estimatedLength = items.size() * 10; // Примерная оценка
        StringBuilder sb1 = new StringBuilder(estimatedLength);
        
        for (String item : items) {
            sb1.append(item).append(", ");
        }
        // Удаляем последнюю запятую
        if (sb1.length() > 0) {
            sb1.setLength(sb1.length() - 2);
        }
        System.out.println("Список: " + sb1);
        
        // 2. Используйте method chaining для читаемости
        StringBuilder sb2 = new StringBuilder(100)
                .append("Method chaining ")
                .append("makes code ")
                .append("more readable ")
                .append("and fluent");
        System.out.println("Chaining: " + sb2);
        
        // 3. Переиспользуйте StringBuilder для множественных операций
        StringBuilder reusableBuilder = new StringBuilder(200);
        
        for (int i = 1; i <= 3; i++) {
            reusableBuilder.setLength(0); // Сброс содержимого
            reusableBuilder.append("Processing item #").append(i)
                          .append(" at ").append(System.currentTimeMillis());
            
            System.out.println("Reusable: " + reusableBuilder);
            
            // Симуляция работы
            try { Thread.sleep(100); } catch (InterruptedException e) {}
        }
        
        // 4. Используйте для форматирования сложных строк
        StringBuilder formatted = formatUserInfo(
            "Иван Петров", "ivan@example.com", 30, "Разработчик");
        System.out.println("Formatted: " + formatted);
        
        // 5. Эффективная работа с большими данными
        processLargeDataSet();
    }
    
    private static StringBuilder formatUserInfo(String name, String email, int age, String position) {
        StringBuilder info = new StringBuilder(200);
        
        info.append("╔══════════════════════════════════╗\n")
            .append("║ Информация о пользователе        ║\n")
            .append("╠══════════════════════════════════╣\n")
            .append(String.format("║ Имя: %-25s║%n", name))
            .append(String.format("║ Email: %-23s║%n", email))
            .append(String.format("║ Возраст: %-21d║%n", age))
            .append(String.format("║ Должность: %-19s║%n", position))
            .append("╚══════════════════════════════════╝");
        
        return info;
    }
    
    private static void processLargeDataSet() {
        System.out.println("\n--- Обработка больших данных ---");
        
        // Имитируем обработку большого количества записей
        int recordCount = 10000;
        StringBuilder logBuilder = new StringBuilder(recordCount * 50);
        
        long startTime = System.currentTimeMillis();
        
        for (int i = 0; i < recordCount; i++) {
            logBuilder.append("Record #").append(i)
                     .append(": processed at ").append(System.currentTimeMillis())
                     .append(" - status: OK\n");
            
            // Периодически выводим прогресс и очищаем буфер для экономии памяти
            if (i % 1000 == 0 && i > 0) {
                // В реальном приложении здесь был бы вывод в файл или БД
                // System.out.print(logBuilder.toString()); // Закомментировано для экономии вывода
                logBuilder.setLength(0); // Очищаем буфер
                
                System.out.printf("Обработано %d записей...%n", i);
            }
        }
        
        long duration = System.currentTimeMillis() - startTime;
        System.out.printf("Обработка %d записей завершена за %d мс%n", recordCount, duration);
    }
    
    // ❌ Плохие практики (антипаттерны)
    public static void badPractices() {
        System.out.println("\n=== Антипаттерны (плохие практики) ===");
        
        // ❌ Плохо: создание нового StringBuilder для каждой мелкой операции
        List<String> words = Arrays.asList("bad", "practice", "example");
        String result = "";
        for (String word : words) {
            StringBuilder temp = new StringBuilder(); // Плохо! Создаем каждый раз
            temp.append(result).append(word).append(" ");
            result = temp.toString();
        }
        System.out.println("Bad practice result: " + result.trim());
        
        // ❌ Плохо: не учитываем capacity для больших строк
        StringBuilder sb = new StringBuilder(); // capacity = 16
        for (int i = 0; i < 1000; i++) {
            sb.append("This is a long string that causes capacity expansion ");
        }
        System.out.println("Final capacity after many expansions: " + sb.capacity());
        
        // ❌ Плохо: преобразование в String на каждой итерации
        StringBuilder sb2 = new StringBuilder();
        for (int i = 0; i < 5; i++) {
            sb2.append("item").append(i);
            
            // Плохо: промежуточное преобразование в String
            String intermediate = sb2.toString();
            if (intermediate.length() > 10) {
                // Что-то делаем со строкой...
            }
        }
        
        System.out.println("💡 Избегайте этих практик для лучшей производительности!");
    }
    
    // Утилитарные методы
    public static StringBuilder join(String delimiter, String... elements) {
        if (elements.length == 0) {
            return new StringBuilder();
        }
        
        StringBuilder result = new StringBuilder(elements.length * 20);
        result.append(elements[0]);
        
        for (int i = 1; i < elements.length; i++) {
            result.append(delimiter).append(elements[i]);
        }
        
        return result;
    }
    
    public static StringBuilder repeat(String str, int count) {
        if (count <= 0) {
            return new StringBuilder();
        }
        
        StringBuilder result = new StringBuilder(str.length() * count);
        for (int i = 0; i < count; i++) {
            result.append(str);
        }
        
        return result;
    }
    
    public static void utilityMethodsDemo() {
        System.out.println("\n=== Утилитарные методы ===");
        
        StringBuilder joined = join(" | ", "Java", "Python", "JavaScript", "Go");
        System.out.println("Joined: " + joined);
        
        StringBuilder repeated = repeat("Hello ", 5);
        System.out.println("Repeated: " + repeated);
        
        // Создание ASCII арта
        StringBuilder border = repeat("=", 50);
        System.out.println(border);
        System.out.println("      STRINGBUILDER BEST PRACTICES DEMO");
        System.out.println(border);
    }
    
    public static void main(String[] args) {
        goodPractices();
        badPractices();
        utilityMethodsDemo();
    }
}
```

---

## 🔗 Связанные темы
- [[String-Code|String API]] - работа с неизменяемыми строками
- [[Arrays-Code|Arrays-Code]] - работа с массивами символов
- [[Collections-Overview|Collections]] - альтернативы для объединения строк
- [[Performance-Tips|Оптимизация производительности]] - общие принципы оптимизации