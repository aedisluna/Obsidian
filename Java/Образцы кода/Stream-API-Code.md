# Stream API в Java
*Функциональная обработка коллекций и данных*

#java #stream-api #lambda #functional-programming #коллекции

---

## 📋 Содержание
- [[#Введение в Stream API]]
- [[#Создание потоков]]
- [[#Промежуточные операции]]
- [[#Терминальные операции]]
- [[#Collectors]]
- [[#Параллельные потоки]]
- [[#Практические примеры]]

---

## 🎯 Введение в Stream API

> [!info] Stream API - это мощный инструмент для функциональной обработки коллекций, появившийся в Java 8

### Что такое Stream?
- **Поток данных** - последовательность элементов из источника
- **Функциональный подход** - декларативное описание операций
- **Ленивые вычисления** - выполнение только при необходимости
- **Неизменяемость** - операции не изменяют исходную коллекцию

### Основные принципы:
1. **Источник** - коллекция, массив, I/O канал
2. **Промежуточные операции** - filter, map, sorted
3. **Терминальные операции** - collect, forEach, reduce
4. **Конвейер** - цепочка операций от источника до результата

### Преимущества Stream API:
- ✅ **Читаемость** - код более выразительный и понятный
- ✅ **Краткость** - меньше boilerplate кода
- ✅ **Функциональность** - поддержка функционального программирования
- ✅ **Параллелизм** - легкое распараллеливание операций
- ✅ **Композиция** - легко комбинировать операции

---

## 🔧 Создание потоков

### Из коллекций
```java
import java.util.*;
import java.util.stream.*;

public class StreamCreationExample {
    public static void main(String[] args) {
        
        // Из List
        List<String> names = Arrays.asList("Анна", "Борис", "Виктор", "Дарья");
        Stream<String> streamFromList = names.stream();
        
        // Из Set
        Set<Integer> numbers = Set.of(1, 2, 3, 4, 5);
        Stream<Integer> streamFromSet = numbers.stream();
        
        // Из Map (через значения или ключи)
        Map<String, Integer> ages = Map.of("Анна", 25, "Борис", 30);
        Stream<String> streamFromMapKeys = ages.keySet().stream();
        Stream<Integer> streamFromMapValues = ages.values().stream();
        Stream<Map.Entry<String, Integer>> streamFromMapEntries = ages.entrySet().stream();
    }
}
```

### Из массивов
```java
public class ArrayStreamExample {
    public static void main(String[] args) {
        
        // Из массива объектов
        String[] names = {"Александр", "Елена", "Михаил"};
        Stream<String> streamFromArray = Arrays.stream(names);
        
        // Из массива примитивов
        int[] numbers = {1, 2, 3, 4, 5};
        IntStream intStream = Arrays.stream(numbers);
        
        // Из части массива
        Stream<String> partialStream = Arrays.stream(names, 1, 3); // Елена, Михаил
    }
}
```

### Создание потоков методами Stream
```java
public class StreamFactoryMethods {
    public static void main(String[] args) {
        
        // Пустой поток
        Stream<String> emptyStream = Stream.empty();
        
        // Поток из элементов
        Stream<String> streamOf = Stream.of("Java", "Python", "JavaScript");
        
        // Бесконечный поток
        Stream<Integer> infiniteStream = Stream.iterate(0, n -> n + 2); // 0, 2, 4, 6...
        
        // Ограниченный бесконечный поток
        Stream<Integer> limitedStream = Stream.iterate(1, n -> n * 2).limit(10); // 1, 2, 4, 8...
        
        // Поток случайных чисел
        Stream<Double> randomStream = Stream.generate(Math::random).limit(5);
        
        // Диапазон чисел
        IntStream rangeStream = IntStream.range(1, 6); // 1, 2, 3, 4, 5
        IntStream rangeClosedStream = IntStream.rangeClosed(1, 5); // 1, 2, 3, 4, 5
        
        // Из строки
        IntStream charStream = "Hello".chars(); // поток кодов символов
        
        // Демонстрация
        System.out.println("Четные числа от 0 до 10:");
        Stream.iterate(0, n -> n + 2)
              .limit(6)
              .forEach(System.out::println);
        
        System.out.println("\n5 случайных чисел:");
        Stream.generate(Math::random)
              .limit(5)
              .forEach(num -> System.out.printf("%.3f ", num));
    }
}
```

---

## 🔄 Промежуточные операции

> [!tip] Промежуточные операции возвращают новый Stream и выполняются лениво

### Фильтрация - filter()
```java
import java.util.stream.Collectors;

public class FilterExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // Четные числа
        List<Integer> evenNumbers = numbers.stream()
            .filter(n -> n % 2 == 0)
            .collect(Collectors.toList());
        System.out.println("Четные числа: " + evenNumbers);
        
        // Числа больше 5
        List<Integer> bigNumbers = numbers.stream()
            .filter(n -> n > 5)
            .collect(Collectors.toList());
        System.out.println("Числа > 5: " + bigNumbers);
        
        // Сложный фильтр
        List<String> names = Arrays.asList("Анна", "Борис", "Виктор", "Дарья", "Елена");
        List<String> filteredNames = names.stream()
            .filter(name -> name.length() > 4)
            .filter(name -> name.startsWith("А") || name.startsWith("Е"))
            .collect(Collectors.toList());
        System.out.println("Имена (длина > 4, начинаются с А или Е): " + filteredNames);
    }
}
```

### Преобразование - map()
```java
public class MapExample {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("java", "python", "javascript", "go");
        
        // Преобразование в верхний регистр
        List<String> upperCaseWords = words.stream()
            .map(String::toUpperCase)
            .collect(Collectors.toList());
        System.out.println("Верхний регистр: " + upperCaseWords);
        
        // Преобразование в длины строк
        List<Integer> lengths = words.stream()
            .map(String::length)
            .collect(Collectors.toList());
        System.out.println("Длины строк: " + lengths);
        
        // Преобразование чисел
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        List<Integer> squares = numbers.stream()
            .map(n -> n * n)
            .collect(Collectors.toList());
        System.out.println("Квадраты: " + squares);
        
        // Преобразование объектов
        List<Person> people = Arrays.asList(
            new Person("Анна", 25),
            new Person("Борис", 30),
            new Person("Виктор", 28)
        );
        
        List<String> names = people.stream()
            .map(Person::getName)
            .collect(Collectors.toList());
        System.out.println("Имена: " + names);
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
}
```

### Сплющивание - flatMap()
```java
public class FlatMapExample {
    public static void main(String[] args) {
        
        // Сплющивание списков
        List<List<String>> listOfLists = Arrays.asList(
            Arrays.asList("Java", "Python"),
            Arrays.asList("JavaScript", "TypeScript"),
            Arrays.asList("Go", "Rust", "C++")
        );
        
        List<String> flatList = listOfLists.stream()
            .flatMap(List::stream)
            .collect(Collectors.toList());
        System.out.println("Сплющенный список: " + flatList);
        
        // Разбиение строк на слова
        List<String> sentences = Arrays.asList(
            "Java очень популярный язык",
            "Python простой в изучении",
            "JavaScript работает в браузере"
        );
        
        List<String> allWords = sentences.stream()
            .flatMap(sentence -> Arrays.stream(sentence.split(" ")))
            .collect(Collectors.toList());
        System.out.println("Все слова: " + allWords);
        
        // Сплющивание Optional
        List<String> strings = Arrays.asList("1", "2", "foo", "3", "bar");
        List<Integer> numbers = strings.stream()
            .flatMap(s -> {
                try {
                    return Stream.of(Integer.parseInt(s));
                } catch (NumberFormatException e) {
                    return Stream.empty();
                }
            })
            .collect(Collectors.toList());
        System.out.println("Только числа: " + numbers);
    }
}
```

### Сортировка - sorted()
```java
public class SortedExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Дарья", "Анна", "Виктор", "Борис", "Елена");
        
        // Естественная сортировка
        List<String> sortedNames = names.stream()
            .sorted()
            .collect(Collectors.toList());
        System.out.println("По алфавиту: " + sortedNames);
        
        // Обратная сортировка
        List<String> reverseSorted = names.stream()
            .sorted(Comparator.reverseOrder())
            .collect(Collectors.toList());
        System.out.println("По убыванию: " + reverseSorted);
        
        // Сортировка по длине
        List<String> sortedByLength = names.stream()
            .sorted(Comparator.comparing(String::length))
            .collect(Collectors.toList());
        System.out.println("По длине: " + sortedByLength);
        
        // Сложная сортировка объектов
        List<Person> people = Arrays.asList(
            new Person("Анна", 25),
            new Person("Борис", 30),
            new Person("Анна", 28),
            new Person("Виктор", 25)
        );
        
        List<Person> sortedPeople = people.stream()
            .sorted(Comparator.comparing(Person::getName)
                   .thenComparing(Person::getAge))
            .collect(Collectors.toList());
        
        System.out.println("Люди по имени, затем по возрасту:");
        sortedPeople.forEach(p -> System.out.println(p.getName() + " - " + p.getAge()));
    }
}
```

### Удаление дубликатов и ограничения
```java
public class DistinctAndLimitExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 2, 3, 3, 3, 4, 4, 5);
        
        // Удаление дубликатов
        List<Integer> distinctNumbers = numbers.stream()
            .distinct()
            .collect(Collectors.toList());
        System.out.println("Уникальные числа: " + distinctNumbers);
        
        // Первые N элементов
        List<Integer> firstThree = numbers.stream()
            .limit(3)
            .collect(Collectors.toList());
        System.out.println("Первые 3: " + firstThree);
        
        // Пропуск N элементов
        List<Integer> skipTwo = numbers.stream()
            .skip(2)
            .collect(Collectors.toList());
        System.out.println("Пропустить 2: " + skipTwo);
        
        // Комбинирование операций
        List<Integer> processed = numbers.stream()
            .distinct()           // Убрать дубликаты
            .skip(1)             // Пропустить первый
            .limit(3)            // Взять следующие 3
            .collect(Collectors.toList());
        System.out.println("Обработанные: " + processed);
        
        // takeWhile и dropWhile (Java 9+)
        List<Integer> sequence = Arrays.asList(1, 2, 3, 4, 3, 2, 1);
        
        List<Integer> takeWhileResult = sequence.stream()
            .takeWhile(n -> n < 4)  // Берем пока условие истинно
            .collect(Collectors.toList());
        System.out.println("takeWhile (< 4): " + takeWhileResult); // [1, 2, 3]
        
        List<Integer> dropWhileResult = sequence.stream()
            .dropWhile(n -> n < 4)  // Пропускаем пока условие истинно
            .collect(Collectors.toList());
        System.out.println("dropWhile (< 4): " + dropWhileResult); // [4, 3, 2, 1]
    }
}
```

---

## 🎯 Терминальные операции

> [!tip] Терминальные операции завершают поток и запускают обработку всех промежуточных операций

### Сбор результатов - collect()
```java
public class CollectExample {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("java", "python", "javascript", "go", "rust");
        
        // В список
        List<String> list = words.stream()
            .filter(w -> w.length() > 4)
            .collect(Collectors.toList());
        
        // В множество
        Set<String> set = words.stream()
            .map(String::toUpperCase)
            .collect(Collectors.toSet());
        
        // В строку
        String joined = words.stream()
            .collect(Collectors.joining(", "));
        System.out.println("Соединенные: " + joined);
        
        String joinedWithPrefixSuffix = words.stream()
            .collect(Collectors.joining(", ", "[", "]"));
        System.out.println("С префиксом/суффиксом: " + joinedWithPrefixSuffix);
        
        // В Map
        List<Person> people = Arrays.asList(
            new Person("Анна", 25),
            new Person("Борис", 30),
            new Person("Виктор", 28)
        );
        
        Map<String, Integer> nameToAge = people.stream()
            .collect(Collectors.toMap(Person::getName, Person::getAge));
        System.out.println("Имя -> Возраст: " + nameToAge);
        
        // Группировка
        Map<Integer, List<String>> wordsByLength = words.stream()
            .collect(Collectors.groupingBy(String::length));
        System.out.println("Группировка по длине: " + wordsByLength);
        
        // Разделение
        Map<Boolean, List<String>> partitioned = words.stream()
            .collect(Collectors.partitioningBy(w -> w.length() > 4));
        System.out.println("Длинные слова: " + partitioned.get(true));
        System.out.println("Короткие слова: " + partitioned.get(false));
    }
}
```

### Поиск элементов
```java
public class FindExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Анна", "Борис", "Виктор", "Дарья");
        
        // Поиск любого элемента
        Optional<String> anyLongName = names.stream()
            .filter(name -> name.length() > 4)
            .findAny();
        anyLongName.ifPresent(name -> System.out.println("Любое длинное имя: " + name));
        
        // Поиск первого элемента
        Optional<String> firstLongName = names.stream()
            .filter(name -> name.length() > 4)
            .findFirst();
        firstLongName.ifPresent(name -> System.out.println("Первое длинное имя: " + name));
        
        // Проверка условий
        boolean anyStartsWithA = names.stream()
            .anyMatch(name -> name.startsWith("А"));
        System.out.println("Есть имена на А: " + anyStartsWithA);
        
        boolean allLongerThan3 = names.stream()
            .allMatch(name -> name.length() > 3);
        System.out.println("Все имена длиннее 3 символов: " + allLongerThan3);
        
        boolean noneStartsWithZ = names.stream()
            .noneMatch(name -> name.startsWith("З"));
        System.out.println("Нет имен на З: " + noneStartsWithZ);
        
        // Подсчет
        long count = names.stream()
            .filter(name -> name.length() <= 5)
            .count();
        System.out.println("Имен длиной <= 5: " + count);
    }
}
```

### Агрегирующие операции
```java
public class AggregateExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 5, 3, 9, 2, 8, 4);
        
        // Минимум и максимум
        Optional<Integer> min = numbers.stream().min(Integer::compareTo);
        Optional<Integer> max = numbers.stream().max(Integer::compareTo);
        
        min.ifPresent(m -> System.out.println("Минимум: " + m));
        max.ifPresent(m -> System.out.println("Максимум: " + m));
        
        // Сумма (для IntStream)
        int sum = numbers.stream().mapToInt(Integer::intValue).sum();
        System.out.println("Сумма: " + sum);
        
        // Среднее значение
        OptionalDouble average = numbers.stream().mapToInt(Integer::intValue).average();
        average.ifPresent(avg -> System.out.printf("Среднее: %.2f%n", avg));
        
        // Статистика
        IntSummaryStatistics stats = numbers.stream()
            .mapToInt(Integer::intValue)
            .summaryStatistics();
        
        System.out.println("Статистика: " + stats);
        System.out.println("Количество: " + stats.getCount());
        System.out.println("Сумма: " + stats.getSum());
        System.out.println("Минимум: " + stats.getMin());
        System.out.println("Максимум: " + stats.getMax());
        System.out.printf("Среднее: %.2f%n", stats.getAverage());
    }
}
```

### Свертка - reduce()
```java
public class ReduceExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        
        // Сумма всех элементов
        Optional<Integer> sum = numbers.stream()
            .reduce((a, b) -> a + b);
        sum.ifPresent(s -> System.out.println("Сумма: " + s));
        
        // Сумма с начальным значением
        Integer sumWithIdentity = numbers.stream()
            .reduce(0, (a, b) -> a + b);
        System.out.println("Сумма с начальным значением: " + sumWithIdentity);
        
        // Произведение
        Optional<Integer> product = numbers.stream()
            .reduce((a, b) -> a * b);
        product.ifPresent(p -> System.out.println("Произведение: " + p));
        
        // Максимум
        Optional<Integer> max = numbers.stream()
            .reduce(Integer::max);
        max.ifPresent(m -> System.out.println("Максимум через reduce: " + m));
        
        // Конкатенация строк
        List<String> words = Arrays.asList("Java", "это", "мощный", "язык");
        String sentence = words.stream()
            .reduce("Фраза:", (acc, word) -> acc + " " + word);
        System.out.println(sentence);
        
        // Сложная свертка с объектами
        List<Person> people = Arrays.asList(
            new Person("Анна", 25),
            new Person("Борис", 30),
            new Person("Виктор", 28)
        );
        
        // Суммарный возраст
        int totalAge = people.stream()
            .map(Person::getAge)
            .reduce(0, Integer::sum);
        System.out.println("Общий возраст: " + totalAge);
        
        // Самый длинное имя
        Optional<String> longestName = people.stream()
            .map(Person::getName)
            .reduce((name1, name2) -> name1.length() >= name2.length() ? name1 : name2);
        longestName.ifPresent(name -> System.out.println("Самое длинное имя: " + name));
    }
}
```

---

## 📦 Collectors

> [!tip] Collectors - это набор готовых коллекторов для типичных операций сбора данных

### Базовые коллекторы
```java
public class BasicCollectorsExample {
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Анна", 25),
            new Person("Борис", 30),
            new Person("Виктор", 28),
            new Person("Дарья", 25),
            new Person("Елена", 32)
        );
        
        // Сбор в различные коллекции
        List<String> namesList = people.stream()
            .map(Person::getName)
            .collect(Collectors.toList());
        
        Set<Integer> agesSet = people.stream()
            .map(Person::getAge)
            .collect(Collectors.toSet());
        
        LinkedList<String> namesLinkedList = people.stream()
            .map(Person::getName)
            .collect(Collectors.toCollection(LinkedList::new));
        
        // Подсчет элементов
        long count = people.stream()
            .collect(Collectors.counting());
        System.out.println("Количество людей: " + count);
        
        // Статистика по возрасту
        IntSummaryStatistics ageStats = people.stream()
            .collect(Collectors.summarizingInt(Person::getAge));
        System.out.println("Статистика возрастов: " + ageStats);
        
        // Среднее значение
        OptionalDouble averageAge = people.stream()
            .collect(Collectors.averagingInt(Person::getAge));
        averageAge.ifPresent(avg -> System.out.printf("Средний возраст: %.1f%n", avg));
        
        // Сумма
        int totalAge = people.stream()
            .collect(Collectors.summingInt(Person::getAge));
        System.out.println("Общий возраст: " + totalAge);
        
        // Минимум и максимум
        Optional<Person> youngest = people.stream()
            .collect(Collectors.minBy(Comparator.comparing(Person::getAge)));
        youngest.ifPresent(p -> System.out.println("Самый молодой: " + p.getName()));
        
        Optional<Person> oldest = people.stream()
            .collect(Collectors.maxBy(Comparator.comparing(Person::getAge)));
        oldest.ifPresent(p -> System.out.println("Самый старший: " + p.getName()));
    }
}
```

### Группировка
```java
public class GroupingExample {
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Анна", 25),
            new Person("Борис", 30),
            new Person("Виктор", 28),
            new Person("Дарья", 25),
            new Person("Елена", 32),
            new Person("Игорь", 30)
        );
        
        // Группировка по возрасту
        Map<Integer, List<Person>> peopleByAge = people.stream()
            .collect(Collectors.groupingBy(Person::getAge));
        
        System.out.println("Группировка по возрасту:");
        peopleByAge.forEach((age, group) -> {
            System.out.println("Возраст " + age + ": " + 
                group.stream().map(Person::getName).collect(Collectors.toList()));
        });
        
        // Группировка с подсчетом
        Map<Integer, Long> countByAge = people.stream()
            .collect(Collectors.groupingBy(Person::getAge, Collectors.counting()));
        System.out.println("Количество по возрастам: " + countByAge);
        
        // Группировка с преобразованием
        Map<Integer, List<String>> namesByAge = people.stream()
            .collect(Collectors.groupingBy(
                Person::getAge, 
                Collectors.mapping(Person::getName, Collectors.toList())
            ));
        System.out.println("Имена по возрастам: " + namesByAge);
        
        // Многоуровневая группировка
        Map<String, Map<Integer, List<Person>>> groupedByFirstLetterAndAge = people.stream()
            .collect(Collectors.groupingBy(
                person -> person.getName().substring(0, 1),
                Collectors.groupingBy(Person::getAge)
            ));
        
        System.out.println("Группировка по первой букве и возрасту:");
        groupedByFirstLetterAndAge.forEach((letter, ageGroup) -> {
            System.out.println("Буква " + letter + ":");
            ageGroup.forEach((age, peopleList) -> {
                System.out.println("  Возраст " + age + ": " + 
                    peopleList.stream().map(Person::getName).collect(Collectors.toList()));
            });
        });
        
        // Группировка по пользовательским категориям
        Map<String, List<Person>> peopleByAgeCategory = people.stream()
            .collect(Collectors.groupingBy(person -> {
                int age = person.getAge();
                if (age < 26) return "Молодые";
                else if (age < 31) return "Средние";
                else return "Старшие";
            }));
        
        System.out.println("Группировка по возрастным категориям:");
        peopleByAgeCategory.forEach((category, group) -> {
            System.out.println(category + ": " + 
                group.stream().map(Person::getName).collect(Collectors.toList()));
        });
    }
}
```

### Разделение и сложные коллекторы
```java
public class AdvancedCollectorsExample {
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Анна", 25),
            new Person("Борис", 30),
            new Person("Виктор", 28),
            new Person("Дарья", 25),
            new Person("Елена", 32)
        );
        
        // Разделение (partitioning)
        Map<Boolean, List<Person>> partitionedByAge = people.stream()
            .collect(Collectors.partitioningBy(person -> person.getAge() > 27));
        
        System.out.println("Старше 27: " + 
            partitionedByAge.get(true).stream().map(Person::getName).collect(Collectors.toList()));
        System.out.println("27 и младше: " + 
            partitionedByAge.get(false).stream().map(Person::getName).collect(Collectors.toList()));
        
        // Разделение с подсчетом
        Map<Boolean, Long> partitionCounts = people.stream()
            .collect(Collectors.partitioningBy(person -> person.getAge() > 27, Collectors.counting()));
        System.out.println("Количество по возрастным группам: " + partitionCounts);
        
        // Собственный коллектор для соединения имен
        String allNames = people.stream()
            .map(Person::getName)
            .collect(Collectors.joining(" | ", "Люди: [", "]"));
        System.out.println(allNames);
        
        // Коллектор для создания Map
        Map<String, Integer> nameToAge = people.stream()
            .collect(Collectors.toMap(
                Person::getName, 
                Person::getAge,
                (existingAge, newAge) -> existingAge // Обработка дубликатов
            ));
        System.out.println("Имя -> Возраст: " + nameToAge);
        
        // Композиция коллекторов
        Map<Integer, String> ageToNames = people.stream()
            .collect(Collectors.groupingBy(
                Person::getAge,
                Collectors.mapping(
                    Person::getName,
                    Collectors.joining(", ")
                )
            ));
        System.out.println("Возраст -> Имена: " + ageToNames);
    }
}
```

---

## ⚡ Параллельные потоки

> [!warning] Параллельные потоки эффективны для CPU-интенсивных операций над большими коллекциями

### Создание и использование параллельных потоков
```java
public class ParallelStreamExample {
    public static void main(String[] args) {
        
        // Создание параллельного потока
        List<Integer> numbers = IntStream.range(1, 1000000).boxed().collect(Collectors.toList());
        
        // Последовательная обработка
        long startTime = System.currentTimeMillis();
        long sequentialSum = numbers.stream()
            .mapToLong(Integer::longValue)
            .sum();
        long sequentialTime = System.currentTimeMillis() - startTime;
        
        // Параллельная обработка
        startTime = System.currentTimeMillis();
        long parallelSum = numbers.parallelStream()
            .mapToLong(Integer::longValue)
            .sum();
        long parallelTime = System.currentTimeMillis() - startTime;
        
        System.out.println("Последовательная сумма: " + sequentialSum + " за " + sequentialTime + "мс");
        System.out.println("Параллельная сумма: " + parallelSum + " за " + parallelTime + "мс");
        
        // Сложная параллельная операция
        List<String> words = Arrays.asList("Java", "Python", "JavaScript", "Go", "Rust", "C++", "Kotlin", "Scala");
        
        Map<Integer, List<String>> wordsByLength = words.parallelStream()
            .collect(Collectors.groupingBy(String::length));
        
        System.out.println("Слова по длине (параллельно): " + wordsByLength);
        
        // Проверка на параллельность
        boolean isParallel = numbers.parallelStream().isParallel();
        System.out.println("Поток параллельный: " + isParallel);
        
        // Принудительное последовательное выполнение
        long sequentialFromParallel = numbers.parallelStream()
            .sequential()  // Делаем последовательным
            .mapToLong(Integer::longValue)
            .sum();
        System.out.println("Последовательная сумма из параллельного потока: " + sequentialFromParallel);
    }
}
```

### Когда использовать параллельные потоки
```java
public class ParallelStreamGuidelines {
    
    // ✅ Хорошо для параллелизации - CPU-интенсивные операции
    public static void goodParallelExample() {
        List<Integer> numbers = IntStream.range(1, 1000000).boxed().collect(Collectors.toList());
        
        // Сложные вычисления выигрывают от параллелизации
        OptionalDouble average = numbers.parallelStream()
            .mapToDouble(n -> Math.sqrt(n * n * n)) // Тяжелые вычисления
            .average();
        
        System.out.println("Средняя квадратного корня кубов: " + average.orElse(0));
    }
    
    // ❌ Плохо для параллелизации - простые операции
    public static void badParallelExample() {
        List<String> words = Arrays.asList("Java", "Python", "Go");
        
        // Для маленьких коллекций overhead больше выгоды
        List<String> upperCase = words.parallelStream()
            .map(String::toUpperCase) // Простая операция
            .collect(Collectors.toList());
    }
    
    // ❌ Опасно - операции с состоянием
    public static void dangerousParallelExample() {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        List<Integer> results = new ArrayList<>(); // Небезопасно для параллельности!
        
        // Это может привести к непредсказуемым результатам
        numbers.parallelStream()
            .forEach(results::add); // Race condition!
        
        // ✅ Правильный способ
        List<Integer> safeResults = numbers.parallelStream()
            .collect(Collectors.toList());
    }
    
    public static void main(String[] args) {
        goodParallelExample();
    }
}
```

---

## 💼 Практические примеры

### Анализ данных о продажах
```java
import java.time.LocalDate;
import java.math.BigDecimal;

class Sale {
    private String product;
    private BigDecimal amount;
    private LocalDate date;
    private String region;
    
    public Sale(String product, BigDecimal amount, LocalDate date, String region) {
        this.product = product;
        this.amount = amount;
        this.date = date;
        this.region = region;
    }
    
    // Геттеры
    public String getProduct() { return product; }
    public BigDecimal getAmount() { return amount; }
    public LocalDate getDate() { return date; }
    public String getRegion() { return region; }
}

public class SalesAnalysisExample {
    public static void main(String[] args) {
        List<Sale> sales = Arrays.asList(
            new Sale("Laptop", new BigDecimal("50000"), LocalDate.of(2024, 1, 15), "Москва"),
            new Sale("Phone", new BigDecimal("25000"), LocalDate.of(2024, 1, 20), "СПб"),
            new Sale("Tablet", new BigDecimal("15000"), LocalDate.of(2024, 2, 5), "Москва"),
            new Sale("Laptop", new BigDecimal("48000"), LocalDate.of(2024, 2, 10), "Казань"),
            new Sale("Phone", new BigDecimal("27000"), LocalDate.of(2024, 2, 25), "СПб"),
            new Sale("Monitor", new BigDecimal("12000"), LocalDate.of(2024, 3, 1), "Москва")
        );
        
        // Общая сумма продаж
        BigDecimal totalSales = sales.stream()
            .map(Sale::getAmount)
            .reduce(BigDecimal.ZERO, BigDecimal::add);
        System.out.println("Общие продажи: " + totalSales + " руб.");
        
        // Продажи по регионам
        Map<String, BigDecimal> salesByRegion = sales.stream()
            .collect(Collectors.groupingBy(
                Sale::getRegion,
                Collectors.reducing(BigDecimal.ZERO, Sale::getAmount, BigDecimal::add)
            ));
        
        System.out.println("Продажи по регионам:");
        salesByRegion.entrySet().stream()
            .sorted(Map.Entry.<String, BigDecimal>comparingByValue().reversed())
            .forEach(entry -> System.out.printf("  %s: %s руб.%n", entry.getKey(), entry.getValue()));
        
        // Топ-3 продукта по продажам
        Map<String, BigDecimal> salesByProduct = sales.stream()
            .collect(Collectors.groupingBy(
                Sale::getProduct,
                Collectors.reducing(BigDecimal.ZERO, Sale::getAmount, BigDecimal::add)
            ));
        
        System.out.println("Топ-3 продукта:");
        salesByProduct.entrySet().stream()
            .sorted(Map.Entry.<String, BigDecimal>comparingByValue().reversed())
            .limit(3)
            .forEach(entry -> System.out.printf("  %s: %s руб.%n", entry.getKey(), entry.getValue()));
        
        // Средняя сумма сделки по месяцам
        Map<Integer, OptionalDouble> averageSalesByMonth = sales.stream()
            .collect(Collectors.groupingBy(
                sale -> sale.getDate().getMonthValue(),
                Collectors.averagingDouble(sale -> sale.getAmount().doubleValue())
            ));
        
        System.out.println("Средняя сумма сделки по месяцам:");
        averageSalesByMonth.entrySet().stream()
            .sorted(Map.Entry.comparingByKey())
            .forEach(entry -> System.out.printf("  Месяц %d: %.2f руб.%n", 
                entry.getKey(), entry.getValue().orElse(0.0)));
        
        // Найти самую большую сделку в каждом регионе
        Map<String, Optional<Sale>> biggestSaleByRegion = sales.stream()
            .collect(Collectors.groupingBy(
                Sale::getRegion,
                Collectors.maxBy(Comparator.comparing(Sale::getAmount))
            ));
        
        System.out.println("Самые большие сделки по регионам:");
        biggestSaleByRegion.forEach((region, optionalSale) -> {
            optionalSale.ifPresent(sale -> 
                System.out.printf("  %s: %s за %s руб.%n", 
                    region, sale.getProduct(), sale.getAmount()));
        });
    }
}
```

### Обработка текстовых данных
```java
public class TextProcessingExample {
    public static void main(String[] args) {
        String text = """
            Java является одним из самых популярных языков программирования в мире.
            Он был создан компанией Sun Microsystems в 1995 году.
            Java используется для разработки веб-приложений, мобильных приложений и desktop приложений.
            Основные преимущества Java: портабельность, безопасность и производительность.
            """;
        
        // Разбиваем на предложения и обрабатываем
        List<String> sentences = Arrays.stream(text.split("[.!?]"))
            .map(String::trim)
            .filter(s -> !s.isEmpty())
            .collect(Collectors.toList());
        
        System.out.println("Количество предложений: " + sentences.size());
        
        // Находим все слова и считаем их частоту
        Map<String, Long> wordFrequency = sentences.stream()
            .flatMap(sentence -> Arrays.stream(sentence.toLowerCase().split("\\s+")))
            .map(word -> word.replaceAll("[^а-яёa-z]", "")) // Убираем знаки препинания
            .filter(word -> word.length() > 2) // Только слова длиннее 2 символов
            .collect(Collectors.groupingBy(word -> word, Collectors.counting()));
        
        // Топ-10 самых частых слов
        System.out.println("Топ-10 самых частых слов:");
        wordFrequency.entrySet().stream()
            .sorted(Map.Entry.<String, Long>comparingByValue().reversed())
            .limit(10)
            .forEach(entry -> System.out.printf("  %s: %d раз%n", entry.getKey(), entry.getValue()));
        
        // Средняя длина слов
        OptionalDouble averageWordLength = sentences.stream()
            .flatMap(sentence -> Arrays.stream(sentence.split("\\s+")))
            .map(word -> word.replaceAll("[^а-яёa-zA-Z]", ""))
            .filter(word -> !word.isEmpty())
            .mapToInt(String::length)
            .average();
        
        averageWordLength.ifPresent(avg -> 
            System.out.printf("Средняя длина слова: %.2f символов%n", avg));
        
        // Самое длинное предложение
        Optional<String> longestSentence = sentences.stream()
            .max(Comparator.comparing(String::length));
        
        longestSentence.ifPresent(sentence -> 
            System.out.println("Самое длинное предложение: " + sentence));
        
        // Предложения со словом "Java"
        List<String> sentencesWithJava = sentences.stream()
            .filter(sentence -> sentence.toLowerCase().contains("java"))
            .collect(Collectors.toList());
        
        System.out.println("Предложения со словом 'Java': " + sentencesWithJava.size());
        sentencesWithJava.forEach(sentence -> System.out.println("  - " + sentence));
    }
}
```

---

## 💡 Лучшие практики Stream API

### Performance и оптимизация
```java
// ✅ Используй примитивные стримы для чисел
IntStream.range(1, 1000)
    .filter(n -> n % 2 == 0)
    .sum(); // Быстрее чем boxed типы

// ✅ Ограничивай данные как можно раньше
list.stream()
    .filter(expensive::predicate)  // Дешевая операция
    .limit(10)                     // Ограничиваем рано
    .map(expensive::operation)     // Дорогая операция
    .collect(Collectors.toList());

// ❌ Не создавай промежуточные коллекции без необходимости
list.stream()
    .collect(Collectors.toList())  // Ненужная материализация
    .stream()
    .filter(predicate)
    .collect(Collectors.toList());

// ✅ Лучше объединить в один поток
list.stream()
    .filter(predicate)
    .collect(Collectors.toList());
```

### Читаемость кода
```java
// ✅ Разбивай длинные цепочки на строки
people.stream()
    .filter(person -> person.getAge() > 18)
    .filter(person -> person.getCity().equals("Москва"))
    .map(Person::getName)
    .sorted()
    .collect(Collectors.toList());

// ✅ Используй осмысленные имена переменных
people.stream()
    .collect(Collectors.groupingBy(
        person -> person.getAge() / 10 * 10, // Возрастные декады
        Collectors.mapping(Person::getName, Collectors.toList())
    ));
```

---

## 🔗 Связанные темы
- [[Lambda-Expressions-Code|Lambda выражения]] - основа функционального программирования
- [[Collections-Overview|Collections Framework]] - коллекции для обработки
- [[Method-References|Ссылки на методы]] - краткий синтаксис
- [[Functional-Interfaces|Функциональные интерфейсы]] - базовые функциональные типы