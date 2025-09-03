# Интерфейсы в Java
*Контракты и абстракция в объектно-ориентированном программировании*

#java #interfaces #oop #abstraction #контракты

---

## 📋 Содержание
- [[#Что такое интерфейс]]
- [[#Создание и реализация интерфейсов]]
- [[#Default и static методы]]
- [[#Множественная реализация]]
- [[#Интерфейсы и наследование]]
- [[#Функциональные интерфейсы]]
- [[#Практические примеры]]

---

## 🎯 Что такое интерфейс

> [!info] Интерфейс - это контракт, определяющий набор методов, которые должен реализовать класс

### Особенности интерфейсов:
- **Только абстрактные методы** (до Java 8)
- **Default и static методы** (с Java 8)
- **Все методы public** по умолчанию
- **Все поля public static final** (константы)
- **Множественная реализация** - класс может реализовывать несколько интерфейсов
- **Наследование интерфейсов** - интерфейс может расширять другой интерфейс

### Зачем нужны интерфейсы?
- ✅ **Определение контрактов** - что должен уметь класс
- ✅ **Множественное наследование** поведения
- ✅ **Слабая связанность** - программирование против интерфейсов
- ✅ **Полиморфизм** - один интерфейс, разные реализации
- ✅ **Тестируемость** - легко создавать моки и заглушки

---

## 📝 Создание и реализация интерфейсов

### Базовый интерфейс
```java
// Определение интерфейса
public interface Drawable {
    // Абстрактные методы (неявно public abstract)
    void draw();
    void move(int x, int y);
    void resize(double scale);
    
    // Константы (неявно public static final)
    String DEFAULT_COLOR = "BLACK";
    int MAX_SIZE = 1000;
}

// Реализация интерфейса
public class Circle implements Drawable {
    private int x, y;
    private int radius;
    private String color;
    
    public Circle(int x, int y, int radius) {
        this.x = x;
        this.y = y;
        this.radius = radius;
        this.color = DEFAULT_COLOR; // Используем константу из интерфейса
    }
    
    @Override
    public void draw() {
        System.out.printf("Рисую круг: центр(%d, %d), радиус=%d, цвет=%s%n", 
                         x, y, radius, color);
    }
    
    @Override
    public void move(int newX, int newY) {
        System.out.printf("Перемещаю круг с (%d, %d) в (%d, %d)%n", x, y, newX, newY);
        this.x = newX;
        this.y = newY;
    }
    
    @Override
    public void resize(double scale) {
        int oldRadius = radius;
        radius = (int)(radius * scale);
        if (radius > MAX_SIZE / 2) { // Используем константу из интерфейса
            radius = MAX_SIZE / 2;
        }
        System.out.printf("Изменяю размер круга: %d -> %d%n", oldRadius, radius);
    }
    
    // Дополнительные методы класса
    public int getRadius() { return radius; }
    public void setColor(String color) { this.color = color; }
}

public class Rectangle implements Drawable {
    private int x, y;
    private int width, height;
    private String color;
    
    public Rectangle(int x, int y, int width, int height) {
        this.x = x;
        this.y = y;
        this.width = width;
        this.height = height;
        this.color = DEFAULT_COLOR;
    }
    
    @Override
    public void draw() {
        System.out.printf("Рисую прямоугольник: (%d, %d), %dx%d, цвет=%s%n", 
                         x, y, width, height, color);
    }
    
    @Override
    public void move(int newX, int newY) {
        System.out.printf("Перемещаю прямоугольник с (%d, %d) в (%d, %d)%n", x, y, newX, newY);
        this.x = newX;
        this.y = newY;
    }
    
    @Override
    public void resize(double scale) {
        int oldWidth = width, oldHeight = height;
        width = (int)(width * scale);
        height = (int)(height * scale);
        
        if (width > MAX_SIZE) width = MAX_SIZE;
        if (height > MAX_SIZE) height = MAX_SIZE;
        
        System.out.printf("Изменяю размер прямоугольника: %dx%d -> %dx%d%n", 
                         oldWidth, oldHeight, width, height);
    }
    
    public void setColor(String color) { this.color = color; }
}

// Использование
public class BasicInterfaceExample {
    public static void main(String[] args) {
        // Полиморфизм - работаем через интерфейс
        Drawable[] shapes = {
            new Circle(10, 20, 5),
            new Rectangle(50, 60, 100, 80),
            new Circle(0, 0, 10)
        };
        
        System.out.println("=== Рисуем фигуры ===");
        for (Drawable shape : shapes) {
            shape.draw(); // Вызывается нужная реализация
        }
        
        System.out.println("\n=== Перемещаем фигуры ===");
        for (int i = 0; i < shapes.length; i++) {
            shapes[i].move(i * 10, i * 10);
        }
        
        System.out.println("\n=== Изменяем размер ===");
        for (Drawable shape : shapes) {
            shape.resize(1.5);
        }
        
        System.out.println("\n=== Рисуем после изменений ===");
        for (Drawable shape : shapes) {
            shape.draw();
        }
    }
}
```

### Интерфейсы с константами
```java
public interface DatabaseConstants {
    // Константы подключения
    String DEFAULT_HOST = "localhost";
    int DEFAULT_PORT = 5432;
    String DEFAULT_DRIVER = "postgresql";
    
    // Константы запросов
    int MAX_CONNECTIONS = 100;
    int CONNECTION_TIMEOUT = 30000; // миллисекунды
    int QUERY_TIMEOUT = 60000;
    
    // SQL константы
    String SELECT_ALL = "SELECT * FROM ";
    String COUNT_QUERY = "SELECT COUNT(*) FROM ";
}

public interface HttpConstants {
    // HTTP методы
    String GET = "GET";
    String POST = "POST";
    String PUT = "PUT";
    String DELETE = "DELETE";
    
    // HTTP коды ответов
    int OK = 200;
    int CREATED = 201;
    int BAD_REQUEST = 400;
    int UNAUTHORIZED = 401;
    int NOT_FOUND = 404;
    int INTERNAL_SERVER_ERROR = 500;
    
    // Headers
    String CONTENT_TYPE = "Content-Type";
    String AUTHORIZATION = "Authorization";
    String USER_AGENT = "User-Agent";
}

// Использование констант
public class WebService implements HttpConstants, DatabaseConstants {
    
    public void handleRequest(String method, String path) {
        switch (method) {
            case GET:
                System.out.println("Обрабатываю GET запрос к " + path);
                if (path.equals("/users")) {
                    getUsersFromDatabase();
                }
                break;
                
            case POST:
                System.out.println("Обрабатываю POST запрос к " + path);
                break;
                
            default:
                System.out.println("Неподдерживаемый метод: " + method);
                sendResponse(BAD_REQUEST, "Method not allowed");
        }
    }
    
    private void getUsersFromDatabase() {
        System.out.println("Подключаюсь к БД: " + DEFAULT_HOST + ":" + DEFAULT_PORT);
        System.out.println("Выполняю запрос: " + SELECT_ALL + "users");
        sendResponse(OK, "Users data");
    }
    
    private void sendResponse(int statusCode, String message) {
        System.out.println("Отправляю ответ: " + statusCode + " - " + message);
    }
    
    public static void main(String[] args) {
        WebService service = new WebService();
        
        service.handleRequest(GET, "/users");
        service.handleRequest(POST, "/users");
        service.handleRequest("PATCH", "/users");
    }
}
```

---

## 🔧 Default и static методы (Java 8+)

### Default методы
```java
public interface Printable {
    // Абстрактный метод - должен быть реализован
    String getContent();
    
    // Default метод - может быть переопределен, но не обязательно
    default void print() {
        System.out.println("Печатаю: " + getContent());
    }
    
    default void printFormatted() {
        System.out.println("=== ДОКУМЕНТ ===");
        System.out.println(getContent());
        System.out.println("================");
    }
    
    default void printWithHeader(String header) {
        System.out.println("--- " + header + " ---");
        print();
    }
    
    // Static метод - принадлежит интерфейсу, нельзя переопределить
    static void printSeparator() {
        System.out.println("----------------------------------------");
    }
    
    static void printMultiple(Printable... items) {
        for (Printable item : items) {
            item.print();
            printSeparator();
        }
    }
}

// Простая реализация
public class Document implements Printable {
    private String title;
    private String content;
    
    public Document(String title, String content) {
        this.title = title;
        this.content = content;
    }
    
    @Override
    public String getContent() {
        return title + "\\n" + content;
    }
    
    // Используем default методы как есть
}

// Реализация с переопределением default метода
public class FancyDocument implements Printable {
    private String title;
    private String content;
    private String author;
    
    public FancyDocument(String title, String content, String author) {
        this.title = title;
        this.content = content;
        this.author = author;
    }
    
    @Override
    public String getContent() {
        return String.format("Название: %s\\nАвтор: %s\\n\\n%s", title, author, content);
    }
    
    // Переопределяем default метод
    @Override
    public void print() {
        System.out.println("┌" + "─".repeat(50) + "┐");
        System.out.println("│" + centerText(title, 50) + "│");
        System.out.println("│" + centerText("by " + author, 50) + "│");
        System.out.println("├" + "─".repeat(50) + "┤");
        
        String[] lines = content.split("\\n");
        for (String line : lines) {
            System.out.println("│ " + padRight(line, 48) + " │");
        }
        System.out.println("└" + "─".repeat(50) + "┘");
    }
    
    private String centerText(String text, int width) {
        if (text.length() >= width) return text.substring(0, width);
        int padding = (width - text.length()) / 2;
        return " ".repeat(padding) + text + " ".repeat(width - text.length() - padding);
    }
    
    private String padRight(String text, int width) {
        if (text.length() >= width) return text.substring(0, width);
        return text + " ".repeat(width - text.length());
    }
}

// Использование
public class DefaultMethodsExample {
    public static void main(String[] args) {
        Document doc1 = new Document("Простой документ", "Это обычный документ\\nс простым содержимым.");
        
        FancyDocument doc2 = new FancyDocument(
            "Красивый документ", 
            "Этот документ имеет\\nкрасивое оформление\\nи выглядит лучше.",
            "Автор Авторович"
        );
        
        System.out.println("=== Простая печать ===");
        doc1.print(); // Использует default метод
        
        Printable.printSeparator(); // Static метод
        
        doc2.print(); // Использует переопределенный метод
        
        System.out.println("\\n=== Форматированная печать ===");
        doc1.printFormatted(); // Default метод
        
        System.out.println("\\n=== Печать с заголовком ===");
        doc1.printWithHeader("ВАЖНЫЙ ДОКУМЕНТ");
        
        System.out.println("\\n=== Множественная печать ===");
        Printable.printMultiple(doc1, doc2); // Static метод
    }
}
```

### Решение конфликтов default методов
```java
interface A {
    default void method() {
        System.out.println("Метод из интерфейса A");
    }
}

interface B {
    default void method() {
        System.out.println("Метод из интерфейса B");
    }
}

// Класс должен явно разрешить конфликт
public class ConflictResolver implements A, B {
    
    @Override
    public void method() {
        // Вариант 1: Выбираем конкретную реализацию
        A.super.method();
        
        // Вариант 2: Вызываем обе реализации
        // A.super.method();
        // B.super.method();
        
        // Вариант 3: Создаем собственную реализацию
        // System.out.println("Собственная реализация метода");
    }
}

interface Loggable {
    default void log(String message) {
        System.out.println("[LOG] " + message);
    }
    
    default void logError(String message) {
        System.err.println("[ERROR] " + message);
    }
}

interface Debuggable {
    default void log(String message) {
        System.out.println("[DEBUG] " + message);
    }
    
    default void trace(String message) {
        System.out.println("[TRACE] " + message);
    }
}

public class Service implements Loggable, Debuggable {
    
    @Override
    public void log(String message) {
        // Комбинируем поведение обоих интерфейсов
        Loggable.super.log(message);
        Debuggable.super.log(message);
    }
    
    public void doWork() {
        log("Начинаю работу");
        trace("Детали выполнения");
        logError("Что-то пошло не так");
    }
    
    public static void main(String[] args) {
        new Service().doWork();
    }
}
```

---

## 🔀 Множественная реализация

> [!tip] Класс может реализовывать несколько интерфейсов, что позволяет комбинировать различные возможности

### Комбинирование интерфейсов
```java
interface Flyable {
    void fly();
    default int getMaxAltitude() { return 1000; }
}

interface Swimmable {
    void swim();
    default int getMaxDepth() { return 100; }
}

interface Walkable {
    void walk();
    default int getMaxSpeed() { return 10; }
}

// Утка может летать, плавать и ходить
public class Duck implements Flyable, Swimmable, Walkable {
    private String name;
    
    public Duck(String name) {
        this.name = name;
    }
    
    @Override
    public void fly() {
        System.out.println(name + " летит на высоте " + getMaxAltitude() + "м");
    }
    
    @Override
    public void swim() {
        System.out.println(name + " плавает на глубине до " + getMaxDepth() + "м");
    }
    
    @Override
    public void walk() {
        System.out.println(name + " ходит со скоростью " + getMaxSpeed() + " км/ч");
    }
    
    // Переопределяем некоторые default методы
    @Override
    public int getMaxSpeed() {
        return 5; // Утки ходят медленнее
    }
}

// Рыба может только плавать
public class Fish implements Swimmable {
    private String species;
    
    public Fish(String species) {
        this.species = species;
    }
    
    @Override
    public void swim() {
        System.out.println(species + " плавает на глубине до " + getMaxDepth() + "м");
    }
    
    @Override
    public int getMaxDepth() {
        return 500; // Рыбы плавают глубже
    }
}

// Птица может летать и ходить
public class Bird implements Flyable, Walkable {
    private String species;
    
    public Bird(String species) {
        this.species = species;
    }
    
    @Override
    public void fly() {
        System.out.println(species + " летит на высоте " + getMaxAltitude() + "м");
    }
    
    @Override
    public void walk() {
        System.out.println(species + " ходит со скоростью " + getMaxSpeed() + " км/ч");
    }
    
    @Override
    public int getMaxAltitude() {
        return 3000; // Птицы летают выше
    }
}

// Использование
public class MultipleInterfacesExample {
    public static void main(String[] args) {
        Duck duck = new Duck("Кряква");
        Fish fish = new Fish("Карп");
        Bird bird = new Bird("Орел");
        
        System.out.println("=== Утка ===");
        duck.fly();
        duck.swim();
        duck.walk();
        
        System.out.println("\\n=== Рыба ===");
        fish.swim();
        
        System.out.println("\\n=== Птица ===");
        bird.fly();
        bird.walk();
        
        // Полиморфизм с разными интерфейсами
        System.out.println("\\n=== Полиморфизм ===");
        
        Flyable[] flyers = {duck, bird};
        for (Flyable flyer : flyers) {
            flyer.fly();
        }
        
        Swimmable[] swimmers = {duck, fish};
        for (Swimmable swimmer : swimmers) {
            swimmer.swim();
        }
        
        Walkable[] walkers = {duck, bird};
        for (Walkable walker : walkers) {
            walker.walk();
        }
    }
}
```

### Интерфейсы-маркеры
```java
// Маркерные интерфейсы (без методов) - указывают на специальные свойства классов
interface Serializable {
    // Пустой интерфейс - маркер для сериализации
}

interface Cloneable {
    // Маркер для клонирования
}

interface Deprecated {
    // Маркер для устаревших классов
}

public class User implements Serializable, Cloneable {
    private String name;
    private int age;
    
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Реализация клонирования
    @Override
    public User clone() {
        try {
            return (User) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new RuntimeException("Ошибка клонирования", e);
        }
    }
    
    // Геттеры и toString
    public String getName() { return name; }
    public int getAge() { return age; }
    
    @Override
    public String toString() {
        return "User{name='" + name + "', age=" + age + "}";
    }
}

// Утилитный класс для работы с маркерными интерфейсами
public class ObjectUtils {
    
    public static boolean isSerializable(Object obj) {
        return obj instanceof Serializable;
    }
    
    public static boolean isCloneable(Object obj) {
        return obj instanceof Cloneable;
    }
    
    public static <T> T cloneIfPossible(T obj) {
        if (obj instanceof Cloneable) {
            try {
                return (T) obj.getClass().getMethod("clone").invoke(obj);
            } catch (Exception e) {
                System.err.println("Ошибка клонирования: " + e.getMessage());
            }
        }
        return obj;
    }
    
    public static void main(String[] args) {
        User user = new User("Иван", 30);
        String text = "Обычная строка";
        
        System.out.println("User сериализуемый? " + isSerializable(user));
        System.out.println("User клонируемый? " + isCloneable(user));
        System.out.println("String сериализуемый? " + isSerializable(text));
        System.out.println("String клонируемый? " + isCloneable(text));
        
        User clonedUser = cloneIfPossible(user);
        System.out.println("Оригинал: " + user);
        System.out.println("Клон: " + clonedUser);
        System.out.println("Это разные объекты? " + (user != clonedUser));
    }
}
```

---

## 🧬 Интерфейсы и наследование

### Наследование интерфейсов
```java
// Базовый интерфейс
interface Shape {
    double getArea();
    double getPerimeter();
}

// Расширение интерфейса
interface ColoredShape extends Shape {
    String getColor();
    void setColor(String color);
    
    default void printInfo() {
        System.out.printf("Фигура: площадь=%.2f, периметр=%.2f, цвет=%s%n",
                         getArea(), getPerimeter(), getColor());
    }
}

// Множественное наследование интерфейсов
interface Movable {
    void move(int deltaX, int deltaY);
    int getX();
    int getY();
}

interface Scalable {
    void scale(double factor);
}

// Комбинированный интерфейс
interface GraphicShape extends ColoredShape, Movable, Scalable {
    // Объединяет все возможности
    
    default void animateMove(int targetX, int targetY, int steps) {
        int currentX = getX();
        int currentY = getY();
        
        int deltaX = (targetX - currentX) / steps;
        int deltaY = (targetY - currentY) / steps;
        
        for (int i = 0; i < steps; i++) {
            move(deltaX, deltaY);
            System.out.printf("Шаг %d: позиция (%d, %d)%n", i + 1, getX(), getY());
            
            try {
                Thread.sleep(100); // Имитация анимации
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                break;
            }
        }
    }
}

// Реализация всех интерфейсов
public class AnimatedCircle implements GraphicShape {
    private int x, y;
    private double radius;
    private String color;
    
    public AnimatedCircle(int x, int y, double radius, String color) {
        this.x = x;
        this.y = y;
        this.radius = radius;
        this.color = color;
    }
    
    // Реализация Shape
    @Override
    public double getArea() {
        return Math.PI * radius * radius;
    }
    
    @Override
    public double getPerimeter() {
        return 2 * Math.PI * radius;
    }
    
    // Реализация ColoredShape
    @Override
    public String getColor() {
        return color;
    }
    
    @Override
    public void setColor(String color) {
        this.color = color;
    }
    
    // Реализация Movable
    @Override
    public void move(int deltaX, int deltaY) {
        this.x += deltaX;
        this.y += deltaY;
    }
    
    @Override
    public int getX() {
        return x;
    }
    
    @Override
    public int getY() {
        return y;
    }
    
    // Реализация Scalable
    @Override
    public void scale(double factor) {
        radius *= factor;
        System.out.printf("Масштабирование: новый радиус = %.2f%n", radius);
    }
    
    public double getRadius() {
        return radius;
    }
    
    @Override
    public String toString() {
        return String.format("AnimatedCircle{x=%d, y=%d, radius=%.2f, color='%s'}", 
                           x, y, radius, color);
    }
}

// Использование
public class InterfaceInheritanceExample {
    public static void main(String[] args) {
        AnimatedCircle circle = new AnimatedCircle(10, 20, 5.0, "красный");
        
        System.out.println("Начальное состояние:");
        System.out.println(circle);
        circle.printInfo();
        
        System.out.println("\\n=== Перемещение ===");
        circle.move(5, -3);
        System.out.println("После перемещения: " + circle);
        
        System.out.println("\\n=== Масштабирование ===");
        circle.scale(1.5);
        circle.printInfo();
        
        System.out.println("\\n=== Изменение цвета ===");
        circle.setColor("синий");
        circle.printInfo();
        
        System.out.println("\\n=== Анимация ===");
        circle.animateMove(50, 50, 5);
        
        System.out.println("\\n=== Полиморфизм ===");
        // Можем использовать объект как любой из интерфейсов
        Shape shape = circle;
        System.out.println("Как Shape - площадь: " + shape.getArea());
        
        Movable movable = circle;
        movable.move(-10, -10);
        System.out.println("Как Movable - позиция: (" + movable.getX() + ", " + movable.getY() + ")");
        
        ColoredShape colored = circle;
        colored.setColor("зеленый");
        System.out.println("Как ColoredShape - цвет: " + colored.getColor());
    }
}
```

---

## ⚡ Функциональные интерфейсы

> [!info] Функциональный интерфейс содержит ровно один абстрактный метод и может использоваться с lambda выражениями

### Создание функциональных интерфейсов
```java
@FunctionalInterface
public interface Calculator {
    double calculate(double a, double b);
    
    // Default методы разрешены
    default void printResult(double a, double b) {
        System.out.printf("%.2f op %.2f = %.2f%n", a, b, calculate(a, b));
    }
    
    // Static методы разрешены
    static Calculator add() {
        return (a, b) -> a + b;
    }
    
    static Calculator multiply() {
        return (a, b) -> a * b;
    }
}

@FunctionalInterface
public interface StringProcessor {
    String process(String input);
    
    default StringProcessor andThen(StringProcessor after) {
        return input -> after.process(this.process(input));
    }
    
    static StringProcessor identity() {
        return str -> str;
    }
}

@FunctionalInterface
public interface Validator<T> {
    boolean isValid(T item);
    
    default Validator<T> and(Validator<T> other) {
        return item -> this.isValid(item) && other.isValid(item);
    }
    
    default Validator<T> or(Validator<T> other) {
        return item -> this.isValid(item) || other.isValid(item);
    }
    
    default Validator<T> negate() {
        return item -> !this.isValid(item);
    }
}

// Использование функциональных интерфейсов
public class FunctionalInterfaceExample {
    public static void main(String[] args) {
        
        // Использование Calculator с lambda
        Calculator addition = (a, b) -> a + b;
        Calculator subtraction = (a, b) -> a - b;
        Calculator division = (a, b) -> b != 0 ? a / b : Double.NaN;
        
        System.out.println("=== Калькулятор ===");
        performCalculation(addition, 10, 5);
        performCalculation(subtraction, 10, 5);
        performCalculation(division, 10, 5);
        performCalculation(Calculator.multiply(), 10, 5);
        
        // Использование StringProcessor
        StringProcessor toUpper = String::toUpperCase;
        StringProcessor addPrefix = str -> ">>> " + str;
        StringProcessor addSuffix = str -> str + " <<<";
        
        StringProcessor combined = toUpper.andThen(addPrefix).andThen(addSuffix);
        
        System.out.println("\\n=== Обработка строк ===");
        String result = combined.process("hello world");
        System.out.println("Результат: " + result);
        
        // Использование Validator
        Validator<String> notEmpty = str -> str != null && !str.trim().isEmpty();
        Validator<String> minLength = str -> str != null && str.length() >= 3;
        Validator<String> hasDigit = str -> str != null && str.matches(".*\\d.*");
        
        Validator<String> strongValidator = notEmpty.and(minLength).and(hasDigit);
        Validator<String> weakValidator = notEmpty.or(minLength);
        
        System.out.println("\\n=== Валидация ===");
        String[] testStrings = {"", "ab", "abc", "abc123", "test", "1"};
        
        for (String test : testStrings) {
            System.out.printf("'%s': строгая=%b, слабая=%b%n", 
                test, strongValidator.isValid(test), weakValidator.isValid(test));
        }
    }
    
    private static void performCalculation(Calculator calc, double a, double b) {
        calc.printResult(a, b);
    }
}
```

### Встроенные функциональные интерфейсы
```java
import java.util.function.*;
import java.util.*;

public class BuiltInFunctionalInterfaces {
    public static void main(String[] args) {
        
        // Predicate<T> - проверка условия
        Predicate<Integer> isEven = n -> n % 2 == 0;
        Predicate<String> isLong = s -> s.length() > 5;
        
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        List<Integer> evenNumbers = numbers.stream()
            .filter(isEven)
            .collect(Collectors.toList());
        
        System.out.println("Четные числа: " + evenNumbers);
        
        // Function<T, R> - преобразование
        Function<String, Integer> stringLength = String::length;
        Function<Integer, String> intToHex = Integer::toHexString;
        
        List<String> words = Arrays.asList("Java", "Lambda", "Interface");
        List<Integer> lengths = words.stream()
            .map(stringLength)
            .collect(Collectors.toList());
        
        System.out.println("Длины слов: " + lengths);
        
        // Consumer<T> - потребление без возврата
        Consumer<String> printer = System.out::println;
        Consumer<String> logger = s -> System.out.println("[LOG] " + s);
        
        Consumer<String> combined = printer.andThen(logger);
        
        System.out.println("\\nИспользование Consumer:");
        combined.accept("Тестовое сообщение");
        
        // Supplier<T> - поставка значений
        Supplier<String> timestampSupplier = () -> "Время: " + System.currentTimeMillis();
        Supplier<Integer> randomSupplier = () -> (int)(Math.random() * 100);
        
        System.out.println("\\n" + timestampSupplier.get());
        System.out.println("Случайное число: " + randomSupplier.get());
        
        // UnaryOperator<T> - операция над одним операндом
        UnaryOperator<String> addExclamation = s -> s + "!";
        UnaryOperator<Integer> square = n -> n * n;
        
        System.out.println("\\n" + addExclamation.apply("Привет"));
        System.out.println("Квадрат 7: " + square.apply(7));
        
        // BinaryOperator<T> - операция над двумя операндами
        BinaryOperator<Integer> sum = Integer::sum;
        BinaryOperator<String> concat = (s1, s2) -> s1 + " " + s2;
        
        System.out.println("\\nСумма 15 + 25 = " + sum.apply(15, 25));
        System.out.println(concat.apply("Hello", "World"));
        
        // BiFunction<T, U, R> - функция двух аргументов
        BiFunction<String, Integer, String> repeat = String::repeat;
        BiFunction<Integer, Integer, Double> divide = (a, b) -> (double) a / b;
        
        System.out.println("\\n" + repeat.apply("Java ", 3));
        System.out.println("20 / 3 = " + divide.apply(20, 3));
    }
}
```

---

## 💼 Практические примеры

### Система событий (Observer Pattern)
```java
@FunctionalInterface
interface EventListener<T> {
    void onEvent(T event);
}

class Event {
    private final String type;
    private final Object data;
    private final long timestamp;
    
    public Event(String type, Object data) {
        this.type = type;
        this.data = data;
        this.timestamp = System.currentTimeMillis();
    }
    
    public String getType() { return type; }
    public Object getData() { return data; }
    public long getTimestamp() { return timestamp; }
    
    @Override
    public String toString() {
        return String.format("Event{type='%s', data=%s, timestamp=%d}", type, data, timestamp);
    }
}

class EventBus {
    private final Map<String, List<EventListener<Event>>> listeners = new HashMap<>();
    
    public void subscribe(String eventType, EventListener<Event> listener) {
        listeners.computeIfAbsent(eventType, k -> new ArrayList<>()).add(listener);
    }
    
    public void unsubscribe(String eventType, EventListener<Event> listener) {
        List<EventListener<Event>> eventListeners = listeners.get(eventType);
        if (eventListeners != null) {
            eventListeners.remove(listener);
        }
    }
    
    public void publish(Event event) {
        List<EventListener<Event>> eventListeners = listeners.get(event.getType());
        if (eventListeners != null) {
            for (EventListener<Event> listener : eventListeners) {
                try {
                    listener.onEvent(event);
                } catch (Exception e) {
                    System.err.println("Ошибка в слушателе: " + e.getMessage());
                }
            }
        }
    }
    
    public void publishAsync(Event event) {
        new Thread(() -> publish(event)).start();
    }
}

// Использование системы событий
public class EventSystemExample {
    public static void main(String[] args) {
        EventBus eventBus = new EventBus();
        
        // Подписываемся на события пользователей
        eventBus.subscribe("user.login", event -> {
            System.out.println("📋 Логирование: Пользователь вошел - " + event.getData());
        });
        
        eventBus.subscribe("user.login", event -> {
            System.out.println("📧 Email: Отправляем уведомление о входе для " + event.getData());
        });
        
        eventBus.subscribe("user.logout", event -> {
            System.out.println("📋 Логирование: Пользователь вышел - " + event.getData());
        });
        
        // Подписываемся на события заказов
        eventBus.subscribe("order.created", event -> {
            System.out.println("🛒 Заказ создан: " + event.getData());
        });
        
        eventBus.subscribe("order.created", event -> {
            System.out.println("💳 Обрабатываем платеж для заказа: " + event.getData());
        });
        
        eventBus.subscribe("order.completed", event -> {
            System.out.println("✅ Заказ выполнен: " + event.getData());
            System.out.println("📦 Отправляем товар");
        });
        
        // Публикуем события
        System.out.println("=== События пользователей ===");
        eventBus.publish(new Event("user.login", "john_doe"));
        eventBus.publish(new Event("user.logout", "john_doe"));
        
        System.out.println("\\n=== События заказов ===");
        eventBus.publish(new Event("order.created", "ORDER-001"));
        eventBus.publish(new Event("order.completed", "ORDER-001"));
        
        // Асинхронная публикация
        System.out.println("\\n=== Асинхронные события ===");
        eventBus.publishAsync(new Event("user.login", "jane_smith"));
        
        try {
            Thread.sleep(100); // Ждем асинхронное выполнение
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

### Система валидации
```java
@FunctionalInterface
interface ValidationRule<T> {
    ValidationResult validate(T object);
    
    default ValidationRule<T> and(ValidationRule<T> other) {
        return object -> {
            ValidationResult result1 = this.validate(object);
            if (!result1.isValid()) {
                return result1;
            }
            return other.validate(object);
        };
    }
    
    static <T> ValidationRule<T> of(Predicate<T> predicate, String errorMessage) {
        return object -> predicate.test(object) ? 
            ValidationResult.valid() : 
            ValidationResult.invalid(errorMessage);
    }
}

class ValidationResult {
    private final boolean valid;
    private final String errorMessage;
    
    private ValidationResult(boolean valid, String errorMessage) {
        this.valid = valid;
        this.errorMessage = errorMessage;
    }
    
    public static ValidationResult valid() {
        return new ValidationResult(true, null);
    }
    
    public static ValidationResult invalid(String errorMessage) {
        return new ValidationResult(false, errorMessage);
    }
    
    public boolean isValid() { return valid; }
    public String getErrorMessage() { return errorMessage; }
    
    @Override
    public String toString() {
        return valid ? "VALID" : "INVALID: " + errorMessage;
    }
}

class UserData {
    private String username;
    private String email;
    private int age;
    private String password;
    
    public UserData(String username, String email, int age, String password) {
        this.username = username;
        this.email = email;
        this.age = age;
        this.password = password;
    }
    
    // Геттеры
    public String getUsername() { return username; }
    public String getEmail() { return email; }
    public int getAge() { return age; }
    public String getPassword() { return password; }
    
    @Override
    public String toString() {
        return String.format("UserData{username='%s', email='%s', age=%d}", username, email, age);
    }
}

class UserValidator {
    private static final ValidationRule<UserData> USERNAME_RULE = ValidationRule.of(
        user -> user.getUsername() != null && user.getUsername().length() >= 3,
        "Имя пользователя должно быть не менее 3 символов"
    );
    
    private static final ValidationRule<UserData> EMAIL_RULE = ValidationRule.of(
        user -> user.getEmail() != null && user.getEmail().matches("^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$"),
        "Неверный формат email"
    );
    
    private static final ValidationRule<UserData> AGE_RULE = ValidationRule.of(
        user -> user.getAge() >= 18 && user.getAge() <= 120,
        "Возраст должен быть от 18 до 120 лет"
    );
    
    private static final ValidationRule<UserData> PASSWORD_RULE = ValidationRule.of(
        user -> user.getPassword() != null && user.getPassword().length() >= 8,
        "Пароль должен быть не менее 8 символов"
    );
    
    private static final ValidationRule<UserData> STRONG_PASSWORD_RULE = ValidationRule.of(
        user -> user.getPassword() != null && 
                 user.getPassword().matches(".*[A-Z].*") &&
                 user.getPassword().matches(".*[a-z].*") &&
                 user.getPassword().matches(".*\\d.*"),
        "Пароль должен содержать заглавные и строчные буквы, и цифры"
    );
    
    public static ValidationResult validateBasic(UserData user) {
        return USERNAME_RULE
            .and(EMAIL_RULE)
            .and(AGE_RULE)
            .and(PASSWORD_RULE)
            .validate(user);
    }
    
    public static ValidationResult validateStrong(UserData user) {
        return validateBasic(user).isValid() ? 
            STRONG_PASSWORD_RULE.validate(user) : 
            validateBasic(user);
    }
}

// Использование системы валидации
public class ValidationSystemExample {
    public static void main(String[] args) {
        UserData[] users = {
            new UserData("john", "john@example.com", 25, "password123"),
            new UserData("a", "invalid-email", 15, "123"),
            new UserData("alice", "alice@example.com", 30, "StrongPass123"),
            new UserData("bob", "bob@test.com", 28, "weakpass")
        };
        
        System.out.println("=== Базовая валидация ===");
        for (UserData user : users) {
            ValidationResult result = UserValidator.validateBasic(user);
            System.out.printf("%-40s -> %s%n", user, result);
        }
        
        System.out.println("\\n=== Строгая валидация ===");
        for (UserData user : users) {
            ValidationResult result = UserValidator.validateStrong(user);
            System.out.printf("%-40s -> %s%n", user, result);
        }
        
        // Кастомная валидация
        System.out.println("\\n=== Кастомная валидация ===");
        ValidationRule<UserData> customRule = ValidationRule.of(
            user -> !user.getUsername().toLowerCase().contains("admin"),
            "Имя пользователя не может содержать 'admin'"
        );
        
        UserData adminUser = new UserData("administrator", "admin@example.com", 35, "AdminPass123");
        ValidationResult customResult = UserValidator.validateStrong(adminUser)
            .isValid() ? customRule.validate(adminUser) : UserValidator.validateStrong(adminUser);
        
        System.out.printf("%-40s -> %s%n", adminUser, customResult);
    }
}
```

---

## 💡 Лучшие практики интерфейсов

### 1. Проектирование интерфейсов
```java
// ✅ Хорошо - маленькие, целенаправленные интерфейсы (ISP - Interface Segregation Principle)
interface Readable {
    String read();
}

interface Writable {
    void write(String data);
}

interface Closeable {
    void close();
}

// ✅ Клиенты реализуют только нужные интерфейсы
class FileReader implements Readable, Closeable {
    public String read() { return "file content"; }
    public void close() { System.out.println("File closed"); }
}

class FileWriter implements Writable, Closeable {
    public void write(String data) { System.out.println("Writing: " + data); }
    public void close() { System.out.println("File closed"); }
}

// ❌ Плохо - "толстый" интерфейс
interface BadFileHandler {
    String read();    // Не все классы могут читать
    void write(String data);  // Не все классы могут писать
    void close();
    void compress();  // Не всем нужно сжатие
    void encrypt();   // Не всем нужно шифрование
}
```

### 2. Именование интерфейсов
```java
// ✅ Хорошие имена интерфейсов
interface Comparable<T> { }      // Описывает возможность
interface Runnable { }           // Описывает действие
interface EventListener { }      // Роль объекта
interface UserRepository { }     // Абстракция сервиса
interface PaymentProcessor { }   // Абстракция процесса

// ❌ Плохие имена
interface IUser { }              // Избегай префикса I
interface UserImpl { }           // Impl - это для классов
interface AbstractUser { }       // Abstract - тоже для классов
```

### 3. Default методы
```java
// ✅ Используй default методы для расширения без нарушения совместимости
interface Collection<T> {
    boolean add(T item);
    boolean remove(T item);
    int size();
    
    // Default методы добавлены в Java 8 без нарушения совместимости
    default boolean isEmpty() {
        return size() == 0;
    }
    
    default void clear() {
        // Базовая реализация через существующие методы
        Iterator<T> iterator = iterator();
        while (iterator.hasNext()) {
            iterator.next();
            iterator.remove();
        }
    }
}

// ❌ Не злоупотребляй default методами
interface BadExample {
    void essentialMethod();
    
    // Слишком много логики в default методе
    default void complexDefaultMethod() {
        // 50+ строк сложной логики
        // Лучше вынести в утилитный класс
    }
}
```

---

## 🔗 Связанные темы
- [[OOP-Principles-Code|Принципы ООП]] - абстракция и полиморфизм
- [[Lambda-Expressions-Code|Lambda выражения]] - использование с функциональными интерфейсами
- [[Classes-and-Objects|Классы и объекты]] - реализация интерфейсов
- [[Inheritance|Наследование]] - отличия от наследования классов