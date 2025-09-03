# Принципы ООП в Java
*Объектно-ориентированное программирование*

#java #oop #encapsulation #inheritance #polymorphism #abstraction

---

## 📋 Содержание
- [[#Основы ООП]]
- [[#Инкапсуляция]]
- [[#Наследование]]
- [[#Полиморфизм]]
- [[#Абстракция]]
- [[#SOLID принципы]]
- [[#Практические примеры]]

---

## 🎯 Основы ООП

> [!info] Объектно-ориентированное программирование (ООП) - это парадигма программирования, основанная на концепции объектов, которые содержат данные (поля) и код (методы)

### Четыре основных принципа ООП
1. **Инкапсуляция** - сокрытие внутренней реализации
2. **Наследование** - создание новых классов на основе существующих
3. **Полиморфизм** - один интерфейс, множество реализаций
4. **Абстракция** - выделение важных характеристик объекта

### Класс vs Объект
```java
// Класс - это шаблон или чертеж для создания объектов
public class Car {
    // Поля (состояние объекта)
    private String brand;
    private String model;
    private int year;
    private boolean isRunning;
    
    // Конструктор
    public Car(String brand, String model, int year) {
        this.brand = brand;
        this.model = model;
        this.year = year;
        this.isRunning = false;
    }
    
    // Методы (поведение объекта)
    public void start() {
        if (!isRunning) {
            isRunning = true;
            System.out.println(brand + " " + model + " запущен");
        }
    }
    
    public void stop() {
        if (isRunning) {
            isRunning = false;
            System.out.println(brand + " " + model + " остановлен");
        }
    }
    
    // Геттеры и сеттеры
    public String getBrand() { return brand; }
    public String getModel() { return model; }
    public int getYear() { return year; }
    public boolean isRunning() { return isRunning; }
}

// Создание и использование объектов
public class CarExample {
    public static void main(String[] args) {
        // Создаем объекты (экземпляры класса)
        Car car1 = new Car("Toyota", "Camry", 2022);
        Car car2 = new Car("BMW", "X5", 2023);
        
        // Используем методы объектов
        car1.start();
        car2.start();
        
        System.out.println("Машина 1: " + car1.getBrand() + " " + car1.getModel() + 
                          " (" + car1.getYear() + ") - работает: " + car1.isRunning());
        
        car1.stop();
    }
}
```

### Преимущества ООП
- ✅ **Модульность** - код организован в логические блоки
- ✅ **Переиспользование** - код можно использовать повторно
- ✅ **Поддерживаемость** - легче изменять и расширять
- ✅ **Безопасность** - инкапсуляция защищает данные
- ✅ **Масштабируемость** - легко добавлять новый функционал

---

## 🔒 Инкапсуляция

> [!tip] Инкапсуляция - это сокрытие внутренней реализации класса и предоставление контролируемого доступа к данным через методы

### Уровни доступа в Java
```java
public class AccessModifiersExample {
    
    public String publicField;        // Доступно везде
    protected String protectedField;  // Доступно в пакете и наследникам
    String packagePrivateField;      // Доступно только в пакете (default)
    private String privateField;     // Доступно только в этом классе
    
    public AccessModifiersExample() {
        publicField = "Public";
        protectedField = "Protected";
        packagePrivateField = "Package";
        privateField = "Private";
    }
    
    // Публичные методы для работы с приватными данными
    public String getPrivateField() {
        return privateField;
    }
    
    public void setPrivateField(String value) {
        // Можем добавить валидацию
        if (value != null && !value.trim().isEmpty()) {
            this.privateField = value;
        }
    }
}
```

### Правильная инкапсуляция
```java
public class BankAccount {
    // Приватные поля - никто не может получить прямой доступ
    private String accountNumber;
    private String ownerName;
    private double balance;
    private List<Transaction> transactionHistory;
    
    // Конструктор
    public BankAccount(String accountNumber, String ownerName, double initialBalance) {
        this.accountNumber = accountNumber;
        this.ownerName = ownerName;
        this.balance = Math.max(0, initialBalance); // Баланс не может быть отрицательным
        this.transactionHistory = new ArrayList<>();
        
        if (initialBalance > 0) {
            transactionHistory.add(new Transaction("INITIAL_DEPOSIT", initialBalance));
        }
    }
    
    // Контролируемый доступ через методы
    public String getAccountNumber() {
        return accountNumber; // Только чтение
    }
    
    public String getOwnerName() {
        return ownerName; // Только чтение
    }
    
    public double getBalance() {
        return balance; // Только чтение
    }
    
    // Методы для изменения состояния с проверками
    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Сумма депозита должна быть положительной");
        }
        
        balance += amount;
        transactionHistory.add(new Transaction("DEPOSIT", amount));
        System.out.println("Депозит: " + amount + ". Новый баланс: " + balance);
    }
    
    public void withdraw(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Сумма снятия должна быть положительной");
        }
        
        if (amount > balance) {
            throw new IllegalArgumentException("Недостаточно средств на счете");
        }
        
        balance -= amount;
        transactionHistory.add(new Transaction("WITHDRAWAL", amount));
        System.out.println("Снятие: " + amount + ". Новый баланс: " + balance);
    }
    
    // Возвращаем копию списка, а не оригинал
    public List<Transaction> getTransactionHistory() {
        return new ArrayList<>(transactionHistory);
    }
    
    // Приватный метод - внутренняя логика
    private boolean hasEnoughFunds(double amount) {
        return balance >= amount;
    }
    
    @Override
    public String toString() {
        return String.format("BankAccount{accountNumber='%s', owner='%s', balance=%.2f}", 
                           accountNumber, ownerName, balance);
    }
}

// Вспомогательный класс
class Transaction {
    private final String type;
    private final double amount;
    private final LocalDateTime timestamp;
    
    public Transaction(String type, double amount) {
        this.type = type;
        this.amount = amount;
        this.timestamp = LocalDateTime.now();
    }
    
    // Геттеры (только чтение)
    public String getType() { return type; }
    public double getAmount() { return amount; }
    public LocalDateTime getTimestamp() { return timestamp; }
    
    @Override
    public String toString() {
        return String.format("%s: %.2f (%s)", type, amount, 
                           timestamp.format(DateTimeFormatter.ofPattern("dd.MM.yyyy HH:mm")));
    }
}

// Использование инкапсулированного класса
public class EncapsulationExample {
    public static void main(String[] args) {
        BankAccount account = new BankAccount("123456789", "Иван Иванов", 1000.0);
        
        // ✅ Правильный способ работы с объектом
        System.out.println("Начальное состояние: " + account);
        
        account.deposit(500);
        account.withdraw(200);
        
        // ❌ Это не сработает - поля приватные
        // account.balance = 1000000; // Ошибка компиляции
        // account.transactionHistory.clear(); // Ошибка компиляции
        
        // ✅ Безопасный доступ к данным
        System.out.println("Баланс: " + account.getBalance());
        System.out.println("История операций:");
        account.getTransactionHistory().forEach(System.out::println);
    }
}
```

### Паттерны инкапсуляции
```java
// Immutable класс (неизменяемый)
public final class Person {
    private final String name;
    private final int age;
    private final List<String> hobbies;
    
    public Person(String name, int age, List<String> hobbies) {
        this.name = name;
        this.age = age;
        // Создаем копию списка для безопасности
        this.hobbies = hobbies != null ? new ArrayList<>(hobbies) : new ArrayList<>();
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    
    // Возвращаем неизменяемую копию
    public List<String> getHobbies() {
        return Collections.unmodifiableList(hobbies);
    }
    
    @Override
    public String toString() {
        return String.format("Person{name='%s', age=%d, hobbies=%s}", name, age, hobbies);
    }
}

// Builder паттерн для сложных объектов
public class Computer {
    private final String processor;
    private final int ramGB;
    private final int storageGB;
    private final String graphics;
    private final boolean hasWiFi;
    private final boolean hasBluetooth;
    
    // Приватный конструктор
    private Computer(Builder builder) {
        this.processor = builder.processor;
        this.ramGB = builder.ramGB;
        this.storageGB = builder.storageGB;
        this.graphics = builder.graphics;
        this.hasWiFi = builder.hasWiFi;
        this.hasBluetooth = builder.hasBluetooth;
    }
    
    // Геттеры
    public String getProcessor() { return processor; }
    public int getRamGB() { return ramGB; }
    public int getStorageGB() { return storageGB; }
    public String getGraphics() { return graphics; }
    public boolean hasWiFi() { return hasWiFi; }
    public boolean hasBluetooth() { return hasBluetooth; }
    
    // Статический внутренний класс Builder
    public static class Builder {
        private String processor;
        private int ramGB;
        private int storageGB;
        private String graphics = "Integrated";
        private boolean hasWiFi = true;
        private boolean hasBluetooth = false;
        
        public Builder(String processor, int ramGB, int storageGB) {
            this.processor = processor;
            this.ramGB = ramGB;
            this.storageGB = storageGB;
        }
        
        public Builder graphics(String graphics) {
            this.graphics = graphics;
            return this;
        }
        
        public Builder withWiFi(boolean hasWiFi) {
            this.hasWiFi = hasWiFi;
            return this;
        }
        
        public Builder withBluetooth(boolean hasBluetooth) {
            this.hasBluetooth = hasBluetooth;
            return this;
        }
        
        public Computer build() {
            return new Computer(this);
        }
    }
    
    @Override
    public String toString() {
        return String.format("Computer{processor='%s', ram=%dGB, storage=%dGB, graphics='%s', WiFi=%s, Bluetooth=%s}",
                           processor, ramGB, storageGB, graphics, hasWiFi, hasBluetooth);
    }
}

// Использование Builder
public class BuilderExample {
    public static void main(String[] args) {
        Computer gaming = new Computer.Builder("Intel i9", 32, 1000)
                .graphics("RTX 4080")
                .withWiFi(true)
                .withBluetooth(true)
                .build();
        
        Computer office = new Computer.Builder("Intel i5", 8, 256)
                .withWiFi(true)
                .build();
        
        System.out.println("Игровой ПК: " + gaming);
        System.out.println("Офисный ПК: " + office);
    }
}
```

---

## 🧬 Наследование

> [!tip] Наследование позволяет создавать новые классы на основе существующих, наследуя их свойства и методы

### Базовое наследование
```java
// Родительский класс (суперкласс)
public class Vehicle {
    protected String brand;
    protected String model;
    protected int year;
    protected boolean isRunning;
    
    public Vehicle(String brand, String model, int year) {
        this.brand = brand;
        this.model = model;
        this.year = year;
        this.isRunning = false;
    }
    
    public void start() {
        if (!isRunning) {
            isRunning = true;
            System.out.println(brand + " " + model + " запущен");
        }
    }
    
    public void stop() {
        if (isRunning) {
            isRunning = false;
            System.out.println(brand + " " + model + " остановлен");
        }
    }
    
    public void displayInfo() {
        System.out.println("Транспорт: " + brand + " " + model + " (" + year + ")");
    }
    
    // Геттеры
    public String getBrand() { return brand; }
    public String getModel() { return model; }
    public int getYear() { return year; }
    public boolean isRunning() { return isRunning; }
}

// Дочерний класс (подкласс)
public class Car extends Vehicle {
    private int numberOfDoors;
    private String fuelType;
    private double fuelLevel;
    
    public Car(String brand, String model, int year, int numberOfDoors, String fuelType) {
        // Вызов конструктора родительского класса
        super(brand, model, year);
        this.numberOfDoors = numberOfDoors;
        this.fuelType = fuelType;
        this.fuelLevel = 50.0; // Половина бака по умолчанию
    }
    
    // Переопределение метода родительского класса
    @Override
    public void start() {
        if (fuelLevel <= 0) {
            System.out.println("Невозможно запустить " + brand + " " + model + " - нет топлива!");
            return;
        }
        
        super.start(); // Вызов метода родительского класса
        System.out.println("Двигатель работает на " + fuelType);
    }
    
    // Дополнительные методы для Car
    public void refuel(double amount) {
        if (amount > 0) {
            fuelLevel = Math.min(100, fuelLevel + amount);
            System.out.println("Заправлено. Уровень топлива: " + fuelLevel + "%");
        }
    }
    
    public void drive(double distance) {
        if (!isRunning) {
            System.out.println("Сначала запустите автомобиль!");
            return;
        }
        
        double fuelConsumption = distance * 0.1; // 0.1% топлива на км
        if (fuelLevel < fuelConsumption) {
            System.out.println("Недостаточно топлива для поездки на " + distance + " км");
            return;
        }
        
        fuelLevel -= fuelConsumption;
        System.out.println("Проехали " + distance + " км. Топлива осталось: " + fuelLevel + "%");
    }
    
    @Override
    public void displayInfo() {
        super.displayInfo(); // Вызов метода родителя
        System.out.println("Дверей: " + numberOfDoors + ", Топливо: " + fuelType + " (" + fuelLevel + "%)");
    }
    
    // Геттеры для новых полей
    public int getNumberOfDoors() { return numberOfDoors; }
    public String getFuelType() { return fuelType; }
    public double getFuelLevel() { return fuelLevel; }
}

// Еще один дочерний класс
public class Motorcycle extends Vehicle {
    private boolean hasSidecar;
    private int engineCapacity;
    
    public Motorcycle(String brand, String model, int year, int engineCapacity, boolean hasSidecar) {
        super(brand, model, year);
        this.engineCapacity = engineCapacity;
        this.hasSidecar = hasSidecar;
    }
    
    @Override
    public void start() {
        super.start();
        System.out.println("Двигатель " + engineCapacity + "cc заведен");
        if (hasSidecar) {
            System.out.println("Внимание: у мотоцикла есть коляска!");
        }
    }
    
    public void wheelie() {
        if (isRunning && !hasSidecar) {
            System.out.println("Выполняем трюк - езда на заднем колесе!");
        } else if (hasSidecar) {
            System.out.println("Нельзя сделать wheelie с коляской!");
        } else {
            System.out.println("Сначала запустите мотоцикл!");
        }
    }
    
    @Override
    public void displayInfo() {
        super.displayInfo();
        System.out.println("Объем двигателя: " + engineCapacity + "cc, Коляска: " + 
                          (hasSidecar ? "есть" : "нет"));
    }
    
    public int getEngineCapacity() { return engineCapacity; }
    public boolean hasSidecar() { return hasSidecar; }
}

// Использование наследования
public class InheritanceExample {
    public static void main(String[] args) {
        // Создаем объекты разных типов
        Vehicle vehicle = new Vehicle("Generic", "Transport", 2020);
        Car car = new Car("Toyota", "Camry", 2022, 4, "Бензин");
        Motorcycle motorcycle = new Motorcycle("Yamaha", "R1", 2023, 1000, false);
        
        System.out.println("=== Информация о транспорте ===");
        vehicle.displayInfo();
        car.displayInfo();
        motorcycle.displayInfo();
        
        System.out.println("\n=== Запуск транспорта ===");
        vehicle.start();
        car.start();
        motorcycle.start();
        
        System.out.println("\n=== Специальные методы ===");
        car.refuel(30);
        car.drive(100);
        motorcycle.wheelie();
        
        // Полиморфизм - одинаковые методы, разное поведение
        System.out.println("\n=== Полиморфизм ===");
        Vehicle[] vehicles = {vehicle, car, motorcycle};
        
        for (Vehicle v : vehicles) {
            v.start(); // Каждый тип запускается по-своему
        }
    }
}
```

### Множественное наследование интерфейсов
```java
// Интерфейсы для множественного наследования
interface Flyable {
    void fly();
    
    default void takeOff() {
        System.out.println("Взлет!");
    }
    
    default void land() {
        System.out.println("Посадка!");
    }
}

interface Swimmable {
    void swim();
    
    default void dive() {
        System.out.println("Ныряем!");
    }
    
    default void surfaceUp() {
        System.out.println("Всплываем!");
    }
}

interface Walkable {
    void walk();
    
    default void run() {
        System.out.println("Бежим!");
    }
}

// Класс может реализовывать несколько интерфейсов
public class Duck implements Flyable, Swimmable, Walkable {
    private String name;
    
    public Duck(String name) {
        this.name = name;
    }
    
    @Override
    public void fly() {
        System.out.println(name + " летит по небу");
    }
    
    @Override
    public void swim() {
        System.out.println(name + " плывет по воде");
    }
    
    @Override
    public void walk() {
        System.out.println(name + " ходит по земле");
    }
    
    // Можем переопределить default методы
    @Override
    public void takeOff() {
        System.out.println(name + " разбегается и взлетает");
    }
    
    public void quack() {
        System.out.println(name + " крякает: Кря-кря!");
    }
}

// Другой пример множественного наследования
public class Fish implements Swimmable {
    private String species;
    
    public Fish(String species) {
        this.species = species;
    }
    
    @Override
    public void swim() {
        System.out.println(species + " плавает в воде");
    }
    
    @Override
    public void dive() {
        System.out.println(species + " ныряет на глубину");
    }
}

public class Bird implements Flyable {
    private String species;
    
    public Bird(String species) {
        this.species = species;
    }
    
    @Override
    public void fly() {
        System.out.println(species + " парит в небе");
    }
    
    public void sing() {
        System.out.println(species + " поет красивую песню");
    }
}

// Использование множественного наследования
public class MultipleInheritanceExample {
    public static void main(String[] args) {
        Duck duck = new Duck("Кряква");
        Fish fish = new Fish("Лосось");
        Bird bird = new Bird("Соловей");
        
        System.out.println("=== Утка - универсальное существо ===");
        duck.walk();
        duck.run();
        duck.swim();
        duck.dive();
        duck.takeOff();
        duck.fly();
        duck.land();
        duck.quack();
        
        System.out.println("\n=== Рыба ===");
        fish.swim();
        fish.dive();
        fish.surfaceUp();
        
        System.out.println("\n=== Птица ===");
        bird.fly();
        bird.takeOff();
        bird.land();
        bird.sing();
        
        // Полиморфизм с интерфейсами
        System.out.println("\n=== Полиморфизм с интерфейсами ===");
        Flyable[] flyingCreatures = {duck, bird};
        for (Flyable creature : flyingCreatures) {
            creature.fly();
        }
        
        Swimmable[] swimmingCreatures = {duck, fish};
        for (Swimmable creature : swimmingCreatures) {
            creature.swim();
        }
    }
}
```

---

## 🎭 Полиморфизм

> [!tip] Полиморфизм позволяет объектам разных типов реагировать на одинаковые сообщения по-разному

### Runtime полиморфизм (переопределение методов)
```java
// Базовый класс
public abstract class Shape {
    protected String color;
    
    public Shape(String color) {
        this.color = color;
    }
    
    // Абстрактные методы - должны быть реализованы в дочерних классах
    public abstract double getArea();
    public abstract double getPerimeter();
    public abstract void draw();
    
    // Конкретный метод, который могут переопределить дочерние классы
    public void displayInfo() {
        System.out.printf("Фигура: %s, Цвет: %s, Площадь: %.2f, Периметр: %.2f%n",
                         getClass().getSimpleName(), color, getArea(), getPerimeter());
    }
    
    // Геттер
    public String getColor() { return color; }
}

// Дочерний класс - Круг
public class Circle extends Shape {
    private double radius;
    
    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }
    
    @Override
    public double getArea() {
        return Math.PI * radius * radius;
    }
    
    @Override
    public double getPerimeter() {
        return 2 * Math.PI * radius;
    }
    
    @Override
    public void draw() {
        System.out.println("Рисуем круг радиусом " + radius + " цвета " + color);
    }
    
    public double getRadius() { return radius; }
}

// Дочерний класс - Прямоугольник
public class Rectangle extends Shape {
    private double width;
    private double height;
    
    public Rectangle(String color, double width, double height) {
        super(color);
        this.width = width;
        this.height = height;
    }
    
    @Override
    public double getArea() {
        return width * height;
    }
    
    @Override
    public double getPerimeter() {
        return 2 * (width + height);
    }
    
    @Override
    public void draw() {
        System.out.println("Рисуем прямоугольник " + width + "x" + height + " цвета " + color);
    }
    
    public double getWidth() { return width; }
    public double getHeight() { return height; }
}

// Дочерний класс - Треугольник
public class Triangle extends Shape {
    private double sideA, sideB, sideC;
    
    public Triangle(String color, double sideA, double sideB, double sideC) {
        super(color);
        this.sideA = sideA;
        this.sideB = sideB;
        this.sideC = sideC;
    }
    
    @Override
    public double getArea() {
        // Формула Герона
        double s = (sideA + sideB + sideC) / 2;
        return Math.sqrt(s * (s - sideA) * (s - sideB) * (s - sideC));
    }
    
    @Override
    public double getPerimeter() {
        return sideA + sideB + sideC;
    }
    
    @Override
    public void draw() {
        System.out.printf("Рисуем треугольник со сторонами %.1f, %.1f, %.1f цвета %s%n",
                         sideA, sideB, sideC, color);
    }
    
    public double getSideA() { return sideA; }
    public double getSideB() { return sideB; }
    public double getSideC() { return sideC; }
}

// Класс для работы с фигурами
public class ShapeProcessor {
    
    // Полиморфный метод - работает с любой фигурой
    public void processShape(Shape shape) {
        System.out.println("\n--- Обработка фигуры ---");
        shape.draw();           // Вызывается соответствующий метод draw()
        shape.displayInfo();   // Полиморфно вызывается getArea() и getPerimeter()
    }
    
    // Расчет общей площади массива фигур
    public double calculateTotalArea(Shape[] shapes) {
        double total = 0;
        for (Shape shape : shapes) {
            total += shape.getArea(); // Полиморфный вызов
        }
        return total;
    }
    
    // Поиск самой большой фигуры по площади
    public Shape findLargestShape(Shape[] shapes) {
        if (shapes.length == 0) return null;
        
        Shape largest = shapes[0];
        for (Shape shape : shapes) {
            if (shape.getArea() > largest.getArea()) {
                largest = shape;
            }
        }
        return largest;
    }
    
    // Рисование всех фигур
    public void drawAllShapes(Shape[] shapes) {
        System.out.println("Рисуем все фигуры:");
        for (Shape shape : shapes) {
            shape.draw(); // Каждая фигура рисуется по-своему
        }
    }
}

// Демонстрация полиморфизма
public class PolymorphismExample {
    public static void main(String[] args) {
        // Создаем массив разных фигур
        Shape[] shapes = {
            new Circle("красный", 5.0),
            new Rectangle("синий", 4.0, 6.0),
            new Triangle("зеленый", 3.0, 4.0, 5.0),
            new Circle("желтый", 3.0),
            new Rectangle("фиолетовый", 5.0, 5.0)
        };
        
        ShapeProcessor processor = new ShapeProcessor();
        
        // Полиморфная обработка каждой фигуры
        for (Shape shape : shapes) {
            processor.processShape(shape);
        }
        
        System.out.println("\n=== Массовые операции ===");
        
        // Расчет общей площади
        double totalArea = processor.calculateTotalArea(shapes);
        System.out.printf("Общая площадь всех фигур: %.2f%n", totalArea);
        
        // Поиск самой большой фигуры
        Shape largest = processor.findLargestShape(shapes);
        System.out.println("Самая большая фигура:");
        largest.displayInfo();
        
        // Рисование всех фигур
        System.out.println();
        processor.drawAllShapes(shapes);
        
        // Демонстрация instanceof и приведения типов
        System.out.println("\n=== Работа с конкретными типами ===");
        for (Shape shape : shapes) {
            if (shape instanceof Circle) {
                Circle circle = (Circle) shape;
                System.out.println("Круг с радиусом: " + circle.getRadius());
            } else if (shape instanceof Rectangle) {
                Rectangle rectangle = (Rectangle) shape;
                if (rectangle.getWidth() == rectangle.getHeight()) {
                    System.out.println("Найден квадрат со стороной: " + rectangle.getWidth());
                } else {
                    System.out.println("Прямоугольник " + rectangle.getWidth() + "x" + rectangle.getHeight());
                }
            } else if (shape instanceof Triangle) {
                Triangle triangle = (Triangle) shape;
                System.out.printf("Треугольник со сторонами: %.1f, %.1f, %.1f%n",
                                triangle.getSideA(), triangle.getSideB(), triangle.getSideC());
            }
        }
    }
}
```

### Полиморфизм с интерфейсами
```java
// Интерфейс для различных способов оплаты
public interface PaymentProcessor {
    boolean processPayment(double amount);
    String getPaymentMethod();
    
    // Default метод
    default void logTransaction(double amount) {
        System.out.println("Транзакция на сумму " + amount + " через " + getPaymentMethod());
    }
}

// Различные реализации
public class CreditCardProcessor implements PaymentProcessor {
    private String cardNumber;
    private String cardHolder;
    
    public CreditCardProcessor(String cardNumber, String cardHolder) {
        this.cardNumber = cardNumber;
        this.cardHolder = cardHolder;
    }
    
    @Override
    public boolean processPayment(double amount) {
        System.out.println("Обработка платежа по кредитной карте");
        System.out.println("Карта: **** **** **** " + cardNumber.substring(cardNumber.length() - 4));
        System.out.println("Владелец: " + cardHolder);
        System.out.println("Сумма: $" + amount);
        
        // Имитация обработки
        return amount > 0 && amount <= 10000; // Лимит кредитной карты
    }
    
    @Override
    public String getPaymentMethod() {
        return "Кредитная карта";
    }
}

public class PayPalProcessor implements PaymentProcessor {
    private String email;
    
    public PayPalProcessor(String email) {
        this.email = email;
    }
    
    @Override
    public boolean processPayment(double amount) {
        System.out.println("Обработка платежа через PayPal");
        System.out.println("Email: " + email);
        System.out.println("Сумма: $" + amount);
        
        // Имитация обработки
        return amount > 0 && amount <= 5000; // Лимит PayPal
    }
    
    @Override
    public String getPaymentMethod() {
        return "PayPal";
    }
}

public class CryptoProcessor implements PaymentProcessor {
    private String walletAddress;
    private String cryptocurrency;
    
    public CryptoProcessor(String walletAddress, String cryptocurrency) {
        this.walletAddress = walletAddress;
        this.cryptocurrency = cryptocurrency;
    }
    
    @Override
    public boolean processPayment(double amount) {
        System.out.println("Обработка платежа в криптовалюте");
        System.out.println("Кошелек: " + walletAddress.substring(0, 8) + "...");
        System.out.println("Валюта: " + cryptocurrency);
        System.out.println("Сумма: " + amount + " USD эквивалент");
        
        // Криптовалюта имеет меньшие лимиты из-за волатильности
        return amount > 0 && amount <= 1000;
    }
    
    @Override
    public String getPaymentMethod() {
        return "Криптовалюта (" + cryptocurrency + ")";
    }
    
    @Override
    public void logTransaction(double amount) {
        // Переопределяем для добавления дополнительной информации
        System.out.println("Блокчейн транзакция на сумму " + amount + " USD в " + cryptocurrency);
        System.out.println("Комиссия сети будет рассчитана отдельно");
    }
}

// Класс магазина, использующий полиморфизм
public class OnlineStore {
    private List<PaymentProcessor> availableProcessors;
    
    public OnlineStore() {
        availableProcessors = new ArrayList<>();
    }
    
    public void addPaymentMethod(PaymentProcessor processor) {
        availableProcessors.add(processor);
        System.out.println("Добавлен способ оплаты: " + processor.getPaymentMethod());
    }
    
    // Полиморфная обработка платежа
    public boolean processOrder(double amount, PaymentProcessor processor) {
        System.out.println("\n=== Обработка заказа ===");
        System.out.println("Сумма заказа: $" + amount);
        
        boolean success = processor.processPayment(amount);
        
        if (success) {
            processor.logTransaction(amount);
            System.out.println("✅ Платеж успешно обработан!");
        } else {
            System.out.println("❌ Ошибка обработки платежа");
        }
        
        return success;
    }
    
    // Попытка оплаты через разные процессоры
    public boolean tryProcessOrder(double amount) {
        System.out.println("\n=== Попытка оплаты разными способами ===");
        
        for (PaymentProcessor processor : availableProcessors) {
            System.out.println("Пробуем: " + processor.getPaymentMethod());
            if (processor.processPayment(amount)) {
                processor.logTransaction(amount);
                System.out.println("✅ Успешно оплачено через " + processor.getPaymentMethod());
                return true;
            }
        }
        
        System.out.println("❌ Не удалось обработать платеж ни одним способом");
        return false;
    }
    
    public void displayAvailablePaymentMethods() {
        System.out.println("Доступные способы оплаты:");
        for (PaymentProcessor processor : availableProcessors) {
            System.out.println("- " + processor.getPaymentMethod());
        }
    }
}

// Демонстрация полиморфизма с интерфейсами
public class InterfacePolymorphismExample {
    public static void main(String[] args) {
        OnlineStore store = new OnlineStore();
        
        // Добавляем различные способы оплаты
        store.addPaymentMethod(new CreditCardProcessor("1234567890123456", "John Doe"));
        store.addPaymentMethod(new PayPalProcessor("john.doe@email.com"));
        store.addPaymentMethod(new CryptoProcessor("1A2B3C4D5E6F7G8H9I0J", "Bitcoin"));
        
        store.displayAvailablePaymentMethods();
        
        // Тестируем различные суммы
        double[] testAmounts = {100, 1500, 7500, 15000};
        
        for (double amount : testAmounts) {
            System.out.println("\n" + "=".repeat(50));
            System.out.println("Тестируем сумму: $" + amount);
            store.tryProcessOrder(amount);
        }
        
        // Прямое использование конкретных процессоров
        System.out.println("\n" + "=".repeat(50));
        System.out.println("Прямое использование процессоров:");
        
        PaymentProcessor[] processors = {
            new CreditCardProcessor("9876543210987654", "Jane Smith"),
            new PayPalProcessor("jane.smith@email.com"),
            new CryptoProcessor("9Z8Y7X6W5V4U3T2S1R0Q", "Ethereum")
        };
        
        for (PaymentProcessor processor : processors) {
            store.processOrder(250, processor);
        }
    }
}
```

---

## 🎨 Абстракция

> [!tip] Абстракция позволяет скрывать сложные детали реализации и предоставлять простой интерфейс для взаимодействия

### Абстрактные классы
```java
// Абстрактный базовый класс
public abstract class DatabaseConnection {
    protected String url;
    protected String username;
    protected String password;
    protected boolean isConnected;
    
    public DatabaseConnection(String url, String username, String password) {
        this.url = url;
        this.username = username;
        this.password = password;
        this.isConnected = false;
    }
    
    // Абстрактные методы - должны быть реализованы в дочерних классах
    public abstract void connect();
    public abstract void disconnect();
    public abstract ResultSet executeQuery(String query);
    public abstract int executeUpdate(String query);
    
    // Конкретные методы, общие для всех БД
    public boolean isConnected() {
        return isConnected;
    }
    
    public String getUrl() {
        return url;
    }
    
    // Template Method паттерн
    public final void performTransaction(List<String> queries) {
        connect();
        beginTransaction();
        
        try {
            for (String query : queries) {
                executeUpdate(query);
            }
            commitTransaction();
            System.out.println("Транзакция успешно завершена");
        } catch (Exception e) {
            rollbackTransaction();
            System.err.println("Ошибка транзакции: " + e.getMessage());
        } finally {
            disconnect();
        }
    }
    
    // Хуки для транзакций (могут быть переопределены)
    protected void beginTransaction() {
        System.out.println("Начало транзакции");
    }
    
    protected void commitTransaction() {
        System.out.println("Подтверждение транзакции");
    }
    
    protected void rollbackTransaction() {
        System.out.println("Отмена транзакции");
    }
}

// Конкретная реализация для MySQL
public class MySQLConnection extends DatabaseConnection {
    
    public MySQLConnection(String url, String username, String password) {
        super(url, username, password);
    }
    
    @Override
    public void connect() {
        System.out.println("Подключение к MySQL: " + url);
        // Имитация подключения
        try {
            Thread.sleep(100);
            isConnected = true;
            System.out.println("✅ Успешно подключен к MySQL");
        } catch (InterruptedException e) {
            System.err.println("❌ Ошибка подключения к MySQL");
        }
    }
    
    @Override
    public void disconnect() {
        if (isConnected) {
            System.out.println("Отключение от MySQL");
            isConnected = false;
            System.out.println("✅ Отключен от MySQL");
        }
    }
    
    @Override
    public ResultSet executeQuery(String query) {
        if (!isConnected) {
            throw new RuntimeException("Не подключен к базе данных");
        }
        
        System.out.println("MySQL Query: " + query);
        // Имитация выполнения запроса
        return new MockResultSet("MySQL результат для: " + query);
    }
    
    @Override
    public int executeUpdate(String query) {
        if (!isConnected) {
            throw new RuntimeException("Не подключен к базе данных");
        }
        
        System.out.println("MySQL Update: " + query);
        return 1; // Имитация успешного обновления
    }
    
    @Override
    protected void beginTransaction() {
        super.beginTransaction();
        System.out.println("START TRANSACTION; (MySQL)");
    }
    
    @Override
    protected void commitTransaction() {
        super.commitTransaction();
        System.out.println("COMMIT; (MySQL)");
    }
    
    @Override
    protected void rollbackTransaction() {
        super.rollbackTransaction();
        System.out.println("ROLLBACK; (MySQL)");
    }
}

// Конкретная реализация для PostgreSQL
public class PostgreSQLConnection extends DatabaseConnection {
    
    public PostgreSQLConnection(String url, String username, String password) {
        super(url, username, password);
    }
    
    @Override
    public void connect() {
        System.out.println("Подключение к PostgreSQL: " + url);
        try {
            Thread.sleep(150);
            isConnected = true;
            System.out.println("✅ Успешно подключен к PostgreSQL");
        } catch (InterruptedException e) {
            System.err.println("❌ Ошибка подключения к PostgreSQL");
        }
    }
    
    @Override
    public void disconnect() {
        if (isConnected) {
            System.out.println("Отключение от PostgreSQL");
            isConnected = false;
            System.out.println("✅ Отключен от PostgreSQL");
        }
    }
    
    @Override
    public ResultSet executeQuery(String query) {
        if (!isConnected) {
            throw new RuntimeException("Не подключен к базе данных");
        }
        
        System.out.println("PostgreSQL Query: " + query);
        return new MockResultSet("PostgreSQL результат для: " + query);
    }
    
    @Override
    public int executeUpdate(String query) {
        if (!isConnected) {
            throw new RuntimeException("Не подключен к базе данных");
        }
        
        System.out.println("PostgreSQL Update: " + query);
        return 1;
    }
    
    @Override
    protected void beginTransaction() {
        super.beginTransaction();
        System.out.println("BEGIN; (PostgreSQL)");
    }
    
    @Override
    protected void commitTransaction() {
        super.commitTransaction();
        System.out.println("COMMIT; (PostgreSQL)");
    }
    
    @Override
    protected void rollbackTransaction() {
        super.rollbackTransaction();
        System.out.println("ROLLBACK; (PostgreSQL)");
    }
}

// Заглушка для ResultSet
class MockResultSet {
    private String data;
    
    public MockResultSet(String data) {
        this.data = data;
    }
    
    public String getData() {
        return data;
    }
    
    @Override
    public String toString() {
        return "ResultSet: " + data;
    }
}

// Класс для работы с базой данных (использует абстракцию)
public class DatabaseService {
    private DatabaseConnection connection;
    
    public DatabaseService(DatabaseConnection connection) {
        this.connection = connection;
    }
    
    public void performSimpleQuery(String query) {
        System.out.println("\n--- Простой запрос ---");
        connection.connect();
        ResultSet result = connection.executeQuery(query);
        System.out.println("Результат: " + result);
        connection.disconnect();
    }
    
    public void performUpdate(String updateQuery) {
        System.out.println("\n--- Обновление ---");
        connection.connect();
        int affected = connection.executeUpdate(updateQuery);
        System.out.println("Затронуто строк: " + affected);
        connection.disconnect();
    }
    
    public void performComplexTransaction() {
        System.out.println("\n--- Сложная транзакция ---");
        List<String> queries = Arrays.asList(
            "INSERT INTO users (name, email) VALUES ('John', 'john@example.com')",
            "UPDATE accounts SET balance = balance - 100 WHERE user_id = 1",
            "UPDATE accounts SET balance = balance + 100 WHERE user_id = 2"
        );
        
        connection.performTransaction(queries);
    }
}

// Демонстрация абстракции
public class AbstractionExample {
    public static void main(String[] args) {
        System.out.println("=== Работа с разными типами БД ===");
        
        // Создаем различные подключения
        DatabaseConnection mysql = new MySQLConnection("mysql://localhost:3306/testdb", "user", "pass");
        DatabaseConnection postgres = new PostgreSQLConnection("postgresql://localhost:5432/testdb", "user", "pass");
        
        // Создаем сервисы (они не знают с какой именно БД работают)
        DatabaseService mysqlService = new DatabaseService(mysql);
        DatabaseService postgresService = new DatabaseService(postgres);
        
        System.out.println("MySQL операции:");
        mysqlService.performSimpleQuery("SELECT * FROM users");
        mysqlService.performUpdate("UPDATE users SET status = 'active'");
        mysqlService.performComplexTransaction();
        
        System.out.println("\n" + "=".repeat(50));
        
        System.out.println("PostgreSQL операции:");
        postgresService.performSimpleQuery("SELECT * FROM orders");
        postgresService.performUpdate("DELETE FROM logs WHERE created_at < NOW() - INTERVAL '30 days'");
        postgresService.performComplexTransaction();
        
        // Полиморфное использование
        System.out.println("\n" + "=".repeat(50));
        System.out.println("Полиморфное использование:");
        
        DatabaseConnection[] connections = {mysql, postgres};
        
        for (DatabaseConnection conn : connections) {
            System.out.println("\nРабота с БД: " + conn.getClass().getSimpleName());
            DatabaseService service = new DatabaseService(conn);
            service.performSimpleQuery("SELECT COUNT(*) FROM products");
        }
    }
}
```

---

## ⚖️ SOLID принципы

> [!info] SOLID - это пять принципов объектно-ориентированного дизайна, которые помогают создавать поддерживаемый и расширяемый код

### Single Responsibility Principle (SRP)
```java
// ❌ Плохо - класс имеет множество обязанностей
class BadUserManager {
    public void createUser(String name, String email) {
        // Валидация
        if (name == null || name.trim().isEmpty()) {
            throw new IllegalArgumentException("Name is required");
        }
        if (!email.contains("@")) {
            throw new IllegalArgumentException("Invalid email");
        }
        
        // Сохранение в БД
        System.out.println("INSERT INTO users...");
        
        // Отправка email
        System.out.println("Sending welcome email to: " + email);
        
        // Логирование
        System.out.println("User created: " + name);
        
        // Аудит
        System.out.println("Audit: User creation event");
    }
}

// ✅ Хорошо - каждый класс имеет одну обязанность
class User {
    private final String name;
    private final String email;
    
    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }
    
    public String getName() { return name; }
    public String getEmail() { return email; }
}

class UserValidator {
    public void validate(User user) {
        if (user.getName() == null || user.getName().trim().isEmpty()) {
            throw new IllegalArgumentException("Name is required");
        }
        if (!user.getEmail().contains("@")) {
            throw new IllegalArgumentException("Invalid email");
        }
    }
}

class UserRepository {
    public void save(User user) {
        System.out.println("INSERT INTO users (name, email) VALUES ('" + 
                          user.getName() + "', '" + user.getEmail() + "')");
    }
}

class EmailService {
    public void sendWelcomeEmail(String email) {
        System.out.println("Sending welcome email to: " + email);
    }
}

class UserLogger {
    public void logUserCreation(User user) {
        System.out.println("User created: " + user.getName());
    }
}

class AuditService {
    public void logEvent(String event) {
        System.out.println("Audit: " + event);
    }
}

// Сервис, координирующий работу (имеет одну обязанность - координация)
class GoodUserService {
    private final UserValidator validator;
    private final UserRepository repository;
    private final EmailService emailService;
    private final UserLogger logger;
    private final AuditService auditService;
    
    public GoodUserService(UserValidator validator, UserRepository repository, 
                          EmailService emailService, UserLogger logger, AuditService auditService) {
        this.validator = validator;
        this.repository = repository;
        this.emailService = emailService;
        this.logger = logger;
        this.auditService = auditService;
    }
    
    public void createUser(String name, String email) {
        User user = new User(name, email);
        
        validator.validate(user);
        repository.save(user);
        emailService.sendWelcomeEmail(user.getEmail());
        logger.logUserCreation(user);
        auditService.logEvent("User creation event");
    }
}
```

### Open/Closed Principle (OCP)
```java
// ✅ Хорошо - открыт для расширения, закрыт для модификации
abstract class DiscountCalculator {
    public abstract double calculateDiscount(double amount);
    
    public final double applyDiscount(double amount) {
        double discount = calculateDiscount(amount);
        return amount - discount;
    }
}

class RegularCustomerDiscount extends DiscountCalculator {
    @Override
    public double calculateDiscount(double amount) {
        return amount * 0.05; // 5% скидка
    }
}

class VIPCustomerDiscount extends DiscountCalculator {
    @Override
    public double calculateDiscount(double amount) {
        return amount * 0.15; // 15% скидка
    }
}

class SeasonalDiscount extends DiscountCalculator {
    private final double seasonalRate;
    
    public SeasonalDiscount(double seasonalRate) {
        this.seasonalRate = seasonalRate;
    }
    
    @Override
    public double calculateDiscount(double amount) {
        return amount * seasonalRate;
    }
}

// Новый тип скидки - код не нужно менять, только добавлять
class StudentDiscount extends DiscountCalculator {
    @Override
    public double calculateDiscount(double amount) {
        return Math.min(amount * 0.20, 100); // 20% но не больше 100
    }
}

class PriceCalculator {
    public double calculateFinalPrice(double amount, DiscountCalculator discount) {
        return discount.applyDiscount(amount);
    }
}
```

### Liskov Substitution Principle (LSP)
```java
// ✅ Хорошо - подклассы можно заменять базовым классом без нарушения функциональности
abstract class Bird {
    protected String species;
    
    public Bird(String species) {
        this.species = species;
    }
    
    public abstract void eat();
    public abstract void sleep();
    
    public String getSpecies() { return species; }
}

abstract class FlyingBird extends Bird {
    public FlyingBird(String species) {
        super(species);
    }
    
    public abstract void fly();
}

class Sparrow extends FlyingBird {
    public Sparrow() {
        super("Воробей");
    }
    
    @Override
    public void eat() {
        System.out.println("Воробей клюет зерна");
    }
    
    @Override
    public void sleep() {
        System.out.println("Воробей спит в гнезде");
    }
    
    @Override
    public void fly() {
        System.out.println("Воробей летит");
    }
}

class Eagle extends FlyingBird {
    public Eagle() {
        super("Орел");
    }
    
    @Override
    public void eat() {
        System.out.println("Орел охотится на добычу");
    }
    
    @Override
    public void sleep() {
        System.out.println("Орел отдыхает на скале");
    }
    
    @Override
    public void fly() {
        System.out.println("Орел парит в небе");
    }
}

class Penguin extends Bird {
    public Penguin() {
        super("Пингвин");
    }
    
    @Override
    public void eat() {
        System.out.println("Пингвин ест рыбу");
    }
    
    @Override
    public void sleep() {
        System.out.println("Пингвин спит стоя");
    }
    
    public void swim() {
        System.out.println("Пингвин плывет");
    }
}

// Корректное использование - можем заменять без нарушения логики
class BirdCareTaker {
    public void feedBird(Bird bird) {
        System.out.println("Кормим " + bird.getSpecies());
        bird.eat();
    }
    
    public void makeBirdRest(Bird bird) {
        System.out.println(bird.getSpecies() + " отдыхает");
        bird.sleep();
    }
    
    public void makeFlyingBirdFly(FlyingBird bird) {
        System.out.println(bird.getSpecies() + " взлетает");
        bird.fly();
    }
}
```

### Interface Segregation Principle (ISP)
```java
// ❌ Плохо - слишком большой интерфейс
interface BadWorker {
    void work();
    void eat();
    void sleep();
    void attendMeeting();
    void writeCode();
    void designUI();
    void testSoftware();
}

// ✅ Хорошо - разделенные интерфейсы
interface Worker {
    void work();
}

interface Eater {
    void eat();
}

interface Sleeper {
    void sleep();
}

interface MeetingAttendee {
    void attendMeeting();
}

interface Programmer {
    void writeCode();
}

interface Designer {
    void designUI();
}

interface Tester {
    void testSoftware();
}

// Классы реализуют только нужные им интерфейсы
class Developer implements Worker, Eater, Sleeper, MeetingAttendee, Programmer {
    private String name;
    
    public Developer(String name) {
        this.name = name;
    }
    
    @Override
    public void work() {
        System.out.println(name + " разрабатывает код");
    }
    
    @Override
    public void eat() {
        System.out.println(name + " обедает");
    }
    
    @Override
    public void sleep() {
        System.out.println(name + " спит");
    }
    
    @Override
    public void attendMeeting() {
        System.out.println(name + " участвует в совещании");
    }
    
    @Override
    public void writeCode() {
        System.out.println(name + " пишет код");
    }
}

class UIDesigner implements Worker, Eater, Sleeper, Designer {
    private String name;
    
    public UIDesigner(String name) {
        this.name = name;
    }
    
    @Override
    public void work() {
        System.out.println(name + " работает над дизайном");
    }
    
    @Override
    public void eat() {
        System.out.println(name + " обедает");
    }
    
    @Override
    public void sleep() {
        System.out.println(name + " спит");
    }
    
    @Override
    public void designUI() {
        System.out.println(name + " создает UI дизайн");
    }
}

class QATester implements Worker, Eater, Sleeper, MeetingAttendee, Tester {
    private String name;
    
    public QATester(String name) {
        this.name = name;
    }
    
    @Override
    public void work() {
        System.out.println(name + " тестирует продукт");
    }
    
    @Override
    public void eat() {
        System.out.println(name + " обедает");
    }
    
    @Override
    public void sleep() {
        System.out.println(name + " спит");
    }
    
    @Override
    public void attendMeeting() {
        System.out.println(name + " участвует в совещании");
    }
    
    @Override
    public void testSoftware() {
        System.out.println(name + " выполняет тестирование");
    }
}
```

### Dependency Inversion Principle (DIP)
```java
// ✅ Хорошо - зависимость от абстракций, а не от конкретных классов

// Абстракции
interface MessageSender {
    void sendMessage(String recipient, String message);
}

interface UserRepository {
    void save(User user);
    User findByEmail(String email);
}

interface Logger {
    void log(String message);
}

// Конкретные реализации
class EmailSender implements MessageSender {
    @Override
    public void sendMessage(String recipient, String message) {
        System.out.println("Email отправлен на " + recipient + ": " + message);
    }
}

class SMSSender implements MessageSender {
    @Override
    public void sendMessage(String recipient, String message) {
        System.out.println("SMS отправлен на " + recipient + ": " + message);
    }
}

class DatabaseUserRepository implements UserRepository {
    @Override
    public void save(User user) {
        System.out.println("Пользователь сохранен в БД: " + user.getName());
    }
    
    @Override
    public User findByEmail(String email) {
        System.out.println("Поиск пользователя в БД по email: " + email);
        return new User("Found User", email);
    }
}

class FileUserRepository implements UserRepository {
    @Override
    public void save(User user) {
        System.out.println("Пользователь сохранен в файл: " + user.getName());
    }
    
    @Override
    public User findByEmail(String email) {
        System.out.println("Поиск пользователя в файле по email: " + email);
        return new User("Found User", email);
    }
}

class ConsoleLogger implements Logger {
    @Override
    public void log(String message) {
        System.out.println("LOG: " + message);
    }
}

class FileLogger implements Logger {
    @Override
    public void log(String message) {
        System.out.println("FILE LOG: " + message);
    }
}

// Высокоуровневый модуль зависит от абстракций
class UserManagementService {
    private final UserRepository userRepository;
    private final MessageSender messageSender;
    private final Logger logger;
    
    // Dependency Injection через конструктор
    public UserManagementService(UserRepository userRepository, 
                               MessageSender messageSender, 
                               Logger logger) {
        this.userRepository = userRepository;
        this.messageSender = messageSender;
        this.logger = logger;
    }
    
    public void registerUser(String name, String email) {
        logger.log("Начинаем регистрацию пользователя: " + name);
        
        User user = new User(name, email);
        userRepository.save(user);
        messageSender.sendMessage(email, "Добро пожаловать, " + name + "!");
        
        logger.log("Пользователь успешно зарегистрирован: " + name);
    }
    
    public void resetPassword(String email) {
        logger.log("Сброс пароля для: " + email);
        
        User user = userRepository.findByEmail(email);
        String newPassword = "temp123";
        messageSender.sendMessage(email, "Ваш новый пароль: " + newPassword);
        
        logger.log("Пароль сброшен для: " + email);
    }
}

// Демонстрация SOLID принципов
public class SOLIDExample {
    public static void main(String[] args) {
        System.out.println("=== SOLID Principles Demo ===");
        
        // Можем легко менять реализации благодаря DIP
        UserManagementService emailService = new UserManagementService(
            new DatabaseUserRepository(),
            new EmailSender(),
            new ConsoleLogger()
        );
        
        UserManagementService smsService = new UserManagementService(
            new FileUserRepository(),
            new SMSSender(),
            new FileLogger()
        );
        
        System.out.println("Email сервис:");
        emailService.registerUser("Алексей", "alexey@example.com");
        emailService.resetPassword("alexey@example.com");
        
        System.out.println("\nSMS сервис:");
        smsService.registerUser("Мария", "maria@example.com");
        smsService.resetPassword("maria@example.com");
        
        // SRP Demo
        System.out.println("\n=== SRP Demo ===");
        UserValidator validator = new UserValidator();
        UserRepository repository = new DatabaseUserRepository();
        EmailService emailServiceSRP = new EmailService();
        UserLogger userLogger = new UserLogger();
        AuditService auditService = new AuditService();
        
        GoodUserService userService = new GoodUserService(
            validator, repository, emailServiceSRP, userLogger, auditService
        );
        
        userService.createUser("Иван", "ivan@example.com");
        
        // OCP Demo
        System.out.println("\n=== OCP Demo ===");
        PriceCalculator calculator = new PriceCalculator();
        double amount = 1000.0;
        
        DiscountCalculator[] discounts = {
            new RegularCustomerDiscount(),
            new VIPCustomerDiscount(),
            new StudentDiscount(),
            new SeasonalDiscount(0.10)
        };
        
        for (DiscountCalculator discount : discounts) {
            double finalPrice = calculator.calculateFinalPrice(amount, discount);
            System.out.printf("%s: %.2f -> %.2f%n", 
                            discount.getClass().getSimpleName(), amount, finalPrice);
        }
        
        // LSP Demo
        System.out.println("\n=== LSP Demo ===");
        BirdCareTaker careTaker = new BirdCareTaker();
        
        Bird[] birds = {
            new Sparrow(),
            new Eagle(),
            new Penguin()
        };
        
        for (Bird bird : birds) {
            careTaker.feedBird(bird);
            careTaker.makeBirdRest(bird);
            
            if (bird instanceof FlyingBird) {
                careTaker.makeFlyingBirdFly((FlyingBird) bird);
            }
        }
        
        // ISP Demo
        System.out.println("\n=== ISP Demo ===");
        Developer dev = new Developer("Сергей");
        UIDesigner designer = new UIDesigner("Анна");
        QATester tester = new QATester("Ольга");
        
        Worker[] workers = {dev, designer, tester};
        for (Worker worker : workers) {
            worker.work();
        }
        
        MeetingAttendee[] attendees = {dev, tester}; // Дизайнер не участвует в совещаниях
        for (MeetingAttendee attendee : attendees) {
            attendee.attendMeeting();
        }
    }
}
```

---

## 🔗 Связанные темы
- [[Java-Basics|Основы Java]] - базовые концепции языка
- [[Interfaces-Code|Интерфейсы]] - контракты для классов
- [[Constructors-Code|Конструкторы]] - создание объектов
- [[Best-Practices-Code|Лучшие практики]] - качественный ООП код