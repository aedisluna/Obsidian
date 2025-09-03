# Arrays в Java
*Работа с массивами и класс Arrays*

#java #arrays #collections #data-structures #utilities

---

## 📋 Содержание
- [[#Основы массивов]]
- [[#Создание и инициализация]]
- [[#Операции с массивами]]
- [[#Класс Arrays]]
- [[#Многомерные массивы]]
- [[#Производительность]]
- [[#Лучшие практики]]

---

## 🎯 Основы массивов

> [!info] Массивы в Java - это объекты, которые содержат фиксированное количество элементов одного типа. Индексация начинается с 0

### Характеристики массивов в Java
- **Фиксированный размер**: задается при создании
- **Однородные элементы**: все элементы одного типа
- **Ссылочный тип**: массив - это объект в heap
- **Индексация с нуля**: первый элемент имеет индекс 0
- **Length поле**: свойство length содержит размер массива

```java
public class ArrayBasicsDemo {
    
    public static void demonstrateArrayBasics() {
        System.out.println("=== Основы массивов ===");
        
        // Основные концепции
        demonstrateArrayConcepts();
        
        // Типы массивов
        demonstrateArrayTypes();
        
        // Размер и границы
        demonstrateArrayBounds();
        
        // Массивы как объекты
        demonstrateArrayAsObjects();
    }
    
    private static void demonstrateArrayConcepts() {
        System.out.println("\n--- Основные концепции ---");
        
        // Создание и инициализация
        int[] numbers = {1, 2, 3, 4, 5};
        
        System.out.println("Массив numbers:");
        System.out.println("Размер: " + numbers.length);
        System.out.println("Первый элемент: " + numbers[0]);
        System.out.println("Последний элемент: " + numbers[numbers.length - 1]);
        
        // Изменение элементов
        System.out.println("\nДо изменения: " + java.util.Arrays.toString(numbers));
        numbers[2] = 100; // Изменяем третий элемент
        System.out.println("После изменения: " + java.util.Arrays.toString(numbers));
        
        // Итерация
        System.out.println("\nИтерация по массиву:");
        for (int i = 0; i < numbers.length; i++) {
            System.out.printf("numbers[%d] = %d%n", i, numbers[i]);
        }
        
        // Enhanced for loop
        System.out.println("\nEnhanced for loop:");
        for (int number : numbers) {
            System.out.println("Значение: " + number);
        }
    }
    
    private static void demonstrateArrayTypes() {
        System.out.println("\n--- Типы массивов ---");
        
        // Примитивные типы
        int[] intArray = {1, 2, 3};
        double[] doubleArray = {1.1, 2.2, 3.3};
        boolean[] boolArray = {true, false, true};
        char[] charArray = {'a', 'b', 'c'};
        
        System.out.println("Примитивные массивы:");
        System.out.println("int[]: " + java.util.Arrays.toString(intArray));
        System.out.println("double[]: " + java.util.Arrays.toString(doubleArray));
        System.out.println("boolean[]: " + java.util.Arrays.toString(boolArray));
        System.out.println("char[]: " + java.util.Arrays.toString(charArray));
        
        // Объектные типы
        String[] stringArray = {"Hello", "World", "Java"};
        Integer[] integerArray = {1, 2, 3, null}; // Может содержать null
        Object[] objectArray = {"String", 42, 3.14, true, null};
        
        System.out.println("\nОбъектные массивы:");
        System.out.println("String[]: " + java.util.Arrays.toString(stringArray));
        System.out.println("Integer[]: " + java.util.Arrays.toString(integerArray));
        System.out.println("Object[]: " + java.util.Arrays.toString(objectArray));
    }
    
    private static void demonstrateArrayBounds() {
        System.out.println("\n--- Размер и границы ---");
        
        int[] array = new int[5];
        System.out.println("Размер массива: " + array.length);
        System.out.println("Валидные индексы: 0 до " + (array.length - 1));
        
        // Демонстрация выхода за границы
        try {
            System.out.println("Попытка доступа к индексу 5:");
            int value = array[5]; // ArrayIndexOutOfBoundsException
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("❌ Ошибка: " + e.getMessage());
        }
        
        try {
            System.out.println("Попытка доступа к индексу -1:");
            int value = array[-1]; // ArrayIndexOutOfBoundsException
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("❌ Ошибка: " + e.getMessage());
        }
        
        // Безопасный доступ
        int index = 10;
        if (index >= 0 && index < array.length) {
            System.out.println("Элемент: " + array[index]);
        } else {
            System.out.println("✅ Индекс " + index + " вне границ массива");
        }
    }
    
    private static void demonstrateArrayAsObjects() {
        System.out.println("\n--- Массивы как объекты ---");
        
        int[] array1 = {1, 2, 3};
        int[] array2 = {1, 2, 3};
        int[] array3 = array1; // Ссылка на тот же объект
        
        // Сравнение ссылок
        System.out.println("array1 == array2: " + (array1 == array2)); // false - разные объекты
        System.out.println("array1 == array3: " + (array1 == array3)); // true - одинаковые ссылки
        
        // Сравнение содержимого
        System.out.println("Arrays.equals(array1, array2): " + 
                          java.util.Arrays.equals(array1, array2)); // true - одинаковое содержимое
        
        // Метаинформация о массиве
        System.out.println("\nМетаинформация:");
        System.out.println("Класс: " + array1.getClass().getName());
        System.out.println("Суперкласс: " + array1.getClass().getSuperclass().getName());
        System.out.println("Это массив: " + array1.getClass().isArray());
        System.out.println("Тип компонентов: " + array1.getClass().getComponentType().getName());
        
        // Hash code
        System.out.println("\nHash codes:");
        System.out.println("array1.hashCode(): " + array1.hashCode());
        System.out.println("Arrays.hashCode(array1): " + java.util.Arrays.hashCode(array1));
        
        // toString
        System.out.println("\nString representation:");
        System.out.println("array1.toString(): " + array1.toString()); // Неинформативно
        System.out.println("Arrays.toString(array1): " + java.util.Arrays.toString(array1)); // Читаемо
    }
    
    public static void main(String[] args) {
        demonstrateArrayBasics();
    }
}
```

---

## 🏗️ Создание и инициализация

### Способы создания массивов
```java
public class ArrayCreationDemo {
    
    public static void demonstrateArrayCreation() {
        System.out.println("=== Создание и инициализация массивов ===");
        
        // Различные способы создания
        demonstrateCreationMethods();
        
        // Инициализация значениями по умолчанию
        demonstrateDefaultValues();
        
        // Динамическое создание
        demonstrateDynamicCreation();
        
        // Копирование массивов
        demonstrateArrayCopying();
    }
    
    private static void demonstrateCreationMethods() {
        System.out.println("\n--- Способы создания ---");
        
        // 1. Объявление и инициализация литералом
        int[] method1 = {1, 2, 3, 4, 5};
        System.out.println("Метод 1 (литерал): " + java.util.Arrays.toString(method1));
        
        // 2. Создание с помощью new и литерала
        int[] method2 = new int[]{10, 20, 30, 40, 50};
        System.out.println("Метод 2 (new + литерал): " + java.util.Arrays.toString(method2));
        
        // 3. Создание с указанием размера
        int[] method3 = new int[5];
        System.out.println("Метод 3 (только размер): " + java.util.Arrays.toString(method3));
        
        // 4. Создание и заполнение
        int[] method4 = new int[5];
        for (int i = 0; i < method4.length; i++) {
            method4[i] = (i + 1) * 10;
        }
        System.out.println("Метод 4 (создание + заполнение): " + java.util.Arrays.toString(method4));
        
        // 5. Анонимный массив (для передачи в методы)
        demonstrateAnonymousArrays();
    }
    
    private static void demonstrateAnonymousArrays() {
        System.out.println("\n=== Анонимные массивы ===");
        
        // Можно передать анонимный массив в метод
        printArray(new int[]{100, 200, 300});
        printArray(new String[]{"a", "b", "c"});
        
        // Полезно для инициализации в конструкторах или методах
        processData(new double[]{1.1, 2.2, 3.3});
    }
    
    private static void printArray(int[] array) {
        System.out.println("int массив: " + java.util.Arrays.toString(array));
    }
    
    private static void printArray(String[] array) {
        System.out.println("String массив: " + java.util.Arrays.toString(array));
    }
    
    private static void processData(double[] data) {
        double sum = 0;
        for (double value : data) {
            sum += value;
        }
        System.out.println("Сумма данных: " + sum);
    }
    
    private static void demonstrateDefaultValues() {
        System.out.println("\n--- Значения по умолчанию ---");
        
        // Различные типы и их значения по умолчанию
        int[] intArray = new int[3];
        double[] doubleArray = new double[3];
        boolean[] booleanArray = new boolean[3];
        char[] charArray = new char[3];
        String[] stringArray = new String[3];
        Integer[] integerArray = new Integer[3];
        
        System.out.println("Значения по умолчанию:");
        System.out.println("int[]: " + java.util.Arrays.toString(intArray)); // [0, 0, 0]
        System.out.println("double[]: " + java.util.Arrays.toString(doubleArray)); // [0.0, 0.0, 0.0]
        System.out.println("boolean[]: " + java.util.Arrays.toString(booleanArray)); // [false, false, false]
        System.out.println("char[]: " + java.util.Arrays.toString(charArray)); // [\\0, \\0, \\0]
        System.out.println("String[]: " + java.util.Arrays.toString(stringArray)); // [null, null, null]
        System.out.println("Integer[]: " + java.util.Arrays.toString(integerArray)); // [null, null, null]
        
        // Проверка char значений по умолчанию
        System.out.println("\nПроверка char значений:");
        for (int i = 0; i < charArray.length; i++) {
            int charCode = (int) charArray[i];
            System.out.printf("charArray[%d] = '%c' (код: %d)%n", i, charArray[i], charCode);
        }
    }
    
    private static void demonstrateDynamicCreation() {
        System.out.println("\n--- Динамическое создание ---");
        
        // Размер массива может быть определен во время выполнения
        java.util.Scanner scanner = new java.util.Scanner("5\\n1 2 3 4 5"); // Эмуляция ввода
        
        System.out.print("Размер массива (эмуляция): ");
        int size = scanner.nextInt();
        int[] dynamicArray = new int[size];
        
        System.out.println("Введите " + size + " чисел (эмуляция):");
        for (int i = 0; i < dynamicArray.length; i++) {
            dynamicArray[i] = scanner.nextInt();
        }
        
        System.out.println("Созданный массив: " + java.util.Arrays.toString(dynamicArray));
        
        // Создание массива на основе другого массива
        int[] source = {1, 2, 3};
        int[] doubled = new int[source.length * 2];
        
        // Заполняем удвоенными значениями
        for (int i = 0; i < source.length; i++) {
            doubled[i * 2] = source[i];
            doubled[i * 2 + 1] = source[i] * 2;
        }
        
        System.out.println("Исходный массив: " + java.util.Arrays.toString(source));
        System.out.println("Удвоенный массив: " + java.util.Arrays.toString(doubled));
        
        scanner.close();
    }
    
    private static void demonstrateArrayCopying() {
        System.out.println("\n--- Копирование массивов ---");
        
        int[] original = {1, 2, 3, 4, 5};
        System.out.println("Оригинальный массив: " + java.util.Arrays.toString(original));
        
        // 1. Поверхностное копирование (shallow copy) - НЕ создает новый массив
        int[] shallowCopy = original;
        shallowCopy[0] = 999;
        System.out.println("После изменения shallow copy:");
        System.out.println("  original: " + java.util.Arrays.toString(original));
        System.out.println("  shallowCopy: " + java.util.Arrays.toString(shallowCopy));
        System.out.println("  Один и тот же объект: " + (original == shallowCopy));
        
        // Восстанавливаем оригинал
        original[0] = 1;
        
        // 2. clone() - создает новый массив
        int[] cloned = original.clone();
        cloned[0] = 100;
        System.out.println("\\nПосле изменения clone:");
        System.out.println("  original: " + java.util.Arrays.toString(original));
        System.out.println("  cloned: " + java.util.Arrays.toString(cloned));
        System.out.println("  Разные объекты: " + (original != cloned));
        
        // 3. Arrays.copyOf() - создает новый массив заданного размера
        int[] copyOf = java.util.Arrays.copyOf(original, 7); // Увеличиваем размер
        System.out.println("\\nArrays.copyOf (размер 7): " + java.util.Arrays.toString(copyOf));
        
        int[] copyOfSmaller = java.util.Arrays.copyOf(original, 3); // Уменьшаем размер
        System.out.println("Arrays.copyOf (размер 3): " + java.util.Arrays.toString(copyOfSmaller));
        
        // 4. Arrays.copyOfRange() - копирует диапазон
        int[] range = java.util.Arrays.copyOfRange(original, 1, 4); // Индексы 1-3
        System.out.println("Arrays.copyOfRange(1, 4): " + java.util.Arrays.toString(range));
        
        // 5. System.arraycopy() - самый быстрый способ
        int[] systemCopy = new int[original.length];
        System.arraycopy(original, 0, systemCopy, 0, original.length);
        System.out.println("System.arraycopy: " + java.util.Arrays.toString(systemCopy));
        
        // Частичное копирование с помощью System.arraycopy
        int[] partialCopy = new int[10];
        System.arraycopy(original, 0, partialCopy, 2, 3); // Копируем 3 элемента с позиции 2
        System.out.println("Частичное копирование: " + java.util.Arrays.toString(partialCopy));
        
        // Демонстрация копирования объектных массивов
        demonstrateObjectArrayCopying();
    }
    
    private static void demonstrateObjectArrayCopying() {
        System.out.println("\\n=== Копирование объектных массивов ===");
        
        // Создаем массив объектов
        Person[] people = {
            new Person("Alice", 30),
            new Person("Bob", 25),
            new Person("Charlie", 35)
        };
        
        System.out.println("Оригинальный массив людей:");
        printPeople(people);
        
        // Поверхностное копирование массива (но объекты остаются те же!)
        Person[] shallowCopy = people.clone();
        shallowCopy[0].setAge(99); // Изменяем объект
        
        System.out.println("\\nПосле изменения shallow copy:");
        System.out.println("Оригинал: " + people[0]);
        System.out.println("Копия: " + shallowCopy[0]);
        System.out.println("Один объект Person: " + (people[0] == shallowCopy[0])); // true!
        
        // Глубокое копирование (создаем новые объекты)
        Person[] deepCopy = new Person[people.length];
        for (int i = 0; i < people.length; i++) {
            deepCopy[i] = new Person(people[i].getName(), people[i].getAge());
        }
        
        deepCopy[0].setAge(50); // Изменяем копию
        
        System.out.println("\\nПосле изменения deep copy:");
        System.out.println("Оригинал: " + people[0]);
        System.out.println("Глубокая копия: " + deepCopy[0]);
        System.out.println("Разные объекты Person: " + (people[0] != deepCopy[0])); // true!
    }
    
    // Вспомогательный класс для демонстрации
    static class Person {
        private String name;
        private int age;
        
        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        public String getName() { return name; }
        public int getAge() { return age; }
        public void setAge(int age) { this.age = age; }
        
        @Override
        public String toString() {
            return String.format("Person{name='%s', age=%d}", name, age);
        }
    }
    
    private static void printPeople(Person[] people) {
        for (Person person : people) {
            System.out.println("  " + person);
        }
    }
    
    public static void main(String[] args) {
        demonstrateArrayCreation();
    }
}
```

---

## 🛠️ Операции с массивами

### Основные операции
```java
public class ArrayOperationsDemo {
    
    public static void demonstrateArrayOperations() {
        System.out.println("=== Операции с массивами ===");
        
        // Поиск элементов
        demonstrateSearching();
        
        // Изменение массивов
        demonstrateModification();
        
        // Фильтрация и трансформация
        demonstrateFilteringAndMapping();
        
        // Агрегатные операции
        demonstrateAggregation();
    }
    
    private static void demonstrateSearching() {
        System.out.println("\\n--- Поиск элементов ---");
        
        int[] numbers = {10, 25, 30, 45, 50, 65, 70, 85};
        String[] names = {"Alice", "Bob", "Charlie", "David", "Eve"};
        
        System.out.println("Массив чисел: " + java.util.Arrays.toString(numbers));
        System.out.println("Массив имен: " + java.util.Arrays.toString(names));
        
        // Линейный поиск
        int searchValue = 45;
        int linearIndex = linearSearch(numbers, searchValue);
        System.out.printf("\\nЛинейный поиск %d: индекс %d%n", searchValue, linearIndex);
        
        // Поиск в отсортированном массиве (бинарный поиск)
        java.util.Arrays.sort(numbers); // Массив уже отсортирован, но для примера
        int binaryIndex = java.util.Arrays.binarySearch(numbers, searchValue);
        System.out.printf("Бинарный поиск %d: индекс %d%n", searchValue, binaryIndex);
        
        // Поиск несуществующего элемента
        int notFound = java.util.Arrays.binarySearch(numbers, 99);
        System.out.printf("Поиск несуществующего элемента 99: %d%n", notFound);
        System.out.printf("Позиция для вставки: %d%n", -(notFound + 1));
        
        // Поиск в строковом массиве
        String searchName = "Charlie";
        int nameIndex = java.util.Arrays.binarySearch(names, searchName);
        System.out.printf("Поиск имени '%s': индекс %d%n", searchName, nameIndex);
        
        // Поиск с компаратором (нечувствительный к регистру)
        String[] mixedCaseNames = {"alice", "BOB", "Charlie", "DAVID", "eve"};
        java.util.Arrays.sort(mixedCaseNames, String.CASE_INSENSITIVE_ORDER);
        System.out.println("Отсортированный массив (без учета регистра): " + 
                          java.util.Arrays.toString(mixedCaseNames));
        
        int mixedIndex = java.util.Arrays.binarySearch(mixedCaseNames, "bob", 
                                                      String.CASE_INSENSITIVE_ORDER);
        System.out.printf("Поиск 'bob' без учета регистра: индекс %d%n", mixedIndex);
        
        // Поиск первого и последнего вхождения
        int[] duplicates = {1, 2, 2, 2, 3, 4, 4, 5};
        System.out.println("\\nМассив с дубликатами: " + java.util.Arrays.toString(duplicates));
        
        int value = 2;
        int firstIndex = findFirstOccurrence(duplicates, value);
        int lastIndex = findLastOccurrence(duplicates, value);
        System.out.printf("Число %d: первое вхождение - индекс %d, последнее - индекс %d%n", 
                         value, firstIndex, lastIndex);
    }
    
    private static int linearSearch(int[] array, int value) {
        for (int i = 0; i < array.length; i++) {
            if (array[i] == value) {
                return i;
            }
        }
        return -1;
    }
    
    private static int findFirstOccurrence(int[] array, int value) {
        for (int i = 0; i < array.length; i++) {
            if (array[i] == value) {
                return i;
            }
        }
        return -1;
    }
    
    private static int findLastOccurrence(int[] array, int value) {
        for (int i = array.length - 1; i >= 0; i--) {
            if (array[i] == value) {
                return i;
            }
        }
        return -1;
    }
    
    private static void demonstrateModification() {
        System.out.println("\\n--- Изменение массивов ---");
        
        int[] numbers = {1, 2, 3, 4, 5};
        System.out.println("Исходный массив: " + java.util.Arrays.toString(numbers));
        
        // Заполнение одним значением
        int[] filled = new int[5];
        java.util.Arrays.fill(filled, 42);
        System.out.println("Заполненный массив: " + java.util.Arrays.toString(filled));
        
        // Частичное заполнение
        java.util.Arrays.fill(filled, 1, 4, 99); // Индексы от 1 до 3 включительно
        System.out.println("Частично заполненный: " + java.util.Arrays.toString(filled));
        
        // Реверс массива
        int[] reversed = reverseArray(numbers.clone());
        System.out.println("Обращенный массив: " + java.util.Arrays.toString(reversed));
        
        // Сдвиг элементов
        int[] shifted = shiftLeft(numbers.clone(), 2);
        System.out.println("Сдвиг влево на 2: " + java.util.Arrays.toString(shifted));
        
        int[] shiftedRight = shiftRight(numbers.clone(), 1);
        System.out.println("Сдвиг вправо на 1: " + java.util.Arrays.toString(shiftedRight));
        
        // Удаление элемента (создание нового массива)
        int[] withRemoved = removeElement(numbers, 2); // Удаляем индекс 2
        System.out.println("С удаленным элементом [2]: " + java.util.Arrays.toString(withRemoved));
        
        // Вставка элемента
        int[] withInserted = insertElement(numbers, 2, 99); // Вставляем 99 в позицию 2
        System.out.println("С вставленным элементом: " + java.util.Arrays.toString(withInserted));
    }
    
    private static int[] reverseArray(int[] array) {
        for (int i = 0; i < array.length / 2; i++) {
            int temp = array[i];
            array[i] = array[array.length - 1 - i];
            array[array.length - 1 - i] = temp;
        }
        return array;
    }
    
    private static int[] shiftLeft(int[] array, int positions) {
        if (positions >= array.length) return new int[array.length];
        
        int[] result = new int[array.length];
        System.arraycopy(array, positions, result, 0, array.length - positions);
        return result;
    }
    
    private static int[] shiftRight(int[] array, int positions) {
        if (positions >= array.length) return new int[array.length];
        
        int[] result = new int[array.length];
        System.arraycopy(array, 0, result, positions, array.length - positions);
        return result;
    }
    
    private static int[] removeElement(int[] array, int index) {
        if (index < 0 || index >= array.length) return array.clone();
        
        int[] result = new int[array.length - 1];
        System.arraycopy(array, 0, result, 0, index);
        System.arraycopy(array, index + 1, result, index, array.length - index - 1);
        return result;
    }
    
    private static int[] insertElement(int[] array, int index, int value) {
        if (index < 0 || index > array.length) return array.clone();
        
        int[] result = new int[array.length + 1];
        System.arraycopy(array, 0, result, 0, index);
        result[index] = value;
        System.arraycopy(array, index, result, index + 1, array.length - index);
        return result;
    }
    
    private static void demonstrateFilteringAndMapping() {
        System.out.println("\\n--- Фильтрация и трансформация ---");
        
        int[] numbers = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
        System.out.println("Исходный массив: " + java.util.Arrays.toString(numbers));
        
        // Фильтрация четных чисел
        int[] evens = filterEven(numbers);
        System.out.println("Четные числа: " + java.util.Arrays.toString(evens));
        
        // Фильтрация по условию (больше 5)
        int[] greaterThan5 = filter(numbers, x -> x > 5);
        System.out.println("Больше 5: " + java.util.Arrays.toString(greaterThan5));
        
        // Трансформация (умножение на 2)
        int[] doubled = map(numbers, x -> x * 2);
        System.out.println("Умноженные на 2: " + java.util.Arrays.toString(doubled));
        
        // Трансформация (квадраты)
        int[] squares = map(numbers, x -> x * x);
        System.out.println("Квадраты: " + java.util.Arrays.toString(squares));
        
        // Комбинированная операция: четные числа, умноженные на 3
        int[] evenTripled = map(filterEven(numbers), x -> x * 3);
        System.out.println("Четные * 3: " + java.util.Arrays.toString(evenTripled));
        
        // Работа со строками
        String[] words = {"apple", "banana", "cherry", "date", "elderberry"};
        System.out.println("\\nИсходные слова: " + java.util.Arrays.toString(words));
        
        // Фильтрация длинных слов
        String[] longWords = filterStrings(words, s -> s.length() > 5);
        System.out.println("Длинные слова: " + java.util.Arrays.toString(longWords));
        
        // Преобразование в заглавные
        String[] upperCase = mapStrings(words, String::toUpperCase);
        System.out.println("Заглавные: " + java.util.Arrays.toString(upperCase));
    }
    
    @FunctionalInterface
    interface IntPredicate {
        boolean test(int value);
    }
    
    @FunctionalInterface 
    interface IntFunction {
        int apply(int value);
    }
    
    @FunctionalInterface
    interface StringPredicate {
        boolean test(String value);
    }
    
    @FunctionalInterface
    interface StringFunction {
        String apply(String value);
    }
    
    private static int[] filterEven(int[] array) {
        // Сначала подсчитываем количество четных чисел
        int count = 0;
        for (int num : array) {
            if (num % 2 == 0) count++;
        }
        
        // Создаем результирующий массив и заполняем его
        int[] result = new int[count];
        int index = 0;
        for (int num : array) {
            if (num % 2 == 0) {
                result[index++] = num;
            }
        }
        return result;
    }
    
    private static int[] filter(int[] array, IntPredicate predicate) {
        // Подсчитываем элементы, удовлетворяющие условию
        int count = 0;
        for (int num : array) {
            if (predicate.test(num)) count++;
        }
        
        // Создаем и заполняем результат
        int[] result = new int[count];
        int index = 0;
        for (int num : array) {
            if (predicate.test(num)) {
                result[index++] = num;
            }
        }
        return result;
    }
    
    private static int[] map(int[] array, IntFunction function) {
        int[] result = new int[array.length];
        for (int i = 0; i < array.length; i++) {
            result[i] = function.apply(array[i]);
        }
        return result;
    }
    
    private static String[] filterStrings(String[] array, StringPredicate predicate) {
        int count = 0;
        for (String s : array) {
            if (predicate.test(s)) count++;
        }
        
        String[] result = new String[count];
        int index = 0;
        for (String s : array) {
            if (predicate.test(s)) {
                result[index++] = s;
            }
        }
        return result;
    }
    
    private static String[] mapStrings(String[] array, StringFunction function) {
        String[] result = new String[array.length];
        for (int i = 0; i < array.length; i++) {
            result[i] = function.apply(array[i]);
        }
        return result;
    }
    
    private static void demonstrateAggregation() {
        System.out.println("\\n--- Агрегатные операции ---");
        
        int[] numbers = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3};
        System.out.println("Массив: " + java.util.Arrays.toString(numbers));
        
        // Базовые статистики
        int sum = sum(numbers);
        double average = average(numbers);
        int min = min(numbers);
        int max = max(numbers);
        
        System.out.printf("Сумма: %d%n", sum);
        System.out.printf("Среднее: %.2f%n", average);
        System.out.printf("Минимум: %d%n", min);
        System.out.printf("Максимум: %d%n", max);
        
        // Подсчет элементов
        int count = count(numbers, 5);
        System.out.printf("Количество пятерок: %d%n", count);
        
        // Проверки
        boolean hasEven = any(numbers, x -> x % 2 == 0);
        boolean allPositive = all(numbers, x -> x > 0);
        
        System.out.printf("Есть четные: %b%n", hasEven);
        System.out.printf("Все положительные: %b%n", allPositive);
        
        // Уникальные элементы
        int[] unique = getUniqueElements(numbers);
        System.out.println("Уникальные элементы: " + java.util.Arrays.toString(unique));
        
        // Частотная таблица
        java.util.Map<Integer, Integer> frequency = getFrequency(numbers);
        System.out.println("Частотная таблица: " + frequency);
    }
    
    private static int sum(int[] array) {
        int sum = 0;
        for (int num : array) {
            sum += num;
        }
        return sum;
    }
    
    private static double average(int[] array) {
        if (array.length == 0) return 0.0;
        return (double) sum(array) / array.length;
    }
    
    private static int min(int[] array) {
        if (array.length == 0) throw new IllegalArgumentException("Empty array");
        
        int min = array[0];
        for (int i = 1; i < array.length; i++) {
            if (array[i] < min) {
                min = array[i];
            }
        }
        return min;
    }
    
    private static int max(int[] array) {
        if (array.length == 0) throw new IllegalArgumentException("Empty array");
        
        int max = array[0];
        for (int i = 1; i < array.length; i++) {
            if (array[i] > max) {
                max = array[i];
            }
        }
        return max;
    }
    
    private static int count(int[] array, int value) {
        int count = 0;
        for (int num : array) {
            if (num == value) count++;
        }
        return count;
    }
    
    private static boolean any(int[] array, IntPredicate predicate) {
        for (int num : array) {
            if (predicate.test(num)) {
                return true;
            }
        }
        return false;
    }
    
    private static boolean all(int[] array, IntPredicate predicate) {
        for (int num : array) {
            if (!predicate.test(num)) {
                return false;
            }
        }
        return true;
    }
    
    private static int[] getUniqueElements(int[] array) {
        java.util.Set<Integer> uniqueSet = new java.util.LinkedHashSet<>();
        for (int num : array) {
            uniqueSet.add(num);
        }
        
        return uniqueSet.stream().mapToInt(Integer::intValue).toArray();
    }
    
    private static java.util.Map<Integer, Integer> getFrequency(int[] array) {
        java.util.Map<Integer, Integer> frequency = new java.util.LinkedHashMap<>();
        for (int num : array) {
            frequency.put(num, frequency.getOrDefault(num, 0) + 1);
        }
        return frequency;
    }
    
    public static void main(String[] args) {
        demonstrateArrayOperations();
    }
}
```

---

## 🛠️ Класс Arrays

### Утилиты для работы с массивами
```java
public class ArraysClassDemo {
    
    public static void demonstrateArraysClass() {
        System.out.println("=== Класс Arrays ===");
        
        // Сортировка
        demonstrateSorting();
        
        // Поиск
        demonstrateSearching();
        
        // Сравнение
        demonstrateComparison();
        
        // Заполнение
        demonstrateFilling();
        
        // Преобразование
        demonstrateConversion();
        
        // Параллельные операции
        demonstrateParallelOperations();
    }
    
    private static void demonstrateSorting() {
        System.out.println("\\n--- Сортировка ---");
        
        // Сортировка примитивных типов
        int[] numbers = {64, 34, 25, 12, 22, 11, 90};
        System.out.println("До сортировки: " + java.util.Arrays.toString(numbers));
        
        java.util.Arrays.sort(numbers);
        System.out.println("После сортировки: " + java.util.Arrays.toString(numbers));
        
        // Частичная сортировка
        int[] partialSort = {64, 34, 25, 12, 22, 11, 90};
        java.util.Arrays.sort(partialSort, 1, 5); // Сортируем элементы с индекса 1 по 4
        System.out.println("Частичная сортировка [1-5): " + java.util.Arrays.toString(partialSort));
        
        // Сортировка строк
        String[] words = {"banana", "apple", "cherry", "date"};
        System.out.println("\\nДо сортировки строк: " + java.util.Arrays.toString(words));
        java.util.Arrays.sort(words);
        System.out.println("После сортировки: " + java.util.Arrays.toString(words));
        
        // Сортировка с компаратором
        String[] caseInsensitive = {"Banana", "apple", "Cherry", "date"};
        java.util.Arrays.sort(caseInsensitive, String.CASE_INSENSITIVE_ORDER);
        System.out.println("Нечувствительная к регистру: " + java.util.Arrays.toString(caseInsensitive));
        
        // Обратная сортировка
        Integer[] boxedNumbers = {64, 34, 25, 12, 22, 11, 90};
        java.util.Arrays.sort(boxedNumbers, java.util.Collections.reverseOrder());
        System.out.println("Обратная сортировка: " + java.util.Arrays.toString(boxedNumbers));
        
        // Сортировка пользовательских объектов
        Person[] people = {
            new Person("Alice", 30),
            new Person("Bob", 25),
            new Person("Charlie", 35),
            new Person("Alice", 28)
        };
        
        System.out.println("\\nДо сортировки людей:");
        printPeople(people);
        
        // Сортировка по возрасту
        java.util.Arrays.sort(people, java.util.Comparator.comparingInt(Person::getAge));
        System.out.println("\\nПо возрасту:");
        printPeople(people);
        
        // Сортировка по имени, затем по возрасту
        java.util.Arrays.sort(people, 
            java.util.Comparator.comparing(Person::getName)
                                .thenComparingInt(Person::getAge));
        System.out.println("\\nПо имени, затем по возрасту:");
        printPeople(people);
    }
    
    private static void demonstrateSearching() {
        System.out.println("\\n--- Поиск ---");
        
        int[] sortedNumbers = {1, 3, 5, 7, 9, 11, 13, 15, 17, 19};
        System.out.println("Отсортированный массив: " + java.util.Arrays.toString(sortedNumbers));
        
        // Бинарный поиск существующих элементов
        int[] searchValues = {1, 7, 19, 6, 20};
        
        for (int value : searchValues) {
            int index = java.util.Arrays.binarySearch(sortedNumbers, value);
            if (index >= 0) {
                System.out.printf("Найден %d на позиции %d%n", value, index);
            } else {
                int insertionPoint = -(index + 1);
                System.out.printf("Не найден %d, позиция для вставки: %d%n", value, insertionPoint);
            }
        }
        
        // Поиск в диапазоне
        int rangeIndex = java.util.Arrays.binarySearch(sortedNumbers, 2, 8, 9);
        System.out.printf("Поиск 9 в диапазоне [2, 8): %d%n", rangeIndex);
        
        // Поиск строк
        String[] sortedWords = {"apple", "banana", "cherry", "date", "elderberry"};
        int wordIndex = java.util.Arrays.binarySearch(sortedWords, "cherry");
        System.out.printf("Индекс 'cherry': %d%n", wordIndex);
        
        // Поиск с компаратором
        String[] mixedCase = {"Apple", "banana", "Cherry", "date", "Elderberry"};
        java.util.Arrays.sort(mixedCase, String.CASE_INSENSITIVE_ORDER);
        int mixedIndex = java.util.Arrays.binarySearch(mixedCase, "BANANA", String.CASE_INSENSITIVE_ORDER);
        System.out.printf("Поиск 'BANANA' без учета регистра: %d%n", mixedIndex);
    }
    
    private static void demonstrateComparison() {
        System.out.println("\\n--- Сравнение массивов ---");
        
        int[] array1 = {1, 2, 3, 4, 5};
        int[] array2 = {1, 2, 3, 4, 5};
        int[] array3 = {1, 2, 3, 4, 6};
        int[] array4 = {1, 2, 3};
        
        // Проверка на равенство
        System.out.println("array1.equals(array2): " + array1.equals(array2)); // false (ссылки)
        System.out.println("Arrays.equals(array1, array2): " + java.util.Arrays.equals(array1, array2)); // true
        System.out.println("Arrays.equals(array1, array3): " + java.util.Arrays.equals(array1, array3)); // false
        System.out.println("Arrays.equals(array1, array4): " + java.util.Arrays.equals(array1, array4)); // false
        
        // Лексикографическое сравнение
        int comparison1 = java.util.Arrays.compare(array1, array2);
        int comparison2 = java.util.Arrays.compare(array1, array3);
        int comparison3 = java.util.Arrays.compare(array1, array4);
        
        System.out.printf("compare(array1, array2): %d%n", comparison1); // 0
        System.out.printf("compare(array1, array3): %d%n", comparison2); // < 0
        System.out.printf("compare(array1, array4): %d%n", comparison3); // > 0
        
        // Сравнение строковых массивов
        String[] words1 = {"apple", "banana"};
        String[] words2 = {"apple", "cherry"};
        
        int wordComparison = java.util.Arrays.compare(words1, words2);
        System.out.printf("Сравнение строковых массивов: %d%n", wordComparison);
        
        // Hash код массива
        System.out.println("\\nHash коды:");
        System.out.println("array1.hashCode(): " + array1.hashCode()); // Неинформативно
        System.out.println("Arrays.hashCode(array1): " + java.util.Arrays.hashCode(array1)); // На основе содержимого
        System.out.println("Arrays.hashCode(array2): " + java.util.Arrays.hashCode(array2)); // Должен быть равен предыдущему
        
        // Многомерные массивы
        int[][] matrix1 = {{1, 2}, {3, 4}};
        int[][] matrix2 = {{1, 2}, {3, 4}};
        
        System.out.println("\\nМногомерные массивы:");
        System.out.println("Arrays.equals(matrix1, matrix2): " + java.util.Arrays.equals(matrix1, matrix2)); // false!
        System.out.println("Arrays.deepEquals(matrix1, matrix2): " + java.util.Arrays.deepEquals(matrix1, matrix2)); // true
        
        System.out.println("Arrays.hashCode(matrix1): " + java.util.Arrays.hashCode(matrix1));
        System.out.println("Arrays.deepHashCode(matrix1): " + java.util.Arrays.deepHashCode(matrix1));
    }
    
    private static void demonstrateFilling() {
        System.out.println("\\n--- Заполнение массивов ---");
        
        // Полное заполнение
        int[] filled = new int[10];
        java.util.Arrays.fill(filled, 42);
        System.out.println("Заполнен 42: " + java.util.Arrays.toString(filled));
        
        // Частичное заполнение
        java.util.Arrays.fill(filled, 2, 7, 99);
        System.out.println("Частично заполнен 99 [2, 7): " + java.util.Arrays.toString(filled));
        
        // Заполнение объектных массивов
        String[] strings = new String[5];
        java.util.Arrays.fill(strings, "Hello");
        System.out.println("Строки: " + java.util.Arrays.toString(strings));
        
        // Заполнение null
        java.util.Arrays.fill(strings, 1, 3, null);
        System.out.println("С null [1, 3): " + java.util.Arrays.toString(strings));
        
        // Заполнение булевых значений
        boolean[] booleans = new boolean[8];
        java.util.Arrays.fill(booleans, 0, 4, true);
        System.out.println("Булевы [0, 4): " + java.util.Arrays.toString(booleans));
    }
    
    private static void demonstrateConversion() {
        System.out.println("\\n--- Преобразование ---");
        
        // toString для различных типов
        int[] ints = {1, 2, 3, 4, 5};
        double[] doubles = {1.1, 2.2, 3.3};
        boolean[] booleans = {true, false, true};
        String[] strings = {"a", "b", "c", null, "e"};
        
        System.out.println("int[]: " + java.util.Arrays.toString(ints));
        System.out.println("double[]: " + java.util.Arrays.toString(doubles));
        System.out.println("boolean[]: " + java.util.Arrays.toString(booleans));
        System.out.println("String[] (с null): " + java.util.Arrays.toString(strings));
        
        // Многомерные массивы
        int[][] matrix = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
        System.out.println("\\nОбычный toString многомерного:");
        System.out.println(java.util.Arrays.toString(matrix)); // Неинформативно
        
        System.out.println("deepToString многомерного:");
        System.out.println(java.util.Arrays.deepToString(matrix)); // Читаемо
        
        // Трехмерный массив
        int[][][] cube = {{{1, 2}, {3, 4}}, {{5, 6}, {7, 8}}};
        System.out.println("\\nТрехмерный массив:");
        System.out.println(java.util.Arrays.deepToString(cube));
        
        // Массив объектов
        Object[] mixed = {42, "Hello", 3.14, new int[]{1, 2, 3}, null};
        System.out.println("\\nСмешанный массив:");
        System.out.println(java.util.Arrays.toString(mixed));
    }
    
    private static void demonstrateParallelOperations() {
        System.out.println("\\n--- Параллельные операции (Java 8+) ---");
        
        // Параллельная сортировка
        int[] largeArray = generateRandomArray(100000);
        int[] copy1 = largeArray.clone();
        int[] copy2 = largeArray.clone();
        
        // Обычная сортировка
        long startTime = System.currentTimeMillis();
        java.util.Arrays.sort(copy1);
        long sequentialTime = System.currentTimeMillis() - startTime;
        
        // Параллельная сортировка
        startTime = System.currentTimeMillis();
        java.util.Arrays.parallelSort(copy2);
        long parallelTime = System.currentTimeMillis() - startTime;
        
        System.out.printf("Сортировка %d элементов:%n", largeArray.length);
        System.out.printf("Последовательная: %d мс%n", sequentialTime);
        System.out.printf("Параллельная: %d мс%n", parallelTime);
        
        if (parallelTime > 0) {
            System.out.printf("Ускорение: %.2fx%n", (double)sequentialTime / parallelTime);
        }
        
        // Проверяем, что результаты одинаковы
        boolean identical = java.util.Arrays.equals(copy1, copy2);
        System.out.println("Результаты идентичны: " + identical);
        
        // Параллельное заполнение
        int[] parallelFilled = new int[10];
        java.util.Arrays.parallelSetAll(parallelFilled, i -> i * i); // Квадраты индексов
        System.out.println("\\nПараллельное заполнение (квадраты): " + 
                          java.util.Arrays.toString(parallelFilled));
        
        // Параллельная операция с генератором
        double[] randomArray = new double[10];
        java.util.Arrays.parallelSetAll(randomArray, i -> Math.random());
        System.out.println("Случайные числа: " + java.util.Arrays.toString(randomArray));
        
        // Параллельный префикс (накопленная сумма)
        int[] numbers = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
        System.out.println("\\nИсходный массив: " + java.util.Arrays.toString(numbers));
        
        int[] prefixSum = numbers.clone();
        java.util.Arrays.parallelPrefix(prefixSum, Integer::sum);
        System.out.println("Накопленная сумма: " + java.util.Arrays.toString(prefixSum));
        
        int[] prefixProduct = numbers.clone();
        java.util.Arrays.parallelPrefix(prefixProduct, (a, b) -> a * b);
        System.out.println("Накопленное произведение: " + java.util.Arrays.toString(prefixProduct));
    }
    
    private static int[] generateRandomArray(int size) {
        java.util.Random random = new java.util.Random();
        int[] array = new int[size];
        for (int i = 0; i < size; i++) {
            array[i] = random.nextInt(10000);
        }
        return array;
    }
    
    // Вспомогательный класс
    static class Person {
        private final String name;
        private final int age;
        
        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        public String getName() { return name; }
        public int getAge() { return age; }
        
        @Override
        public String toString() {
            return String.format("Person{name='%s', age=%d}", name, age);
        }
    }
    
    private static void printPeople(Person[] people) {
        for (Person person : people) {
            System.out.println("  " + person);
        }
    }
    
    public static void main(String[] args) {
        demonstrateArraysClass();
    }
}
```

---

## 🧮 Многомерные массивы

### Двумерные и многомерные массивы
```java
public class MultidimensionalArraysDemo {
    
    public static void demonstrateMultidimensionalArrays() {
        System.out.println("=== Многомерные массивы ===");
        
        // Двумерные массивы
        demonstrateTwoDimensionalArrays();
        
        // Неровные массивы (Jagged Arrays)
        demonstrateJaggedArrays();
        
        // Трехмерные массивы
        demonstrateThreeDimensionalArrays();
        
        // Операции с матрицами
        demonstrateMatrixOperations();
    }
    
    private static void demonstrateTwoDimensionalArrays() {
        System.out.println("\\n--- Двумерные массивы ---");
        
        // Создание и инициализация
        int[][] matrix1 = {
            {1, 2, 3},
            {4, 5, 6}, 
            {7, 8, 9}
        };
        
        System.out.println("Матрица 1 (инициализация литералом):");
        printMatrix(matrix1);
        
        // Создание с указанием размеров
        int[][] matrix2 = new int[3][4]; // 3 строки, 4 столбца
        
        // Заполнение значениями
        int value = 1;
        for (int i = 0; i < matrix2.length; i++) {
            for (int j = 0; j < matrix2[i].length; j++) {
                matrix2[i][j] = value++;
            }
        }
        
        System.out.println("\\nМатрица 2 (3x4):");
        printMatrix(matrix2);
        
        // Доступ к элементам
        System.out.println("\\nДоступ к элементам matrix1:");
        System.out.printf("matrix1[0][0] = %d%n", matrix1[0][0]); // Первый элемент
        System.out.printf("matrix1[1][2] = %d%n", matrix1[1][2]); // Второй ряд, третий элемент
        System.out.printf("matrix1[2][2] = %d%n", matrix1[2][2]); // Последний элемент
        
        // Размеры
        System.out.printf("\\nРазмеры matrix2: %d x %d%n", matrix2.length, matrix2[0].length);
        System.out.printf("Количество строк: %d%n", matrix2.length);
        System.out.printf("Количество столбцов в первой строке: %d%n", matrix2[0].length);
        
        // Итерация
        System.out.println("\\nИтерация по matrix1:");
        for (int i = 0; i < matrix1.length; i++) {
            for (int j = 0; j < matrix1[i].length; j++) {
                System.out.printf("%3d", matrix1[i][j]);
            }
            System.out.println();
        }
        
        // Enhanced for loop
        System.out.println("\\nEnhanced for loop:");
        for (int[] row : matrix1) {
            for (int element : row) {
                System.out.printf("%3d", element);
            }
            System.out.println();
        }
    }
    
    private static void demonstrateJaggedArrays() {
        System.out.println("\\n--- Неровные массивы (Jagged Arrays) ---");
        
        // Создание неровного массива
        int[][] jagged = new int[4][];
        jagged[0] = new int[1];      // Первая строка - 1 элемент
        jagged[1] = new int[3];      // Вторая строка - 3 элемента
        jagged[2] = new int[2];      // Третья строка - 2 элемента
        jagged[3] = new int[4];      // Четвертая строка - 4 элемента
        
        // Заполнение
        int counter = 1;
        for (int i = 0; i < jagged.length; i++) {
            for (int j = 0; j < jagged[i].length; j++) {
                jagged[i][j] = counter++;
            }
        }
        
        System.out.println("Неровный массив:");
        printJaggedArray(jagged);
        
        // Инициализация литералом
        String[][] schedule = {
            {"Понедельник", "Математика", "Физика"},
            {"Вторник", "История", "Литература", "Химия", "Биология"},
            {"Среда", "Английский"},
            {"Четверг", "Программирование", "Базы данных", "Сети"}
        };
        
        System.out.println("\\nРасписание (неровный массив строк):");
        for (int i = 0; i < schedule.length; i++) {
            System.out.printf("Строка %d (%d элементов): %s%n", 
                            i, schedule[i].length, 
                            java.util.Arrays.toString(schedule[i]));
        }
        
        // Треугольный массив (числа Паскаля)
        System.out.println("\\nТреугольник Паскаля:");
        int[][] pascal = createPascalTriangle(6);
        printPascalTriangle(pascal);
    }
    
    private static int[][] createPascalTriangle(int rows) {
        int[][] triangle = new int[rows][];
        
        for (int i = 0; i < rows; i++) {
            triangle[i] = new int[i + 1];
            triangle[i][0] = 1; // Первый элемент всегда 1
            triangle[i][i] = 1; // Последний элемент всегда 1
            
            // Заполняем средние элементы
            for (int j = 1; j < i; j++) {
                triangle[i][j] = triangle[i-1][j-1] + triangle[i-1][j];
            }
        }
        
        return triangle;
    }
    
    private static void printPascalTriangle(int[][] triangle) {
        int maxWidth = 4;
        int totalWidth = triangle[triangle.length - 1].length * maxWidth;
        
        for (int i = 0; i < triangle.length; i++) {
            // Центрирование строки
            int padding = (totalWidth - triangle[i].length * maxWidth) / 2;
            System.out.print(" ".repeat(Math.max(0, padding)));
            
            for (int j = 0; j < triangle[i].length; j++) {
                System.out.printf("%-" + maxWidth + "d", triangle[i][j]);
            }
            System.out.println();
        }
    }
    
    private static void demonstrateThreeDimensionalArrays() {
        System.out.println("\\n--- Трехмерные массивы ---");
        
        // 3D массив: блоки x строки x столбцы
        int[][][] cube = new int[2][3][3]; // 2 блока по 3x3
        
        // Заполнение
        int value = 1;
        for (int block = 0; block < cube.length; block++) {
            for (int row = 0; row < cube[block].length; row++) {
                for (int col = 0; col < cube[block][row].length; col++) {
                    cube[block][row][col] = value++;
                }
            }
        }
        
        System.out.println("Трехмерный массив (2x3x3):");
        for (int block = 0; block < cube.length; block++) {
            System.out.printf("Блок %d:%n", block);
            printMatrix(cube[block]);
            System.out.println();
        }
        
        // Размеры 3D массива
        System.out.printf("Размеры: %d блоков, %d строк, %d столбцов%n",
                         cube.length, 
                         cube[0].length, 
                         cube[0][0].length);
        
        // Доступ к элементу
        System.out.printf("Элемент [1][2][1]: %d%n", cube[1][2][1]);
        
        // RGB изображение (пример использования 3D массива)
        System.out.println("\\nПример: RGB изображение 2x2 пикселя");
        int[][][] image = new int[2][2][3]; // Высота x Ширина x RGB
        
        // Заполнение цветами
        image[0][0] = new int[]{255, 0, 0};    // Красный
        image[0][1] = new int[]{0, 255, 0};    // Зеленый
        image[1][0] = new int[]{0, 0, 255};    // Синий
        image[1][1] = new int[]{255, 255, 0};  // Желтый
        
        for (int y = 0; y < image.length; y++) {
            for (int x = 0; x < image[y].length; x++) {
                System.out.printf("Пиксель [%d][%d]: RGB(%d, %d, %d)%n",
                                y, x, 
                                image[y][x][0], 
                                image[y][x][1], 
                                image[y][x][2]);
            }
        }
    }
    
    private static void demonstrateMatrixOperations() {
        System.out.println("\\n--- Операции с матрицами ---");
        
        int[][] matrix1 = {
            {1, 2, 3},
            {4, 5, 6}
        }; // 2x3
        
        int[][] matrix2 = {
            {7, 8},
            {9, 10},
            {11, 12}
        }; // 3x2
        
        System.out.println("Матрица 1 (2x3):");
        printMatrix(matrix1);
        
        System.out.println("\\nМатрица 2 (3x2):");
        printMatrix(matrix2);
        
        // Транспонирование
        int[][] transposed = transpose(matrix1);
        System.out.println("\\nТранспонированная матрица 1:");
        printMatrix(transposed);
        
        // Умножение матриц
        int[][] product = multiplyMatrices(matrix1, matrix2);
        System.out.println("\\nПроизведение матриц (2x3 * 3x2 = 2x2):");
        if (product != null) {
            printMatrix(product);
        }
        
        // Сложение матриц одинакового размера
        int[][] a = {{1, 2}, {3, 4}};
        int[][] b = {{5, 6}, {7, 8}};
        
        System.out.println("\\nМатрица A:");
        printMatrix(a);
        System.out.println("\\nМатрица B:");
        printMatrix(b);
        
        int[][] sum = addMatrices(a, b);
        System.out.println("\\nСумма A + B:");
        if (sum != null) {
            printMatrix(sum);
        }
        
        // Поиск максимального элемента
        int max = findMaxElement(matrix1);
        System.out.printf("\\nМаксимальный элемент в матрице 1: %d%n", max);
        
        // Сумма всех элементов
        int totalSum = sumAllElements(matrix1);
        System.out.printf("Сумма всех элементов матрицы 1: %d%n", totalSum);
        
        // Поиск элемента
        int[] position = findElement(matrix1, 5);
        if (position != null) {
            System.out.printf("Элемент 5 найден в позиции [%d][%d]%n", position[0], position[1]);
        } else {
            System.out.println("Элемент 5 не найден");
        }
    }
    
    // Вспомогательные методы для работы с матрицами
    private static void printMatrix(int[][] matrix) {
        for (int[] row : matrix) {
            for (int element : row) {
                System.out.printf("%4d", element);
            }
            System.out.println();
        }
    }
    
    private static void printJaggedArray(int[][] array) {
        for (int i = 0; i < array.length; i++) {
            System.out.printf("Строка %d: %s%n", i, java.util.Arrays.toString(array[i]));
        }
    }
    
    private static int[][] transpose(int[][] matrix) {
        int rows = matrix.length;
        int cols = matrix[0].length;
        
        int[][] result = new int[cols][rows];
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                result[j][i] = matrix[i][j];
            }
        }
        
        return result;
    }
    
    private static int[][] multiplyMatrices(int[][] a, int[][] b) {
        int aRows = a.length;
        int aCols = a[0].length;
        int bRows = b.length;
        int bCols = b[0].length;
        
        // Проверяем возможность умножения
        if (aCols != bRows) {
            System.err.println("Невозможно умножить матрицы: несовместимые размеры");
            return null;
        }
        
        int[][] result = new int[aRows][bCols];
        
        for (int i = 0; i < aRows; i++) {
            for (int j = 0; j < bCols; j++) {
                for (int k = 0; k < aCols; k++) {
                    result[i][j] += a[i][k] * b[k][j];
                }
            }
        }
        
        return result;
    }
    
    private static int[][] addMatrices(int[][] a, int[][] b) {
        if (a.length != b.length || a[0].length != b[0].length) {
            System.err.println("Невозможно сложить матрицы: разные размеры");
            return null;
        }
        
        int[][] result = new int[a.length][a[0].length];
        
        for (int i = 0; i < a.length; i++) {
            for (int j = 0; j < a[i].length; j++) {
                result[i][j] = a[i][j] + b[i][j];
            }
        }
        
        return result;
    }
    
    private static int findMaxElement(int[][] matrix) {
        int max = matrix[0][0];
        
        for (int[] row : matrix) {
            for (int element : row) {
                if (element > max) {
                    max = element;
                }
            }
        }
        
        return max;
    }
    
    private static int sumAllElements(int[][] matrix) {
        int sum = 0;
        
        for (int[] row : matrix) {
            for (int element : row) {
                sum += element;
            }
        }
        
        return sum;
    }
    
    private static int[] findElement(int[][] matrix, int target) {
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[i].length; j++) {
                if (matrix[i][j] == target) {
                    return new int[]{i, j};
                }
            }
        }
        return null;
    }
    
    public static void main(String[] args) {
        demonstrateMultidimensionalArrays();
    }
}
```

---

## ⚡ Производительность

### Оптимизация работы с массивами
```java
public class ArrayPerformanceDemo {
    
    public static void demonstrateArrayPerformance() {
        System.out.println("=== Производительность массивов ===");
        
        // Сравнение доступа к элементам
        demonstrateAccessPerformance();
        
        // Сравнение копирования
        demonstrateCopyPerformance();
        
        // Сравнение поиска
        demonstrateSearchPerformance();
        
        // Сравнение сортировки
        demonstrateSortPerformance();
        
        // Влияние размера на производительность
        demonstrateSizeImpact();
    }
    
    private static void demonstrateAccessPerformance() {
        System.out.println("\\n--- Производительность доступа ---");
        
        int size = 10_000_000;
        int[] array = new int[size];
        java.util.ArrayList<Integer> list = new java.util.ArrayList<>(size);
        
        // Заполнение
        for (int i = 0; i < size; i++) {
            array[i] = i;
            list.add(i);
        }
        
        int iterations = 100_000_000;
        java.util.Random random = new java.util.Random();
        
        // Тест доступа к массиву
        long startTime = System.nanoTime();
        long sum = 0;
        for (int i = 0; i < iterations; i++) {
            int index = random.nextInt(size);
            sum += array[index];
        }
        long arrayTime = System.nanoTime() - startTime;
        
        // Тест доступа к ArrayList
        startTime = System.nanoTime();
        sum = 0;
        for (int i = 0; i < iterations; i++) {
            int index = random.nextInt(size);
            sum += list.get(index);
        }
        long listTime = System.nanoTime() - startTime;
        
        System.out.printf("Доступ к %d элементам (%d раз):%n", size, iterations);
        System.out.printf("Массив: %.2f мс%n", arrayTime / 1_000_000.0);
        System.out.printf("ArrayList: %.2f мс%n", listTime / 1_000_000.0);
        System.out.printf("Массив быстрее в %.2f раз%n", (double) listTime / arrayTime);
    }
    
    private static void demonstrateCopyPerformance() {
        System.out.println("\\n--- Производительность копирования ---");
        
        int size = 1_000_000;
        int[] source = new int[size];
        
        // Заполнение исходного массива
        for (int i = 0; i < size; i++) {
            source[i] = i;
        }
        
        // 1. Ручное копирование
        long startTime = System.nanoTime();
        int[] manual = new int[size];
        for (int i = 0; i < size; i++) {
            manual[i] = source[i];
        }
        long manualTime = System.nanoTime() - startTime;
        
        // 2. System.arraycopy
        startTime = System.nanoTime();
        int[] systemCopy = new int[size];
        System.arraycopy(source, 0, systemCopy, 0, size);
        long systemTime = System.nanoTime() - startTime;
        
        // 3. Arrays.copyOf
        startTime = System.nanoTime();
        int[] arraysCopy = java.util.Arrays.copyOf(source, size);
        long arraysTime = System.nanoTime() - startTime;
        
        // 4. clone()
        startTime = System.nanoTime();
        int[] cloned = source.clone();
        long cloneTime = System.nanoTime() - startTime;
        
        System.out.printf("Копирование %d элементов:%n", size);
        System.out.printf("Ручное копирование: %.2f мс%n", manualTime / 1_000_000.0);
        System.out.printf("System.arraycopy: %.2f мс (быстрее в %.2fx)%n", 
                         systemTime / 1_000_000.0, (double) manualTime / systemTime);
        System.out.printf("Arrays.copyOf: %.2f мс (быстрее в %.2fx)%n", 
                         arraysTime / 1_000_000.0, (double) manualTime / arraysTime);
        System.out.printf("clone(): %.2f мс (быстрее в %.2fx)%n", 
                         cloneTime / 1_000_000.0, (double) manualTime / cloneTime);
        
        // Проверяем корректность
        boolean allEqual = java.util.Arrays.equals(manual, systemCopy) &&
                          java.util.Arrays.equals(manual, arraysCopy) &&
                          java.util.Arrays.equals(manual, cloned);
        System.out.println("Все копии идентичны: " + allEqual);
    }
    
    private static void demonstrateSearchPerformance() {
        System.out.println("\\n--- Производительность поиска ---");
        
        int size = 1_000_000;
        int[] unsorted = new int[size];
        
        // Заполнение случайными числами
        java.util.Random random = new java.util.Random();
        for (int i = 0; i < size; i++) {
            unsorted[i] = random.nextInt(size);
        }
        
        int[] sorted = unsorted.clone();
        java.util.Arrays.sort(sorted);
        
        int searchValue = sorted[size / 2]; // Элемент в середине
        int iterations = 10000;
        
        // Линейный поиск в несортированном массиве
        long startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            linearSearch(unsorted, searchValue);
        }
        long linearTime = System.nanoTime() - startTime;
        
        // Бинарный поиск в отсортированном массиве
        startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            java.util.Arrays.binarySearch(sorted, searchValue);
        }
        long binaryTime = System.nanoTime() - startTime;
        
        System.out.printf("Поиск в массиве из %d элементов (%d раз):%n", size, iterations);
        System.out.printf("Линейный поиск: %.2f мс%n", linearTime / 1_000_000.0);
        System.out.printf("Бинарный поиск: %.2f мс (быстрее в %.2fx)%n", 
                         binaryTime / 1_000_000.0, (double) linearTime / binaryTime);
    }
    
    private static int linearSearch(int[] array, int target) {
        for (int i = 0; i < array.length; i++) {
            if (array[i] == target) {
                return i;
            }
        }
        return -1;
    }
    
    private static void demonstrateSortPerformance() {
        System.out.println("\\n--- Производительность сортировки ---");
        
        int[] sizes = {10_000, 100_000, 1_000_000};
        
        for (int size : sizes) {
            System.out.printf("\\nСортировка %d элементов:%n", size);
            
            // Генерация тестовых данных
            int[] randomData = generateRandomArray(size);
            int[] sortedData = randomData.clone();
            java.util.Arrays.sort(sortedData);
            int[] reversedData = sortedData.clone();
            reverseArray(reversedData);
            
            // Тест на случайных данных
            testSortingPerformance("Случайные данные", randomData.clone());
            
            // Тест на уже отсортированных данных
            testSortingPerformance("Отсортированные данные", sortedData.clone());
            
            // Тест на обратно отсортированных данных
            testSortingPerformance("Обратно отсортированные", reversedData.clone());
        }
    }
    
    private static void testSortingPerformance(String description, int[] data) {
        System.out.printf("  %s:%n", description);
        
        // Arrays.sort (Timsort)
        int[] copy1 = data.clone();
        long startTime = System.nanoTime();
        java.util.Arrays.sort(copy1);
        long sortTime = System.nanoTime() - startTime;
        
        // Параллельная сортировка
        int[] copy2 = data.clone();
        startTime = System.nanoTime();
        java.util.Arrays.parallelSort(copy2);
        long parallelTime = System.nanoTime() - startTime;
        
        System.out.printf("    Arrays.sort: %.2f мс%n", sortTime / 1_000_000.0);
        System.out.printf("    parallelSort: %.2f мс", parallelTime / 1_000_000.0);
        
        if (parallelTime > 0 && sortTime > parallelTime) {
            System.out.printf(" (быстрее в %.2fx)", (double) sortTime / parallelTime);
        }
        System.out.println();
    }
    
    private static int[] generateRandomArray(int size) {
        java.util.Random random = new java.util.Random();
        int[] array = new int[size];
        for (int i = 0; i < size; i++) {
            array[i] = random.nextInt(size);
        }
        return array;
    }
    
    private static void reverseArray(int[] array) {
        for (int i = 0; i < array.length / 2; i++) {
            int temp = array[i];
            array[i] = array[array.length - 1 - i];
            array[array.length - 1 - i] = temp;
        }
    }
    
    private static void demonstrateSizeImpact() {
        System.out.println("\\n--- Влияние размера на производительность ---");
        
        int[] sizes = {1000, 10_000, 100_000, 1_000_000};
        System.out.printf("%-12s | %-12s | %-12s | %-12s%n", 
                         "Размер", "Создание", "Заполнение", "Сортировка");
        System.out.println("-".repeat(60));
        
        for (int size : sizes) {
            // Создание
            long startTime = System.nanoTime();
            int[] array = new int[size];
            long createTime = System.nanoTime() - startTime;
            
            // Заполнение
            startTime = System.nanoTime();
            java.util.Random random = new java.util.Random();
            for (int i = 0; i < size; i++) {
                array[i] = random.nextInt(size);
            }
            long fillTime = System.nanoTime() - startTime;
            
            // Сортировка
            startTime = System.nanoTime();
            java.util.Arrays.sort(array);
            long sortTime = System.nanoTime() - startTime;
            
            System.out.printf("%-12s | %8.2f мс | %8.2f мс | %8.2f мс%n",
                             formatNumber(size),
                             createTime / 1_000_000.0,
                             fillTime / 1_000_000.0,
                             sortTime / 1_000_000.0);
        }
        
        // Тест памяти
        demonstrateMemoryUsage();
    }
    
    private static String formatNumber(int number) {
        if (number >= 1_000_000) {
            return (number / 1_000_000) + "M";
        } else if (number >= 1_000) {
            return (number / 1_000) + "K";
        } else {
            return String.valueOf(number);
        }
    }
    
    private static void demonstrateMemoryUsage() {
        System.out.println("\\n--- Использование памяти ---");
        
        Runtime runtime = Runtime.getRuntime();
        
        // Принудительная очистка памяти
        System.gc();
        long memoryBefore = runtime.totalMemory() - runtime.freeMemory();
        
        // Создание больших массивов
        int size = 10_000_000;
        int[] intArray = new int[size];           // 4 байта на элемент
        long[] longArray = new long[size];        // 8 байт на элемент
        double[] doubleArray = new double[size];  // 8 байт на элемент
        
        long memoryAfter = runtime.totalMemory() - runtime.freeMemory();
        long memoryUsed = memoryAfter - memoryBefore;
        
        System.out.printf("Создано 3 массива по %d элементов:%n", size);
        System.out.printf("int[]: ~%d MB%n", (size * 4) / (1024 * 1024));
        System.out.printf("long[]: ~%d MB%n", (size * 8) / (1024 * 1024));
        System.out.printf("double[]: ~%d MB%n", (size * 8) / (1024 * 1024));
        System.out.printf("Теоретически: ~%d MB%n", (size * (4 + 8 + 8)) / (1024 * 1024));
        System.out.printf("Фактически использовано: %d MB%n", memoryUsed / (1024 * 1024));
        
        // Освобождение памяти
        intArray = null;
        longArray = null;
        doubleArray = null;
        System.gc();
        
        long memoryAfterGC = runtime.totalMemory() - runtime.freeMemory();
        long freed = memoryAfter - memoryAfterGC;
        System.out.printf("Освобождено после GC: %d MB%n", freed / (1024 * 1024));
    }
    
    public static void main(String[] args) {
        demonstrateArrayPerformance();
    }
}
```

---

## 🏆 Лучшие практики

### Рекомендации по использованию
```java
public class ArrayBestPractices {
    
    public static void demonstrateBestPractices() {
        System.out.println("=== Лучшие практики работы с массивами ===");
        
        goodPractices();
        badPractices();
        performanceTips();
        memoryTips();
        securityConsiderations();
    }
    
    private static void goodPractices() {
        System.out.println("\\n✅ Хорошие практики:");
        
        // 1. Проверка границ
        demonstrateBoundsChecking();
        
        // 2. Правильное копирование
        demonstrateProperCopying();
        
        // 3. Использование enhanced for loop где возможно
        demonstrateEnhancedForLoop();
        
        // 4. Правильная работа с null
        demonstrateNullHandling();
        
        // 5. Использование утилит Arrays
        demonstrateArraysUtilities();
    }
    
    private static void demonstrateBoundsChecking() {
        System.out.println("\\n--- 1. Проверка границ ---");
        
        int[] array = {1, 2, 3, 4, 5};
        
        // ✅ Правильная проверка границ
        int index = getUserInput(); // Эмуляция пользовательского ввода
        
        if (isValidIndex(array, index)) {
            System.out.println("Элемент: " + array[index]);
        } else {
            System.out.println("Некорректный индекс: " + index);
        }
        
        // ✅ Безопасная итерация
        for (int i = 0; i < array.length; i++) { // Используем array.length
            System.out.printf("array[%d] = %d%n", i, array[i]);
        }
        
        // ✅ Безопасное получение элемента с значением по умолчанию
        int value = getElementSafely(array, index, -1);
        System.out.println("Безопасное получение: " + value);
        
        // ✅ Безопасная установка элемента
        boolean success = setElementSafely(array, index, 99);
        System.out.println("Элемент установлен: " + success);
    }
    
    private static boolean isValidIndex(int[] array, int index) {
        return array != null && index >= 0 && index < array.length;
    }
    
    private static int getElementSafely(int[] array, int index, int defaultValue) {
        return isValidIndex(array, index) ? array[index] : defaultValue;
    }
    
    private static boolean setElementSafely(int[] array, int index, int value) {
        if (isValidIndex(array, index)) {
            array[index] = value;
            return true;
        }
        return false;
    }
    
    private static int getUserInput() {
        return 10; // Эмуляция некорректного индекса
    }
    
    private static void demonstrateProperCopying() {
        System.out.println("\\n--- 2. Правильное копирование ---");
        
        int[] original = {1, 2, 3, 4, 5};
        
        // ✅ Глубокое копирование примитивов
        int[] deepCopy = original.clone(); // или Arrays.copyOf
        deepCopy[0] = 999;
        System.out.println("Оригинал: " + java.util.Arrays.toString(original));
        System.out.println("Копия: " + java.util.Arrays.toString(deepCopy));
        
        // ✅ Правильное копирование объектных массивов
        Person[] people = {
            new Person("Alice", 30),
            new Person("Bob", 25)
        };
        
        // Поверхностная копия массива (но объекты остаются те же)
        Person[] shallowCopy = people.clone();
        
        // Глубокая копия массива и объектов
        Person[] deepObjectCopy = deepCopyPersonArray(people);
        
        // Изменяем возраст в оригинале
        people[0].setAge(35);
        
        System.out.println("\\nПосле изменения оригинала:");
        System.out.println("Оригинал: " + people[0]);
        System.out.println("Поверхностная копия: " + shallowCopy[0]); // Тоже изменилось!
        System.out.println("Глубокая копия: " + deepObjectCopy[0]); // Не изменилось
    }
    
    private static Person[] deepCopyPersonArray(Person[] original) {
        Person[] copy = new Person[original.length];
        for (int i = 0; i < original.length; i++) {
            copy[i] = new Person(original[i].getName(), original[i].getAge());
        }
        return copy;
    }
    
    private static void demonstrateEnhancedForLoop() {
        System.out.println("\\n--- 3. Enhanced for loop ---");
        
        int[] numbers = {1, 2, 3, 4, 5};
        
        // ✅ Когда индекс не нужен - используем enhanced for
        System.out.println("Вывод элементов (enhanced for):");
        for (int number : numbers) {
            System.out.print(number + " ");
        }
        System.out.println();
        
        // ✅ Когда нужен индекс - используем обычный for
        System.out.println("\\nВывод с индексами (обычный for):");
        for (int i = 0; i < numbers.length; i++) {
            System.out.printf("numbers[%d] = %d%n", i, numbers[i]);
        }
        
        // ✅ Для модификации элементов - только обычный for
        System.out.println("\\nУдвоение элементов:");
        for (int i = 0; i < numbers.length; i++) {
            numbers[i] *= 2; // Enhanced for не позволит модификацию
        }
        System.out.println(java.util.Arrays.toString(numbers));
        
        // ✅ Enhanced for с объектами - можно изменять свойства объектов
        Person[] people = {new Person("Alice", 30), new Person("Bob", 25)};
        
        System.out.println("\\nУвеличиваем возраст (enhanced for с объектами):");
        for (Person person : people) {
            person.setAge(person.getAge() + 1); // Это работает!
        }
        printPeople(people);
    }
    
    private static void demonstrateNullHandling() {
        System.out.println("\\n--- 4. Работа с null ---");
        
        // ✅ Проверка массива на null
        int[] nullArray = null;
        int[] emptyArray = {};
        int[] normalArray = {1, 2, 3};
        
        System.out.println("Безопасная проверка размера:");
        System.out.println("Null массив: " + safeLength(nullArray));
        System.out.println("Пустой массив: " + safeLength(emptyArray));
        System.out.println("Обычный массив: " + safeLength(normalArray));
        
        // ✅ Безопасная итерация
        System.out.println("\\nБезопасная итерация:");
        safeIterate(nullArray);
        safeIterate(emptyArray);
        safeIterate(normalArray);
        
        // ✅ Работа с массивами объектов, содержащих null
        String[] strings = {"Hello", null, "World", null, "Java"};
        System.out.println("\\nОбработка null элементов:");
        
        for (int i = 0; i < strings.length; i++) {
            String safe = strings[i] != null ? strings[i] : "[null]";
            System.out.printf("strings[%d] = %s%n", i, safe);
        }
        
        // Фильтрация null значений
        String[] filtered = filterNulls(strings);
        System.out.println("Без null: " + java.util.Arrays.toString(filtered));
    }
    
    private static int safeLength(int[] array) {
        return array != null ? array.length : 0;
    }
    
    private static void safeIterate(int[] array) {
        if (array == null) {
            System.out.println("  Массив null - итерация пропущена");
            return;
        }
        
        if (array.length == 0) {
            System.out.println("  Массив пуст");
            return;
        }
        
        System.out.print("  Элементы: ");
        for (int value : array) {
            System.out.print(value + " ");
        }
        System.out.println();
    }
    
    private static String[] filterNulls(String[] array) {
        if (array == null) return new String[0];
        
        // Подсчитываем не-null элементы
        int count = 0;
        for (String s : array) {
            if (s != null) count++;
        }
        
        // Создаем результирующий массив
        String[] result = new String[count];
        int index = 0;
        for (String s : array) {
            if (s != null) {
                result[index++] = s;
            }
        }
        
        return result;
    }
    
    private static void demonstrateArraysUtilities() {
        System.out.println("\\n--- 5. Утилиты Arrays ---");
        
        int[] array = {3, 1, 4, 1, 5, 9, 2, 6, 5};
        System.out.println("Исходный массив: " + java.util.Arrays.toString(array));
        
        // ✅ Используйте Arrays.sort() вместо самописных алгоритмов
        int[] sorted = array.clone();
        java.util.Arrays.sort(sorted);
        System.out.println("Отсортированный: " + java.util.Arrays.toString(sorted));
        
        // ✅ Используйте Arrays.binarySearch() для поиска в отсортированных массивах
        int index = java.util.Arrays.binarySearch(sorted, 5);
        System.out.println("Индекс элемента 5: " + index);
        
        // ✅ Используйте Arrays.equals() для сравнения содержимого
        int[] copy = array.clone();
        System.out.println("Массивы равны: " + java.util.Arrays.equals(array, copy));
        
        // ✅ Используйте Arrays.fill() для заполнения
        int[] filled = new int[10];
        java.util.Arrays.fill(filled, 42);
        System.out.println("Заполненный: " + java.util.Arrays.toString(filled));
        
        // ✅ Используйте Arrays.copyOf() для создания копий нужного размера
        int[] expanded = java.util.Arrays.copyOf(array, array.length * 2);
        System.out.println("Расширенная копия: " + java.util.Arrays.toString(expanded));
    }
    
    private static void badPractices() {
        System.out.println("\\n❌ Плохие практики (избегайте):");
        
        demonstrateBadBoundsHandling();
        demonstrateBadCopying();
        demonstrateBadComparison();
        demonstrateBadInitialization();
    }
    
    private static void demonstrateBadBoundsHandling() {
        System.out.println("\\n--- ❌ Плохая проверка границ ---");
        
        int[] array = {1, 2, 3, 4, 5};
        
        // ❌ Плохо: жестко заданные индексы
        try {
            System.out.println("array[10]: " + array[10]); // ArrayIndexOutOfBoundsException
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("❌ Ошибка: жестко заданный индекс выходит за границы");
        }
        
        // ❌ Плохо: жестко заданная длина в цикле
        System.out.println("❌ Не делайте так:");
        System.out.println("for (int i = 0; i < 10; i++) { ... } // Что если array.length != 10?");
        
        // ❌ Плохо: отсутствие проверки на null
        int[] nullArray = null;
        try {
            System.out.println("Длина null массива: " + nullArray.length);
        } catch (NullPointerException e) {
            System.out.println("❌ NPE: проверяйте на null перед обращением к length");
        }
    }
    
    private static void demonstrateBadCopying() {
        System.out.println("\\n--- ❌ Плохое копирование ---");
        
        int[] original = {1, 2, 3, 4, 5};
        
        // ❌ Плохо: присваивание ссылки вместо копирования
        int[] notACopy = original;
        notACopy[0] = 999;
        System.out.println("❌ 'Копия': " + java.util.Arrays.toString(notACopy));
        System.out.println("❌ Оригинал тоже изменился: " + java.util.Arrays.toString(original));
        
        // Восстанавливаем оригинал
        original[0] = 1;
        
        // ❌ Плохо: ручное копирование когда есть готовые методы
        System.out.println("❌ Не используйте ручные циклы для копирования:");
        System.out.println("for (int i = 0; i < src.length; i++) dst[i] = src[i];");
        System.out.println("✅ Используйте: Arrays.copyOf(), clone(), System.arraycopy()");
    }
    
    private static void demonstrateBadComparison() {
        System.out.println("\\n--- ❌ Плохое сравнение ---");
        
        int[] array1 = {1, 2, 3};
        int[] array2 = {1, 2, 3};
        
        // ❌ Плохо: сравнение ссылок вместо содержимого
        System.out.println("❌ array1 == array2: " + (array1 == array2)); // Всегда false!
        System.out.println("✅ Arrays.equals(array1, array2): " + java.util.Arrays.equals(array1, array2));
        
        // ❌ Плохо: использование array.toString() для отладки
        System.out.println("❌ array1.toString(): " + array1.toString()); // Неинформативно
        System.out.println("✅ Arrays.toString(array1): " + java.util.Arrays.toString(array1));
    }
    
    private static void demonstrateBadInitialization() {
        System.out.println("\\n--- ❌ Плохая инициализация ---");
        
        // ❌ Плохо: создание массива размером 0 вместо null
        String[] emptyButNotNull = new String[0]; // Лучше использовать null или константу
        
        // ❌ Плохо: магические числа для размера
        int[] magicSizeArray = new int[42]; // Что означает 42?
        
        // ✅ Лучше: именованные константы
        final int MAX_USERS = 100;
        int[] users = new int[MAX_USERS];
        
        System.out.println("✅ Используйте именованные константы для размеров массивов");
        System.out.println("✅ Размер users: " + users.length + " (MAX_USERS = " + MAX_USERS + ")");
    }
    
    private static void performanceTips() {
        System.out.println("\\n=== Советы по производительности ===");
        
        System.out.println("1. 🚀 Используйте примитивные массивы вместо коллекций когда возможно");
        System.out.println("2. 🚀 Предварительно определяйте размер массива если это возможно");
        System.out.println("3. 🚀 Используйте System.arraycopy() для копирования больших массивов");
        System.out.println("4. 🚀 Используйте Arrays.binarySearch() только для отсортированных массивов");
        System.out.println("5. 🚀 Рассмотрите parallelSort() для больших массивов (>8192 элементов)");
        
        // Демонстрация размера и производительности
        demonstratePerformanceTips();
    }
    
    private static void demonstratePerformanceTips() {
        System.out.println("\\n--- Практические советы ---");
        
        // Совет 1: Предварительное определение размера
        java.util.List<String> dynamicList = new java.util.ArrayList<>();
        long startTime = System.nanoTime();
        for (int i = 0; i < 10000; i++) {
            dynamicList.add("Item " + i);
        }
        long dynamicTime = System.nanoTime() - startTime;
        
        java.util.List<String> preAllocatedList = new java.util.ArrayList<>(10000);
        startTime = System.nanoTime();
        for (int i = 0; i < 10000; i++) {
            preAllocatedList.add("Item " + i);
        }
        long preAllocatedTime = System.nanoTime() - startTime;
        
        System.out.printf("ArrayList без предварительного размера: %.2f мс%n", 
                         dynamicTime / 1_000_000.0);
        System.out.printf("ArrayList с предварительным размером: %.2f мс%n", 
                         preAllocatedTime / 1_000_000.0);
        
        if (preAllocatedTime > 0) {
            System.out.printf("Улучшение: %.2fx%n", (double)dynamicTime / preAllocatedTime);
        }
    }
    
    private static void memoryTips() {
        System.out.println("\\n=== Советы по памяти ===");
        
        System.out.println("1. 💾 Используйте примитивные типы вместо оберток когда возможно");
        System.out.println("2. 💾 Освобождайте ссылки на большие массивы установкой в null");
        System.out.println("3. 💾 Используйте Arrays.copyOfRange() для извлечения подмассивов");
        System.out.println("4. 💾 Рассмотрите использование ByteBuffer для больших данных");
        System.out.println("5. 💾 Избегайте создания временных массивов в циклах");
        
        // Демонстрация экономии памяти
        demonstrateMemoryUsage();
    }
    
    private static void demonstrateMemoryUsage() {
        System.out.println("\\n--- Пример экономии памяти ---");
        
        // Сравнение памяти: Integer[] vs int[]
        Runtime runtime = Runtime.getRuntime();
        
        int size = 1_000_000;
        
        // int[] - примитивные типы
        System.gc();
        long memBefore = runtime.totalMemory() - runtime.freeMemory();
        int[] primitiveArray = new int[size];
        long memAfter = runtime.totalMemory() - runtime.freeMemory();
        long primitiveMemory = memAfter - memBefore;
        
        // Integer[] - объекты-обертки
        System.gc();
        memBefore = runtime.totalMemory() - runtime.freeMemory();
        Integer[] boxedArray = new Integer[size];
        for (int i = 0; i < size; i++) {
            boxedArray[i] = i; // Автобоксинг
        }
        memAfter = runtime.totalMemory() - runtime.freeMemory();
        long boxedMemory = memAfter - memBefore;
        
        System.out.printf("int[%d]: ~%d MB%n", size, primitiveMemory / (1024 * 1024));
        System.out.printf("Integer[%d]: ~%d MB%n", size, boxedMemory / (1024 * 1024));
        
        if (primitiveMemory > 0) {
            System.out.printf("Integer[] использует в %.2fx больше памяти%n", 
                             (double)boxedMemory / primitiveMemory);
        }
        
        // Освобождение памяти
        primitiveArray = null;
        boxedArray = null;
        System.gc();
    }
    
    private static void securityConsiderations() {
        System.out.println("\\n=== Вопросы безопасности ===");
        
        System.out.println("1. 🔒 Не возвращайте ссылки на внутренние массивы");
        System.out.println("2. 🔒 Клонируйте массивы при получении извне");
        System.out.println("3. 🔒 Проверяйте входные данные на null и размер");
        System.out.println("4. 🔒 Используйте final для массивов-констант");
        System.out.println("5. 🔒 Очищайте чувствительные данные после использования");
        
        demonstrateSecurityIssues();
    }
    
    private static void demonstrateSecurityIssues() {
        System.out.println("\\n--- Примеры безопасности ---");
        
        // Демонстрация проблемы безопасности
        SecureDataHolder holder = new SecureDataHolder();
        
        // ❌ Плохо: получаем прямую ссылку
        int[] directReference = holder.getDataUnsafe();
        directReference[0] = 999; // Изменяем внутренние данные!
        
        System.out.println("После изменения через прямую ссылку:");
        System.out.println("Внутренние данные: " + java.util.Arrays.toString(holder.getDataUnsafe()));
        
        // ✅ Хорошо: получаем копию
        int[] safeCopy = holder.getDataSafe();
        safeCopy[0] = 777; // Изменяем копию
        
        System.out.println("\\nПосле изменения копии:");
        System.out.println("Внутренние данные: " + java.util.Arrays.toString(holder.getDataUnsafe()));
        System.out.println("Копия: " + java.util.Arrays.toString(safeCopy));
    }
    
    // Пример класса для демонстрации безопасности
    static class SecureDataHolder {
        private final int[] internalData = {1, 2, 3, 4, 5};
        
        // ❌ Небезопасный метод - возвращает прямую ссылку
        public int[] getDataUnsafe() {
            return internalData;
        }
        
        // ✅ Безопасный метод - возвращает копию
        public int[] getDataSafe() {
            return internalData.clone();
        }
        
        // ✅ Безопасная установка данных
        public void setData(int[] newData) {
            if (newData == null) {
                throw new IllegalArgumentException("Data cannot be null");
            }
            if (newData.length != internalData.length) {
                throw new IllegalArgumentException("Invalid data length");
            }
            
            // Копируем данные, а не ссылку
            System.arraycopy(newData, 0, this.internalData, 0, newData.length);
        }
    }
    
    // Вспомогательные классы
    static class Person {
        private String name;
        private int age;
        
        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        public String getName() { return name; }
        public int getAge() { return age; }
        public void setAge(int age) { this.age = age; }
        
        @Override
        public String toString() {
            return String.format("Person{name='%s', age=%d}", name, age);
        }
    }
    
    private static void printPeople(Person[] people) {
        for (Person person : people) {
            System.out.println("  " + person);
        }
    }
    
    public static void main(String[] args) {
        demonstrateBestPractices();
    }
}
```

---

## 🔗 Связанные темы
- [[Collections-Overview|Collections Framework]] - альтернативы массивам
- [[String-Code|String-Code]] - массивы символов и строки
- [[Performance-Tips|Оптимизация производительности]] - общие принципы оптимизации
- [[Memory-Management|Управление памятью]] - работа с памятью в Java