# Конструкторы в Java
*Создание и инициализация объектов*

#java #constructors #oop #initialization #объекты

---

## 📋 Содержание
- [[#Что такое конструктор]]
- [[#Типы конструкторов]]
- [[#Перегрузка конструкторов]]
- [[#Наследование и конструкторы]]
- [[#Лучшие практики]]
- [[#Практические примеры]]

---

## 🎯 Что такое конструктор

> [!info] Конструктор - это специальный метод класса, который вызывается при создании объекта и служит для его инициализации

### Особенности конструкторов:
- **Имя совпадает с именем класса**
- **Не имеет возвращаемого типа** (даже void)
- **Вызывается автоматически** при создании объекта с `new`
- **Не наследуется** подклассами
- **Может быть перегружен** (несколько конструкторов)

### Зачем нужны конструкторы?
- ✅ **Инициализация полей** - установка начальных значений
- ✅ **Валидация данных** - проверка корректности параметров
- ✅ **Настройка объекта** - выполнение начальной конфигурации
- ✅ **Обеспечение инвариантов** - гарантия корректного состояния объекта

---

## 🏗️ Типы конструкторов

### 1. Конструктор по умолчанию
```java
public class Student {
    private String name;
    private int age;
    private String university;
    
    // Конструктор по умолчанию (no-arg constructor)
    public Student() {
        // Инициализация значениями по умолчанию
        this.name = "Неизвестно";
        this.age = 18;
        this.university = "Не указан";
        
        System.out.println("Создан студент с параметрами по умолчанию");
    }
    
    // Геттеры
    public String getName() { return name; }
    public int getAge() { return age; }
    public String getUniversity() { return university; }
    
    @Override
    public String toString() {
        return String.format("Student{name='%s', age=%d, university='%s'}", 
                           name, age, university);
    }
}

// Использование
public class DefaultConstructorExample {
    public static void main(String[] args) {
        // Вызывается конструктор по умолчанию
        Student student = new Student();
        System.out.println(student);
    }
}
```

### 2. Параметризованный конструктор
```java
public class Book {
    private String title;
    private String author;
    private int pages;
    private double price;
    
    // Параметризованный конструктор
    public Book(String title, String author, int pages, double price) {
        // Валидация входных данных
        if (title == null || title.trim().isEmpty()) {
            throw new IllegalArgumentException("Название книги не может быть пустым");
        }
        if (author == null || author.trim().isEmpty()) {
            throw new IllegalArgumentException("Автор не может быть пустым");
        }
        if (pages <= 0) {
            throw new IllegalArgumentException("Количество страниц должно быть положительным");
        }
        if (price < 0) {
            throw new IllegalArgumentException("Цена не может быть отрицательной");
        }
        
        // Инициализация полей
        this.title = title.trim();
        this.author = author.trim();
        this.pages = pages;
        this.price = price;
        
        System.out.println("Создана книга: " + title);
    }
    
    // Геттеры и сеттеры
    public String getTitle() { return title; }
    public String getAuthor() { return author; }
    public int getPages() { return pages; }
    public double getPrice() { return price; }
    
    @Override
    public String toString() {
        return String.format("Book{title='%s', author='%s', pages=%d, price=%.2f}", 
                           title, author, pages, price);
    }
}

// Использование
public class ParameterizedConstructorExample {
    public static void main(String[] args) {
        try {
            Book book1 = new Book("Effective Java", "Joshua Bloch", 412, 2500.0);
            Book book2 = new Book("Clean Code", "Robert Martin", 464, 2200.0);
            
            System.out.println(book1);
            System.out.println(book2);
            
            // Попытка создать книгу с некорректными данными
            Book invalidBook = new Book("", "Author", -10, 100);
            
        } catch (IllegalArgumentException e) {
            System.err.println("Ошибка создания книги: " + e.getMessage());
        }
    }
}
```

### 3. Конструктор копирования
```java
public class Point {
    private double x;
    private double y;
    
    // Основной конструктор
    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }
    
    // Конструктор копирования
    public Point(Point other) {
        if (other == null) {
            throw new IllegalArgumentException("Копируемый объект не может быть null");
        }
        this.x = other.x;
        this.y = other.y;
        System.out.println("Создана копия точки: " + other);
    }
    
    // Конструктор по умолчанию (начало координат)
    public Point() {
        this(0.0, 0.0);
    }
    
    // Методы для работы с точкой
    public double getX() { return x; }
    public double getY() { return y; }
    
    public void setX(double x) { this.x = x; }
    public void setY(double y) { this.y = y; }
    
    public double distanceToOrigin() {
        return Math.sqrt(x * x + y * y);
    }
    
    public double distanceTo(Point other) {
        double dx = this.x - other.x;
        double dy = this.y - other.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
    
    @Override
    public String toString() {
        return String.format("Point(%.2f, %.2f)", x, y);
    }
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Point point = (Point) obj;
        return Double.compare(point.x, x) == 0 && Double.compare(point.y, y) == 0;
    }
}

// Использование
public class CopyConstructorExample {
    public static void main(String[] args) {
        Point original = new Point(3.0, 4.0);
        Point copy = new Point(original);
        Point origin = new Point();
        
        System.out.println("Оригинал: " + original);
        System.out.println("Копия: " + copy);
        System.out.println("Начало координат: " + origin);
        
        // Изменяем оригинал
        original.setX(10.0);
        
        System.out.println("После изменения оригинала:");
        System.out.println("Оригинал: " + original);
        System.out.println("Копия: " + copy); // Остается неизменной
        
        System.out.println("Расстояние от копии до начала координат: " + 
                          copy.distanceToOrigin());
    }
}
```

---

## 🔄 Перегрузка конструкторов

> [!tip] Перегрузка конструкторов позволяет создавать объекты разными способами

### Множественные конструкторы
```java
public class Person {
    private String firstName;
    private String lastName;
    private int age;
    private String email;
    private String phone;
    
    // Полный конструктор
    public Person(String firstName, String lastName, int age, String email, String phone) {
        this.firstName = validateAndTrim(firstName, "Имя");
        this.lastName = validateAndTrim(lastName, "Фамилия");
        this.age = validateAge(age);
        this.email = email != null ? email.trim() : null;
        this.phone = phone != null ? phone.trim() : null;
    }
    
    // Конструктор без контактных данных
    public Person(String firstName, String lastName, int age) {
        this(firstName, lastName, age, null, null);
    }
    
    // Конструктор только с именем и фамилией
    public Person(String firstName, String lastName) {
        this(firstName, lastName, 0, null, null);
    }
    
    // Конструктор с email
    public Person(String firstName, String lastName, String email) {
        this(firstName, lastName, 0, email, null);
    }
    
    // Конструктор из полного имени
    public Person(String fullName) {
        String[] parts = fullName.trim().split("\\s+", 2);
        if (parts.length < 2) {
            throw new IllegalArgumentException("Полное имя должно содержать имя и фамилию");
        }
        
        this.firstName = parts[0];
        this.lastName = parts[1];
        this.age = 0;
        this.email = null;
        this.phone = null;
    }
    
    // Приватные методы валидации
    private String validateAndTrim(String value, String fieldName) {
        if (value == null || value.trim().isEmpty()) {
            throw new IllegalArgumentException(fieldName + " не может быть пустым");
        }
        return value.trim();
    }
    
    private int validateAge(int age) {
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("Возраст должен быть от 0 до 150 лет");
        }
        return age;
    }
    
    // Геттеры и сеттеры
    public String getFirstName() { return firstName; }
    public String getLastName() { return lastName; }
    public int getAge() { return age; }
    public String getEmail() { return email; }
    public String getPhone() { return phone; }
    
    public String getFullName() {
        return firstName + " " + lastName;
    }
    
    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("Person{");
        sb.append("name='").append(getFullName()).append("'");
        if (age > 0) sb.append(", age=").append(age);
        if (email != null) sb.append(", email='").append(email).append("'");
        if (phone != null) sb.append(", phone='").append(phone).append("'");
        sb.append("}");
        return sb.toString();
    }
}

// Использование
public class ConstructorOverloadingExample {
    public static void main(String[] args) {
        // Различные способы создания объектов Person
        Person person1 = new Person("Иван", "Петров", 30, "ivan@example.com", "123-456-7890");
        Person person2 = new Person("Анна", "Сидорова", 25);
        Person person3 = new Person("Михаил", "Иванов");
        Person person4 = new Person("Елена", "Козлова", "elena@example.com");
        Person person5 = new Person("Сергей Николаевич Федоров");
        
        System.out.println("Созданные люди:");
        System.out.println("1. " + person1);
        System.out.println("2. " + person2);
        System.out.println("3. " + person3);
        System.out.println("4. " + person4);
        System.out.println("5. " + person5);
        
        // Попытка создать с некорректными данными
        try {
            Person invalidPerson = new Person("", "Петров");
        } catch (IllegalArgumentException e) {
            System.err.println("Ошибка: " + e.getMessage());
        }
    }
}
```

### Цепочка конструкторов (Constructor Chaining)
```java
public class BankAccount {
    private String accountNumber;
    private String ownerName;
    private double balance;
    private String accountType;
    private boolean isActive;
    
    // Самый полный конструктор
    public BankAccount(String accountNumber, String ownerName, double balance, 
                      String accountType, boolean isActive) {
        // Валидация данных
        if (accountNumber == null || accountNumber.trim().isEmpty()) {
            throw new IllegalArgumentException("Номер счета не может быть пустым");
        }
        if (ownerName == null || ownerName.trim().isEmpty()) {
            throw new IllegalArgumentException("Имя владельца не может быть пустым");
        }
        if (balance < 0) {
            throw new IllegalArgumentException("Баланс не может быть отрицательным");
        }
        
        this.accountNumber = accountNumber.trim();
        this.ownerName = ownerName.trim();
        this.balance = balance;
        this.accountType = accountType != null ? accountType : "ОБЫЧНЫЙ";
        this.isActive = isActive;
        
        System.out.println("Создан банковский счет: " + accountNumber);
    }
    
    // Конструктор с типом счета по умолчанию
    public BankAccount(String accountNumber, String ownerName, double balance, boolean isActive) {
        this(accountNumber, ownerName, balance, "ОБЫЧНЫЙ", isActive);
    }
    
    // Конструктор с активным счетом по умолчанию
    public BankAccount(String accountNumber, String ownerName, double balance) {
        this(accountNumber, ownerName, balance, "ОБЫЧНЫЙ", true);
    }
    
    // Конструктор с нулевым балансом
    public BankAccount(String accountNumber, String ownerName) {
        this(accountNumber, ownerName, 0.0);
    }
    
    // Методы для работы со счетом
    public boolean deposit(double amount) {
        if (!isActive) {
            throw new IllegalStateException("Счет неактивен");
        }
        if (amount <= 0) {
            return false;
        }
        
        balance += amount;
        return true;
    }
    
    public boolean withdraw(double amount) {
        if (!isActive) {
            throw new IllegalStateException("Счет неактивен");
        }
        if (amount <= 0 || amount > balance) {
            return false;
        }
        
        balance -= amount;
        return true;
    }
    
    // Геттеры
    public String getAccountNumber() { return accountNumber; }
    public String getOwnerName() { return ownerName; }
    public double getBalance() { return balance; }
    public String getAccountType() { return accountType; }
    public boolean isActive() { return isActive; }
    
    public void setActive(boolean active) { this.isActive = active; }
    
    @Override
    public String toString() {
        return String.format("BankAccount{number='%s', owner='%s', balance=%.2f, type='%s', active=%b}",
                           accountNumber, ownerName, balance, accountType, isActive);
    }
}

// Использование
public class ConstructorChainingExample {
    public static void main(String[] args) {
        // Разные способы создания банковских счетов
        BankAccount account1 = new BankAccount("001", "Иван Петров", 10000.0, "ПРЕМИУМ", true);
        BankAccount account2 = new BankAccount("002", "Анна Сидорова", 5000.0, true);
        BankAccount account3 = new BankAccount("003", "Петр Козлов", 1000.0);
        BankAccount account4 = new BankAccount("004", "Мария Федорова");
        
        System.out.println("Созданные счета:");
        System.out.println(account1);
        System.out.println(account2);
        System.out.println(account3);
        System.out.println(account4);
        
        // Операции со счетами
        account3.deposit(500.0);
        account4.deposit(2000.0);
        account4.withdraw(200.0);
        
        System.out.println("\nПосле операций:");
        System.out.println(account3);
        System.out.println(account4);
    }
}
```

---

## 🧬 Наследование и конструкторы

> [!warning] Конструкторы не наследуются! Каждый класс должен определить свои конструкторы

### Вызов конструктора суперкласса
```java
// Базовый класс
class Vehicle {
    protected String brand;
    protected int year;
    protected double maxSpeed;
    
    // Конструктор базового класса
    public Vehicle(String brand, int year, double maxSpeed) {
        if (brand == null || brand.trim().isEmpty()) {
            throw new IllegalArgumentException("Марка не может быть пустой");
        }
        if (year < 1885) { // Первый автомобиль
            throw new IllegalArgumentException("Год должен быть не ранее 1885");
        }
        if (maxSpeed <= 0) {
            throw new IllegalArgumentException("Максимальная скорость должна быть положительной");
        }
        
        this.brand = brand.trim();
        this.year = year;
        this.maxSpeed = maxSpeed;
        
        System.out.println("Создано транспортное средство: " + brand);
    }
    
    // Конструктор по умолчанию
    public Vehicle() {
        this("Неизвестно", 2000, 100.0);
    }
    
    public void start() {
        System.out.println(brand + " заводится...");
    }
    
    // Геттеры
    public String getBrand() { return brand; }
    public int getYear() { return year; }
    public double getMaxSpeed() { return maxSpeed; }
    
    @Override
    public String toString() {
        return String.format("%s{brand='%s', year=%d, maxSpeed=%.1f}", 
                           getClass().getSimpleName(), brand, year, maxSpeed);
    }
}

// Производный класс
class Car extends Vehicle {
    private int doors;
    private String fuelType;
    
    // Конструктор с явным вызовом super()
    public Car(String brand, int year, double maxSpeed, int doors, String fuelType) {
        super(brand, year, maxSpeed); // Вызов конструктора родителя
        
        if (doors < 2 || doors > 5) {
            throw new IllegalArgumentException("Количество дверей должно быть от 2 до 5");
        }
        if (fuelType == null || fuelType.trim().isEmpty()) {
            throw new IllegalArgumentException("Тип топлива не может быть пустым");
        }
        
        this.doors = doors;
        this.fuelType = fuelType.trim();
        
        System.out.println("Создан автомобиль с " + doors + " дверьми");
    }
    
    // Конструктор с параметрами по умолчанию
    public Car(String brand, int year, double maxSpeed) {
        this(brand, year, maxSpeed, 4, "Бензин");
    }
    
    // Конструктор по умолчанию
    public Car() {
        super(); // Можно опустить - вызывается автоматически
        this.doors = 4;
        this.fuelType = "Бензин";
    }
    
    @Override
    public void start() {
        System.out.println("Поворачиваю ключ зажигания в " + brand);
        super.start(); // Вызов метода родителя
    }
    
    public void honk() {
        System.out.println(brand + " сигналит: БИП-БИП!");
    }
    
    // Геттеры
    public int getDoors() { return doors; }
    public String getFuelType() { return fuelType; }
    
    @Override
    public String toString() {
        return String.format("Car{brand='%s', year=%d, maxSpeed=%.1f, doors=%d, fuelType='%s'}", 
                           brand, year, maxSpeed, doors, fuelType);
    }
}

// Еще один производный класс
class ElectricCar extends Car {
    private double batteryCapacity; // кВт⋅ч
    private int chargingTime; // минуты
    
    public ElectricCar(String brand, int year, double maxSpeed, int doors, 
                      double batteryCapacity, int chargingTime) {
        super(brand, year, maxSpeed, doors, "Электричество");
        
        if (batteryCapacity <= 0) {
            throw new IllegalArgumentException("Емкость батареи должна быть положительной");
        }
        if (chargingTime <= 0) {
            throw new IllegalArgumentException("Время зарядки должно быть положительным");
        }
        
        this.batteryCapacity = batteryCapacity;
        this.chargingTime = chargingTime;
        
        System.out.println("Создан электромобиль с батареей " + batteryCapacity + " кВт⋅ч");
    }
    
    public ElectricCar(String brand, int year, double maxSpeed) {
        this(brand, year, maxSpeed, 4, 75.0, 45);
    }
    
    @Override
    public void start() {
        System.out.println("Нажимаю кнопку запуска в " + brand);
        System.out.println(brand + " бесшумно запускается...");
    }
    
    public void charge() {
        System.out.println("Заряжаю " + brand + " (" + batteryCapacity + " кВт⋅ч за " + chargingTime + " мин)");
    }
    
    // Геттеры
    public double getBatteryCapacity() { return batteryCapacity; }
    public int getChargingTime() { return chargingTime; }
    
    @Override
    public String toString() {
        return String.format("ElectricCar{brand='%s', year=%d, maxSpeed=%.1f, doors=%d, battery=%.1f kWh, charging=%d min}", 
                           brand, year, maxSpeed, getDoors(), batteryCapacity, chargingTime);
    }
}

// Использование
public class InheritanceConstructorExample {
    public static void main(String[] args) {
        System.out.println("=== Создание транспортных средств ===");
        
        Vehicle vehicle = new Vehicle("Базовое ТС", 2020, 120.0);
        Car car = new Car("Toyota", 2022, 180.0, 4, "Гибрид");
        ElectricCar tesla = new ElectricCar("Tesla", 2023, 250.0, 4, 100.0, 30);
        
        System.out.println("\n=== Информация о транспорте ===");
        System.out.println(vehicle);
        System.out.println(car);
        System.out.println(tesla);
        
        System.out.println("\n=== Запуск транспорта ===");
        vehicle.start();
        car.start();
        car.honk();
        tesla.start();
        tesla.charge();
        
        // Создание с параметрами по умолчанию
        System.out.println("\n=== Объекты по умолчанию ===");
        Car defaultCar = new Car();
        ElectricCar defaultElectric = new ElectricCar("Nissan", 2021, 160.0);
        
        System.out.println(defaultCar);
        System.out.println(defaultElectric);
    }
}
```

### Порядок выполнения конструкторов
```java
class A {
    public A() {
        System.out.println("1. Конструктор класса A");
    }
    
    public A(String message) {
        System.out.println("1. Конструктор A с параметром: " + message);
    }
}

class B extends A {
    public B() {
        super(); // Неявный вызов
        System.out.println("2. Конструктор класса B");
    }
    
    public B(String message) {
        super(message); // Явный вызов с параметром
        System.out.println("2. Конструктор B с параметром: " + message);
    }
}

class C extends B {
    public C() {
        System.out.println("3. Конструктор класса C");
    }
    
    public C(String message) {
        super(message);
        System.out.println("3. Конструктор C с параметром: " + message);
    }
}

public class ConstructorOrderExample {
    public static void main(String[] args) {
        System.out.println("Создание объекта C():");
        C obj1 = new C();
        
        System.out.println("\nСоздание объекта C(String):");
        C obj2 = new C("test");
    }
}
```

---

## 💡 Лучшие практики

### 1. Валидация параметров
```java
public class User {
    private String username;
    private String email;
    private int age;
    
    public User(String username, String email, int age) {
        // ✅ Валидируй параметры в конструкторе
        this.username = validateUsername(username);
        this.email = validateEmail(email);
        this.age = validateAge(age);
    }
    
    private String validateUsername(String username) {
        if (username == null || username.trim().isEmpty()) {
            throw new IllegalArgumentException("Имя пользователя не может быть пустым");
        }
        if (username.length() < 3 || username.length() > 20) {
            throw new IllegalArgumentException("Имя пользователя должно быть от 3 до 20 символов");
        }
        if (!username.matches("^[a-zA-Z0-9_]+$")) {
            throw new IllegalArgumentException("Имя пользователя может содержать только буквы, цифры и подчеркивания");
        }
        return username.trim();
    }
    
    private String validateEmail(String email) {
        if (email == null || email.trim().isEmpty()) {
            throw new IllegalArgumentException("Email не может быть пустым");
        }
        if (!email.matches("^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$")) {
            throw new IllegalArgumentException("Неверный формат email");
        }
        return email.trim().toLowerCase();
    }
    
    private int validateAge(int age) {
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("Возраст должен быть от 0 до 150 лет");
        }
        return age;
    }
    
    // Геттеры...
}
```

### 2. Использование Builder Pattern для множества параметров
```java
public class DatabaseConnection {
    private final String host;
    private final int port;
    private final String database;
    private final String username;
    private final String password;
    private final int connectionTimeout;
    private final int queryTimeout;
    private final boolean useSSL;
    private final String encoding;
    
    // Приватный конструктор - используется только Builder'ом
    private DatabaseConnection(Builder builder) {
        this.host = builder.host;
        this.port = builder.port;
        this.database = builder.database;
        this.username = builder.username;
        this.password = builder.password;
        this.connectionTimeout = builder.connectionTimeout;
        this.queryTimeout = builder.queryTimeout;
        this.useSSL = builder.useSSL;
        this.encoding = builder.encoding;
    }
    
    // Builder class
    public static class Builder {
        // Обязательные параметры
        private final String host;
        private final String database;
        private final String username;
        
        // Опциональные параметры с значениями по умолчанию
        private int port = 5432;
        private String password = "";
        private int connectionTimeout = 30000;
        private int queryTimeout = 60000;
        private boolean useSSL = false;
        private String encoding = "UTF-8";
        
        public Builder(String host, String database, String username) {
            this.host = host;
            this.database = database;
            this.username = username;
        }
        
        public Builder port(int port) {
            this.port = port;
            return this;
        }
        
        public Builder password(String password) {
            this.password = password;
            return this;
        }
        
        public Builder connectionTimeout(int timeout) {
            this.connectionTimeout = timeout;
            return this;
        }
        
        public Builder queryTimeout(int timeout) {
            this.queryTimeout = timeout;
            return this;
        }
        
        public Builder useSSL(boolean useSSL) {
            this.useSSL = useSSL;
            return this;
        }
        
        public Builder encoding(String encoding) {
            this.encoding = encoding;
            return this;
        }
        
        public DatabaseConnection build() {
            return new DatabaseConnection(this);
        }
    }
    
    // Геттеры
    public String getHost() { return host; }
    public int getPort() { return port; }
    public String getDatabase() { return database; }
    public String getUsername() { return username; }
    // password обычно не предоставляется через getter
    public int getConnectionTimeout() { return connectionTimeout; }
    public int getQueryTimeout() { return queryTimeout; }
    public boolean isUseSSL() { return useSSL; }
    public String getEncoding() { return encoding; }
    
    @Override
    public String toString() {
        return String.format("DatabaseConnection{host='%s', port=%d, database='%s', username='%s', useSSL=%b}", 
                           host, port, database, username, useSSL);
    }
}

// Использование Builder Pattern
public class BuilderPatternExample {
    public static void main(String[] args) {
        // Минимальная конфигурация
        DatabaseConnection simpleConnection = new DatabaseConnection.Builder(
            "localhost", "mydb", "user"
        ).build();
        
        // Полная конфигурация
        DatabaseConnection fullConnection = new DatabaseConnection.Builder(
            "database.example.com", "production_db", "admin"
        )
        .port(5433)
        .password("secret123")
        .connectionTimeout(45000)
        .queryTimeout(120000)
        .useSSL(true)
        .encoding("UTF-8")
        .build();
        
        System.out.println("Простое подключение: " + simpleConnection);
        System.out.println("Полное подключение: " + fullConnection);
    }
}
```

### 3. Defensive copying для изменяемых объектов
```java
import java.util.*;

public class Order {
    private final String orderId;
    private final Date orderDate;
    private final List<String> items;
    private final Map<String, Object> metadata;
    
    public Order(String orderId, Date orderDate, List<String> items, Map<String, Object> metadata) {
        if (orderId == null || orderId.trim().isEmpty()) {
            throw new IllegalArgumentException("ID заказа не может быть пустым");
        }
        if (orderDate == null) {
            throw new IllegalArgumentException("Дата заказа не может быть null");
        }
        if (items == null) {
            throw new IllegalArgumentException("Список товаров не может быть null");
        }
        
        this.orderId = orderId.trim();
        
        // ✅ Defensive copying для изменяемых объектов
        this.orderDate = new Date(orderDate.getTime()); // Копия даты
        this.items = new ArrayList<>(items); // Копия списка
        this.metadata = metadata != null ? new HashMap<>(metadata) : new HashMap<>();
    }
    
    // Геттеры с defensive copying
    public String getOrderId() {
        return orderId; // String неизменяемы
    }
    
    public Date getOrderDate() {
        return new Date(orderDate.getTime()); // Возвращаем копию
    }
    
    public List<String> getItems() {
        return new ArrayList<>(items); // Возвращаем копию
    }
    
    public Map<String, Object> getMetadata() {
        return new HashMap<>(metadata); // Возвращаем копию
    }
    
    // Методы для безопасного изменения
    public void addItem(String item) {
        if (item != null && !item.trim().isEmpty()) {
            items.add(item.trim());
        }
    }
    
    public void addMetadata(String key, Object value) {
        if (key != null && !key.trim().isEmpty()) {
            metadata.put(key.trim(), value);
        }
    }
    
    @Override
    public String toString() {
        return String.format("Order{id='%s', date=%s, items=%d, metadata=%d}", 
                           orderId, orderDate, items.size(), metadata.size());
    }
}

// Использование
public class DefensiveCopyingExample {
    public static void main(String[] args) {
        List<String> originalItems = new ArrayList<>(Arrays.asList("Товар1", "Товар2"));
        Map<String, Object> originalMetadata = new HashMap<>();
        originalMetadata.put("priority", "high");
        originalMetadata.put("customer", "VIP");
        
        Order order = new Order("ORD-001", new Date(), originalItems, originalMetadata);
        
        System.out.println("Исходный заказ: " + order);
        
        // Попытка изменить исходные коллекции
        originalItems.add("Товар3");
        originalMetadata.put("priority", "low");
        
        System.out.println("После изменения исходных коллекций: " + order);
        System.out.println("Товары в заказе: " + order.getItems());
        
        // Попытка изменить полученные коллекции
        List<String> itemsCopy = order.getItems();
        itemsCopy.add("Товар4");
        
        System.out.println("После попытки изменить копию товаров: " + order.getItems());
    }
}
```

---

## 🔗 Связанные темы
- [[OOP-Principles-Code|Принципы ООП]] - объектно-ориентированное программирование
- [[Inheritance|Наследование]] - наследование в Java
- [[Classes-and-Objects|Классы и объекты]] - создание классов
- [[Exception-Handling-Code|Обработка исключений]] - валидация в конструкторах