# Switch-Case в Java
*Управляющие конструкции для множественного ветвления*

#java #control-flow #switch #pattern-matching #expressions

---

## 📋 Содержание
- [[#Основы Switch-Case]]
- [[#Классический Switch]]
- [[#Современный Switch (Java 12+)]]
- [[#Switch Expressions]]
- [[#Pattern Matching]]
- [[#Производительность]]
- [[#Лучшие практики]]

---

## 🎯 Основы Switch-Case

> [!info] Switch-case - это управляющая конструкция, которая позволяет выбирать одну из множества альтернатив на основе значения переменной

### Поддерживаемые типы данных
- **Примитивы**: byte, short, char, int
- **Обертки примитивов**: Byte, Short, Character, Integer  
- **Перечисления (enum)**
- **String** (с Java 7)
- **Pattern matching** (с Java 17+)

```java
public class SwitchBasicsDemo {
    
    public static void demonstrateSupportedTypes() {
        System.out.println("=== Поддерживаемые типы в switch ===");
        
        // 1. int
        int day = 3;
        switchWithInt(day);
        
        // 2. String (Java 7+)
        String dayName = "Wednesday";
        switchWithString(dayName);
        
        // 3. enum
        DayOfWeek enumDay = DayOfWeek.WEDNESDAY;
        switchWithEnum(enumDay);
        
        // 4. Character
        char grade = 'B';
        switchWithChar(grade);
    }
    
    private static void switchWithInt(int day) {
        System.out.println("\n--- Switch с int ---");
        String dayName;
        
        switch (day) {
            case 1:
                dayName = "Понедельник";
                break;
            case 2:
                dayName = "Вторник";
                break;
            case 3:
                dayName = "Среда";
                break;
            case 4:
                dayName = "Четверг";
                break;
            case 5:
                dayName = "Пятница";
                break;
            case 6:
            case 7:
                dayName = "Выходной";
                break;
            default:
                dayName = "Неизвестный день";
                break;
        }
        
        System.out.println("День " + day + ": " + dayName);
    }
    
    private static void switchWithString(String day) {
        System.out.println("\n--- Switch с String ---");
        String season;
        
        switch (day.toLowerCase()) {
            case "monday":
            case "tuesday":
            case "wednesday":
            case "thursday":
            case "friday":
                season = "Рабочий день";
                break;
            case "saturday":
            case "sunday":
                season = "Выходной";
                break;
            default:
                season = "Неизвестный день";
                break;
        }
        
        System.out.println(day + " - " + season);
    }
    
    private static void switchWithEnum(DayOfWeek day) {
        System.out.println("\n--- Switch с enum ---");
        boolean isWorkday;
        
        switch (day) {
            case MONDAY:
            case TUESDAY:
            case WEDNESDAY:
            case THURSDAY:
            case FRIDAY:
                isWorkday = true;
                break;
            case SATURDAY:
            case SUNDAY:
                isWorkday = false;
                break;
            default:
                isWorkday = false;
                break;
        }
        
        System.out.println(day + " - " + (isWorkday ? "рабочий день" : "выходной"));
    }
    
    private static void switchWithChar(char grade) {
        System.out.println("\n--- Switch с char ---");
        String description;
        
        switch (grade) {
            case 'A':
            case 'a':
                description = "Отлично";
                break;
            case 'B':
            case 'b':
                description = "Хорошо";
                break;
            case 'C':
            case 'c':
                description = "Удовлетворительно";
                break;
            case 'D':
            case 'd':
                description = "Неудовлетворительно";
                break;
            case 'F':
            case 'f':
                description = "Провал";
                break;
            default:
                description = "Неизвестная оценка";
                break;
        }
        
        System.out.println("Оценка " + grade + ": " + description);
    }
    
    enum DayOfWeek {
        MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
    }
    
    public static void main(String[] args) {
        demonstrateSupportedTypes();
    }
}
```

---

## 🔄 Классический Switch

### Основной синтаксис и fall-through
```java
public class ClassicSwitchDemo {
    
    public static void demonstrateFallThrough() {
        System.out.println("=== Fall-through поведение ===");
        
        int month = 2;
        System.out.println("Месяц: " + month);
        
        switch (month) {
            case 12:
            case 1:
            case 2:
                System.out.println("Зима");
                System.out.println("Холодно на улице");
                break;
            case 3:
            case 4:
            case 5:
                System.out.println("Весна");
                System.out.println("Природа просыпается");
                break;
            case 6:
            case 7:
            case 8:
                System.out.println("Лето");
                System.out.println("Пора отпусков");
                break;
            case 9:
            case 10:
            case 11:
                System.out.println("Осень");
                System.out.println("Время урожая");
                break;
            default:
                System.out.println("Неизвестный месяц");
                break;
        }
        
        // Демонстрация случайного fall-through (частая ошибка)
        demonstrateAccidentalFallThrough();
        
        // Демонстрация преднамеренного fall-through
        demonstrateIntentionalFallThrough();
    }
    
    private static void demonstrateAccidentalFallThrough() {
        System.out.println("\n--- Случайный fall-through (ошибка) ---");
        
        int errorCode = 404;
        
        switch (errorCode) {
            case 200:
                System.out.println("OK");
                // ❌ Забыли break!
            case 404:
                System.out.println("Not Found");
                break;
            case 500:
                System.out.println("Internal Server Error");
                break;
            default:
                System.out.println("Unknown error");
        }
        
        System.out.println("Результат: выведется и 'OK' и 'Not Found'");
    }
    
    private static void demonstrateIntentionalFallThrough() {
        System.out.println("\n--- Преднамеренный fall-through ---");
        
        char character = 'B';
        System.out.println("Символ: " + character);
        
        switch (character) {
            case 'A':
            case 'E':
            case 'I':
            case 'O':
            case 'U':
            case 'a':
            case 'e':
            case 'i':
            case 'o':
            case 'u':
                System.out.println("Гласная буква");
                break;
            case 'Y':
            case 'y':
                System.out.println("Иногда гласная, иногда согласная");
                break;
            default:
                System.out.println("Согласная буква");
                break;
        }
    }
    
    public static void demonstrateComplexLogic() {
        System.out.println("\n=== Сложная логика в классическом switch ===");
        
        UserRole role = UserRole.MANAGER;
        String department = "IT";
        
        switch (role) {
            case ADMIN:
                System.out.println("Полный доступ ко всем системам");
                grantSystemAccess("ALL");
                break;
                
            case MANAGER:
                System.out.println("Доступ к управленческим функциям");
                grantSystemAccess("MANAGEMENT");
                
                // Дополнительные проверки для менеджеров
                if ("IT".equals(department)) {
                    System.out.println("Дополнительный доступ к техническим системам");
                    grantSystemAccess("TECHNICAL");
                }
                break;
                
            case USER:
                System.out.println("Базовый доступ");
                grantSystemAccess("BASIC");
                break;
                
            case GUEST:
                System.out.println("Ограниченный доступ только на чтение");
                grantSystemAccess("READ_ONLY");
                break;
                
            default:
                System.out.println("Неизвестная роль, доступ запрещен");
                throw new IllegalArgumentException("Unknown role: " + role);
        }
    }
    
    private static void grantSystemAccess(String level) {
        System.out.println("  → Предоставлен доступ уровня: " + level);
    }
    
    enum UserRole {
        ADMIN, MANAGER, USER, GUEST
    }
    
    public static void main(String[] args) {
        demonstrateFallThrough();
        demonstrateComplexLogic();
    }
}
```

---

## 🆕 Современный Switch (Java 12+)

### Switch Expressions
```java
public class ModernSwitchDemo {
    
    public static void demonstrateSwitchExpressions() {
        System.out.println("=== Switch Expressions (Java 12+) ===");
        
        // Старый стиль vs новый стиль
        int dayOfWeek = 3;
        
        // ❌ Классический switch (многословный)
        String dayTypeOld;
        switch (dayOfWeek) {
            case 1:
            case 7:
                dayTypeOld = "Weekend";
                break;
            case 2:
            case 3:
            case 4:
            case 5:
            case 6:
                dayTypeOld = "Weekday";
                break;
            default:
                dayTypeOld = "Invalid day";
                break;
        }
        
        // ✅ Switch expression (компактный)
        String dayTypeNew = switch (dayOfWeek) {
            case 1, 7 -> "Weekend";
            case 2, 3, 4, 5, 6 -> "Weekday";
            default -> "Invalid day";
        };
        
        System.out.println("Классический switch: " + dayTypeOld);
        System.out.println("Switch expression: " + dayTypeNew);
        
        demonstrateYieldKeyword();
        demonstrateMultipleStatements();
    }
    
    private static void demonstrateYieldKeyword() {
        System.out.println("\n--- Ключевое слово yield ---");
        
        Grade grade = Grade.B;
        
        String description = switch (grade) {
            case A -> {
                System.out.println("Анализирую оценку A...");
                yield "Отличная работа!";
            }
            case B -> {
                System.out.println("Анализирую оценку B...");
                yield "Хорошая работа!";
            }
            case C -> {
                System.out.println("Анализирую оценку C...");
                yield "Удовлетворительно";
            }
            case D, F -> {
                System.out.println("Анализирую низкую оценку...");
                String message = grade == Grade.D ? "Нужно постараться" : "Необходимо переделать";
                yield message;
            }
        };
        
        System.out.println("Итоговое описание: " + description);
    }
    
    private static void demonstrateMultipleStatements() {
        System.out.println("\n--- Множественные выражения ---");
        
        Operation operation = Operation.DIVIDE;
        double a = 15.0;
        double b = 3.0;
        
        double result = switch (operation) {
            case ADD -> {
                System.out.println("Выполняем сложение");
                yield a + b;
            }
            case SUBTRACT -> {
                System.out.println("Выполняем вычитание");
                yield a - b;
            }
            case MULTIPLY -> {
                System.out.println("Выполняем умножение");
                yield a * b;
            }
            case DIVIDE -> {
                System.out.println("Выполняем деление");
                if (b == 0) {
                    System.err.println("Ошибка: деление на ноль!");
                    yield Double.NaN;
                }
                yield a / b;
            }
            case POWER -> {
                System.out.println("Выполняем возведение в степень");
                yield Math.pow(a, b);
            }
        };
        
        System.out.printf("Результат %.1f %s %.1f = %.2f%n", a, operation.symbol, b, result);
    }
    
    public static void demonstrateAdvancedFeatures() {
        System.out.println("\n=== Продвинутые возможности ===");
        
        // Switch expression в качестве аргумента метода
        processResult(
            switch (getRandomGrade()) {
                case A -> "Excellent";
                case B -> "Good";  
                case C -> "Average";
                case D -> "Below average";
                case F -> "Failed";
            }
        );
        
        // Switch expression для инициализации final переменной
        final String message = switch (getCurrentHour()) {
            case 6, 7, 8, 9, 10, 11 -> "Доброе утро!";
            case 12, 13, 14, 15, 16, 17 -> "Добрый день!";
            case 18, 19, 20, 21, 22 -> "Добрый вечер!";
            default -> "Доброй ночи!";
        };
        
        System.out.println("Приветствие: " + message);
        
        // Вложенные switch expressions
        demonstrateNestedSwitch();
    }
    
    private static void demonstrateNestedSwitch() {
        System.out.println("\n--- Вложенные switch выражения ---");
        
        Season season = Season.WINTER;
        Weather weather = Weather.SUNNY;
        
        String activity = switch (season) {
            case SPRING, SUMMER -> switch (weather) {
                case SUNNY -> "Пикник в парке";
                case RAINY -> "Кафе с верандой";
                case CLOUDY -> "Прогулка по городу";
            };
            case AUTUMN -> switch (weather) {
                case SUNNY -> "Фотосессия с листвой";
                case RAINY -> "Музей или галерея";
                case CLOUDY -> "Уютное кафе";
            };
            case WINTER -> switch (weather) {
                case SUNNY -> "Лыжи или сноуборд";
                case RAINY -> "Домашний кинотеатр";
                case CLOUDY -> "Торговый центр";
            };
        };
        
        System.out.printf("В %s при %s погоде: %s%n", 
                         season.name().toLowerCase(), 
                         weather.name().toLowerCase(), 
                         activity);
    }
    
    // Вспомогательные методы
    private static Grade getRandomGrade() {
        Grade[] grades = Grade.values();
        return grades[(int)(Math.random() * grades.length)];
    }
    
    private static int getCurrentHour() {
        return java.time.LocalTime.now().getHour();
    }
    
    private static void processResult(String result) {
        System.out.println("Обработка результата: " + result);
    }
    
    enum Grade { A, B, C, D, F }
    
    enum Operation {
        ADD("+"), SUBTRACT("-"), MULTIPLY("*"), DIVIDE("/"), POWER("^");
        
        final String symbol;
        
        Operation(String symbol) {
            this.symbol = symbol;
        }
    }
    
    enum Season { SPRING, SUMMER, AUTUMN, WINTER }
    enum Weather { SUNNY, RAINY, CLOUDY }
    
    public static void main(String[] args) {
        demonstrateSwitchExpressions();
        demonstrateAdvancedFeatures();
    }
}
```

---

## 🎯 Pattern Matching (Java 17+)

### Паттерны в Switch
```java
public class SwitchPatternMatching {
    
    // Требует Java 17+ с preview features
    public static void demonstratePatternMatching() {
        System.out.println("=== Pattern Matching в Switch ===");
        
        // Примеры различных типов данных
        Object[] testData = {
            "Hello World",
            42,
            3.14,
            new java.util.ArrayList<String>(),
            null,
            new Person("Alice", 30),
            new Student("Bob", 20, "Computer Science")
        };
        
        for (Object obj : testData) {
            String result = analyzeObject(obj);
            System.out.println("Объект: " + obj + " → " + result);
        }
        
        demonstrateGuardedPatterns();
    }
    
    // Pattern matching with type patterns
    private static String analyzeObject(Object obj) {
        return switch (obj) {
            case null -> "Это null значение";
            
            case String s when s.length() > 10 -> 
                "Длинная строка: " + s.substring(0, 10) + "...";
                
            case String s -> 
                "Строка длиной " + s.length() + ": '" + s + "'";
            
            case Integer i when i > 100 -> 
                "Большое число: " + i;
                
            case Integer i when i < 0 -> 
                "Отрицательное число: " + i;
                
            case Integer i -> 
                "Число: " + i;
            
            case Double d -> 
                "Дробное число: " + String.format("%.2f", d);
            
            case java.util.List<?> list when list.isEmpty() -> 
                "Пустой список";
                
            case java.util.List<?> list -> 
                "Список из " + list.size() + " элементов";
            
            case Person(var name, var age) when age >= 18 -> 
                "Совершеннолетний: " + name + " (" + age + " лет)";
                
            case Person(var name, var age) -> 
                "Несовершеннолетний: " + name + " (" + age + " лет)";
            
            default -> 
                "Неизвестный тип: " + obj.getClass().getSimpleName();
        };
    }
    
    private static void demonstrateGuardedPatterns() {
        System.out.println("\n--- Защищенные паттерны (Guard conditions) ---");
        
        Shape[] shapes = {
            new Circle(5.0),
            new Rectangle(3.0, 4.0),
            new Rectangle(5.0, 5.0), // Квадрат
            new Triangle(3.0, 4.0, 5.0)
        };
        
        for (Shape shape : shapes) {
            String description = switch (shape) {
                case Circle(var radius) when radius > 10 -> 
                    "Большой круг радиусом " + radius;
                    
                case Circle(var radius) -> 
                    "Круг радиусом " + radius;
                
                case Rectangle(var width, var height) when width == height -> 
                    "Квадрат со стороной " + width;
                    
                case Rectangle(var width, var height) when width > height -> 
                    "Широкий прямоугольник " + width + "×" + height;
                    
                case Rectangle(var width, var height) -> 
                    "Высокий прямоугольник " + width + "×" + height;
                
                case Triangle(var a, var b, var c) when isRightTriangle(a, b, c) -> 
                    "Прямоугольный треугольник со сторонами " + a + ", " + b + ", " + c;
                    
                case Triangle(var a, var b, var c) -> 
                    "Треугольник со сторонами " + a + ", " + b + ", " + c;
                    
                default -> "Неизвестная фигура";
            };
            
            System.out.println(description);
        }
    }
    
    private static boolean isRightTriangle(double a, double b, double c) {
        // Проверяем теорему Пифагора для всех комбинаций
        double[] sides = {a, b, c};
        java.util.Arrays.sort(sides);
        return Math.abs(sides[0]*sides[0] + sides[1]*sides[1] - sides[2]*sides[2]) < 0.001;
    }
    
    public static void demonstrateComplexPatterns() {
        System.out.println("\n=== Сложные паттерны ===");
        
        // Работа с вложенными структурами данных
        var transactions = java.util.List.of(
            new Transaction("DEPOSIT", 1000.0, "APPROVED"),
            new Transaction("WITHDRAWAL", 500.0, "APPROVED"),
            new Transaction("WITHDRAWAL", 2000.0, "REJECTED"),
            new Transaction("TRANSFER", 750.0, "PENDING")
        );
        
        for (Transaction tx : transactions) {
            String result = switch (tx) {
                case Transaction(var type, var amount, var status) 
                    when "APPROVED".equals(status) && "DEPOSIT".equals(type) -> 
                    "✅ Пополнение на " + amount + " успешно обработано";
                
                case Transaction(var type, var amount, var status) 
                    when "APPROVED".equals(status) && "WITHDRAWAL".equals(type) -> 
                    "✅ Снятие " + amount + " успешно обработано";
                
                case Transaction(var type, var amount, "REJECTED") -> 
                    "❌ Операция " + type + " на сумму " + amount + " отклонена";
                
                case Transaction(var type, var amount, "PENDING") -> 
                    "⏳ Операция " + type + " на сумму " + amount + " ожидает обработки";
                
                default -> "❓ Неизвестный тип транзакции";
            };
            
            System.out.println(result);
        }
    }
    
    // Records для pattern matching
    record Person(String name, int age) {}
    record Student(String name, int age, String major) {}
    record Transaction(String type, double amount, String status) {}
    
    // Sealed classes для ограничения иерархии
    sealed interface Shape permits Circle, Rectangle, Triangle {}
    record Circle(double radius) implements Shape {}
    record Rectangle(double width, double height) implements Shape {}
    record Triangle(double a, double b, double c) implements Shape {}
    
    public static void main(String[] args) {
        // Примечание: для запуска нужна Java 17+ с включенными preview features
        // javac --enable-preview --source 17 SwitchPatternMatching.java
        // java --enable-preview SwitchPatternMatching
        
        System.out.println("Для запуска pattern matching требуется Java 17+ с preview features");
        // demonstratePatternMatching();
        // demonstrateComplexPatterns();
    }
}
```

---

## ⚡ Производительность

### Оптимизация Switch
```java
public class SwitchPerformance {
    
    public static void performanceComparison() {
        System.out.println("=== Сравнение производительности ===");
        
        int iterations = 10_000_000;
        
        // Тест с небольшим количеством случаев
        testSmallSwitch(iterations);
        
        // Тест с большим количеством случаев
        testLargeSwitch(iterations);
        
        // Тест плотных vs разреженных значений
        testDenseVsSparseValues(iterations);
        
        // Switch vs if-else chains
        testSwitchVsIfElse(iterations);
    }
    
    private static void testSmallSwitch(int iterations) {
        System.out.println("\n--- Малый switch (5 случаев) ---");
        
        // Switch statement
        long startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            int value = i % 5;
            String result = processSmallSwitch(value);
        }
        long switchTime = System.nanoTime() - startTime;
        
        // If-else chain
        startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            int value = i % 5;
            String result = processSmallIfElse(value);
        }
        long ifElseTime = System.nanoTime() - startTime;
        
        System.out.printf("Switch: %.2f мс%n", switchTime / 1_000_000.0);
        System.out.printf("If-else: %.2f мс%n", ifElseTime / 1_000_000.0);
        System.out.printf("Разница: %.1fx%n", (double)ifElseTime / switchTime);
    }
    
    private static String processSmallSwitch(int value) {
        return switch (value) {
            case 0 -> "Zero";
            case 1 -> "One";
            case 2 -> "Two";
            case 3 -> "Three";
            case 4 -> "Four";
            default -> "Other";
        };
    }
    
    private static String processSmallIfElse(int value) {
        if (value == 0) return "Zero";
        else if (value == 1) return "One";
        else if (value == 2) return "Two";
        else if (value == 3) return "Three";
        else if (value == 4) return "Four";
        else return "Other";
    }
    
    private static void testLargeSwitch(int iterations) {
        System.out.println("\n--- Большой switch (20 случаев) ---");
        
        // Switch statement
        long startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            int value = i % 20;
            String result = processLargeSwitch(value);
        }
        long switchTime = System.nanoTime() - startTime;
        
        // If-else chain
        startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            int value = i % 20;
            String result = processLargeIfElse(value);
        }
        long ifElseTime = System.nanoTime() - startTime;
        
        System.out.printf("Switch: %.2f мс%n", switchTime / 1_000_000.0);
        System.out.printf("If-else: %.2f мс%n", ifElseTime / 1_000_000.0);
        System.out.printf("Разница: %.1fx%n", (double)ifElseTime / switchTime);
    }
    
    private static String processLargeSwitch(int value) {
        return switch (value) {
            case 0 -> "Zero"; case 1 -> "One"; case 2 -> "Two"; case 3 -> "Three";
            case 4 -> "Four"; case 5 -> "Five"; case 6 -> "Six"; case 7 -> "Seven";
            case 8 -> "Eight"; case 9 -> "Nine"; case 10 -> "Ten"; case 11 -> "Eleven";
            case 12 -> "Twelve"; case 13 -> "Thirteen"; case 14 -> "Fourteen"; case 15 -> "Fifteen";
            case 16 -> "Sixteen"; case 17 -> "Seventeen"; case 18 -> "Eighteen"; case 19 -> "Nineteen";
            default -> "Other";
        };
    }
    
    private static String processLargeIfElse(int value) {
        if (value == 0) return "Zero";
        else if (value == 1) return "One";
        else if (value == 2) return "Two";
        else if (value == 3) return "Three";
        else if (value == 4) return "Four";
        else if (value == 5) return "Five";
        else if (value == 6) return "Six";
        else if (value == 7) return "Seven";
        else if (value == 8) return "Eight";
        else if (value == 9) return "Nine";
        else if (value == 10) return "Ten";
        else if (value == 11) return "Eleven";
        else if (value == 12) return "Twelve";
        else if (value == 13) return "Thirteen";
        else if (value == 14) return "Fourteen";
        else if (value == 15) return "Fifteen";
        else if (value == 16) return "Sixteen";
        else if (value == 17) return "Seventeen";
        else if (value == 18) return "Eighteen";
        else if (value == 19) return "Nineteen";
        else return "Other";
    }
    
    private static void testDenseVsSparseValues(int iterations) {
        System.out.println("\n--- Плотные vs разреженные значения ---");
        
        // Плотные значения (0, 1, 2, 3, 4) - можно использовать tableswitch
        long startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            int value = i % 5;
            String result = processDenseSwitch(value);
        }
        long denseTime = System.nanoTime() - startTime;
        
        // Разреженные значения (1, 100, 1000, 10000, 100000) - используется lookupswitch
        int[] sparseValues = {1, 100, 1000, 10000, 100000};
        startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            int value = sparseValues[i % sparseValues.length];
            String result = processSparseSwitch(value);
        }
        long sparseTime = System.nanoTime() - startTime;
        
        System.out.printf("Плотные значения: %.2f мс%n", denseTime / 1_000_000.0);
        System.out.printf("Разреженные значения: %.2f мс%n", sparseTime / 1_000_000.0);
        System.out.printf("Разница: %.1fx%n", (double)sparseTime / denseTime);
    }
    
    private static String processDenseSwitch(int value) {
        return switch (value) {
            case 0 -> "Zero";
            case 1 -> "One";
            case 2 -> "Two";
            case 3 -> "Three";
            case 4 -> "Four";
            default -> "Other";
        };
    }
    
    private static String processSparseSwitch(int value) {
        return switch (value) {
            case 1 -> "One";
            case 100 -> "Hundred";
            case 1000 -> "Thousand";
            case 10000 -> "Ten Thousand";
            case 100000 -> "Hundred Thousand";
            default -> "Other";
        };
    }
    
    private static void testSwitchVsIfElse(int iterations) {
        System.out.println("\n--- Switch vs If-else для строк ---");
        
        String[] testStrings = {"apple", "banana", "cherry", "date", "elderberry"};
        
        // Switch с String
        long startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            String value = testStrings[i % testStrings.length];
            String result = processStringSwitch(value);
        }
        long switchTime = System.nanoTime() - startTime;
        
        // If-else с String
        startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            String value = testStrings[i % testStrings.length];
            String result = processStringIfElse(value);
        }
        long ifElseTime = System.nanoTime() - startTime;
        
        System.out.printf("Switch с String: %.2f мс%n", switchTime / 1_000_000.0);
        System.out.printf("If-else с String: %.2f мс%n", ifElseTime / 1_000_000.0);
        System.out.printf("Разница: %.1fx%n", (double)ifElseTime / switchTime);
    }
    
    private static String processStringSwitch(String value) {
        return switch (value) {
            case "apple" -> "Red fruit";
            case "banana" -> "Yellow fruit";
            case "cherry" -> "Small red fruit";
            case "date" -> "Sweet brown fruit";
            case "elderberry" -> "Dark purple berry";
            default -> "Unknown fruit";
        };
    }
    
    private static String processStringIfElse(String value) {
        if ("apple".equals(value)) return "Red fruit";
        else if ("banana".equals(value)) return "Yellow fruit";
        else if ("cherry".equals(value)) return "Small red fruit";
        else if ("date".equals(value)) return "Sweet brown fruit";
        else if ("elderberry".equals(value)) return "Dark purple berry";
        else return "Unknown fruit";
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
public class SwitchBestPractices {
    
    public static void demonstrateBestPractices() {
        System.out.println("=== Лучшие практики Switch ===");
        
        goodPracticesExamples();
        badPracticesExamples();
        modernAlternatives();
    }
    
    private static void goodPracticesExamples() {
        System.out.println("\n✅ Хорошие практики:");
        
        // 1. Всегда используйте default case
        processOrderStatus(OrderStatus.SHIPPED);
        
        // 2. Используйте enum вместо констант
        calculateDiscount(CustomerType.VIP, 100.0);
        
        // 3. Группируйте связанные случаи
        categorizeDay(java.time.DayOfWeek.FRIDAY);
        
        // 4. Используйте switch expressions для возврата значений
        demonstrateSwitchExpressions();
        
        // 5. Правильная обработка null
        safeStringSwitch(null);
        
        // 6. Используйте константы для magic numbers
        processHttpStatus(200);
    }
    
    private static void processOrderStatus(OrderStatus status) {
        System.out.println("\n--- Обработка статуса заказа ---");
        
        switch (status) {
            case PENDING -> {
                System.out.println("Заказ ожидает обработки");
                // Отправить уведомление менеджеру
            }
            case PROCESSING -> {
                System.out.println("Заказ обрабатывается");
                // Зарезервировать товары на складе
            }
            case SHIPPED -> {
                System.out.println("Заказ отгружен");
                // Отправить трекинговый номер
            }
            case DELIVERED -> {
                System.out.println("Заказ доставлен");
                // Запросить отзыв
            }
            case CANCELLED -> {
                System.out.println("Заказ отменен");
                // Освободить зарезервированные товары
            }
            default -> {
                System.err.println("Неизвестный статус: " + status);
                // Логирование ошибки
                throw new IllegalArgumentException("Unsupported order status: " + status);
            }
        }
    }
    
    private static double calculateDiscount(CustomerType customerType, double amount) {
        System.out.println("\n--- Расчет скидки ---");
        
        double discount = switch (customerType) {
            case REGULAR -> 0.05; // 5%
            case PREMIUM -> 0.10; // 10%
            case VIP -> 0.15; // 15%
            case EMPLOYEE -> 0.25; // 25%
        };
        
        double discountAmount = amount * discount;
        System.out.printf("Скидка для %s: %.1f%% (%.2f)%n", 
                         customerType, discount * 100, discountAmount);
        
        return discountAmount;
    }
    
    private static void categorizeDay(java.time.DayOfWeek day) {
        System.out.println("\n--- Категоризация дня ---");
        
        String category = switch (day) {
            case MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY -> "Рабочий день";
            case SATURDAY, SUNDAY -> "Выходной";
        };
        
        System.out.println(day + " - " + category);
    }
    
    private static void demonstrateSwitchExpressions() {
        System.out.println("\n--- Switch expressions ---");
        
        Priority priority = Priority.HIGH;
        
        // ✅ Хорошо - компактно и выразительно
        String urgency = switch (priority) {
            case LOW -> "Можно отложить";
            case MEDIUM -> "Запланировать на неделю";
            case HIGH -> "Выполнить сегодня";
            case CRITICAL -> "Немедленно!";
        };
        
        int escalationHours = switch (priority) {
            case LOW -> 72;
            case MEDIUM -> 24;
            case HIGH -> 4;
            case CRITICAL -> 1;
        };
        
        System.out.printf("Приоритет %s: %s (эскалация через %d ч.)%n", 
                         priority, urgency, escalationHours);
    }
    
    private static void safeStringSwitch(String input) {
        System.out.println("\n--- Безопасная обработка строк ---");
        
        // ✅ Безопасная обработка null
        if (input == null) {
            System.out.println("Получен null input");
            return;
        }
        
        String result = switch (input.toLowerCase().trim()) {
            case "да", "yes", "y" -> "Подтверждено";
            case "нет", "no", "n" -> "Отклонено";
            case "" -> "Пустой ввод";
            default -> "Неопознанный ответ: " + input;
        };
        
        System.out.println("Результат: " + result);
    }
    
    private static void processHttpStatus(int status) {
        System.out.println("\n--- HTTP статус ---");
        
        // ✅ Используем константы вместо magic numbers
        String message = switch (status) {
            case HTTP_OK -> "Успешно";
            case HTTP_NOT_FOUND -> "Не найдено";
            case HTTP_INTERNAL_ERROR -> "Ошибка сервера";
            case HTTP_UNAUTHORIZED -> "Не авторизован";
            default -> "Неизвестный статус: " + status;
        };
        
        System.out.println("HTTP " + status + ": " + message);
    }
    
    private static void badPracticesExamples() {
        System.out.println("\n❌ Плохие практики (избегайте):");
        
        // ❌ Отсутствие break (fall-through без намерения)
        demonstrateBadFallThrough();
        
        // ❌ Отсутствие default case
        demonstrateMissingDefault();
        
        // ❌ Слишком сложная логика в case
        demonstrateComplexCase();
        
        // ❌ Magic numbers
        demonstrateMagicNumbers();
    }
    
    private static void demonstrateBadFallThrough() {
        System.out.println("\n--- Плохой fall-through ---");
        
        int level = 2;
        System.out.println("❌ Случайный fall-through:");
        
        switch (level) {
            case 1:
                System.out.println("Базовый уровень");
                // Забыли break!
            case 2:
                System.out.println("Средний уровень");
                break;
            case 3:
                System.out.println("Продвинутый уровень");
                break;
        }
        
        System.out.println("Результат: выводится и 'Базовый' и 'Средний'");
    }
    
    private static void demonstrateMissingDefault() {
        System.out.println("\n--- Отсутствие default ---");
        
        Color color = Color.PURPLE; // Не покрыт в switch
        
        String description = "Неизвестный цвет"; // Значение по умолчанию
        switch (color) {
            case RED -> description = "Красный";
            case GREEN -> description = "Зеленый";  
            case BLUE -> description = "Синий";
            // ❌ Нет default case для PURPLE!
        }
        
        System.out.println("❌ " + color + " не обработан: " + description);
    }
    
    private static void demonstrateComplexCase() {
        System.out.println("\n--- Слишком сложная логика в case ---");
        
        UserAction action = UserAction.LOGIN;
        
        // ❌ Плохо - слишком много логики в case
        switch (action) {
            case LOGIN -> {
                System.out.println("Обработка входа...");
                // 20+ строк кода для обработки логина
                validateUser();
                checkPermissions();
                logActivity();
                updateLastLogin();
                sendWelcomeMessage();
                // ... еще много кода
                System.out.println("Слишком много логики в case!");
            }
            // ... другие cases
        }
        
        // ✅ Лучше - вынести в отдельные методы
        switch (action) {
            case LOGIN -> handleLogin();
            case LOGOUT -> handleLogout();
            case REGISTER -> handleRegistration();
            default -> handleUnknownAction(action);
        }
    }
    
    private static void demonstrateMagicNumbers() {
        System.out.println("\n--- Magic numbers ---");
        
        int code = 404;
        
        // ❌ Плохо - magic numbers
        String badResult = switch (code) {
            case 200 -> "OK";
            case 404 -> "Not Found";
            case 500 -> "Error";
            default -> "Unknown";
        };
        
        // ✅ Хорошо - именованные константы
        String goodResult = switch (code) {
            case HTTP_OK -> "OK";
            case HTTP_NOT_FOUND -> "Not Found";
            case HTTP_INTERNAL_ERROR -> "Error";
            default -> "Unknown";
        };
        
        System.out.println("Результат: " + goodResult);
    }
    
    private static void modernAlternatives() {
        System.out.println("\n=== Современные альтернативы ===");
        
        // 1. Map для простых сопоставлений
        demonstrateMapAlternative();
        
        // 2. Полиморфизм вместо switch по типам
        demonstratePolymorphismAlternative();
        
        // 3. Strategy pattern
        demonstrateStrategyPattern();
    }
    
    private static void demonstrateMapAlternative() {
        System.out.println("\n--- Map как альтернатива Switch ---");
        
        // Для простых сопоставлений Map может быть лучше
        Map<String, String> fruitColors = Map.of(
            "apple", "red",
            "banana", "yellow", 
            "orange", "orange",
            "grape", "purple"
        );
        
        String fruit = "apple";
        String color = fruitColors.getOrDefault(fruit, "unknown");
        System.out.println(fruit + " is " + color);
    }
    
    private static void demonstratePolymorphismAlternative() {
        System.out.println("\n--- Полиморфизм вместо Switch ---");
        
        // Вместо switch по типам используйте полиморфизм
        Animal[] animals = {
            new Dog(), new Cat(), new Bird()
        };
        
        for (Animal animal : animals) {
            // ✅ Полиморфизм - лучше чем switch по типам
            animal.makeSound();
        }
    }
    
    private static void demonstrateStrategyPattern() {
        System.out.println("\n--- Strategy Pattern ---");
        
        // Вместо большого switch используйте Strategy
        PaymentProcessor processor = new PaymentProcessor();
        
        processor.processPayment("credit_card", 100.0);
        processor.processPayment("paypal", 50.0);
        processor.processPayment("bitcoin", 25.0);
    }
    
    // Перечисления
    enum OrderStatus { PENDING, PROCESSING, SHIPPED, DELIVERED, CANCELLED }
    enum CustomerType { REGULAR, PREMIUM, VIP, EMPLOYEE }
    enum Priority { LOW, MEDIUM, HIGH, CRITICAL }
    enum Color { RED, GREEN, BLUE, PURPLE }
    enum UserAction { LOGIN, LOGOUT, REGISTER }
    
    // HTTP константы
    private static final int HTTP_OK = 200;
    private static final int HTTP_NOT_FOUND = 404;
    private static final int HTTP_INTERNAL_ERROR = 500;
    private static final int HTTP_UNAUTHORIZED = 401;
    
    // Вспомогательные методы
    private static void validateUser() { System.out.println("  Валидация пользователя"); }
    private static void checkPermissions() { System.out.println("  Проверка прав"); }
    private static void logActivity() { System.out.println("  Логирование активности"); }
    private static void updateLastLogin() { System.out.println("  Обновление времени входа"); }
    private static void sendWelcomeMessage() { System.out.println("  Отправка приветствия"); }
    
    private static void handleLogin() { System.out.println("Обработка входа в системе"); }
    private static void handleLogout() { System.out.println("Обработка выхода из системы"); }
    private static void handleRegistration() { System.out.println("Обработка регистрации"); }
    private static void handleUnknownAction(UserAction action) { 
        System.out.println("Неизвестное действие: " + action); 
    }
    
    // Классы для демонстрации полиморфизма
    abstract static class Animal {
        abstract void makeSound();
    }
    
    static class Dog extends Animal {
        void makeSound() { System.out.println("Собака: Гав!"); }
    }
    
    static class Cat extends Animal {
        void makeSound() { System.out.println("Кошка: Мяу!"); }
    }
    
    static class Bird extends Animal {
        void makeSound() { System.out.println("Птица: Чирик!"); }
    }
    
    // Strategy Pattern пример
    static class PaymentProcessor {
        private final Map<String, PaymentStrategy> strategies = Map.of(
            "credit_card", new CreditCardPayment(),
            "paypal", new PayPalPayment(),
            "bitcoin", new BitcoinPayment()
        );
        
        public void processPayment(String method, double amount) {
            PaymentStrategy strategy = strategies.get(method);
            if (strategy != null) {
                strategy.pay(amount);
            } else {
                System.out.println("Неподдерживаемый метод платежа: " + method);
            }
        }
    }
    
    interface PaymentStrategy {
        void pay(double amount);
    }
    
    static class CreditCardPayment implements PaymentStrategy {
        public void pay(double amount) {
            System.out.println("Оплата картой: $" + amount);
        }
    }
    
    static class PayPalPayment implements PaymentStrategy {
        public void pay(double amount) {
            System.out.println("Оплата через PayPal: $" + amount);
        }
    }
    
    static class BitcoinPayment implements PaymentStrategy {
        public void pay(double amount) {
            System.out.println("Оплата Bitcoin: $" + amount);
        }
    }
    
    public static void main(String[] args) {
        demonstrateBestPractices();
    }
}
```

---

## 🔗 Связанные темы
- [[Enum-Code|Перечисления]] - использование enum в switch
- [[Exception-Handling-Code|Exception Handling]] - обработка ошибок в switch
- [[OOP-Principles-Code|ООП принципы]] - альтернативы через полиморфизм
- [[Best-Practices-Code|Лучшие практики]] - общие рекомендации по коду