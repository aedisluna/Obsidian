# Lambda выражения в Java
*Функциональное программирование и лаконичный код*

#java #lambda #functional-programming #java8 #streams

---

## 📋 Содержание
- [[#Основы Lambda]]
- [[#Функциональные интерфейсы]]
- [[#Method References]]
- [[#Встроенные функциональные интерфейсы]]
- [[#Lambda в коллекциях]]
- [[#Практические примеры]]

---

## 🎯 Основы Lambda

> [!info] Lambda выражения - это анонимные функции, которые позволяют передавать код как данные

### Синтаксис Lambda
```java
// Полная форма
(параметры) -> { тело функции }

// Сокращенная форма для одного выражения
(параметры) -> выражение

// Примеры
(x, y) -> x + y                    // Два параметра, возвращает сумму
x -> x * 2                         // Один параметр, скобки опциональны
() -> System.out.println("Hello")  // Без параметров
```

### Сравнение с анонимными классами
```java
// ❌ До Java 8 - анонимные классы
Runnable oldWay = new Runnable() {
    @Override
    public void run() {
        System.out.println("Старый способ");
    }
};

// ✅ Java 8+ - Lambda выражения
Runnable newWay = () -> System.out.println("Новый способ");

// ❌ Анонимный класс для Comparator
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return a.length() - b.length();
    }
});

// ✅ Lambda версия
Collections.sort(names, (a, b) -> a.length() - b.length());

// ✅ Еще более короткая версия с method reference
Collections.sort(names, Comparator.comparing(String::length));
```

### Типы Lambda выражений
```java
public class LambdaTypes {
    
    public static void main(String[] args) {
        // 1. Без параметров
        Runnable noParams = () -> System.out.println("Без параметров");
        noParams.run();
        
        // 2. Один параметр (скобки опциональны)
        Function<String, Integer> oneParam = s -> s.length();
        System.out.println("Java".length()); // 4
        
        // 3. Несколько параметров
        BinaryOperator<Integer> twoParams = (a, b) -> a + b;
        System.out.println(twoParams.apply(5, 3)); // 8
        
        // 4. Блок кода
        Consumer<String> block = name -> {
            String greeting = "Hello, " + name;
            System.out.println(greeting);
        };
        block.accept("World");
        
        // 5. Возврат значения
        Function<Integer, String> returnValue = x -> {
            if (x > 0) {
                return "positive";
            } else if (x < 0) {
                return "negative";
            } else {
                return "zero";
            }
        };
        System.out.println(returnValue.apply(5)); // positive
    }
}
```

---

## 🔧 Функциональные интерфейсы

### Создание собственных функциональных интерфейсов
```java
// Простой функциональный интерфейс
@FunctionalInterface
public interface Calculator {
    double calculate(double a, double b);
    
    // default методы разрешены
    default double square(double x) {
        return calculate(x, x);
    }
    
    // static методы тоже разрешены
    static double add(double a, double b) {
        return a + b;
    }
}

// Использование
public class CalculatorExample {
    
    public static void main(String[] args) {
        // Различные реализации через lambda
        Calculator addition = (a, b) -> a + b;
        Calculator subtraction = (a, b) -> a - b;
        Calculator multiplication = (a, b) -> a * b;
        Calculator division = (a, b) -> b != 0 ? a / b : 0;
        
        double x = 10, y = 3;
        
        System.out.println("Сложение: " + addition.calculate(x, y));
        System.out.println("Вычитание: " + subtraction.calculate(x, y));
        System.out.println("Умножение: " + multiplication.calculate(x, y));
        System.out.println("Деление: " + division.calculate(x, y));
        
        // Использование как параметр
        System.out.println("Результат операции: " + 
                          performOperation(x, y, (a, b) -> Math.pow(a, b)));
    }
    
    public static double performOperation(double a, double b, Calculator calc) {
        return calc.calculate(a, b);
    }
}

// Функциональный интерфейс для валидации
@FunctionalInterface
public interface Validator<T> {
    boolean validate(T value);
    
    // Метод для объединения валидаторов
    default Validator<T> and(Validator<T> other) {
        return value -> this.validate(value) && other.validate(value);
    }
    
    default Validator<T> or(Validator<T> other) {
        return value -> this.validate(value) || other.validate(value);
    }
}

// Использование валидатора
public class ValidationExample {
    
    public static void main(String[] args) {
        // Различные валидаторы
        Validator<String> notEmpty = s -> s != null && !s.trim().isEmpty();
        Validator<String> minLength = s -> s != null && s.length() >= 3;
        Validator<String> maxLength = s -> s != null && s.length() <= 20;
        Validator<String> alphanumeric = s -> s != null && s.matches("[a-zA-Z0-9]+");
        
        // Комбинируем валидаторы
        Validator<String> usernameValidator = notEmpty
                .and(minLength)
                .and(maxLength)
                .and(alphanumeric);
        
        // Тестируем
        String[] testCases = {"", "ab", "validusername", "invalid-username", "verylongusernamethatexceedslimit"};
        
        for (String testCase : testCases) {
            boolean isValid = usernameValidator.validate(testCase);
            System.out.printf("'%s' - %s%n", testCase, isValid ? "валиден" : "невалиден");
        }
    }
}
```

### Обобщенные функциональные интерфейсы
```java
// Тройная функция
@FunctionalInterface
public interface TriFunction<T, U, V, R> {
    R apply(T t, U u, V v);
}

// Предикат с двумя параметрами
@FunctionalInterface
public interface BiPredicate<T, U> {
    boolean test(T t, U u);
}

// Потребитель с исключением
@FunctionalInterface
public interface ThrowingConsumer<T> {
    void accept(T t) throws Exception;
}

public class GenericFunctionalExample {
    
    public static void main(String[] args) {
        // Тройная функция - объединение строк
        TriFunction<String, String, String, String> combiner = 
            (first, middle, last) -> first + " " + middle + " " + last;
        
        System.out.println(combiner.apply("John", "Q", "Smith"));
        
        // BiPredicate - проверка диапазона
        BiPredicate<Integer, Integer> inRange = 
            (value, max) -> value >= 0 && value <= max;
        
        System.out.println("50 в диапазоне 0-100: " + inRange.test(50, 100));
        System.out.println("150 в диапазоне 0-100: " + inRange.test(150, 100));
        
        // ThrowingConsumer - для операций, которые могут бросить исключение
        ThrowingConsumer<String> fileProcessor = filename -> {
            // Симуляция операции с файлом
            if (filename.isEmpty()) {
                throw new IllegalArgumentException("Filename cannot be empty");
            }
            System.out.println("Обработка файла: " + filename);
        };
        
        try {
            fileProcessor.accept("document.txt");
            fileProcessor.accept(""); // Бросит исключение
        } catch (Exception e) {
            System.err.println("Ошибка: " + e.getMessage());
        }
    }
}
```

---

## 📌 Method References

### Типы method references
```java
import java.util.*;
import java.util.function.*;

public class MethodReferencesExample {
    
    public static void main(String[] args) {
        List<String> names = Arrays.asList("alice", "bob", "charlie", "diana");
        
        // 1. Статические method references
        // Lambda: s -> Integer.parseInt(s)
        // Method reference: Integer::parseInt
        List<String> numbers = Arrays.asList("1", "2", "3", "4", "5");
        List<Integer> integers = numbers.stream()
                                       .map(Integer::parseInt)
                                       .collect(Collectors.toList());
        System.out.println("Числа: " + integers);
        
        // 2. Instance method references на конкретном объекте
        // Lambda: s -> System.out.println(s)
        // Method reference: System.out::println
        System.out.println("Имена:");
        names.forEach(System.out::println);
        
        // 3. Instance method references на произвольном объекте типа
        // Lambda: s -> s.toUpperCase()
        // Method reference: String::toUpperCase
        List<String> upperNames = names.stream()
                                     .map(String::toUpperCase)
                                     .collect(Collectors.toList());
        System.out.println("Верхний регистр: " + upperNames);
        
        // 4. Constructor references
        // Lambda: s -> new Person(s)
        // Method reference: Person::new
        List<Person> persons = names.stream()
                                  .map(Person::new)
                                  .collect(Collectors.toList());
        persons.forEach(System.out::println);
        
        // 5. Array constructor references
        String[] array = names.toArray(String[]::new);
        System.out.println("Массив: " + Arrays.toString(array));
    }
}

class Person {
    private String name;
    
    public Person(String name) {
        this.name = name;
    }
    
    public String getName() {
        return name;
    }
    
    @Override
    public String toString() {
        return "Person{name='" + name + "'}";
    }
}

// Более сложные примеры method references
public class AdvancedMethodReferences {
    
    public static void main(String[] args) {
        List<String> words = Arrays.asList("Java", "Python", "JavaScript", "Go", "Kotlin");
        
        // Сортировка с method reference
        words.sort(String::compareToIgnoreCase);
        System.out.println("Отсортированный список: " + words);
        
        // Группировка по длине строки
        Map<Integer, List<String>> byLength = words.stream()
                .collect(Collectors.groupingBy(String::length));
        System.out.println("Группировка по длине: " + byLength);
        
        // Использование в reduce
        Optional<String> longest = words.stream()
                .reduce(BinaryOperator.maxBy(Comparator.comparing(String::length)));
        longest.ifPresent(s -> System.out.println("Самое длинное слово: " + s));
        
        // Method reference с несколькими параметрами
        BinaryOperator<String> concatenator = String::concat;
        System.out.println("Объединение: " + concatenator.apply("Hello", "World"));
        
        // Bound method reference (привязанный к объекту)
        String prefix = "Язык: ";
        Function<String, String> prefixer = prefix::concat;
        words.stream()
             .map(prefixer)
             .forEach(System.out::println);
    }
}
```

### Method references vs Lambda
```java
public class MethodReferenceVsLambda {
    
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // Когда использовать lambda, а когда method reference
        
        // ✅ Method reference - когда просто вызываем существующий метод
        numbers.stream()
               .map(String::valueOf)     // Вместо n -> String.valueOf(n)
               .forEach(System.out::println); // Вместо s -> System.out.println(s)
        
        // ✅ Lambda - когда нужна дополнительная логика
        numbers.stream()
               .map(n -> "Number: " + n)     // Не может быть method reference
               .forEach(System.out::println);
        
        // ✅ Method reference - для конструкторов
        List<String> stringNumbers = numbers.stream()
                .map(String::valueOf)
                .collect(Collectors.toCollection(ArrayList::new)); // Вместо () -> new ArrayList<>()
        
        // ✅ Lambda - когда нужны условия или трансформации
        numbers.stream()
               .filter(n -> n % 2 == 0)      // Не может быть method reference
               .map(n -> n * n)              // Не может быть method reference
               .forEach(System.out::println);
        
        // Примеры эквивалентности
        demonstrateEquivalence(numbers);
    }
    
    private static void demonstrateEquivalence(List<Integer> numbers) {
        System.out.println("\n=== Эквивалентные формы ===");
        
        // Статический метод
        Function<String, Integer> parser1 = s -> Integer.parseInt(s);
        Function<String, Integer> parser2 = Integer::parseInt;
        
        // Instance метод
        Predicate<String> isEmpty1 = s -> s.isEmpty();
        Predicate<String> isEmpty2 = String::isEmpty;
        
        // Конструктор
        Supplier<List<String>> listCreator1 = () -> new ArrayList<>();
        Supplier<List<String>> listCreator2 = ArrayList::new;
        
        // Bound method reference
        String text = "Hello";
        Predicate<String> startsWithHello1 = s -> text.startsWith(s);
        // К сожалению, bound method reference здесь не подойдет напрямую
        
        System.out.println("Парсер 1: " + parser1.apply("42"));
        System.out.println("Парсер 2: " + parser2.apply("42"));
        
        System.out.println("isEmpty 1: " + isEmpty1.test(""));
        System.out.println("isEmpty 2: " + isEmpty2.test(""));
        
        System.out.println("List creator 1: " + listCreator1.get().getClass());
        System.out.println("List creator 2: " + listCreator2.get().getClass());
    }
}
```

---

## 📦 Встроенные функциональные интерфейсы

### Основные интерфейсы java.util.function
```java
import java.util.function.*;
import java.util.*;

public class BuiltInFunctionalInterfaces {
    
    public static void main(String[] args) {
        demonstrateFunction();
        demonstratePredicate();
        demonstrateConsumer();
        demonstrateSupplier();
        demonstrateBinaryOperator();
        demonstrateUnaryOperator();
    }
    
    // Function<T, R> - принимает T, возвращает R
    private static void demonstrateFunction() {
        System.out.println("=== Function<T, R> ===");
        
        Function<String, Integer> lengthCalculator = String::length;
        Function<Integer, String> numberFormatter = n -> "Number: " + n;
        Function<String, String> upperCase = String::toUpperCase;
        
        // Применение функций
        String text = "Hello World";
        Integer length = lengthCalculator.apply(text);
        String formatted = numberFormatter.apply(length);
        String upper = upperCase.apply(text);
        
        System.out.println("Текст: " + text);
        System.out.println("Длина: " + length);
        System.out.println("Форматированная длина: " + formatted);
        System.out.println("Верхний регистр: " + upper);
        
        // Композиция функций
        Function<String, String> lengthToFormattedString = 
            lengthCalculator.andThen(numberFormatter);
        
        System.out.println("Композиция: " + lengthToFormattedString.apply("Java"));
        
        // Цепочка функций
        Function<String, Integer> wordCount = text -> text.split("\\s+").length;
        Function<Integer, Double> percentage = count -> (count / 10.0) * 100;
        
        Function<String, Double> textToPercentage = wordCount.andThen(percentage);
        System.out.println("Процент слов: " + textToPercentage.apply("Java is awesome"));
    }
    
    // Predicate<T> - принимает T, возвращает boolean
    private static void demonstratePredicate() {
        System.out.println("\n=== Predicate<T> ===");
        
        Predicate<Integer> isEven = n -> n % 2 == 0;
        Predicate<Integer> isPositive = n -> n > 0;
        Predicate<String> isLongWord = s -> s.length() > 5;
        Predicate<String> startsWithA = s -> s.startsWith("A");
        
        // Простое использование
        System.out.println("4 четное? " + isEven.test(4));
        System.out.println("5 четное? " + isEven.test(5));
        
        // Комбинирование предикатов
        Predicate<Integer> isEvenAndPositive = isEven.and(isPositive);
        Predicate<Integer> isEvenOrNegative = isEven.or(n -> n < 0);
        Predicate<Integer> isOdd = isEven.negate();
        
        List<Integer> numbers = Arrays.asList(-2, -1, 0, 1, 2, 3, 4, 5, 6);
        
        System.out.println("Четные и положительные: " + 
                          numbers.stream().filter(isEvenAndPositive).collect(Collectors.toList()));
        System.out.println("Четные или отрицательные: " + 
                          numbers.stream().filter(isEvenOrNegative).collect(Collectors.toList()));
        System.out.println("Нечетные: " + 
                          numbers.stream().filter(isOdd).collect(Collectors.toList()));
        
        // Предикаты для строк
        List<String> words = Arrays.asList("Apple", "Banana", "Apricot", "Cherry", "Avocado");
        
        Predicate<String> complexPredicate = isLongWord.and(startsWithA);
        System.out.println("Длинные слова на A: " + 
                          words.stream().filter(complexPredicate).collect(Collectors.toList()));
    }
    
    // Consumer<T> - принимает T, ничего не возвращает
    private static void demonstrateConsumer() {
        System.out.println("\n=== Consumer<T> ===");
        
        Consumer<String> printer = System.out::println;
        Consumer<String> upperPrinter = s -> System.out.println(s.toUpperCase());
        Consumer<List<String>> listPrinter = list -> {
            System.out.println("Размер списка: " + list.size());
            list.forEach(item -> System.out.println("- " + item));
        };
        
        // Использование
        printer.accept("Обычная печать");
        upperPrinter.accept("печать в верхнем регистре");
        
        // Цепочка consumers
        Consumer<String> chain = printer.andThen(upperPrinter).andThen(s -> 
            System.out.println("Длина: " + s.length()));
        
        chain.accept("Цепочка обработки");
        
        // Consumer для коллекций
        List<String> languages = Arrays.asList("Java", "Python", "JavaScript");
        listPrinter.accept(languages);
        
        // Практический пример - логирование
        Consumer<Exception> errorLogger = ex -> {
            System.err.println("ОШИБКА: " + ex.getMessage());
            System.err.println("Класс: " + ex.getClass().getSimpleName());
        };
        
        try {
            throw new RuntimeException("Тестовая ошибка");
        } catch (Exception e) {
            errorLogger.accept(e);
        }
    }
    
    // Supplier<T> - не принимает параметры, возвращает T
    private static void demonstrateSupplier() {
        System.out.println("\n=== Supplier<T> ===");
        
        Supplier<String> randomString = () -> "Random-" + Math.random();
        Supplier<LocalDateTime> currentTime = LocalDateTime::now;
        Supplier<List<String>> emptyList = ArrayList::new;
        Supplier<Integer> randomNumber = () -> (int)(Math.random() * 100);
        
        // Использование
        System.out.println("Случайная строка: " + randomString.get());
        System.out.println("Текущее время: " + currentTime.get());
        System.out.println("Пустой список: " + emptyList.get());
        
        // Lazy evaluation
        System.out.println("Случайные числа:");
        Stream.generate(randomNumber)
              .limit(5)
              .forEach(n -> System.out.println("- " + n));
        
        // Supplier для создания объектов
        Supplier<Person> personFactory = () -> new Person("Generated-" + System.currentTimeMillis());
        
        List<Person> persons = Stream.generate(personFactory)
                                   .limit(3)
                                   .collect(Collectors.toList());
        
        persons.forEach(System.out::println);
    }
    
    // BinaryOperator<T> extends BiFunction<T, T, T>
    private static void demonstrateBinaryOperator() {
        System.out.println("\n=== BinaryOperator<T> ===");
        
        BinaryOperator<Integer> adder = (a, b) -> a + b;
        BinaryOperator<Integer> multiplier = (a, b) -> a * b;
        BinaryOperator<String> concatenator = (a, b) -> a + " " + b;
        BinaryOperator<Integer> max = Integer::max;
        BinaryOperator<Integer> min = Integer::min;
        
        // Использование
        System.out.println("5 + 3 = " + adder.apply(5, 3));
        System.out.println("5 * 3 = " + multiplier.apply(5, 3));
        System.out.println("Объединение: " + concatenator.apply("Hello", "World"));
        System.out.println("Максимум (10, 15): " + max.apply(10, 15));
        System.out.println("Минимум (10, 15): " + min.apply(10, 15));
        
        // В reduce операциях
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        
        Optional<Integer> sum = numbers.stream().reduce(adder);
        Optional<Integer> product = numbers.stream().reduce(multiplier);
        Optional<Integer> maximum = numbers.stream().reduce(max);
        
        sum.ifPresent(s -> System.out.println("Сумма: " + s));
        product.ifPresent(p -> System.out.println("Произведение: " + p));
        maximum.ifPresent(m -> System.out.println("Максимум: " + m));
        
        // Для строк
        List<String> words = Arrays.asList("Java", "is", "awesome");
        String sentence = words.stream().reduce("", concatenator);
        System.out.println("Предложение: '" + sentence.trim() + "'");
    }
    
    // UnaryOperator<T> extends Function<T, T>
    private static void demonstrateUnaryOperator() {
        System.out.println("\n=== UnaryOperator<T> ===");
        
        UnaryOperator<String> upperCase = String::toUpperCase;
        UnaryOperator<String> addPrefix = s -> ">>> " + s;
        UnaryOperator<Integer> square = x -> x * x;
        UnaryOperator<Integer> increment = x -> x + 1;
        
        // Использование
        System.out.println("Верхний регистр: " + upperCase.apply("hello"));
        System.out.println("С префиксом: " + addPrefix.apply("important"));
        System.out.println("Квадрат 5: " + square.apply(5));
        System.out.println("Инкремент 10: " + increment.apply(10));
        
        // Композиция унарных операторов
        UnaryOperator<String> formatter = addPrefix.compose(upperCase);
        System.out.println("Композиция: " + formatter.apply("java"));
        
        UnaryOperator<String> chain = upperCase.andThen(addPrefix);
        System.out.println("Цепочка: " + chain.apply("lambda"));
        
        // В потоках
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        List<Integer> processed = numbers.stream()
                                        .map(square)
                                        .map(increment)
                                        .collect(Collectors.toList());
        
        System.out.println("Обработанные числа: " + processed);
        
        // Итеративное применение
        String text = "java";
        for (int i = 0; i < 3; i++) {
            text = upperCase.apply(text);
            System.out.println("Итерация " + (i+1) + ": " + text);
        }
    }
}
```

### Специализированные функциональные интерфейсы
```java
import java.util.function.*;

public class SpecializedFunctionalInterfaces {
    
    public static void main(String[] args) {
        demonstratePrimitive();
        demonstrateBi();
        demonstrateToType();
    }
    
    // Примитивные специализации
    private static void demonstratePrimitive() {
        System.out.println("=== Примитивные специализации ===");
        
        // IntFunction, LongFunction, DoubleFunction
        IntFunction<String> intToString = i -> "Number: " + i;
        LongFunction<String> longToString = l -> "Long: " + l;
        DoubleFunction<String> doubleToString = d -> String.format("%.2f", d);
        
        System.out.println(intToString.apply(42));
        System.out.println(longToString.apply(123456789L));
        System.out.println(doubleToString.apply(3.14159));
        
        // IntPredicate, LongPredicate, DoublePredicate
        IntPredicate isEven = i -> i % 2 == 0;
        DoublePredicate isPositive = d -> d > 0;
        
        System.out.println("10 четное? " + isEven.test(10));
        System.out.println("3.14 положительное? " + isPositive.test(3.14));
        
        // IntConsumer, LongConsumer, DoubleConsumer
        IntConsumer intPrinter = i -> System.out.println("Int: " + i);
        DoubleConsumer doublePrinter = d -> System.out.printf("Double: %.3f%n", d);
        
        intPrinter.accept(100);
        doublePrinter.accept(2.71828);
        
        // IntSupplier, LongSupplier, DoubleSupplier
        IntSupplier randomInt = () -> (int)(Math.random() * 100);
        DoubleSupplier randomDouble = Math::random;
        
        System.out.println("Случайное int: " + randomInt.getAsInt());
        System.out.println("Случайное double: " + randomDouble.getAsDouble());
        
        // IntUnaryOperator, LongUnaryOperator, DoubleUnaryOperator
        IntUnaryOperator factorial = n -> {
            int result = 1;
            for (int i = 1; i <= n; i++) {
                result *= i;
            }
            return result;
        };
        
        DoubleUnaryOperator sqrt = Math::sqrt;
        
        System.out.println("Факториал 5: " + factorial.applyAsInt(5));
        System.out.println("Квадратный корень из 16: " + sqrt.applyAsDouble(16));
        
        // IntBinaryOperator, LongBinaryOperator, DoubleBinaryOperator
        IntBinaryOperator gcd = (a, b) -> {
            while (b != 0) {
                int temp = b;
                b = a % b;
                a = temp;
            }
            return a;
        };
        
        DoubleBinaryOperator power = Math::pow;
        
        System.out.println("НОД(15, 25): " + gcd.applyAsInt(15, 25));
        System.out.println("2^3: " + power.applyAsDouble(2, 3));
    }
    
    // Би-функции (два параметра)
    private static void demonstrateBi() {
        System.out.println("\n=== Би-функции ===");
        
        // BiFunction<T, U, R>
        BiFunction<String, Integer, String> repeat = (str, times) -> {
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < times; i++) {
                sb.append(str);
            }
            return sb.toString();
        };
        
        System.out.println("Повтор: " + repeat.apply("Ha", 3));
        
        // BiPredicate<T, U>
        BiPredicate<String, String> startsWith = String::startsWith;
        BiPredicate<Integer, Integer> isGreater = (a, b) -> a > b;
        
        System.out.println("'Hello' начинается с 'He'? " + startsWith.test("Hello", "He"));
        System.out.println("10 > 5? " + isGreater.test(10, 5));
        
        // BiConsumer<T, U>
        BiConsumer<String, Integer> indexPrinter = (str, index) -> 
            System.out.printf("[%d] %s%n", index, str);
        
        BiConsumer<Map<String, Object>, String> mapLogger = (map, key) -> {
            Object value = map.get(key);
            System.out.printf("Ключ '%s': %s%n", key, value);
        };
        
        indexPrinter.accept("Java", 1);
        indexPrinter.accept("Python", 2);
        
        Map<String, Object> data = Map.of("name", "John", "age", 30, "city", "Moscow");
        mapLogger.accept(data, "name");
        mapLogger.accept(data, "age");
        
        // Комбинирование BiConsumer
        BiConsumer<String, String> logger1 = (level, message) -> 
            System.out.printf("[%s] %s%n", level, message);
        BiConsumer<String, String> logger2 = (level, message) -> 
            System.out.printf("Timestamp: %s - [%s] %s%n", 
                            java.time.LocalTime.now(), level, message);
        
        BiConsumer<String, String> combinedLogger = logger1.andThen(logger2);
        combinedLogger.accept("INFO", "Система запущена");
    }
    
    // ToXxx конвертеры
    private static void demonstrateToType() {
        System.out.println("\n=== ToXxx конвертеры ===");
        
        // ToIntFunction<T>
        ToIntFunction<String> stringLength = String::length;
        ToIntFunction<LocalDateTime> hourExtractor = dt -> dt.getHour();
        
        System.out.println("Длина 'Hello World': " + stringLength.applyAsInt("Hello World"));
        System.out.println("Текущий час: " + hourExtractor.applyAsInt(LocalDateTime.now()));
        
        // ToLongFunction<T>
        ToLongFunction<String> hashCode = s -> s.hashCode();
        System.out.println("Хэш 'Java': " + hashCode.applyAsLong("Java"));
        
        // ToDoubleFunction<T>
        ToDoubleFunction<Circle> areaCalculator = circle -> Math.PI * circle.radius * circle.radius;
        Circle circle = new Circle(5.0);
        System.out.printf("Площадь круга: %.2f%n", areaCalculator.applyAsDouble(circle));
        
        // ToIntBiFunction<T, U>
        ToIntBiFunction<String, String> compareIgnoreCase = String::compareToIgnoreCase;
        System.out.println("Сравнение 'Apple' и 'apple': " + 
                          compareIgnoreCase.applyAsInt("Apple", "apple"));
        
        // ToDoubleBiFunction<T, U>
        ToDoubleBiFunction<Double, Double> distance = (x1, x2) -> Math.abs(x1 - x2);
        System.out.printf("Расстояние между 3.5 и 7.2: %.1f%n", 
                         distance.applyAsDouble(3.5, 7.2));
        
        // Использование в потоках
        List<String> words = Arrays.asList("Java", "Python", "JavaScript", "Go");
        
        // Сумма длин всех слов
        int totalLength = words.stream()
                             .mapToInt(stringLength)
                             .sum();
        System.out.println("Общая длина всех слов: " + totalLength);
        
        // Средняя длина слова
        double averageLength = words.stream()
                                  .mapToInt(String::length)
                                  .average()
                                  .orElse(0.0);
        System.out.printf("Средняя длина слова: %.1f%n", averageLength);
    }
    
    // Вспомогательный класс
    static class Circle {
        final double radius;
        
        Circle(double radius) {
            this.radius = radius;
        }
    }
}
```

---

## 💼 Lambda в коллекциях

### Работа со списками
```java
import java.util.*;
import java.util.function.*;
import java.util.stream.Collectors;

public class LambdaWithCollections {
    
    public static void main(String[] args) {
        demonstrateListOperations();
        demonstrateMapOperations();
        demonstrateSetOperations();
        demonstrateCustomCollections();
    }
    
    private static void demonstrateListOperations() {
        System.out.println("=== Операции со списками ===");
        
        List<String> languages = new ArrayList<>(Arrays.asList(
            "Java", "Python", "JavaScript", "Go", "Kotlin", "Scala", "Clojure"
        ));
        
        // forEach с lambda
        System.out.println("Все языки:");
        languages.forEach(lang -> System.out.println("- " + lang));
        
        // removeIf с lambda
        languages.removeIf(lang -> lang.length() > 6);
        System.out.println("После удаления длинных названий: " + languages);
        
        // replaceAll с lambda
        languages.replaceAll(String::toUpperCase);
        System.out.println("В верхнем регистре: " + languages);
        
        // sort с lambda
        languages.sort((a, b) -> a.length() - b.length());
        System.out.println("Сортировка по длине: " + languages);
        
        // sort с method reference
        languages.sort(String::compareTo);
        System.out.println("Алфавитная сортировка: " + languages);
        
        // Сложная сортировка
        List<Person> people = Arrays.asList(
            new Person("Alice", 30, "Engineer"),
            new Person("Bob", 25, "Designer"),
            new Person("Charlie", 35, "Engineer"),
            new Person("Diana", 28, "Manager")
        );
        
        // Сортировка по профессии, затем по возрасту
        people.sort(Comparator.comparing(Person::getProfession)
                            .thenComparing(Person::getAge));
        
        System.out.println("Люди, отсортированные по профессии и возрасту:");
        people.forEach(System.out::println);
        
        // Поиск с lambda
        Optional<Person> engineer = people.stream()
                .filter(p -> "Engineer".equals(p.getProfession()))
                .findFirst();
        
        engineer.ifPresent(p -> System.out.println("Найден инженер: " + p));
    }
    
    private static void demonstrateMapOperations() {
        System.out.println("\n=== Операции с картами ===");
        
        Map<String, Integer> wordCounts = new HashMap<>();
        wordCounts.put("Java", 100);
        wordCounts.put("Python", 85);
        wordCounts.put("JavaScript", 120);
        wordCounts.put("Go", 45);
        
        // forEach для Map
        System.out.println("Количество упоминаний языков:");
        wordCounts.forEach((lang, count) -> 
            System.out.printf("%-10s: %d раз%n", lang, count));
        
        // compute - вычисление нового значения
        wordCounts.compute("Java", (lang, count) -> count == null ? 1 : count + 10);
        System.out.println("Java после увеличения: " + wordCounts.get("Java"));
        
        // computeIfAbsent - добавление если ключа нет
        wordCounts.computeIfAbsent("Kotlin", lang -> 30);
        System.out.println("Добавлен Kotlin: " + wordCounts.get("Kotlin"));
        
        // computeIfPresent - изменение если ключ есть
        wordCounts.computeIfPresent("Python", (lang, count) -> count * 2);
        System.out.println("Python после удвоения: " + wordCounts.get("Python"));
        
        // merge - объединение значений
        Map<String, Integer> additionalCounts = Map.of(
            "Java", 50,
            "Rust", 25,
            "TypeScript", 40
        );
        
        additionalCounts.forEach((lang, count) -> 
            wordCounts.merge(lang, count, Integer::sum));
        
        System.out.println("После merge операций:");
        wordCounts.entrySet()
                 .stream()
                 .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
                 .forEach(entry -> System.out.printf("%-10s: %d%n", 
                                                   entry.getKey(), entry.getValue()));
        
        // replaceAll для Map
        wordCounts.replaceAll((lang, count) -> count + 5);
        System.out.println("После добавления 5 ко всем значениям:");
        wordCounts.forEach((lang, count) -> System.out.printf("%s: %d ", lang, count));
        System.out.println();
        
        // Группировка в Map с помощью Collectors
        List<Person> people = Arrays.asList(
            new Person("Alice", 30, "Engineer"),
            new Person("Bob", 25, "Designer"),
            new Person("Charlie", 35, "Engineer"),
            new Person("Diana", 28, "Manager"),
            new Person("Eve", 32, "Engineer")
        );
        
        Map<String, List<Person>> byProfession = people.stream()
                .collect(Collectors.groupingBy(Person::getProfession));
        
        System.out.println("\nГруппировка людей по профессиям:");
        byProfession.forEach((prof, peopleList) -> {
            System.out.println(prof + ":");
            peopleList.forEach(p -> System.out.println("  - " + p.getName() + " (" + p.getAge() + ")"));
        });
    }
    
    private static void demonstrateSetOperations() {
        System.out.println("\n=== Операции с множествами ===");
        
        Set<String> programmingLanguages = new HashSet<>(Arrays.asList(
            "Java", "Python", "JavaScript", "Go", "Kotlin"
        ));
        
        Set<String> webLanguages = new HashSet<>(Arrays.asList(
            "JavaScript", "TypeScript", "PHP", "Python", "Java"
        ));
        
        // removeIf для Set
        Set<String> longNames = new HashSet<>(programmingLanguages);
        longNames.removeIf(lang -> lang.length() <= 4);
        System.out.println("Языки с длинными названиями: " + longNames);
        
        // Пересечение множеств
        Set<String> intersection = programmingLanguages.stream()
                .filter(webLanguages::contains)
                .collect(Collectors.toSet());
        System.out.println("Пересечение (языки программирования и веб): " + intersection);
        
        // Объединение множеств
        Set<String> union = programmingLanguages.stream()
                .collect(Collectors.toCollection(TreeSet::new));
        union.addAll(webLanguages);
        System.out.println("Объединение (отсортированное): " + union);
        
        // Разность множеств
        Set<String> difference = programmingLanguages.stream()
                .filter(lang -> !webLanguages.contains(lang))
                .collect(Collectors.toSet());
        System.out.println("Разность (только программирование): " + difference);
    }
    
    private static void demonstrateCustomCollections() {
        System.out.println("\n=== Пользовательские коллекции ===");
        
        // Создание коллекции с фабричным методом
        List<Integer> numbers = Stream.iterate(1, n -> n + 1)
                                    .limit(10)
                                    .collect(Collectors.toList());
        
        System.out.println("Числа 1-10: " + numbers);
        
        // Создание Map из списка
        Map<Integer, String> numberWords = numbers.stream()
                .collect(Collectors.toMap(
                    n -> n,
                    n -> convertToWords(n),
                    (existing, replacement) -> existing,  // Resolver для дублей
                    LinkedHashMap::new                    // Сохраняем порядок
                ));
        
        System.out.println("Числа словами:");
        numberWords.forEach((num, word) -> System.out.println(num + " -> " + word));
        
        // Партиционирование (разделение на две группы)
        Map<Boolean, List<Integer>> partitioned = numbers.stream()
                .collect(Collectors.partitioningBy(n -> n % 2 == 0));
        
        System.out.println("Четные: " + partitioned.get(true));
        System.out.println("Нечетные: " + partitioned.get(false));
        
        // Создание вложенной структуры данных
        List<Order> orders = Arrays.asList(
            new Order("Alice", "Laptop", 1500.0),
            new Order("Bob", "Mouse", 25.0),
            new Order("Alice", "Keyboard", 100.0),
            new Order("Charlie", "Monitor", 300.0),
            new Order("Bob", "Headphones", 75.0)
        );
        
        // Группировка заказов по клиентам и подсчет общей суммы
        Map<String, Double> totalByCustomer = orders.stream()
                .collect(Collectors.groupingBy(
                    Order::getCustomer,
                    Collectors.summingDouble(Order::getAmount)
                ));
        
        System.out.println("\nОбщая сумма заказов по клиентам:");
        totalByCustomer.entrySet()
                      .stream()
                      .sorted(Map.Entry.<String, Double>comparingByValue().reversed())
                      .forEach(entry -> System.out.printf("%-8s: $%.2f%n", 
                                                         entry.getKey(), entry.getValue()));
        
        // Создание статистики
        DoubleSummaryStatistics stats = orders.stream()
                .collect(Collectors.summarizingDouble(Order::getAmount));
        
        System.out.printf("\nСтатистика заказов:\n");
        System.out.printf("Количество: %d%n", stats.getCount());
        System.out.printf("Сумма: $%.2f%n", stats.getSum());
        System.out.printf("Среднее: $%.2f%n", stats.getAverage());
        System.out.printf("Минимум: $%.2f%n", stats.getMin());
        System.out.printf("Максимум: $%.2f%n", stats.getMax());
    }
    
    private static String convertToWords(int number) {
        String[] words = {"", "один", "два", "три", "четыре", "пять", 
                         "шесть", "семь", "восемь", "девять", "десять"};
        return number <= 10 ? words[number] : String.valueOf(number);
    }
}

// Вспомогательные классы
class Person {
    private String name;
    private int age;
    private String profession;
    
    public Person(String name, int age, String profession) {
        this.name = name;
        this.age = age;
        this.profession = profession;
    }
    
    // Геттеры
    public String getName() { return name; }
    public int getAge() { return age; }
    public String getProfession() { return profession; }
    
    @Override
    public String toString() {
        return String.format("%s (%d, %s)", name, age, profession);
    }
}

class Order {
    private String customer;
    private String product;
    private double amount;
    
    public Order(String customer, String product, double amount) {
        this.customer = customer;
        this.product = product;
        this.amount = amount;
    }
    
    // Геттеры
    public String getCustomer() { return customer; }
    public String getProduct() { return product; }
    public double getAmount() { return amount; }
    
    @Override
    public String toString() {
        return String.format("%s: %s ($%.2f)", customer, product, amount);
    }
}
```

---

## 🛠️ Практические примеры

### Обработка файлов с Lambda
```java
import java.io.*;
import java.nio.file.*;
import java.util.*;
import java.util.function.*;
import java.util.stream.Collectors;

public class FileProcessingWithLambda {
    
    public static void main(String[] args) {
        try {
            demonstrateFileReading();
            demonstrateLogAnalysis();
            demonstrateCSVProcessing();
        } catch (IOException e) {
            System.err.println("Ошибка при работе с файлами: " + e.getMessage());
        }
    }
    
    private static void demonstrateFileReading() throws IOException {
        System.out.println("=== Чтение и обработка файлов ===");
        
        // Создаем тестовый файл
        String testContent = "Java programming\n" +
                           "Lambda expressions\n" +
                           "Functional interfaces\n" +
                           "Stream API\n" +
                           "Method references\n";
        
        Path testFile = Paths.get("test.txt");
        Files.write(testFile, testContent.getBytes());
        
        // Чтение файла построчно с обработкой
        System.out.println("Обработка файла построчно:");
        Files.lines(testFile)
             .map(String::toUpperCase)
             .filter(line -> line.contains("JAVA") || line.contains("LAMBDA"))
             .forEach(System.out::println);
        
        // Подсчет статистики по файлу
        Map<Integer, Long> lineLengthStats = Files.lines(testFile)
                .collect(Collectors.groupingBy(
                    String::length,
                    Collectors.counting()
                ));
        
        System.out.println("\nСтатистика длины строк:");
        lineLengthStats.forEach((length, count) -> 
            System.out.printf("Длина %d символов: %d строк%n", length, count));
        
        // Поиск самой длинной строки
        Optional<String> longestLine = Files.lines(testFile)
                .max(Comparator.comparing(String::length));
        
        longestLine.ifPresent(line -> 
            System.out.println("Самая длинная строка: " + line));
        
        // Очистка
        Files.deleteIfExists(testFile);
    }
    
    private static void demonstrateLogAnalysis() throws IOException {
        System.out.println("\n=== Анализ лог-файла ===");
        
        // Создаем тестовый лог
        String logContent = "2023-12-01 10:15:30 INFO Starting application\n" +
                          "2023-12-01 10:15:31 DEBUG Loading configuration\n" +
                          "2023-12-01 10:15:35 WARN Configuration file not found, using defaults\n" +
                          "2023-12-01 10:16:12 INFO User logged in: john_doe\n" +
                          "2023-12-01 10:17:45 ERROR Database connection failed\n" +
                          "2023-12-01 10:17:46 INFO Retrying database connection\n" +
                          "2023-12-01 10:17:50 INFO Database connection established\n" +
                          "2023-12-01 10:20:15 ERROR Invalid user credentials: admin\n" +
                          "2023-12-01 10:22:30 INFO User logged out: john_doe\n";
        
        Path logFile = Paths.get("app.log");
        Files.write(logFile, logContent.getBytes());
        
        // Анализ лог-записей
        List<LogEntry> logEntries = Files.lines(logFile)
                .map(LogEntry::parse)
                .filter(Objects::nonNull)
                .collect(Collectors.toList());
        
        // Группировка по уровню логирования
        Map<String, Long> logLevelStats = logEntries.stream()
                .collect(Collectors.groupingBy(
                    LogEntry::getLevel,
                    Collectors.counting()
                ));
        
        System.out.println("Статистика по уровням логирования:");
        logLevelStats.forEach((level, count) -> 
            System.out.printf("%-5s: %d записей%n", level, count));
        
        // Поиск ошибок
        System.out.println("\nОшибки в логе:");
        logEntries.stream()
                 .filter(entry -> "ERROR".equals(entry.getLevel()))
                 .forEach(entry -> System.out.printf("[%s] %s%n", 
                                                   entry.getTimestamp(), entry.getMessage()));
        
        // Временная статистика (по часам)
        Map<String, Long> hourlyStats = logEntries.stream()
                .collect(Collectors.groupingBy(
                    entry -> entry.getTimestamp().substring(11, 13), // Извлекаем час
                    Collectors.counting()
                ));
        
        System.out.println("\nСтатистика по часам:");
        hourlyStats.entrySet()
                  .stream()
                  .sorted(Map.Entry.comparingByKey())
                  .forEach(entry -> System.out.printf("Час %s: %d записей%n", 
                                                     entry.getKey(), entry.getValue()));
        
        Files.deleteIfExists(logFile);
    }
    
    private static void demonstrateCSVProcessing() throws IOException {
        System.out.println("\n=== Обработка CSV файла ===");
        
        // Создаем тестовый CSV
        String csvContent = "Name,Age,Department,Salary\n" +
                          "John Doe,30,Engineering,75000\n" +
                          "Jane Smith,25,Marketing,55000\n" +
                          "Bob Johnson,35,Engineering,80000\n" +
                          "Alice Brown,28,HR,50000\n" +
                          "Charlie Wilson,32,Marketing,60000\n" +
                          "Diana Davis,29,Engineering,72000\n";
        
        Path csvFile = Paths.get("employees.csv");
        Files.write(csvFile, csvContent.getBytes());
        
        // Чтение и парсинг CSV
        List<Employee> employees = Files.lines(csvFile)
                .skip(1) // Пропускаем заголовок
                .map(Employee::fromCSV)
                .filter(Objects::nonNull)
                .collect(Collectors.toList());
        
        System.out.printf("Загружено %d сотрудников%n", employees.size());
        
        // Группировка по отделам
        Map<String, List<Employee>> byDepartment = employees.stream()
                .collect(Collectors.groupingBy(Employee::getDepartment));
        
        System.out.println("\nСотрудники по отделам:");
        byDepartment.forEach((dept, empList) -> {
            System.out.printf("\n%s (%d человек):%n", dept, empList.size());
            empList.stream()
                  .sorted(Comparator.comparing(Employee::getName))
                  .forEach(emp -> System.out.printf("  - %s, %d лет, $%,.0f%n", 
                                                   emp.getName(), emp.getAge(), emp.getSalary()));
        });
        
        // Статистика зарплат по отделам
        System.out.println("\nСтатистика зарплат по отделам:");
        byDepartment.forEach((dept, empList) -> {
            DoubleSummaryStatistics salaryStats = empList.stream()
                    .mapToDouble(Employee::getSalary)
                    .summaryStatistics();
            
            System.out.printf("%s: средняя $%,.0f, мин $%,.0f, макс $%,.0f%n",
                             dept, salaryStats.getAverage(), 
                             salaryStats.getMin(), salaryStats.getMax());
        });
        
        // Поиск топ-5 самых высокооплачиваемых
        System.out.println("\nТоп-5 по зарплате:");
        employees.stream()
                .sorted(Comparator.comparingDouble(Employee::getSalary).reversed())
                .limit(5)
                .forEach(emp -> System.out.printf("%-12s: $%,.0f (%s)%n", 
                                                emp.getName(), emp.getSalary(), emp.getDepartment()));
        
        // Фильтрация и создание отчета
        String report = employees.stream()
                .filter(emp -> emp.getAge() < 30)
                .filter(emp -> emp.getSalary() > 60000)
                .sorted(Comparator.comparing(Employee::getName))
                .map(emp -> String.format("%s,%d,%s,%.0f", 
                                        emp.getName(), emp.getAge(), 
                                        emp.getDepartment(), emp.getSalary()))
                .collect(Collectors.joining("\n", 
                                          "Name,Age,Department,Salary\n", 
                                          ""));
        
        // Сохраняем отфильтрованный отчет
        Path reportFile = Paths.get("young_high_earners.csv");
        Files.write(reportFile, report.getBytes());
        System.out.println("\nОтчет сохранен в: " + reportFile.getFileName());
        
        Files.deleteIfExists(csvFile);
        Files.deleteIfExists(reportFile);
    }
}

// Вспомогательные классы
class LogEntry {
    private String timestamp;
    private String level;
    private String message;
    
    public LogEntry(String timestamp, String level, String message) {
        this.timestamp = timestamp;
        this.level = level;
        this.message = message;
    }
    
    public static LogEntry parse(String logLine) {
        String[] parts = logLine.split(" ", 4);
        if (parts.length >= 4) {
            String timestamp = parts[0] + " " + parts[1];
            String level = parts[2];
            String message = parts[3];
            return new LogEntry(timestamp, level, message);
        }
        return null;
    }
    
    // Геттеры
    public String getTimestamp() { return timestamp; }
    public String getLevel() { return level; }
    public String getMessage() { return message; }
}

class Employee {
    private String name;
    private int age;
    private String department;
    private double salary;
    
    public Employee(String name, int age, String department, double salary) {
        this.name = name;
        this.age = age;
        this.department = department;
        this.salary = salary;
    }
    
    public static Employee fromCSV(String csvLine) {
        String[] parts = csvLine.split(",");
        if (parts.length == 4) {
            try {
                return new Employee(
                    parts[0].trim(),
                    Integer.parseInt(parts[1].trim()),
                    parts[2].trim(),
                    Double.parseDouble(parts[3].trim())
                );
            } catch (NumberFormatException e) {
                System.err.println("Ошибка парсинга строки: " + csvLine);
                return null;
            }
        }
        return null;
    }
    
    // Геттеры
    public String getName() { return name; }
    public int getAge() { return age; }
    public String getDepartment() { return department; }
    public double getSalary() { return salary; }
    
    @Override
    public String toString() {
        return String.format("Employee{name='%s', age=%d, department='%s', salary=%.0f}", 
                           name, age, department, salary);
    }
}

// Пример создания DSL с Lambda
class ValidationDSL {
    
    public static void main(String[] args) {
        // Создание валидатора через DSL
        Validator<String> passwordValidator = Validator.<String>builder()
                .rule(s -> s != null, "Пароль не может быть null")
                .rule(s -> s.length() >= 8, "Пароль должен быть минимум 8 символов")
                .rule(s -> s.matches(".*[A-Z].*"), "Пароль должен содержать заглавные буквы")
                .rule(s -> s.matches(".*[a-z].*"), "Пароль должен содержать строчные буквы")
                .rule(s -> s.matches(".*\\d.*"), "Пароль должен содержать цифры")
                .build();
        
        // Тестирование
        String[] passwords = {"weak", "StrongPassword123", "nodigits", "NOLOWERCASE123", "NoUppercase123"};
        
        for (String password : passwords) {
            ValidationResult result = passwordValidator.validate(password);
            System.out.printf("Пароль '%s': %s%n", password, 
                             result.isValid() ? "валиден" : "ошибки - " + String.join(", ", result.getErrors()));
        }
    }
}

class Validator<T> {
    private final List<Rule<T>> rules;
    
    private Validator(List<Rule<T>> rules) {
        this.rules = rules;
    }
    
    public static <T> Builder<T> builder() {
        return new Builder<>();
    }
    
    public ValidationResult validate(T value) {
        List<String> errors = new ArrayList<>();
        
        for (Rule<T> rule : rules) {
            if (!rule.test(value)) {
                errors.add(rule.getErrorMessage());
            }
        }
        
        return new ValidationResult(errors);
    }
    
    public static class Builder<T> {
        private final List<Rule<T>> rules = new ArrayList<>();
        
        public Builder<T> rule(Predicate<T> condition, String errorMessage) {
            rules.add(new Rule<>(condition, errorMessage));
            return this;
        }
        
        public Validator<T> build() {
            return new Validator<>(new ArrayList<>(rules));
        }
    }
    
    private static class Rule<T> {
        private final Predicate<T> condition;
        private final String errorMessage;
        
        public Rule(Predicate<T> condition, String errorMessage) {
            this.condition = condition;
            this.errorMessage = errorMessage;
        }
        
        public boolean test(T value) {
            return condition.test(value);
        }
        
        public String getErrorMessage() {
            return errorMessage;
        }
    }
}

class ValidationResult {
    private final List<String> errors;
    
    public ValidationResult(List<String> errors) {
        this.errors = new ArrayList<>(errors);
    }
    
    public boolean isValid() {
        return errors.isEmpty();
    }
    
    public List<String> getErrors() {
        return new ArrayList<>(errors);
    }
}
```

### Создание функциональных утилит
```java
import java.util.*;
import java.util.function.*;
import java.util.stream.Collectors;

public class FunctionalUtilities {
    
    public static void main(String[] args) {
        demonstrateUtilityFunctions();
        demonstrateCurrying();
        demonstrateMemoization();
        demonstratePartialApplication();
    }
    
    // Утилитарные функции высшего порядка
    private static void demonstrateUtilityFunctions() {
        System.out.println("=== Утилитарные функции ===");
        
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // retry - повторная попытка выполнения функции
        Function<Integer, String> unreliableFunction = n -> {
            if (Math.random() < 0.7) {
                throw new RuntimeException("Случайная ошибка для " + n);
            }
            return "Результат: " + n;
        };
        
        Function<Integer, Optional<String>> reliableFunction = retry(unreliableFunction, 3);
        
        Optional<String> result = reliableFunction.apply(5);
        System.out.println("Результат с retry: " + result.orElse("Все попытки неудачны"));
        
        // compose - композиция функций справа налево
        Function<Integer, Integer> multiplyBy2 = x -> x * 2;
        Function<Integer, Integer> add10 = x -> x + 10;
        Function<Integer, String> toString = Object::toString;
        
        Function<Integer, String> composed = compose(toString, compose(add10, multiplyBy2));
        System.out.println("Композиция (2 * 5 + 10 -> String): " + composed.apply(5));
        
        // pipe - композиция функций слева направо
        Function<Integer, String> piped = pipe(multiplyBy2, pipe(add10, toString));
        System.out.println("Pipe (2 * 5 + 10 -> String): " + piped.apply(5));
        
        // filter и map для Optional
        Optional<String> maybeText = Optional.of("Hello World");
        Optional<Integer> textLength = maybeText
                .filter(s -> s.length() > 5)
                .map(String::length);
        
        System.out.println("Длина текста (если > 5): " + textLength.orElse(0));
        
        // flatMap для работы с вложенными Optional
        Function<String, Optional<Integer>> parseInteger = s -> {
            try {
                return Optional.of(Integer.parseInt(s));
            } catch (NumberFormatException e) {
                return Optional.empty();
            }
        };
        
        Optional<String> maybeNumber = Optional.of("42");
        Optional<Integer> parsed = maybeNumber.flatMap(parseInteger);
        System.out.println("Парсинг числа: " + parsed.orElse(-1));
    }
    
    // Каррирование - преобразование функции многих аргументов в цепочку функций одного аргумента
    private static void demonstrateCurrying() {
        System.out.println("\n=== Каррирование ===");
        
        // Обычная функция трех аргументов
        TriFunction<Integer, Integer, Integer, Integer> add3 = (a, b, c) -> a + b + c;
        System.out.println("Обычная функция: " + add3.apply(1, 2, 3));
        
        // Каррированная версия
        Function<Integer, Function<Integer, Function<Integer, Integer>>> curriedAdd3 = 
            a -> b -> c -> a + b + c;
        
        // Использование каррированной функции
        System.out.println("Каррированная функция: " + 
                          curriedAdd3.apply(1).apply(2).apply(3));
        
        // Частичное применение через каррирование
        Function<Integer, Function<Integer, Integer>> add1 = curriedAdd3.apply(1);
        Function<Integer, Integer> add1and2 = add1.apply(2);
        System.out.println("Частичное применение: " + add1and2.apply(3));
        
        // Практический пример: конфигурация логгера
        Function<String, Function<String, Function<String, String>>> logFormatter = 
            level -> timestamp -> message -> 
                String.format("[%s] %s: %s", level, timestamp, message);
        
        Function<String, Function<String, String>> infoLogger = logFormatter.apply("INFO");
        Function<String, String> timestampedInfoLogger = infoLogger.apply("2023-12-01 10:30:00");
        
        System.out.println("Логгер: " + timestampedInfoLogger.apply("Приложение запущено"));
        
        // Каррированная валидация
        Function<Integer, Function<Integer, Function<Integer, Boolean>>> rangeValidator = 
            min -> max -> value -> value >= min && value <= max;
        
        Function<Integer, Boolean> ageValidator = rangeValidator.apply(18).apply(65);
        System.out.println("Возраст 25 валиден? " + ageValidator.apply(25));
        System.out.println("Возраст 100 валиден? " + ageValidator.apply(100));
    }
    
    // Мемоизация - кэширование результатов функции
    private static void demonstrateMemoization() {
        System.out.println("\n=== Мемоизация ===");
        
        // Медленная функция для демонстрации
        Function<Integer, Long> fibonacci = new Function<Integer, Long>() {
            @Override
            public Long apply(Integer n) {
                if (n <= 1) return (long) n;
                return apply(n - 1) + apply(n - 2);
            }
        };
        
        // Мемоизированная версия
        Function<Integer, Long> memoizedFibonacci = memoize(fibonacci);
        
        // Тестирование производительности
        long startTime = System.currentTimeMillis();
        Long result1 = memoizedFibonacci.apply(40);
        long firstCallTime = System.currentTimeMillis() - startTime;
        
        startTime = System.currentTimeMillis();
        Long result2 = memoizedFibonacci.apply(40);
        long secondCallTime = System.currentTimeMillis() - startTime;
        
        System.out.printf("Fibonacci(40) = %d%n", result1);
        System.out.printf("Первый вызов: %d мс%n", firstCallTime);
        System.out.printf("Второй вызов (кэш): %d мс%n", secondCallTime);
        System.out.printf("Ускорение: %.1fx%n", (double) firstCallTime / secondCallTime);
        
        // Мемоизированная функция для работы с API
        Function<String, String> expensiveApiCall = url -> {
            // Имитация медленного API вызова
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            return "Response from " + url;
        };
        
        Function<String, String> cachedApiCall = memoize(expensiveApiCall);
        
        System.out.println("Первый API вызов...");
        startTime = System.currentTimeMillis();
        String apiResult1 = cachedApiCall.apply("https://api.example.com/data");
        System.out.printf("Результат: %s (время: %d мс)%n", 
                         apiResult1, System.currentTimeMillis() - startTime);
        
        System.out.println("Повторный API вызов...");
        startTime = System.currentTimeMillis();
        String apiResult2 = cachedApiCall.apply("https://api.example.com/data");
        System.out.printf("Результат: %s (время: %d мс)%n", 
                         apiResult2, System.currentTimeMillis() - startTime);
    }
    
    // Частичное применение функций
    private static void demonstratePartialApplication() {
        System.out.println("\n=== Частичное применение ===");
        
        // Функция для отправки уведомлений
        TriFunction<String, String, String, String> sendNotification = 
            (recipient, subject, body) -> 
                String.format("To: %s\nSubject: %s\nBody: %s\n", recipient, subject, body);
        
        // Частичное применение - фиксируем получателя
        Function<String, Function<String, String>> notifyUser = 
            partial(sendNotification, "user@example.com");
        
        // Еще больше фиксируем - добавляем тему
        Function<String, String> sendAlert = notifyUser.apply("ALERT");
        
        System.out.println("Уведомление:");
        System.out.println(sendAlert.apply("Система перегружена!"));
        
        // Частичное применение для конфигурации
        TriFunction<String, Integer, Boolean, DatabaseConfig> createDbConfig = 
            (host, port, ssl) -> new DatabaseConfig(host, port, ssl);
        
        // Фиксируем хост для всех конфигураций
        Function<Integer, Function<Boolean, DatabaseConfig>> localhostConfig = 
            partial(createDbConfig, "localhost");
        
        // Фиксируем порт
        Function<Boolean, DatabaseConfig> devConfig = localhostConfig.apply(5432);
        
        DatabaseConfig config1 = devConfig.apply(false);
        DatabaseConfig config2 = devConfig.apply(true);
        
        System.out.println("Dev config (без SSL): " + config1);
        System.out.println("Dev config (с SSL): " + config2);
        
        // Частичное применение для математических операций
        BiFunction<Double, Double, Double> power = Math::pow;
        Function<Double, Double> square = partial(power, 2.0);
        Function<Double, Double> cube = partial(power, 3.0);
        
        System.out.printf("5^2 = %.0f%n", square.apply(5.0));
        System.out.printf("5^3 = %.0f%n", cube.apply(5.0));
    }
    
    // Утилитарные методы
    
    public static <T, R> Function<T, Optional<R>> retry(Function<T, R> function, int maxAttempts) {
        return input -> {
            for (int attempt = 1; attempt <= maxAttempts; attempt++) {
                try {
                    return Optional.of(function.apply(input));
                } catch (Exception e) {
                    if (attempt == maxAttempts) {
                        return Optional.empty();
                    }
                }
            }
            return Optional.empty();
        };
    }
    
    public static <T, U, R> Function<T, R> compose(Function<U, R> f, Function<T, U> g) {
        return x -> f.apply(g.apply(x));
    }
    
    public static <T, U, R> Function<T, R> pipe(Function<T, U> f, Function<U, R> g) {
        return x -> g.apply(f.apply(x));
    }
    
    public static <T, R> Function<T, R> memoize(Function<T, R> function) {
        Map<T, R> cache = new ConcurrentHashMap<>();
        return input -> cache.computeIfAbsent(input, function);
    }
    
    public static <T, U, V, R> Function<U, Function<V, R>> partial(TriFunction<T, U, V, R> function, T firstArg) {
        return secondArg -> thirdArg -> function.apply(firstArg, secondArg, thirdArg);
    }
    
    public static <T, U, R> Function<U, R> partial(BiFunction<T, U, R> function, T firstArg) {
        return secondArg -> function.apply(firstArg, secondArg);
    }
}

// Вспомогательные интерфейсы и классы
@FunctionalInterface
interface TriFunction<T, U, V, R> {
    R apply(T t, U u, V v);
}

class DatabaseConfig {
    private final String host;
    private final int port;
    private final boolean ssl;
    
    public DatabaseConfig(String host, int port, boolean ssl) {
        this.host = host;
        this.port = port;
        this.ssl = ssl;
    }
    
    @Override
    public String toString() {
        return String.format("DatabaseConfig{host='%s', port=%d, ssl=%s}", host, port, ssl);
    }
}
```

---

## 🔗 Связанные темы
- [[Stream-API-Code|Stream API]] - использование lambda в потоках данных
- [[Collections-Overview|Collections Framework]] - lambda в коллекциях
- [[Functional-Interfaces|Функциональные интерфейсы]] - основа для lambda
- [[Method-References|Method References]] - краткая запись lambda