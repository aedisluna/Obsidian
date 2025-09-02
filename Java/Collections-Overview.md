# Java Collections Framework
*Полное руководство по коллекциям в Java*

#java #collections #list #set #map #arraylist #hashmap

---

## 📋 Содержание
- [[#Обзор Collections Framework]]
- [[#Иерархия коллекций]]
- [[#List - Списки]]
- [[#Set - Множества]]
- [[#Map - Карты]]
- [[#Queue - Очереди]]
- [[#Сравнение реализаций]]
- [[#Практические примеры]]

---

## 🎯 Обзор Collections Framework

> [!info] Collections Framework - это единая архитектура для работы с коллекциями объектов

### Основные компоненты:
- **Интерфейсы** - определяют функциональность коллекций
- **Реализации** - конкретные классы коллекций  
- **Алгоритмы** - методы для поиска, сортировки и т.д.
- **Утилиты** - вспомогательные классы и методы

### Преимущества:
- **Единообразие** - общие интерфейсы для всех коллекций
- **Производительность** - оптимизированные реализации
- **Расширяемость** - можно создавать свои коллекции
- **Совместимость** - работает с любыми объектами

---

## 🌳 Иерархия коллекций

```
                    Iterable<E>
                        |
                   Collection<E>
                        |
       ┌────────────────┼────────────────┐
       |                |                |
    List<E>          Set<E>           Queue<E>
       |                |                |
┌──────┼──────┐   ┌─────┼─────┐    ┌─────┼─────┐
|      |      |   |     |     |    |     |     |
ArrayList LinkedList HashSet TreeSet ArrayDeque PriorityQueue
Vector               LinkedHashSet    LinkedList
Stack


                    Map<K,V> (отдельная иерархия)
                        |
        ┌───────────────┼───────────────┐
        |               |               |
    HashMap         TreeMap        LinkedHashMap
    Hashtable       
    Properties
```

---

## 📝 List - Списки

> [!tip] List - упорядоченная коллекция, допускающая дубликаты и индексный доступ

### Основные реализации:

#### ArrayList
```java
import java.util.*;

public class ArrayListDemo {
    public static void main(String[] args) {
        // Создание ArrayList
        List<String> fruits = new ArrayList<>();
        
        // Добавление элементов
        fruits.add("Яблоко");
        fruits.add("Банан");
        fruits.add("Апельсин");
        fruits.add("Яблоко"); // Дубликаты разрешены
        
        System.out.println("Фрукты: " + fruits);
        System.out.println("Размер: " + fruits.size());
        
        // Доступ по индексу
        System.out.println("Первый фрукт: " + fruits.get(0));
        System.out.println("Последний фрукт: " + fruits.get(fruits.size() - 1));
        
        // Поиск элемента
        int index = fruits.indexOf("Банан");
        System.out.println("Банан находится под индексом: " + index);
        
        // Изменение элемента
        fruits.set(1, "Груша");
        System.out.println("После замены: " + fruits);
        
        // Удаление
        fruits.remove("Яблоко");        // Удаляет первое вхождение
        fruits.remove(2);               // Удаляет по индексу
        System.out.println("После удаления: " + fruits);
        
        // Проверка содержания
        if (fruits.contains("Груша")) {
            System.out.println("Груша есть в списке");
        }
        
        // Итерация
        System.out.println("Перебор через enhanced for:");
        for (String fruit : fruits) {
            System.out.println("- " + fruit);
        }
        
        // Итерация с индексами
        System.out.println("Перебор с индексами:");
        for (int i = 0; i < fruits.size(); i++) {
            System.out.printf("%d: %s%n", i, fruits.get(i));
        }
    }
}
```

#### LinkedList
```java
import java.util.*;

public class LinkedListDemo {
    public static void main(String[] args) {
        LinkedList<Integer> numbers = new LinkedList<>();
        
        // Добавление в разные позиции
        numbers.add(10);           // В конец
        numbers.addFirst(5);       // В начало
        numbers.addLast(20);       // В конец (то же что add)
        numbers.add(1, 15);        // По индексу
        
        System.out.println("Числа: " + numbers); // [5, 15, 10, 20]
        
        // Получение элементов
        System.out.println("Первый: " + numbers.getFirst());
        System.out.println("Последний: " + numbers.getLast());
        System.out.println("По индексу 2: " + numbers.get(2));
        
        // Удаление
        numbers.removeFirst();     // Удаляет первый
        numbers.removeLast();      // Удаляет последний
        System.out.println("После удаления: " + numbers);
        
        // LinkedList как очередь (Queue)
        Queue<String> queue = new LinkedList<>();
        queue.offer("Первый");     // Добавить в очередь
        queue.offer("Второй");
        queue.offer("Третий");
        
        System.out.println("Очередь: " + queue);
        System.out.println("Головной элемент: " + queue.peek()); // Не удаляет
        System.out.println("Извлечен: " + queue.poll()); // Удаляет и возвращает
        System.out.println("Очередь после извлечения: " + queue);
        
        // LinkedList как стек (Deque)
        Deque<String> stack = new LinkedList<>();
        stack.push("Нижний");      // Добавить в стек
        stack.push("Средний");
        stack.push("Верхний");
        
        System.out.println("Стек: " + stack);
        System.out.println("Верхний элемент: " + stack.peek());
        System.out.println("Извлечен: " + stack.pop());
        System.out.println("Стек после извлечения: " + stack);
    }
}
```

### ArrayList vs LinkedList

| Характеристика | ArrayList | LinkedList |
|----------------|-----------|------------|
| **Структура данных** | Динамический массив | Двусвязный список |
| **Доступ по индексу** | O(1) - очень быстро | O(n) - медленно |
| **Вставка/удаление в начале** | O(n) - медленно | O(1) - быстро |
| **Вставка/удаление в конце** | O(1) амортизированно | O(1) - быстро |
| **Вставка/удаление в середине** | O(n) - медленно | O(n) - медленно |
| **Память** | Компактное хранение | Дополнительная память на указатели |
| **Лучше использовать** | Частый доступ по индексу | Частые вставки/удаления |

---

## 🎯 Set - Множества

> [!tip] Set - коллекция уникальных элементов (без дубликатов)

### HashSet
```java
import java.util.*;

public class HashSetDemo {
    public static void main(String[] args) {
        Set<String> cities = new HashSet<>();
        
        // Добавление элементов
        cities.add("Москва");
        cities.add("Санкт-Петербург");
        cities.add("Новосибирск");
        cities.add("Москва"); // Дубликат - не добавится
        
        System.out.println("Города: " + cities);
        System.out.println("Размер: " + cities.size()); // 3, не 4
        
        // Проверка содержания
        if (cities.contains("Москва")) {
            System.out.println("Москва есть в множестве");
        }
        
        // Удаление
        cities.remove("Новосибирск");
        System.out.println("После удаления: " + cities);
        
        // Перебор (порядок не гарантируется!)
        System.out.println("Перебор городов:");
        for (String city : cities) {
            System.out.println("- " + city);
        }
        
        // Операции над множествами
        Set<String> russianCities = new HashSet<>(Arrays.asList(
            "Москва", "Санкт-Петербург", "Казань", "Екатеринбург"
        ));
        
        Set<String> europeanCities = new HashSet<>(Arrays.asList(
            "Москва", "Санкт-Петербург", "Париж", "Берлин", "Рим"
        ));
        
        // Пересечение (общие элементы)
        Set<String> intersection = new HashSet<>(russianCities);
        intersection.retainAll(europeanCities);
        System.out.println("Пересечение: " + intersection);
        
        // Объединение
        Set<String> union = new HashSet<>(russianCities);
        union.addAll(europeanCities);
        System.out.println("Объединение: " + union);
        
        // Разность
        Set<String> difference = new HashSet<>(russianCities);
        difference.removeAll(europeanCities);
        System.out.println("Только русские города: " + difference);
    }
}
```

### TreeSet
```java
import java.util.*;

public class TreeSetDemo {
    public static void main(String[] args) {
        // TreeSet автоматически сортирует элементы
        TreeSet<Integer> numbers = new TreeSet<>();
        
        numbers.add(15);
        numbers.add(3);
        numbers.add(25);
        numbers.add(8);
        numbers.add(3); // Дубликат - не добавится
        
        System.out.println("Отсортированные числа: " + numbers); // [3, 8, 15, 25]
        
        // Специальные методы TreeSet
        System.out.println("Наименьший: " + numbers.first());
        System.out.println("Наибольший: " + numbers.last());
        System.out.println("Меньше 10: " + numbers.headSet(10));
        System.out.println("От 10 и больше: " + numbers.tailSet(10));
        System.out.println("От 5 до 20: " + numbers.subSet(5, 20));
        
        // TreeSet со строками
        TreeSet<String> names = new TreeSet<>();
        names.addAll(Arrays.asList("Анна", "Борис", "Виктор", "Анна"));
        System.out.println("Имена по алфавиту: " + names);
        
        // Пользовательская сортировка
        TreeSet<String> namesByLength = new TreeSet<>(
            Comparator.comparing(String::length).thenComparing(String::compareToIgnoreCase)
        );
        namesByLength.addAll(Arrays.asList("Анна", "Александр", "Ян", "Екатерина"));
        System.out.println("Имена по длине и алфавиту: " + namesByLength);
    }
}
```

### LinkedHashSet
```java
import java.util.*;

public class LinkedHashSetDemo {
    public static void main(String[] args) {
        // LinkedHashSet сохраняет порядок вставки
        Set<String> colors = new LinkedHashSet<>();
        
        colors.add("Красный");
        colors.add("Зеленый");
        colors.add("Синий");
        colors.add("Красный"); // Дубликат - не добавится
        colors.add("Желтый");
        
        System.out.println("Цвета в порядке добавления: " + colors);
        // [Красный, Зеленый, Синий, Желтый]
        
        // Сравнение различных Set
        demonstrateSetDifferences();
    }
    
    static void demonstrateSetDifferences() {
        String[] fruits = {"Яблоко", "Банан", "Апельсин", "Яблоко", "Груша"};
        
        Set<String> hashSet = new HashSet<>(Arrays.asList(fruits));
        Set<String> linkedHashSet = new LinkedHashSet<>(Arrays.asList(fruits));
        TreeSet<String> treeSet = new TreeSet<>(Arrays.asList(fruits));
        
        System.out.println("HashSet (произвольный порядок): " + hashSet);
        System.out.println("LinkedHashSet (порядок вставки): " + linkedHashSet);
        System.out.println("TreeSet (отсортированный): " + treeSet);
    }
}
```

---

## 🗺️ Map - Карты

> [!tip] Map - коллекция пар "ключ-значение", где ключи уникальны

### HashMap
```java
import java.util.*;

public class HashMapDemo {
    public static void main(String[] args) {
        // Создание карты
        Map<String, Integer> ages = new HashMap<>();
        
        // Добавление элементов
        ages.put("Анна", 25);
        ages.put("Борис", 30);
        ages.put("Виктор", 28);
        ages.put("Анна", 26); // Обновляет существующий ключ
        
        System.out.println("Возрасты: " + ages);
        System.out.println("Размер: " + ages.size());
        
        // Получение значений
        Integer annaAge = ages.get("Анна");
        System.out.println("Возраст Анны: " + annaAge);
        
        // Безопасное получение с значением по умолчанию
        Integer unknownAge = ages.getOrDefault("Дмитрий", 0);
        System.out.println("Возраст Дмитрия: " + unknownAge);
        
        // Проверки
        if (ages.containsKey("Борис")) {
            System.out.println("Борис есть в карте");
        }
        
        if (ages.containsValue(28)) {
            System.out.println("Кому-то 28 лет");
        }
        
        // Перебор ключей
        System.out.println("Все имена:");
        for (String name : ages.keySet()) {
            System.out.println("- " + name);
        }
        
        // Перебор значений
        System.out.println("Все возрасты:");
        for (Integer age : ages.values()) {
            System.out.println("- " + age);
        }
        
        // Перебор пар ключ-значение
        System.out.println("Пары ключ-значение:");
        for (Map.Entry<String, Integer> entry : ages.entrySet()) {
            System.out.printf("- %s: %d лет%n", entry.getKey(), entry.getValue());
        }
        
        // Современный способ перебора (Java 8+)
        System.out.println("Используя forEach:");
        ages.forEach((name, age) -> System.out.printf("- %s: %d лет%n", name, age));
        
        // Удаление
        ages.remove("Виктор");
        System.out.println("После удаления Виктора: " + ages);
    }
}
```

### Практический пример: Подсчет слов
```java
import java.util.*;

public class WordCountDemo {
    public static void main(String[] args) {
        String text = "Java это круто Java это мощно Python тоже круто";
        String[] words = text.toLowerCase().split(" ");
        
        // Подсчет вхождений слов
        Map<String, Integer> wordCount = new HashMap<>();
        
        for (String word : words) {
            // Способ 1: традиционный
            if (wordCount.containsKey(word)) {
                wordCount.put(word, wordCount.get(word) + 1);
            } else {
                wordCount.put(word, 1);
            }
        }
        
        System.out.println("Подсчет слов (способ 1): " + wordCount);
        
        // Способ 2: используя getOrDefault
        Map<String, Integer> wordCount2 = new HashMap<>();
        for (String word : words) {
            wordCount2.put(word, wordCount2.getOrDefault(word, 0) + 1);
        }
        
        System.out.println("Подсчет слов (способ 2): " + wordCount2);
        
        // Способ 3: используя merge (Java 8+)
        Map<String, Integer> wordCount3 = new HashMap<>();
        for (String word : words) {
            wordCount3.merge(word, 1, Integer::sum);
        }
        
        System.out.println("Подсчет слов (способ 3): " + wordCount3);
        
        // Поиск самого частого слова
        String mostFrequentWord = null;
        int maxCount = 0;
        
        for (Map.Entry<String, Integer> entry : wordCount.entrySet()) {
            if (entry.getValue() > maxCount) {
                mostFrequentWord = entry.getKey();
                maxCount = entry.getValue();
            }
        }
        
        System.out.printf("Самое частое слово: '%s' (%d раз)%n", 
                         mostFrequentWord, maxCount);
    }
}
```

### TreeMap
```java
import java.util.*;

public class TreeMapDemo {
    public static void main(String[] args) {
        // TreeMap сортирует по ключам
        Map<String, String> countries = new TreeMap<>();
        
        countries.put("RU", "Россия");
        countries.put("US", "США");
        countries.put("DE", "Германия");
        countries.put("CN", "Китай");
        countries.put("FR", "Франция");
        
        System.out.println("Страны по алфавиту кодов:");
        countries.forEach((code, name) -> System.out.println(code + ": " + name));
        
        // TreeMap со сложными ключами
        Map<Integer, String> grades = new TreeMap<>(Collections.reverseOrder());
        grades.put(5, "Отлично");
        grades.put(4, "Хорошо");
        grades.put(3, "Удовлетворительно");
        grades.put(2, "Неудовлетворительно");
        
        System.out.println("Оценки по убыванию:");
        grades.forEach((grade, description) -> 
                      System.out.println(grade + ": " + description));
    }
}
```

---

## 🚀 Сравнение реализаций

### List реализации

| Операция | ArrayList | LinkedList | Vector |
|----------|-----------|------------|---------|
| **get(index)** | O(1) | O(n) | O(1) |
| **add(element)** | O(1)* | O(1) | O(1)* |
| **add(index, element)** | O(n) | O(n) | O(n) |
| **remove(index)** | O(n) | O(n) | O(n) |
| **Потокобезопасность** | ❌ | ❌ | ✅ |
| **Дата появления** | Java 1.2 | Java 1.2 | Java 1.0 |

*амортизированная сложность

### Set реализации

| Характеристика | HashSet | LinkedHashSet | TreeSet |
|----------------|---------|---------------|---------|
| **Порядок** | Произвольный | Вставки | Сортированный |
| **add/remove/contains** | O(1) | O(1) | O(log n) |
| **Null значения** | ✅ (одно) | ✅ (одно) | ❌ |
| **Сравнение элементов** | equals/hashCode | equals/hashCode | Comparable/Comparator |

### Map реализации

| Характеристика | HashMap | LinkedHashMap | TreeMap | Hashtable |
|----------------|---------|---------------|---------|-----------|
| **Порядок** | Произвольный | Вставки/доступа | Сортированный | Произвольный |
| **get/put/remove** | O(1) | O(1) | O(log n) | O(1) |
| **Null ключи** | ✅ (один) | ✅ (один) | ❌ | ❌ |
| **Null значения** | ✅ | ✅ | ✅ | ❌ |
| **Потокобезопасность** | ❌ | ❌ | ❌ | ✅ |

---

## 💡 Практические примеры

### Система управления студентами
```java
import java.util.*;

class Student {
    private String name;
    private int id;
    private double gpa;
    
    public Student(String name, int id, double gpa) {
        this.name = name;
        this.id = id;
        this.gpa = gpa;
    }
    
    // Геттеры
    public String getName() { return name; }
    public int getId() { return id; }
    public double getGpa() { return gpa; }
    
    @Override
    public String toString() {
        return String.format("Student{name='%s', id=%d, gpa=%.2f}", name, id, gpa);
    }
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Student student = (Student) obj;
        return id == student.id;
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
}

public class StudentManagement {
    public static void main(String[] args) {
        // Список всех студентов (может содержать дубликаты по имени)
        List<Student> allStudents = new ArrayList<>();
        allStudents.add(new Student("Анна Иванова", 1001, 4.2));
        allStudents.add(new Student("Борис Петров", 1002, 3.8));
        allStudents.add(new Student("Анна Сидорова", 1003, 4.5));
        allStudents.add(new Student("Дмитрий Козлов", 1004, 3.5));
        
        System.out.println("Все студенты:");
        allStudents.forEach(System.out::println);
        
        // Set для уникальных студентов (по ID)
        Set<Student> uniqueStudents = new HashSet<>(allStudents);
        System.out.println("\nУникальные студенты: " + uniqueStudents.size());
        
        // Map: ID -> Student для быстрого поиска
        Map<Integer, Student> studentsById = new HashMap<>();
        for (Student student : allStudents) {
            studentsById.put(student.getId(), student);
        }
        
        // Поиск студента по ID
        Student found = studentsById.get(1002);
        System.out.println("Найденный студент: " + found);
        
        // Группировка по первой букве имени
        Map<Character, List<Student>> studentsByFirstLetter = new HashMap<>();
        
        for (Student student : allStudents) {
            char firstLetter = student.getName().charAt(0);
            studentsByFirstLetter.computeIfAbsent(firstLetter, k -> new ArrayList<>())
                                 .add(student);
        }
        
        System.out.println("\nГруппировка по первой букве:");
        studentsByFirstLetter.forEach((letter, students) -> {
            System.out.println(letter + ": " + students.size() + " студентов");
            students.forEach(s -> System.out.println("  - " + s.getName()));
        });
        
        // Сортировка по GPA (убывание)
        List<Student> sortedByGPA = new ArrayList<>(allStudents);
        sortedByGPA.sort(Comparator.comparing(Student::getGpa).reversed());
        
        System.out.println("\nСтуденты по GPA (убывание):");
        sortedByGPA.forEach(System.out::println);
        
        // Поиск лучших студентов (GPA > 4.0)
        List<Student> topStudents = new ArrayList<>();
        for (Student student : allStudents) {
            if (student.getGpa() > 4.0) {
                topStudents.add(student);
            }
        }
        
        System.out.println("\nЛучшие студенты (GPA > 4.0):");
        topStudents.forEach(System.out::println);
    }
}
```

### Простой кеш с использованием LinkedHashMap
```java
import java.util.*;

class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int maxCapacity;
    
    public LRUCache(int maxCapacity) {
        // true означает порядок доступа (не вставки)
        super(16, 0.75f, true);
        this.maxCapacity = maxCapacity;
    }
    
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > maxCapacity;
    }
    
    public void displayCache() {
        System.out.println("Кеш (от старых к новым): " + this);
    }
}

public class CacheDemo {
    public static void main(String[] args) {
        LRUCache<String, String> cache = new LRUCache<>(3);
        
        // Добавляем элементы
        cache.put("key1", "value1");
        cache.put("key2", "value2");
        cache.put("key3", "value3");
        cache.displayCache();
        
        // Доступ к key1 делает его самым недавним
        cache.get("key1");
        cache.displayCache();
        
        // Добавление нового элемента вытесняет key2 (самый старый)
        cache.put("key4", "value4");
        cache.displayCache();
        
        // Проверяем что key2 удален
        System.out.println("key2 в кеше? " + cache.containsKey("key2"));
    }
}
```

---

## 🎯 Лучшие практики

### Выбор правильной коллекции
```java
// ✅ Используй ArrayList для частого доступа по индексу
List<String> names = new ArrayList<>();

// ✅ Используй LinkedList для частых вставок/удалений в начале/середине
Queue<Task> taskQueue = new LinkedList<>();

// ✅ Используй HashSet для быстрой проверки уникальности
Set<String> uniqueEmails = new HashSet<>();

// ✅ Используй TreeSet для автоматической сортировки
Set<Integer> sortedNumbers = new TreeSet<>();

// ✅ Используй HashMap для быстрого поиска по ключу
Map<String, User> userCache = new HashMap<>();

// ✅ Используй LinkedHashMap для сохранения порядка вставки
Map<String, String> orderedProperties = new LinkedHashMap<>();
```

### Безопасность и производительность
```java
// ✅ Указывай начальную емкость если знаешь примерный размер
List<String> bigList = new ArrayList<>(1000);
Map<String, Integer> bigMap = new HashMap<>(1000);

// ✅ Используй интерфейсы в объявлениях
List<String> list = new ArrayList<>();     // ✅
ArrayList<String> list2 = new ArrayList<>(); // ❌

// ✅ Создавай неизменяемые коллекции когда возможно
List<String> immutableList = Collections.unmodifiableList(list);

// ✅ Используй потокобезопасные коллекции в многопоточности
Map<String, String> syncMap = Collections.synchronizedMap(new HashMap<>());
List<String> syncList = Collections.synchronizedList(new ArrayList<>());

// ✅ Или используй concurrent коллекции
Map<String, String> concurrentMap = new ConcurrentHashMap<>();
```

### Обработка null значений
```java
// ✅ Проверяй на null перед добавлением в TreeSet/TreeMap
TreeSet<String> treeSet = new TreeSet<>();
String value = getValue(); // может вернуть null
if (value != null) {
    treeSet.add(value);
}

// ✅ Используй Optional для безопасной работы с Map
Map<String, String> map = new HashMap<>();
Optional<String> value = Optional.ofNullable(map.get("key"));
value.ifPresent(System.out::println);
```

---

## 🔗 Связанные темы
- [[Stream-API|Stream API]] - функциональная работа с коллекциями
- [[Lambda-Expressions|Lambda выражения]] - краткий синтаксис для функций
- [[Generics|Дженерики]] - типобезопасность коллекций
- [[Iterator|Итераторы]] - перебор коллекций