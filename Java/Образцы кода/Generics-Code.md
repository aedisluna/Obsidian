# Generics в Java
*Параметризованные типы для типобезопасности*

#java #generics #типы #type-safety #параметризация

---

## 📋 Содержание
- [[#Введение в Generics]]
- [[#Базовый синтаксис]]
- [[#Ограничения типов]]
- [[#Wildcards]]
- [[#Generic методы и классы]]
- [[#Type Erasure]]
- [[#Практические примеры]]

---

## 🎯 Введение в Generics

> [!info] Generics (дженерики) - это параметризованные типы, введенные в Java 5 для обеспечения типобезопасности во время компиляции

### Проблемы до Generics (Java 1.4 и ранее):
```java
// До Java 5 - небезопасные коллекции
List list = new ArrayList();
list.add("строка");
list.add(42);           // Можно добавить что угодно
list.add(new Date());

// Небезопасное извлечение с приведением типов
String str = (String) list.get(0); // Может вызвать ClassCastException
Integer num = (Integer) list.get(1); // Приведение во время выполнения
```

### С Generics (Java 5+):
```java
// Типобезопасные коллекции
List<String> stringList = new ArrayList<String>();
stringList.add("строка");
// stringList.add(42); // Ошибка компиляции!

// Безопасное извлечение без приведения
String str = stringList.get(0); // Компилятор знает тип
```

### Преимущества Generics:
- ✅ **Типобезопасность** во время компиляции
- ✅ **Устранение приведений типов** (casting)
- ✅ **Читаемость кода** - ясность о типах данных
- ✅ **Переиспользование кода** - один код для разных типов
- ✅ **Раннее обнаружение ошибок** - на этапе компиляции

---

## 📝 Базовый синтаксис

### Generic классы
```java
// Простой generic класс
public class Box<T> {
    private T content;
    
    public Box(T content) {
        this.content = content;
    }
    
    public T getContent() {
        return content;
    }
    
    public void setContent(T content) {
        this.content = content;
    }
    
    @Override
    public String toString() {
        return "Box{content=" + content + "}";
    }
}

// Использование
public class BasicGenericExample {
    public static void main(String[] args) {
        // Типобезопасное использование
        Box<String> stringBox = new Box<>("Hello, Generics!");
        Box<Integer> intBox = new Box<>(42);
        Box<List<String>> listBox = new Box<>(Arrays.asList("a", "b", "c"));
        
        // Извлечение без casting
        String str = stringBox.getContent();
        Integer num = intBox.getContent();
        List<String> list = listBox.getContent();
        
        System.out.println("String box: " + str);
        System.out.println("Integer box: " + num);
        System.out.println("List box: " + list);
        
        // Diamond operator (Java 7+)
        Box<String> diamondBox = new Box<>("Diamond operator");
        
        // Type inference (Java 10+)
        var autoBox = new Box<>("Автоматическое определение типа");
    }
}
```

### Generic классы с несколькими параметрами
```java
public class Pair<T, U> {
    private T first;
    private U second;
    
    public Pair(T first, U second) {
        this.first = first;
        this.second = second;
    }
    
    public T getFirst() { return first; }
    public U getSecond() { return second; }
    
    public void setFirst(T first) { this.first = first; }
    public void setSecond(U second) { this.second = second; }
    
    // Generic метод
    public <V> Pair<T, V> replaceSecond(V newSecond) {
        return new Pair<>(this.first, newSecond);
    }
    
    @Override
    public String toString() {
        return String.format("Pair{first=%s, second=%s}", first, second);
    }
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        
        Pair<?, ?> pair = (Pair<?, ?>) obj;
        return Objects.equals(first, pair.first) && Objects.equals(second, pair.second);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(first, second);
    }
}

// Утилитный класс с статическими generic методами
public class PairUtils {
    
    // Статический generic метод создания пары
    public static <T, U> Pair<T, U> of(T first, U second) {
        return new Pair<>(first, second);
    }
    
    // Обмен местами элементов пары
    public static <T, U> Pair<U, T> swap(Pair<T, U> pair) {
        return new Pair<>(pair.getSecond(), pair.getFirst());
    }
    
    // Применение функции к первому элементу
    public static <T, U, V> Pair<V, U> mapFirst(Pair<T, U> pair, Function<T, V> mapper) {
        return new Pair<>(mapper.apply(pair.getFirst()), pair.getSecond());
    }
    
    // Применение функции ко второму элементу  
    public static <T, U, V> Pair<T, V> mapSecond(Pair<T, U> pair, Function<U, V> mapper) {
        return new Pair<>(pair.getFirst(), mapper.apply(pair.getSecond()));
    }
}

// Использование
public class PairExample {
    public static void main(String[] args) {
        // Создание пар разных типов
        Pair<String, Integer> nameAge = new Pair<>("Иван", 30);
        Pair<Integer, String> idName = PairUtils.of(1001, "Алиса");
        Pair<Double, Boolean> priceAvailable = PairUtils.of(99.99, true);
        
        System.out.println("Имя и возраст: " + nameAge);
        System.out.println("ID и имя: " + idName);
        System.out.println("Цена и доступность: " + priceAvailable);
        
        // Обмен местами
        Pair<Integer, String> ageNameSwapped = PairUtils.swap(nameAge);
        System.out.println("После обмена: " + ageNameSwapped);
        
        // Преобразование элементов
        Pair<String, String> nameAgeString = PairUtils.mapSecond(nameAge, age -> age + " лет");
        Pair<String, Integer> upperName = PairUtils.mapFirst(nameAge, String::toUpperCase);
        
        System.out.println("Возраст как строка: " + nameAgeString);
        System.out.println("Имя в верхнем регистре: " + upperName);
        
        // Замена второго элемента
        Pair<String, Double> nameSalary = nameAge.replaceSecond(50000.0);
        System.out.println("Имя и зарплата: " + nameSalary);
    }
}
```

### Generic интерфейсы
```java
// Generic интерфейс
public interface Container<T> {
    void add(T item);
    T get(int index);
    int size();
    boolean isEmpty();
    
    default void addAll(Collection<? extends T> items) {
        for (T item : items) {
            add(item);
        }
    }
}

// Реализация generic интерфейса
public class SimpleContainer<T> implements Container<T> {
    private List<T> items = new ArrayList<>();
    
    @Override
    public void add(T item) {
        items.add(item);
    }
    
    @Override
    public T get(int index) {
        return items.get(index);
    }
    
    @Override
    public int size() {
        return items.size();
    }
    
    @Override
    public boolean isEmpty() {
        return items.isEmpty();
    }
    
    @Override
    public String toString() {
        return "SimpleContainer" + items;
    }
}

// Специализация для конкретного типа
public class StringContainer implements Container<String> {
    private final Container<String> delegate = new SimpleContainer<>();
    
    @Override
    public void add(String item) {
        if (item != null && !item.trim().isEmpty()) {
            delegate.add(item.trim());
        }
    }
    
    @Override
    public String get(int index) {
        return delegate.get(index);
    }
    
    @Override
    public int size() {
        return delegate.size();
    }
    
    @Override
    public boolean isEmpty() {
        return delegate.isEmpty();
    }
    
    // Дополнительные методы для работы со строками
    public String join(String delimiter) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < size(); i++) {
            if (i > 0) sb.append(delimiter);
            sb.append(get(i));
        }
        return sb.toString();
    }
    
    @Override
    public String toString() {
        return "StringContainer[" + join(", ") + "]";
    }
}

// Использование
public class GenericInterfaceExample {
    public static void main(String[] args) {
        // Generic контейнер для чисел
        Container<Integer> intContainer = new SimpleContainer<>();
        intContainer.add(1);
        intContainer.add(2);
        intContainer.add(3);
        
        System.out.println("Числовой контейнер: " + intContainer);
        System.out.println("Первый элемент: " + intContainer.get(0));
        
        // Специализированный строковый контейнер
        StringContainer stringContainer = new StringContainer();
        stringContainer.add("Java");
        stringContainer.add("  Generics  "); // Автоматически обрезается
        stringContainer.add(""); // Игнорируется
        stringContainer.add("Example");
        
        System.out.println("Строковый контейнер: " + stringContainer);
        System.out.println("Объединенная строка: " + stringContainer.join(" - "));
        
        // Добавление коллекции
        List<String> moreStrings = Arrays.asList("Hello", "World");
        stringContainer.addAll(moreStrings);
        System.out.println("После добавления коллекции: " + stringContainer);
    }
}
```

---

## 🔒 Ограничения типов (Bounded Type Parameters)

### Upper bounds (extends)
```java
// Ограничение сверху - T должен быть Number или его подклассом
public class NumberBox<T extends Number> {
    private T value;
    
    public NumberBox(T value) {
        this.value = value;
    }
    
    public T getValue() {
        return value;
    }
    
    // Можем вызывать методы Number
    public double getDoubleValue() {
        return value.doubleValue();
    }
    
    public int getIntValue() {
        return value.intValue();
    }
    
    // Сравнение числовых значений
    public boolean isGreaterThan(NumberBox<? extends Number> other) {
        return this.getDoubleValue() > other.getDoubleValue();
    }
    
    @Override
    public String toString() {
        return "NumberBox{value=" + value + ", type=" + value.getClass().getSimpleName() + "}";
    }
}

// Множественные ограничения
public class ComparableBox<T extends Number & Comparable<T>> {
    private T value;
    
    public ComparableBox(T value) {
        this.value = value;
    }
    
    public T getValue() {
        return value;
    }
    
    // Можем использовать как Number, так и Comparable
    public boolean isPositive() {
        return value.doubleValue() > 0;
    }
    
    public int compareTo(ComparableBox<T> other) {
        return this.value.compareTo(other.value);
    }
    
    public ComparableBox<T> max(ComparableBox<T> other) {
        return this.compareTo(other) >= 0 ? this : other;
    }
    
    @Override
    public String toString() {
        return "ComparableBox{value=" + value + "}";
    }
}

// Использование bounded types
public class BoundedTypeExample {
    public static void main(String[] args) {
        // Работает с любыми Number типами
        NumberBox<Integer> intBox = new NumberBox<>(42);
        NumberBox<Double> doubleBox = new NumberBox<>(3.14);
        NumberBox<Long> longBox = new NumberBox<>(1000L);
        
        System.out.println("Integer box: " + intBox);
        System.out.println("Double value as int: " + doubleBox.getIntValue());
        System.out.println("Is int box greater than double box? " + intBox.isGreaterThan(doubleBox));
        
        // Comparable boxes
        ComparableBox<Integer> box1 = new ComparableBox<>(10);
        ComparableBox<Integer> box2 = new ComparableBox<>(20);
        ComparableBox<Integer> box3 = new ComparableBox<>(5);
        
        System.out.println("\\nComparable boxes:");
        System.out.println("Box1: " + box1 + ", positive: " + box1.isPositive());
        System.out.println("Box2: " + box2 + ", positive: " + box2.isPositive());
        
        ComparableBox<Integer> maxBox = box1.max(box2).max(box3);
        System.out.println("Maximum box: " + maxBox);
        
        // Ошибка компиляции - String не extends Number
        // NumberBox<String> stringBox = new NumberBox<>("text"); // Не компилируется
    }
}
```

### Lower bounds и практические примеры
```java
// Утилитный класс для работы с коллекциями
public class CollectionUtils {
    
    // Upper bound - можем читать T или его подклассы
    public static <T> void copy(Collection<? extends T> source, Collection<? super T> destination) {
        for (T item : source) {
            destination.add(item);
        }
    }
    
    // Поиск максимального элемента
    public static <T extends Comparable<? super T>> T max(Collection<T> collection) {
        if (collection.isEmpty()) {
            throw new IllegalArgumentException("Collection is empty");
        }
        
        T max = null;
        for (T item : collection) {
            if (max == null || item.compareTo(max) > 0) {
                max = item;
            }
        }
        return max;
    }
    
    // Фильтрация с сохранением в другую коллекцию
    public static <T> void filter(Collection<? extends T> source, 
                                 Collection<? super T> destination, 
                                 Predicate<? super T> predicate) {
        for (T item : source) {
            if (predicate.test(item)) {
                destination.add(item);
            }
        }
    }
    
    // Преобразование элементов
    public static <T, R> List<R> map(Collection<? extends T> source, Function<? super T, ? extends R> mapper) {
        List<R> result = new ArrayList<>();
        for (T item : source) {
            result.add(mapper.apply(item));
        }
        return result;
    }
}

// Использование с иерархией классов
class Animal {
    protected String name;
    
    public Animal(String name) {
        this.name = name;
    }
    
    public String getName() { return name; }
    
    @Override
    public String toString() {
        return getClass().getSimpleName() + "{name='" + name + "'}";
    }
}

class Dog extends Animal implements Comparable<Dog> {
    public Dog(String name) {
        super(name);
    }
    
    @Override
    public int compareTo(Dog other) {
        return this.name.compareTo(other.name);
    }
}

class Cat extends Animal implements Comparable<Cat> {
    public Cat(String name) {
        super(name);
    }
    
    @Override
    public int compareTo(Cat other) {
        return this.name.compareTo(other.name);
    }
}

// Демонстрация wildcards
public class WildcardExample {
    public static void main(String[] args) {
        List<Dog> dogs = Arrays.asList(
            new Dog("Шарик"),
            new Dog("Бобик"),
            new Dog("Тузик")
        );
        
        List<Cat> cats = Arrays.asList(
            new Cat("Мурка"),
            new Cat("Васька")
        );
        
        // Копирование в коллекцию Animal
        List<Animal> animals = new ArrayList<>();
        CollectionUtils.copy(dogs, animals); // Dogs -> Animals (covariance)
        CollectionUtils.copy(cats, animals); // Cats -> Animals (covariance)
        
        System.out.println("Все животные: " + animals);
        
        // Поиск максимального элемента
        Dog maxDog = CollectionUtils.max(dogs);
        Cat maxCat = CollectionUtils.max(cats);
        
        System.out.println("Максимальная собака: " + maxDog);
        System.out.println("Максимальная кошка: " + maxCat);
        
        // Фильтрация
        List<Animal> longNameAnimals = new ArrayList<>();
        CollectionUtils.filter(animals, longNameAnimals, animal -> animal.getName().length() > 5);
        System.out.println("Животные с длинными именами: " + longNameAnimals);
        
        // Преобразование
        List<String> animalNames = CollectionUtils.map(animals, Animal::getName);
        List<String> upperNames = CollectionUtils.map(animalNames, String::toUpperCase);
        
        System.out.println("Имена животных: " + animalNames);
        System.out.println("Имена в верхнем регистре: " + upperNames);
    }
}
```

---

## 🃏 Wildcards (? - знак вопроса)

### Виды Wildcards
```java
public class WildcardTypes {
    
    // Unbounded wildcard - любой тип
    public static void printList(List<?> list) {
        for (Object item : list) {
            System.out.println(item);
        }
    }
    
    // Upper bounded wildcard - Number или его подклассы
    public static double sum(List<? extends Number> numbers) {
        double sum = 0.0;
        for (Number num : numbers) {
            sum += num.doubleValue();
        }
        return sum;
    }
    
    // Lower bounded wildcard - Integer или его суперклассы  
    public static void addNumbers(List<? super Integer> list) {
        list.add(1);
        list.add(2);
        list.add(3);
    }
    
    // PECS принцип: Producer Extends, Consumer Super
    
    // Producer (источник данных) - используем extends
    public static <T> void copy(List<? extends T> source, List<? super T> destination) {
        for (T item : source) {
            destination.add(item);
        }
    }
    
    // Сложный пример с wildcards
    public static void processData() {
        // Различные типы списков
        List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
        List<Double> doubles = Arrays.asList(1.1, 2.2, 3.3);
        List<Number> numbers = new ArrayList<>();
        List<Object> objects = new ArrayList<>();
        
        System.out.println("=== Печать списков (unbounded wildcard) ===");
        printList(integers);
        printList(doubles);
        
        System.out.println("\\n=== Суммирование (upper bounded) ===");
        System.out.println("Сумма целых чисел: " + sum(integers));
        System.out.println("Сумма дробных чисел: " + sum(doubles));
        
        System.out.println("\\n=== Добавление чисел (lower bounded) ===");
        addNumbers(numbers); // List<Number> - super для Integer
        addNumbers(objects); // List<Object> - super для Integer
        // addNumbers(integers); // Ошибка! List<Integer> не является super для Integer
        
        System.out.println("Numbers after adding: " + numbers);
        System.out.println("Objects after adding: " + objects);
        
        System.out.println("\\n=== Копирование (PECS) ===");
        List<Number> targetNumbers = new ArrayList<>();
        List<Object> targetObjects = new ArrayList<>();
        
        copy(integers, targetNumbers); // Integer extends Number
        copy(integers, targetObjects); // Integer extends Object
        copy(doubles, targetNumbers);  // Double extends Number
        
        System.out.println("Target numbers: " + targetNumbers);
        System.out.println("Target objects: " + targetObjects);
    }
    
    public static void main(String[] args) {
        processData();
    }
}
```

### Capture of wildcards
```java
public class WildcardCapture {
    
    // Проблема: нельзя добавить элемент в List<?>
    public static void problematic(List<?> list) {
        // list.add("string"); // Ошибка компиляции!
        // list.add(42);       // Ошибка компиляции!
        
        // Можно только читать как Object
        Object item = list.get(0);
        System.out.println("Item: " + item);
    }
    
    // Решение: capture helper
    public static void swap(List<?> list, int i, int j) {
        swapHelper(list, i, j);
    }
    
    private static <T> void swapHelper(List<T> list, int i, int j) {
        T temp = list.get(i);
        list.set(i, list.get(j));
        list.set(j, temp);
    }
    
    // Более сложный пример capture
    public static void reverse(List<?> list) {
        reverseHelper(list);
    }
    
    private static <T> void reverseHelper(List<T> list) {
        int size = list.size();
        for (int i = 0; i < size / 2; i++) {
            T temp = list.get(i);
            list.set(i, list.get(size - 1 - i));
            list.set(size - 1 - i, temp);
        }
    }
    
    // Generic метод для работы с любым типом
    public static <T> void shuffle(List<T> list) {
        Random random = new Random();
        for (int i = list.size() - 1; i > 0; i--) {
            int j = random.nextInt(i + 1);
            T temp = list.get(i);
            list.set(i, list.get(j));
            list.set(j, temp);
        }
    }
    
    public static void main(String[] args) {
        List<String> strings = new ArrayList<>(Arrays.asList("a", "b", "c", "d", "e"));
        List<Integer> integers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
        
        System.out.println("Исходные строки: " + strings);
        System.out.println("Исходные числа: " + integers);
        
        // Обмен элементов
        swap(strings, 0, 4);
        swap(integers, 1, 3);
        
        System.out.println("После обмена:");
        System.out.println("Строки: " + strings);
        System.out.println("Числа: " + integers);
        
        // Переворот
        reverse(strings);
        reverse(integers);
        
        System.out.println("После переворота:");
        System.out.println("Строки: " + strings);
        System.out.println("Числа: " + integers);
        
        // Перемешивание
        shuffle(strings);
        shuffle(integers);
        
        System.out.println("После перемешивания:");
        System.out.println("Строки: " + strings);
        System.out.println("Числа: " + integers);
    }
}
```

---

## 🔧 Generic методы

### Статические и нестатические generic методы
```java
public class GenericMethods {
    
    // Статический generic метод
    public static <T> void swap(T[] array, int i, int j) {
        T temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
    
    // Generic метод с bounded type
    public static <T extends Comparable<T>> T findMax(T[] array) {
        if (array.length == 0) {
            return null;
        }
        
        T max = array[0];
        for (int i = 1; i < array.length; i++) {
            if (array[i].compareTo(max) > 0) {
                max = array[i];
            }
        }
        return max;
    }
    
    // Generic метод с wildcards
    public static <T> boolean contains(List<? extends T> list, T element) {
        for (T item : list) {
            if (Objects.equals(item, element)) {
                return true;
            }
        }
        return false;
    }
    
    // Multiple type parameters
    public static <T, U, R> R combine(T first, U second, BiFunction<T, U, R> combiner) {
        return combiner.apply(first, second);
    }
    
    // Generic метод в generic классе
    public static class Utility<T> {
        private T defaultValue;
        
        public Utility(T defaultValue) {
            this.defaultValue = defaultValue;
        }
        
        // Метод использует тип класса
        public T getValueOrDefault(T value) {
            return value != null ? value : defaultValue;
        }
        
        // Generic метод с новым типом
        public <U> Pair<T, U> createPair(U second) {
            return new Pair<>(defaultValue, second);
        }
        
        // Generic метод с bounded type, отличным от типа класса
        public <N extends Number> double calculatePercentage(N value, N total) {
            return (value.doubleValue() / total.doubleValue()) * 100.0;
        }
    }
    
    // Демонстрация использования
    public static void main(String[] args) {
        // Массивы для тестирования
        String[] names = {"Зоя", "Анна", "Елена", "Борис"};
        Integer[] numbers = {5, 2, 8, 1, 9};
        
        System.out.println("Исходные массивы:");
        System.out.println("Имена: " + Arrays.toString(names));
        System.out.println("Числа: " + Arrays.toString(numbers));
        
        // Обмен элементов
        swap(names, 0, 3);
        swap(numbers, 1, 4);
        
        System.out.println("\\nПосле обмена:");
        System.out.println("Имена: " + Arrays.toString(names));
        System.out.println("Числа: " + Arrays.toString(numbers));
        
        // Поиск максимума
        String maxName = findMax(names);
        Integer maxNumber = findMax(numbers);
        
        System.out.println("\\nМаксимальные значения:");
        System.out.println("Максимальное имя: " + maxName);
        System.out.println("Максимальное число: " + maxNumber);
        
        // Проверка содержания
        List<String> nameList = Arrays.asList(names);
        boolean containsAnna = contains(nameList, "Анна");
        boolean containsPeter = contains(nameList, "Петр");
        
        System.out.println("\\nПроверка содержания:");
        System.out.println("Содержит 'Анна': " + containsAnna);
        System.out.println("Содержит 'Петр': " + containsPeter);
        
        // Комбинирование
        String combined = combine("Hello", 42, (str, num) -> str + " " + num);
        Integer sum = combine(10, 20, Integer::sum);
        
        System.out.println("\\nКомбинирование:");
        System.out.println("Строка + число: " + combined);
        System.out.println("Сумма: " + sum);
        
        // Utility класс
        Utility<String> stringUtility = new Utility<>("default");
        String result1 = stringUtility.getValueOrDefault("value");
        String result2 = stringUtility.getValueOrDefault(null);
        
        Pair<String, Integer> pair = stringUtility.createPair(100);
        
        double percentage = stringUtility.calculatePercentage(25, 100);
        
        System.out.println("\\nUtility класс:");
        System.out.println("Value or default ('value'): " + result1);
        System.out.println("Value or default (null): " + result2);
        System.out.println("Созданная пара: " + pair);
        System.out.println("Процент 25 из 100: " + percentage + "%");
    }
}
```

---

## 🧹 Type Erasure

> [!warning] Type Erasure - это процесс удаления информации о generic типах на этапе компиляции для совместимости с предыдущими версиями Java

### Как работает Type Erasure
```java
public class TypeErasureExample {
    
    // До компиляции
    public static void beforeCompilation() {
        List<String> stringList = new ArrayList<String>();
        List<Integer> integerList = new ArrayList<Integer>();
        
        stringList.add("Hello");
        integerList.add(42);
        
        String str = stringList.get(0);
        Integer num = integerList.get(0);
    }
    
    // После компиляции (примерно так выглядит байт-код)
    public static void afterCompilation() {
        List stringList = new ArrayList(); // Без generic типа
        List integerList = new ArrayList(); // Без generic типа
        
        stringList.add("Hello");
        integerList.add(42);
        
        String str = (String) stringList.get(0); // Автоматическое casting
        Integer num = (Integer) integerList.get(0); // Автоматическое casting
    }
    
    // Проблемы, связанные с Type Erasure
    public static void demonstrateProblems() {
        List<String> stringList = new ArrayList<>();
        List<Integer> integerList = new ArrayList<>();
        
        // 1. Нельзя проверить generic type во время выполнения
        System.out.println("stringList instanceof List: " + (stringList instanceof List));
        // System.out.println(stringList instanceof List<String>); // Ошибка компиляции!
        
        // 2. Одинаковый Class объект для разных generic типов
        System.out.println("stringList.getClass(): " + stringList.getClass());
        System.out.println("integerList.getClass(): " + integerList.getClass());
        System.out.println("Same class? " + (stringList.getClass() == integerList.getClass()));
        
        // 3. Нельзя создать массив generic типа
        // List<String>[] arrayOfLists = new List<String>[10]; // Ошибка компиляции!
        List<String>[] arrayOfLists = new List[10]; // Предупреждение, но работает
        
        // 4. Нельзя создать экземпляр generic типа
        // T instance = new T(); // Ошибка компиляции!
    }
    
    // Обходные пути для ограничений Type Erasure
    public static class GenericClass<T> {
        private Class<T> clazz;
        
        // Передаем Class объект для сохранения информации о типе
        public GenericClass(Class<T> clazz) {
            this.clazz = clazz;
        }
        
        // Можем создать экземпляр T
        public T createInstance() throws Exception {
            return clazz.getDeclaredConstructor().newInstance();
        }
        
        // Можем проверить тип
        public boolean isInstance(Object obj) {
            return clazz.isInstance(obj);
        }
        
        // Можем создать массив
        @SuppressWarnings("unchecked")
        public T[] createArray(int size) {
            return (T[]) Array.newInstance(clazz, size);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Демонстрация проблем Type Erasure ===");
        demonstrateProblems();
        
        System.out.println("\\n=== Обходные пути ===");
        try {
            GenericClass<String> stringClass = new GenericClass<>(String.class);
            GenericClass<Integer> integerClass = new GenericClass<>(Integer.class);
            
            // Создание экземпляров
            String str = stringClass.createInstance();
            System.out.println("Создан String: '" + str + "'");
            
            // Проверка типов
            System.out.println("'Hello' is String? " + stringClass.isInstance("Hello"));
            System.out.println("42 is String? " + stringClass.isInstance(42));
            System.out.println("42 is Integer? " + integerClass.isInstance(42));
            
            // Создание массивов
            String[] stringArray = stringClass.createArray(5);
            Integer[] integerArray = integerClass.createArray(3);
            
            System.out.println("Создан массив строк размером: " + stringArray.length);
            System.out.println("Создан массив чисел размером: " + integerArray.length);
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### Raw Types и предупреждения
```java
@SuppressWarnings("unchecked") // Подавляем предупреждения для демонстрации
public class RawTypesExample {
    
    public static void demonstrateRawTypes() {
        // Raw type - List без generic параметра
        List rawList = new ArrayList();
        
        // Можем добавить что угодно
        rawList.add("String");
        rawList.add(42);
        rawList.add(new Date());
        
        System.out.println("Raw list: " + rawList);
        
        // Опасное извлечение - может вызвать ClassCastException
        for (Object item : rawList) {
            System.out.println("Item: " + item + " (" + item.getClass().getSimpleName() + ")");
        }
        
        // Смешивание raw и generic типов
        List<String> genericList = new ArrayList<>();
        genericList.add("Safe string");
        
        // Присваивание generic списка к raw - предупреждение
        List rawFromGeneric = genericList;
        rawFromGeneric.add(123); // Загрязняем типизированный список!
        
        System.out.println("Contaminated generic list: " + genericList);
        
        // Опасная операция - может вызвать ClassCastException
        try {
            for (String str : genericList) {
                System.out.println("String: " + str);
            }
        } catch (ClassCastException e) {
            System.err.println("ClassCastException: " + e.getMessage());
        }
    }
    
    // Правильный способ работы с legacy кодом
    public static void safeLegacyInteraction() {
        // Legacy метод возвращает raw List
        List legacyList = getLegacyList();
        
        // Безопасное преобразование
        List<String> safeList = new ArrayList<>();
        for (Object item : legacyList) {
            if (item instanceof String) {
                safeList.add((String) item);
            }
        }
        
        System.out.println("Safely converted list: " + safeList);
    }
    
    // Имитация legacy метода
    private static List getLegacyList() {
        List list = new ArrayList();
        list.add("Legacy item 1");
        list.add("Legacy item 2");
        list.add(123); // Не-строковый элемент
        return list;
    }
    
    public static void main(String[] args) {
        System.out.println("=== Демонстрация Raw Types ===");
        demonstrateRawTypes();
        
        System.out.println("\\n=== Безопасная работа с legacy кодом ===");
        safeLegacyInteraction();
    }
}
```

---

## 💼 Практические примеры

### Generic Repository Pattern
```java
// Базовая сущность
abstract class Entity {
    protected Long id;
    
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
}

// Конкретные сущности
class User extends Entity {
    private String username;
    private String email;
    
    public User(String username, String email) {
        this.username = username;
        this.email = email;
    }
    
    public String getUsername() { return username; }
    public String getEmail() { return email; }
    
    @Override
    public String toString() {
        return String.format("User{id=%d, username='%s', email='%s'}", id, username, email);
    }
}

class Product extends Entity {
    private String name;
    private double price;
    
    public Product(String name, double price) {
        this.name = name;
        this.price = price;
    }
    
    public String getName() { return name; }
    public double getPrice() { return price; }
    
    @Override
    public String toString() {
        return String.format("Product{id=%d, name='%s', price=%.2f}", id, name, price);
    }
}

// Generic Repository интерфейс
interface Repository<T extends Entity> {
    void save(T entity);
    Optional<T> findById(Long id);
    List<T> findAll();
    void delete(Long id);
    boolean existsById(Long id);
}

// Generic Repository реализация
class InMemoryRepository<T extends Entity> implements Repository<T> {
    private final Map<Long, T> storage = new HashMap<>();
    private Long nextId = 1L;
    
    @Override
    public void save(T entity) {
        if (entity.getId() == null) {
            entity.setId(nextId++);
        }
        storage.put(entity.getId(), entity);
    }
    
    @Override
    public Optional<T> findById(Long id) {
        return Optional.ofNullable(storage.get(id));
    }
    
    @Override
    public List<T> findAll() {
        return new ArrayList<>(storage.values());
    }
    
    @Override
    public void delete(Long id) {
        storage.remove(id);
    }
    
    @Override
    public boolean existsById(Long id) {
        return storage.containsKey(id);
    }
    
    public int count() {
        return storage.size();
    }
}

// Специализированные репозитории
class UserRepository extends InMemoryRepository<User> {
    
    public Optional<User> findByUsername(String username) {
        return findAll().stream()
                .filter(user -> user.getUsername().equals(username))
                .findFirst();
    }
    
    public List<User> findByEmailDomain(String domain) {
        return findAll().stream()
                .filter(user -> user.getEmail().endsWith("@" + domain))
                .collect(Collectors.toList());
    }
}

class ProductRepository extends InMemoryRepository<Product> {
    
    public List<Product> findByPriceRange(double minPrice, double maxPrice) {
        return findAll().stream()
                .filter(product -> product.getPrice() >= minPrice && product.getPrice() <= maxPrice)
                .collect(Collectors.toList());
    }
    
    public Optional<Product> findMostExpensive() {
        return findAll().stream()
                .max(Comparator.comparing(Product::getPrice));
    }
}

// Service слой с generic типами
class EntityService<T extends Entity> {
    protected final Repository<T> repository;
    
    public EntityService(Repository<T> repository) {
        this.repository = repository;
    }
    
    public T create(T entity) {
        repository.save(entity);
        return entity;
    }
    
    public Optional<T> get(Long id) {
        return repository.findById(id);
    }
    
    public List<T> getAll() {
        return repository.findAll();
    }
    
    public boolean update(Long id, T entity) {
        if (repository.existsById(id)) {
            entity.setId(id);
            repository.save(entity);
            return true;
        }
        return false;
    }
    
    public boolean delete(Long id) {
        if (repository.existsById(id)) {
            repository.delete(id);
            return true;
        }
        return false;
    }
}

// Использование Generic Repository Pattern
public class RepositoryPatternExample {
    public static void main(String[] args) {
        // Создаем репозитории
        UserRepository userRepo = new UserRepository();
        ProductRepository productRepo = new ProductRepository();
        
        // Создаем сервисы
        EntityService<User> userService = new EntityService<>(userRepo);
        EntityService<Product> productService = new EntityService<>(productRepo);
        
        // Работа с пользователями
        System.out.println("=== Работа с пользователями ===");
        User user1 = userService.create(new User("john_doe", "john@example.com"));
        User user2 = userService.create(new User("jane_smith", "jane@gmail.com"));
        User user3 = userService.create(new User("admin", "admin@example.com"));
        
        System.out.println("Все пользователи:");
        userService.getAll().forEach(System.out::println);
        
        // Специализированные методы UserRepository
        Optional<User> foundUser = userRepo.findByUsername("jane_smith");
        foundUser.ifPresent(user -> System.out.println("Найден пользователь: " + user));
        
        List<User> exampleUsers = userRepo.findByEmailDomain("example.com");
        System.out.println("Пользователи с @example.com: " + exampleUsers);
        
        // Работа с продуктами
        System.out.println("\\n=== Работа с продуктами ===");
        Product product1 = productService.create(new Product("Laptop", 999.99));
        Product product2 = productService.create(new Product("Mouse", 29.99));
        Product product3 = productService.create(new Product("Keyboard", 79.99));
        Product product4 = productService.create(new Product("Monitor", 299.99));
        
        System.out.println("Все продукты:");
        productService.getAll().forEach(System.out::println);
        
        // Специализированные методы ProductRepository
        List<Product> affordableProducts = productRepo.findByPriceRange(20.0, 100.0);
        System.out.println("Доступные продукты (20-100$): " + affordableProducts);
        
        Optional<Product> mostExpensive = productRepo.findMostExpensive();
        mostExpensive.ifPresent(product -> 
            System.out.println("Самый дорогой продукт: " + product));
        
        // Обновление и удаление
        System.out.println("\\n=== Обновление и удаление ===");
        boolean updated = userService.update(user1.getId(), new User("john_updated", "john.new@example.com"));
        System.out.println("Пользователь обновлен: " + updated);
        
        boolean deleted = productService.delete(product2.getId());
        System.out.println("Продукт удален: " + deleted);
        
        System.out.println("\\nФинальное состояние:");
        System.out.println("Пользователи: " + userService.getAll().size());
        System.out.println("Продукты: " + productService.getAll().size());
    }
}
```

### Generic Builder Pattern
```java
// Generic Builder с fluent interface
public class GenericBuilder<T> {
    private final Supplier<T> instantiator;
    private final List<Consumer<T>> modifiers = new ArrayList<>();
    
    public GenericBuilder(Supplier<T> instantiator) {
        this.instantiator = instantiator;
    }
    
    public static <T> GenericBuilder<T> of(Supplier<T> instantiator) {
        return new GenericBuilder<>(instantiator);
    }
    
    public <U> GenericBuilder<T> with(BiConsumer<T, U> setter, U value) {
        modifiers.add(instance -> setter.accept(instance, value));
        return this;
    }
    
    public GenericBuilder<T> with(Consumer<T> modifier) {
        modifiers.add(modifier);
        return this;
    }
    
    public T build() {
        T instance = instantiator.get();
        modifiers.forEach(modifier -> modifier.accept(instance));
        return instance;
    }
}

// Класс для тестирования builder
class Person {
    private String firstName;
    private String lastName;
    private int age;
    private String email;
    private List<String> hobbies = new ArrayList<>();
    
    // Setters
    public void setFirstName(String firstName) { this.firstName = firstName; }
    public void setLastName(String lastName) { this.lastName = lastName; }
    public void setAge(int age) { this.age = age; }
    public void setEmail(String email) { this.email = email; }
    public void setHobbies(List<String> hobbies) { this.hobbies = hobbies; }
    public void addHobby(String hobby) { this.hobbies.add(hobby); }
    
    // Getters
    public String getFirstName() { return firstName; }
    public String getLastName() { return lastName; }
    public int getAge() { return age; }
    public String getEmail() { return email; }
    public List<String> getHobbies() { return hobbies; }
    
    @Override
    public String toString() {
        return String.format("Person{firstName='%s', lastName='%s', age=%d, email='%s', hobbies=%s}",
                           firstName, lastName, age, email, hobbies);
    }
}

// Специализированный builder
class PersonBuilder {
    private final GenericBuilder<Person> builder;
    
    private PersonBuilder() {
        this.builder = GenericBuilder.of(Person::new);
    }
    
    public static PersonBuilder create() {
        return new PersonBuilder();
    }
    
    public PersonBuilder firstName(String firstName) {
        return with(Person::setFirstName, firstName);
    }
    
    public PersonBuilder lastName(String lastName) {
        return with(Person::setLastName, lastName);
    }
    
    public PersonBuilder age(int age) {
        return with(Person::setAge, age);
    }
    
    public PersonBuilder email(String email) {
        return with(Person::setEmail, email);
    }
    
    public PersonBuilder hobby(String hobby) {
        return with(person -> person.addHobby(hobby));
    }
    
    public PersonBuilder hobbies(String... hobbies) {
        return with(person -> {
            for (String hobby : hobbies) {
                person.addHobby(hobby);
            }
        });
    }
    
    private PersonBuilder with(BiConsumer<Person, String> setter, String value) {
        builder.with(setter, value);
        return this;
    }
    
    private PersonBuilder with(BiConsumer<Person, Integer> setter, Integer value) {
        builder.with(setter, value);
        return this;
    }
    
    private PersonBuilder with(Consumer<Person> modifier) {
        builder.with(modifier);
        return this;
    }
    
    public Person build() {
        return builder.build();
    }
}

// Использование Generic Builder
public class GenericBuilderExample {
    public static void main(String[] args) {
        System.out.println("=== Generic Builder ===");
        
        // Использование универсального builder
        Person person1 = GenericBuilder.of(Person::new)
            .with(Person::setFirstName, "Иван")
            .with(Person::setLastName, "Петров")
            .with(Person::setAge, 30)
            .with(Person::setEmail, "ivan@example.com")
            .with(person -> person.addHobby("Чтение"))
            .with(person -> person.addHobby("Программирование"))
            .build();
        
        System.out.println("Person 1: " + person1);
        
        // Использование специализированного builder
        Person person2 = PersonBuilder.create()
            .firstName("Анна")
            .lastName("Сидорова") 
            .age(25)
            .email("anna@example.com")
            .hobby("Рисование")
            .hobby("Музыка")
            .hobbies("Спорт", "Путешествия")
            .build();
        
        System.out.println("Person 2: " + person2);
        
        // Builder для других классов
        System.out.println("\\n=== Builder для StringBuilder ===");
        StringBuilder sb = GenericBuilder.of(StringBuilder::new)
            .with(StringBuilder::append, "Hello")
            .with(StringBuilder::append, " ")
            .with(StringBuilder::append, "World")
            .with(stringBuilder -> stringBuilder.append("!"))
            .build();
        
        System.out.println("StringBuilder result: " + sb.toString());
        
        // Builder для ArrayList
        System.out.println("\\n=== Builder для ArrayList ===");
        ArrayList<String> list = GenericBuilder.of(ArrayList<String>::new)
            .with((ArrayList<String> l, String item) -> l.add(item), "First")
            .with(l -> l.add("Second"))
            .with(l -> l.add("Third"))
            .with(l -> l.addAll(Arrays.asList("Fourth", "Fifth")))
            .build();
        
        System.out.println("ArrayList result: " + list);
    }
}
```

---

## 💡 Лучшие практики Generics

### 1. Предпочитай wildcards конкретным типам в API
```java
// ✅ Хорошо - более гибкий API
public static void processNumbers(List<? extends Number> numbers) {
    for (Number num : numbers) {
        System.out.println(num.doubleValue());
    }
}

// ❌ Менее гибко
public static void processNumbers(List<Number> numbers) {
    // Работает только с List<Number>, не с List<Integer> или List<Double>
}
```

### 2. Используй PECS принцип
```java
// Producer Extends, Consumer Super

// ✅ Producer - данные выходят из коллекции
public void readFrom(List<? extends T> source) {
    for (T item : source) {
        process(item);
    }
}

// ✅ Consumer - данные входят в коллекцию  
public void writeTo(List<? super T> destination) {
    destination.add(createItem());
}
```

### 3. Не используй raw types
```java
// ❌ Плохо
List list = new ArrayList();
Map map = new HashMap();

// ✅ Хорошо
List<String> list = new ArrayList<>();
Map<String, Integer> map = new HashMap<>();

// ✅ Если нужен произвольный тип
List<?> list = getList();
Map<?, ?> map = getMap();
```

---

## 🔗 Связанные темы
- [[Collections-Overview|Collections Framework]] - применение generics в коллекциях
- [[Lambda-Expressions-Code|Lambda выражения]] - работа с generic функциональными интерфейсами
- [[Exception-Handling-Code|Обработка исключений]] - generic исключения
- [[Reflection-Code|Рефлексия]] - работа с generic типами через рефлексию