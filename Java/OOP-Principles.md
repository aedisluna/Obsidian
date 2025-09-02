# Принципы ООП в Java
*Четыре столпа объектно-ориентированного программирования*

#java #oop #инкапсуляция #наследование #полиморфизм #абстракция

---

## 📋 Содержание
- [[#Обзор принципов ООП]]
- [[#Инкапсуляция]]
- [[#Наследование]]
- [[#Полиморфизм]]
- [[#Абстракция]]
- [[#Практические примеры]]

---

## 🎯 Обзор принципов ООП

> [!info] ООП - это парадигма программирования, основанная на концепции "объектов"

### Четыре основных принципа:

1. **🔒 Инкапсуляция** - сокрытие внутренней реализации
2. **🧬 Наследование** - переиспользование кода через родительские классы
3. **🔄 Полиморфизм** - множественные формы одного интерфейса
4. **🎭 Абстракция** - упрощение сложных систем

---

## 🔒 Инкапсуляция

> [!tip] Инкапсуляция = объединение данных и методов + сокрытие внутренней реализации

### Основные принципы
- **Приватные поля** - данные скрыты от внешнего доступа
- **Публичные методы** - контролируемый доступ к данным
- **Геттеры и сеттеры** - безопасное получение и изменение данных

### Пример: Банковский счет
```java
public class BankAccount {
    // Приватные поля - инкапсулированы
    private String accountNumber;
    private double balance;
    private String ownerName;
    
    // Конструктор
    public BankAccount(String accountNumber, String ownerName) {
        this.accountNumber = accountNumber;
        this.ownerName = ownerName;
        this.balance = 0.0;
    }
    
    // Публичные методы для контролируемого доступа
    public double getBalance() {
        return balance;
    }
    
    public String getAccountNumber() {
        return accountNumber;
    }
    
    public String getOwnerName() {
        return ownerName;
    }
    
    // Методы с бизнес-логикой
    public boolean deposit(double amount) {
        if (amount > 0) {
            balance += amount;
            System.out.println("Внесено: " + amount + " ₽");
            return true;
        }
        System.out.println("Сумма должна быть положительной!");
        return false;
    }
    
    public boolean withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
            System.out.println("Снято: " + amount + " ₽");
            return true;
        }
        System.out.println("Недостаточно средств или неверная сумма!");
        return false;
    }
    
    // Нет сеттера для баланса - он изменяется только через методы
    // deposit() и withdraw()
}
```

### Применение инкапсуляции
```java
public class BankDemo {
    public static void main(String[] args) {
        BankAccount account = new BankAccount("123456789", "Иван Петров");
        
        // ✅ Правильно - через публичные методы
        account.deposit(1000);
        account.withdraw(300);
        System.out.println("Баланс: " + account.getBalance());
        
        // ❌ Неправильно - прямой доступ невозможен
        // account.balance = 1000000; // Ошибка компиляции!
    }
}
```

### Преимущества инкапсуляции
- **Контроль доступа** - можем проверять корректность данных
- **Безопасность** - важные данные защищены от случайного изменения
- **Гибкость** - можем изменить внутреннюю реализацию, не затрагивая внешний код
- **Отладка** - легче найти место, где данные изменяются неправильно

---

## 🧬 Наследование

> [!tip] Наследование позволяет создавать новые классы на основе существующих

### Ключевые понятия
- **Суперкласс (родитель)** - базовый класс
- **Подкласс (наследник)** - класс, который наследует функциональность
- **extends** - ключевое слово для наследования
- **super** - ссылка на родительский класс

### Пример: Иерархия транспорта
```java
// Базовый класс (суперкласс)
public class Vehicle {
    protected String brand;      // protected - доступно наследникам
    protected int year;
    protected double maxSpeed;
    
    public Vehicle(String brand, int year, double maxSpeed) {
        this.brand = brand;
        this.year = year;
        this.maxSpeed = maxSpeed;
    }
    
    public void start() {
        System.out.println(brand + " заводится...");
    }
    
    public void stop() {
        System.out.println(brand + " остановился");
    }
    
    public void displayInfo() {
        System.out.printf("%s %d года, макс. скорость: %.1f км/ч%n", 
                         brand, year, maxSpeed);
    }
}

// Подкласс - наследует от Vehicle
public class Car extends Vehicle {
    private int doors;
    private String fuelType;
    
    public Car(String brand, int year, double maxSpeed, int doors, String fuelType) {
        super(brand, year, maxSpeed); // Вызов конструктора родителя
        this.doors = doors;
        this.fuelType = fuelType;
    }
    
    // Переопределение метода родителя
    @Override
    public void start() {
        System.out.println("Поворачиваю ключ зажигания...");
        super.start(); // Вызов метода родителя
    }
    
    // Новый метод, специфичный для автомобиля
    public void honk() {
        System.out.println(brand + " сигналит: БИП-БИП!");
    }
    
    @Override
    public void displayInfo() {
        super.displayInfo(); // Вызов метода родителя
        System.out.printf("Дверей: %d, топливо: %s%n", doors, fuelType);
    }
}

// Еще один подкласс
public class Motorcycle extends Vehicle {
    private boolean hasSidecar;
    
    public Motorcycle(String brand, int year, double maxSpeed, boolean hasSidecar) {
        super(brand, year, maxSpeed);
        this.hasSidecar = hasSidecar;
    }
    
    @Override
    public void start() {
        System.out.println("Нажимаю на кик-стартер...");
        super.start();
    }
    
    public void wheelie() {
        System.out.println(brand + " встает на заднее колесо!");
    }
    
    @Override
    public void displayInfo() {
        super.displayInfo();
        System.out.println("Коляска: " + (hasSidecar ? "есть" : "нет"));
    }
}
```

### Использование наследования
```java
public class VehicleDemo {
    public static void main(String[] args) {
        // Создаем объекты разных классов
        Vehicle vehicle = new Vehicle("Базовое ТС", 2020, 100);
        Car car = new Car("Toyota", 2022, 180, 4, "Бензин");
        Motorcycle bike = new Motorcycle("Harley-Davidson", 2021, 200, false);
        
        // Все объекты имеют методы базового класса
        vehicle.start();
        car.start();    // Переопределенная версия
        bike.start();   // Переопределенная версия
        
        System.out.println();
        
        // Специфичные методы подклассов
        car.honk();
        bike.wheelie();
        
        System.out.println();
        
        // Информация о транспорте
        vehicle.displayInfo();
        car.displayInfo();
        bike.displayInfo();
    }
}
```

### Правила наследования в Java
- Java поддерживает только **одиночное наследование** классов
- Класс может реализовывать множество интерфейсов
- Все классы неявно наследуются от `Object`
- Конструкторы не наследуются
- `private` члены не наследуются
- `final` классы нельзя наследовать

---

## 🔄 Полиморфизм

> [!tip] Полиморфизм - способность объектов разных типов откликаться на одни и те же методы

### Типы полиморфизма

#### 1. Полиморфизм времени компиляции (перегрузка)
```java
public class Calculator {
    // Перегрузка методов - разные параметры
    public int add(int a, int b) {
        return a + b;
    }
    
    public double add(double a, double b) {
        return a + b;
    }
    
    public int add(int a, int b, int c) {
        return a + b + c;
    }
    
    public String add(String a, String b) {
        return a + b;
    }
}
```

#### 2. Полиморфизм времени выполнения (переопределение)
```java
// Базовый класс
abstract class Animal {
    protected String name;
    
    public Animal(String name) {
        this.name = name;
    }
    
    // Абстрактный метод - должен быть переопределен
    public abstract void makeSound();
    
    // Обычный метод
    public void sleep() {
        System.out.println(name + " спит...");
    }
}

// Подклассы с переопределением
class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }
    
    @Override
    public void makeSound() {
        System.out.println(name + " говорит: Гав-гав!");
    }
    
    // Дополнительный метод
    public void wagTail() {
        System.out.println(name + " виляет хвостом");
    }
}

class Cat extends Animal {
    public Cat(String name) {
        super(name);
    }
    
    @Override
    public void makeSound() {
        System.out.println(name + " говорит: Мяу!");
    }
    
    public void purr() {
        System.out.println(name + " мурлычет");
    }
}

class Cow extends Animal {
    public Cow(String name) {
        super(name);
    }
    
    @Override
    public void makeSound() {
        System.out.println(name + " говорит: Му-у-у!");
    }
}
```

### Демонстрация полиморфизма
```java
public class PolymorphismDemo {
    public static void main(String[] args) {
        // Полиморфная коллекция
        Animal[] animals = {
            new Dog("Шарик"),
            new Cat("Мурка"),
            new Cow("Буренка"),
            new Dog("Бобик")
        };
        
        System.out.println("=== Демонстрация полиморфизма ===");
        
        // Один и тот же код работает с разными типами объектов
        for (Animal animal : animals) {
            animal.makeSound(); // Вызывается нужная версия метода
            animal.sleep();     // Общий метод для всех
            System.out.println();
        }
        
        // Использование instanceof для проверки типа
        System.out.println("=== Специфичные действия ===");
        for (Animal animal : animals) {
            if (animal instanceof Dog) {
                Dog dog = (Dog) animal; // Приведение типа
                dog.wagTail();
            } else if (animal instanceof Cat) {
                Cat cat = (Cat) animal;
                cat.purr();
            }
        }
    }
    
    // Полиморфный метод - принимает любое Animal
    public static void careForAnimal(Animal animal) {
        System.out.println("Ухаживаю за " + animal.name);
        animal.makeSound();
        animal.sleep();
    }
}
```

### Преимущества полиморфизма
- **Гибкость** - один код работает с разными типами
- **Расширяемость** - легко добавлять новые типы
- **Упрощение кода** - меньше условных конструкций
- **Абстракция** - работаем с интерфейсом, а не с конкретной реализацией

---

## 🎭 Абстракция

> [!tip] Абстракция - сокрытие сложности реализации и предоставление простого интерфейса

### Способы реализации абстракции

#### 1. Абстрактные классы
```java
// Абстрактный класс - нельзя создать объект напрямую
public abstract class Shape {
    protected String color;
    protected double x, y; // Координаты
    
    public Shape(String color, double x, double y) {
        this.color = color;
        this.x = x;
        this.y = y;
    }
    
    // Абстрактные методы - обязательны для реализации
    public abstract double calculateArea();
    public abstract double calculatePerimeter();
    public abstract void draw();
    
    // Конкретный метод - общий для всех фигур
    public void move(double dx, double dy) {
        this.x += dx;
        this.y += dy;
        System.out.printf("Фигура перемещена на (%.1f, %.1f)%n", dx, dy);
    }
    
    public void displayInfo() {
        System.out.printf("Фигура: цвет=%s, позиция=(%.1f, %.1f)%n", 
                         color, x, y);
    }
}

// Конкретная реализация
class Circle extends Shape {
    private double radius;
    
    public Circle(String color, double x, double y, double radius) {
        super(color, x, y);
        this.radius = radius;
    }
    
    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
    
    @Override
    public double calculatePerimeter() {
        return 2 * Math.PI * radius;
    }
    
    @Override
    public void draw() {
        System.out.printf("Рисую %s круг с радиусом %.1f в точке (%.1f, %.1f)%n", 
                         color, radius, x, y);
    }
}

class Rectangle extends Shape {
    private double width, height;
    
    public Rectangle(String color, double x, double y, double width, double height) {
        super(color, x, y);
        this.width = width;
        this.height = height;
    }
    
    @Override
    public double calculateArea() {
        return width * height;
    }
    
    @Override
    public double calculatePerimeter() {
        return 2 * (width + height);
    }
    
    @Override
    public void draw() {
        System.out.printf("Рисую %s прямоугольник %dx%.1f в точке (%.1f, %.1f)%n", 
                         color, width, height, x, y);
    }
}
```

#### 2. Интерфейсы
```java
// Интерфейс определяет контракт
public interface Drawable {
    void draw();           // Неявно public abstract
    void setColor(String color);
    
    // Default метод (Java 8+)
    default void highlight() {
        System.out.println("Подсвечиваю объект");
    }
    
    // Статический метод (Java 8+)
    static void printDrawingTips() {
        System.out.println("Совет: используйте яркие цвета!");
    }
}

// Интерфейс для измерения
public interface Measurable {
    double getArea();
    double getPerimeter();
    
    // Default метод с логикой
    default boolean isLargerThan(Measurable other) {
        return this.getArea() > other.getArea();
    }
}

// Класс может реализовывать несколько интерфейсов
class SmartCircle extends Circle implements Drawable, Measurable {
    public SmartCircle(String color, double x, double y, double radius) {
        super(color, x, y, radius);
    }
    
    // Реализация Drawable
    @Override
    public void setColor(String color) {
        this.color = color;
        System.out.println("Цвет изменен на " + color);
    }
    
    // Реализация Measurable
    @Override
    public double getArea() {
        return calculateArea();
    }
    
    @Override
    public double getPerimeter() {
        return calculatePerimeter();
    }
}
```

### Использование абстракции
```java
public class AbstractionDemo {
    public static void main(String[] args) {
        // Работаем через абстракцию
        Shape[] shapes = {
            new Circle("красный", 0, 0, 5),
            new Rectangle("синий", 10, 10, 4, 6),
            new SmartCircle("зеленый", 5, 5, 3)
        };
        
        System.out.println("=== Работа с фигурами ===");
        for (Shape shape : shapes) {
            shape.displayInfo();
            shape.draw();
            System.out.printf("Площадь: %.2f%n", shape.calculateArea());
            System.out.printf("Периметр: %.2f%n", shape.calculatePerimeter());
            System.out.println();
        }
        
        // Работа через интерфейс
        System.out.println("=== Работа через интерфейс ===");
        SmartCircle smartCircle = new SmartCircle("желтый", 0, 0, 4);
        
        if (smartCircle instanceof Drawable) {
            Drawable drawable = smartCircle;
            drawable.highlight();
            drawable.setColor("фиолетовый");
        }
        
        if (smartCircle instanceof Measurable) {
            Measurable measurable = smartCircle;
            Circle anotherCircle = new Circle("белый", 0, 0, 2);
            System.out.println("Умный круг больше обычного? " + 
                             measurable.isLargerThan(anotherCircle));
        }
    }
}
```

---

## 🎯 Практический пример: Система управления сотрудниками

Объединим все принципы ООП в одном примере:

```java
// Абстрактный базовый класс
public abstract class Employee {
    // Инкапсуляция - приватные поля
    private String name;
    private int id;
    private String department;
    protected double baseSalary; // protected для наследников
    
    public Employee(String name, int id, String department, double baseSalary) {
        this.name = name;
        this.id = id;
        this.department = department;
        this.baseSalary = baseSalary;
    }
    
    // Инкапсуляция - геттеры
    public String getName() { return name; }
    public int getId() { return id; }
    public String getDepartment() { return department; }
    
    // Абстракция - абстрактный метод
    public abstract double calculateSalary();
    public abstract String getJobDescription();
    
    // Общий метод для всех сотрудников
    public void displayInfo() {
        System.out.printf("ID: %d, Имя: %s, Отдел: %s%n", id, name, department);
        System.out.println("Должность: " + getJobDescription());
        System.out.printf("Зарплата: %.2f ₽%n", calculateSalary());
    }
}

// Наследование и полиморфизм
class Developer extends Employee {
    private String programmingLanguage;
    private int experienceYears;
    
    public Developer(String name, int id, String department, double baseSalary, 
                    String programmingLanguage, int experienceYears) {
        super(name, id, department, baseSalary);
        this.programmingLanguage = programmingLanguage;
        this.experienceYears = experienceYears;
    }
    
    @Override
    public double calculateSalary() {
        // Бонус за опыт
        double experienceBonus = experienceYears * 5000;
        return baseSalary + experienceBonus;
    }
    
    @Override
    public String getJobDescription() {
        return "Разработчик на " + programmingLanguage;
    }
    
    public void writeCode() {
        System.out.println(getName() + " пишет код на " + programmingLanguage);
    }
}

class Manager extends Employee {
    private int teamSize;
    private double bonusPercentage;
    
    public Manager(String name, int id, String department, double baseSalary, 
                  int teamSize, double bonusPercentage) {
        super(name, id, department, baseSalary);
        this.teamSize = teamSize;
        this.bonusPercentage = bonusPercentage;
    }
    
    @Override
    public double calculateSalary() {
        // Бонус за управление командой
        double managementBonus = teamSize * 3000;
        double percentageBonus = baseSalary * (bonusPercentage / 100);
        return baseSalary + managementBonus + percentageBonus;
    }
    
    @Override
    public String getJobDescription() {
        return "Менеджер команды из " + teamSize + " человек";
    }
    
    public void conductMeeting() {
        System.out.println(getName() + " проводит совещание с командой");
    }
}

// Интерфейс для дополнительной абстракции
interface Trainable {
    void conductTraining(String topic);
    int getTrainingHours();
}

class SeniorDeveloper extends Developer implements Trainable {
    private int trainingHours;
    
    public SeniorDeveloper(String name, int id, String department, double baseSalary, 
                          String programmingLanguage, int experienceYears) {
        super(name, id, department, baseSalary, programmingLanguage, experienceYears);
        this.trainingHours = 0;
    }
    
    @Override
    public double calculateSalary() {
        // Дополнительная надбавка для senior
        return super.calculateSalary() + 20000;
    }
    
    @Override
    public String getJobDescription() {
        return "Senior " + super.getJobDescription();
    }
    
    @Override
    public void conductTraining(String topic) {
        System.out.println(getName() + " проводит тренинг по теме: " + topic);
        trainingHours += 2;
    }
    
    @Override
    public int getTrainingHours() {
        return trainingHours;
    }
}

// Демонстрация всех принципов ООП
public class OOPDemo {
    public static void main(String[] args) {
        // Полиморфизм - разные типы в одном массиве
        Employee[] employees = {
            new Developer("Анна Иванова", 101, "IT", 80000, "Java", 3),
            new Manager("Борис Петров", 102, "IT", 100000, 5, 10),
            new SeniorDeveloper("Виктор Сидоров", 103, "IT", 90000, "Python", 7)
        };
        
        System.out.println("=== Информация о сотрудниках ===");
        double totalSalary = 0;
        
        // Полиморфизм в действии
        for (Employee emp : employees) {
            emp.displayInfo(); // Вызывается нужная версия метода
            totalSalary += emp.calculateSalary();
            System.out.println();
        }
        
        System.out.printf("Общий фонд зарплат: %.2f ₽%n%n", totalSalary);
        
        // Демонстрация специфичных методов
        System.out.println("=== Специфичные действия ===");
        for (Employee emp : employees) {
            if (emp instanceof Developer) {
                ((Developer) emp).writeCode();
            }
            if (emp instanceof Manager) {
                ((Manager) emp).conductMeeting();
            }
            if (emp instanceof Trainable) {
                ((Trainable) emp).conductTraining("Новые технологии");
            }
        }
    }
}
```

---

## 💡 Лучшие практики ООП

### Инкапсуляция
- ✅ Делай поля `private` по умолчанию
- ✅ Предоставляй `public` методы для доступа
- ✅ Валидируй данные в сеттерах
- ❌ Не возвращай изменяемые объекты напрямую

### Наследование
- ✅ Используй наследование для отношения "является"
- ✅ Применяй `@Override` для переопределенных методов
- ✅ Вызывай `super()` в конструкторах наследников
- ❌ Не создавай глубокие иерархии (>3-4 уровня)

### Полиморфизм
- ✅ Программируй против интерфейсов, не реализаций
- ✅ Используй полиморфизм вместо `instanceof` где возможно
- ✅ Переопределяй `equals()`, `hashCode()`, `toString()`
- ❌ Не злоупотребляй приведением типов

### Абстракция
- ✅ Используй интерфейсы для определения контрактов
- ✅ Абстрактные классы - для общей функциональности
- ✅ Держи интерфейсы простыми и целенаправленными
- ❌ Не создавай интерфейсы с одним методом без необходимости

---

## 🔗 Связанные темы
- [[Java-Basics|Основы Java]] - базовые концепции языка
- [[Classes-and-Objects|Классы и объекты]] - создание собственных типов
- [[Interfaces|Интерфейсы]] - контракты в Java
- [[Inheritance|Наследование]] - подробности наследования