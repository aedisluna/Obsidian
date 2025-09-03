# Основы Java
*Фундаментальные концепции языка программирования Java*

#java #основы #переменные #типы-данных #операторы

---

## 📋 Содержание
- [[#Переменные и типы данных]]
- [[#Объявление и инициализация]]
- [[#Примитивные типы]]
- [[#Ссылочные типы]]
- [[#Модификаторы доступа]]
- [[#Операторы]]
- [[#Ключевые слова]]

---

## 🔤 Переменные и типы данных

> [!tip] Переменная - это именованный контейнер для хранения данных в памяти

### Структура переменной
Каждая переменная в Java имеет:
- **Тип** - определяет, какие данные можно хранить
- **Имя** - идентификатор для обращения к переменной  
- **Значение** - данные, которые хранятся в переменной

```java
// Структура: тип имя = значение;
int age = 25;           // Целое число
String name = "Иван";   // Строка
boolean isActive = true; // Логическое значение
```

---

## 📝 Объявление и инициализация

### Объявление переменной
```java
// Только объявление (без инициализации)
int number;
String text;
double price;
```

### Объявление с инициализацией
```java
// Объявление и инициализация в одной строке
int count = 10;
String message = "Привет, мир!";
char grade = 'A';
```

### Множественное объявление
```java
// Несколько переменных одного типа
int a, b, c;                    // Только объявление
int x = 1, y = 2, z = 3;       // С инициализацией
```

> [!warning] **Важно!** Локальные переменные должны быть инициализированы перед использованием

---

## 🔢 Примитивные типы

### Целочисленные типы
| Тип | Размер | Диапазон | Пример |
|-----|--------|----------|---------|
| `byte` | 8 бит | -128 до 127 | `byte b = 100;` |
| `short` | 16 бит | -32,768 до 32,767 | `short s = 1000;` |
| `int` | 32 бита | -2³¹ до 2³¹-1 | `int i = 123456;` |
| `long` | 64 бита | -2⁶³ до 2⁶³-1 | `long l = 123456789L;` |

### Вещественные типы
| Тип | Размер | Точность | Пример |
|-----|--------|----------|---------|
| `float` | 32 бита | ~7 знаков | `float f = 3.14f;` |
| `double` | 64 бита | ~15 знаков | `double d = 3.14159;` |

### Прочие типы
```java
char letter = 'A';        // Символ (16 бит Unicode)
boolean flag = true;      // Логический тип (true/false)
```

### Примеры использования
```java
public class DataTypes {
    public static void main(String[] args) {
        // Целые числа
        byte level = 10;
        short year = 2024;
        int population = 1000000;
        long distance = 384400000L; // Расстояние до Луны в км
        
        // Вещественные числа
        float temperature = 36.6f;
        double pi = 3.141592653589793;
        
        // Символ и логика
        char currency = '₽';
        boolean isReady = false;
        
        System.out.println("Уровень: " + level);
        System.out.println("Температура: " + temperature + "°C");
        System.out.println("π = " + pi);
    }
}
```

---

## 🔗 Ссылочные типы

> [!info] Ссылочные типы хранят не само значение, а ссылку на объект в памяти

### String (строки)
```java
String name = "Александр";
String empty = "";              // Пустая строка
String nullString = null;       // null-ссылка
```

### Массивы
```java
int[] numbers = {1, 2, 3, 4, 5};
String[] names = new String[3];
names[0] = "Анна";
names[1] = "Борис";
names[2] = "Виктор";
```

### Классы и объекты
```java
Scanner scanner = new Scanner(System.in);
ArrayList<String> list = new ArrayList<>();
Date currentDate = new Date();
```

### Отличия от примитивов
```java
// Примитивы - значение по умолчанию
int number;        // По умолчанию = 0
boolean flag;      // По умолчанию = false

// Ссылочные типы - null по умолчанию
String text;       // По умолчанию = null
int[] array;       // По умолчанию = null
```

---

## 🔒 Модификаторы доступа

### Уровни доступа
| Модификатор | Класс | Пакет | Подкласс | Везде |
|-------------|-------|-------|----------|--------|
| `private` | ✅ | ❌ | ❌ | ❌ |
| (default) | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

### Примеры
```java
public class Student {
    private String name;           // Доступно только в этом классе
    protected int age;             // Доступно в пакете и подклассах
    public String university;      // Доступно везде
    String city;                   // Package-private (default)
    
    public String getName() {      // Геттер для private поля
        return name;
    }
    
    public void setName(String name) { // Сеттер для private поля
        this.name = name;
    }
}
```

---

## ⚡ Операторы

### Арифметические операторы
```java
int a = 10, b = 3;

int sum = a + b;        // 13 - сложение
int diff = a - b;       // 7  - вычитание  
int product = a * b;    // 30 - умножение
int quotient = a / b;   // 3  - деление (целочисленное)
int remainder = a % b;  // 1  - остаток от деления

// Инкремент и декремент
int x = 5;
x++;                    // x = 6 (постфиксный)
++x;                    // x = 7 (префиксный)
x--;                    // x = 6 (постфиксный)
--x;                    // x = 5 (префиксный)
```

### Операторы сравнения
```java
int x = 10, y = 20;

boolean isEqual = (x == y);      // false - равно
boolean notEqual = (x != y);     // true  - не равно
boolean less = (x < y);          // true  - меньше
boolean greater = (x > y);       // false - больше
boolean lessEqual = (x <= y);    // true  - меньше или равно
boolean greaterEqual = (x >= y); // false - больше или равно
```

### Логические операторы
```java
boolean a = true, b = false;

boolean and = a && b;      // false - логическое И
boolean or = a || b;       // true  - логическое ИЛИ
boolean not = !a;          // false - логическое НЕ

// Короткое замыкание (short-circuit)
if (obj != null && obj.isValid()) { // Если obj == null, obj.isValid() не вызовется
    // Безопасная проверка
}
```

### Операторы присваивания
```java
int x = 10;

x += 5;    // x = x + 5  = 15
x -= 3;    // x = x - 3  = 12
x *= 2;    // x = x * 2  = 24
x /= 4;    // x = x / 4  = 6
x %= 5;    // x = x % 5  = 1
```

### Тернарный оператор
```java
int age = 20;
String status = (age >= 18) ? "Взрослый" : "Несовершеннолетний";

// Эквивалентно:
String status;
if (age >= 18) {
    status = "Взрослый";
} else {
    status = "Несовершеннолетний";
}
```

---

## 🔑 Ключевые слова Java

### Основные категории
```java
// Модификаторы доступа
public, private, protected

// Модификаторы классов/методов
static, final, abstract, synchronized, volatile, transient

// Управление потоком
if, else, switch, case, default, for, while, do, break, continue

// ООП
class, interface, extends, implements, super, this, new, instanceof

// Обработка исключений
try, catch, finally, throw, throws

// Типы данных
boolean, byte, char, short, int, long, float, double, void

// Другие
package, import, return, null
```

> [!warning] **Зарезервированные слова нельзя использовать как имена переменных!**

---

## 💡 Лучшие практики

### Именование переменных
```java
// ✅ Хорошо
int studentAge;
String firstName;
boolean isReady;
final double MAX_TEMPERATURE = 100.0;

// ❌ Плохо  
int a;
String n;
boolean flag;
double temp;
```

### Правила именования
- **camelCase** для переменных и методов: `userName`, `calculateTotal()`
- **PascalCase** для классов: `StudentManager`, `DatabaseConnection`
- **UPPER_SNAKE_CASE** для констант: `MAX_SIZE`, `DEFAULT_TIMEOUT`
- Используй осмысленные имена: `userAge` вместо `age`

### Инициализация
```java
// ✅ Инициализируй локальные переменные
public void processData() {
    int count = 0;           // Явная инициализация
    String message = "";     // Избегай null для строк
    List<String> items = new ArrayList<>(); // Инициализируй коллекции
}

// ✅ Используй окончательные переменные где возможно
final int MAX_ATTEMPTS = 3;
final String CONFIG_FILE = "config.properties";
```

---

## 🎯 Практические примеры

### Калькулятор
```java
public class Calculator {
    public static void main(String[] args) {
        // Входные данные
        double num1 = 15.5;
        double num2 = 4.2;
        char operation = '+';
        
        double result = 0;
        boolean validOperation = true;
        
        // Выполнение операции
        switch (operation) {
            case '+':
                result = num1 + num2;
                break;
            case '-':
                result = num1 - num2;
                break;
            case '*':
                result = num1 * num2;
                break;
            case '/':
                if (num2 != 0) {
                    result = num1 / num2;
                } else {
                    System.out.println("Ошибка: деление на ноль!");
                    validOperation = false;
                }
                break;
            default:
                System.out.println("Неизвестная операция: " + operation);
                validOperation = false;
        }
        
        // Вывод результата
        if (validOperation) {
            System.out.printf("%.2f %c %.2f = %.2f%n", 
                             num1, operation, num2, result);
        }
    }
}
```

### Работа с типами данных
```java
public class DataTypeDemo {
    public static void main(String[] args) {
        // Демонстрация различных типов
        demonstrateIntegers();
        demonstrateFloatingPoint();
        demonstrateCharAndBoolean();
        demonstrateStrings();
    }
    
    static void demonstrateIntegers() {
        System.out.println("=== Целочисленные типы ===");
        
        byte cores = 8;
        short year = 2024;
        int population = 146_000_000; // Подчеркивания для читаемости
        long fileSize = 2_147_483_648L;
        
        System.out.println("Процессорных ядер: " + cores);
        System.out.println("Год: " + year);
        System.out.println("Население России: " + population);
        System.out.println("Размер файла: " + fileSize + " байт");
    }
    
    static void demonstrateFloatingPoint() {
        System.out.println("\n=== Вещественные типы ===");
        
        float bodyTemp = 36.6f;
        double earthRadius = 6_371_000.0; // в метрах
        
        System.out.printf("Температура тела: %.1f°C%n", bodyTemp);
        System.out.printf("Радиус Земли: %.0f м%n", earthRadius);
        
        // Демонстрация точности
        float f = 0.1f + 0.2f;
        double d = 0.1 + 0.2;
        System.out.printf("float: 0.1 + 0.2 = %.10f%n", f);
        System.out.printf("double: 0.1 + 0.2 = %.10f%n", d);
    }
    
    static void demonstrateCharAndBoolean() {
        System.out.println("\n=== Символы и логика ===");
        
        char currency = '₽';
        char letter = 'Я';
        char unicodeHeart = '\u2764'; // ❤
        
        boolean isJavaFun = true;
        boolean isPerfect = false;
        
        System.out.println("Валюта: " + currency);
        System.out.println("Буква: " + letter);
        System.out.println("Сердце: " + unicodeHeart);
        System.out.println("Java - это весело? " + isJavaFun);
        System.out.println("Код идеален? " + isPerfect);
    }
    
    static void demonstrateStrings() {
        System.out.println("\n=== Строки ===");
        
        String greeting = "Привет";
        String name = "Java";
        String message = greeting + ", " + name + "!";
        
        System.out.println("Сообщение: " + message);
        System.out.println("Длина: " + message.length());
        System.out.println("Верхний регистр: " + message.toUpperCase());
    }
}
```

---

## 🔗 Связанные темы
- [[OOP-Principles|Принципы ООП]] - объектно-ориентированное программирование
- [[String|String]] - подробно о работе со строками
- [[Arrays|Arrays]] - массивы в Java
- [[Switch-Case-Code|Switch-Case-Code]] - оператор выбора