# Перечисления (Enum) в Java
*Типобезопасные константы и их расширенные возможности*

#java #enum #constants #перечисления #типобезопасность

---

## 📋 Содержание
- [[#Основы Enum]]
- [[#Методы и поля в Enum]]
- [[#Enum с конструкторами]]
- [[#Enum как конечный автомат]]
- [[#EnumSet и EnumMap]]
- [[#Практические примеры]]

---

## 🎯 Основы Enum

> [!info] Enum (перечисление) - это специальный класс, представляющий группу констант

### До Enum (проблематичный подход):
```java
// ❌ Проблемы с обычными константами
public class BadConstants {
    public static final int MONDAY = 1;
    public static final int TUESDAY = 2;
    public static final int WEDNESDAY = 3;
    
    public static final int JANUARY = 1;
    public static final int FEBRUARY = 2;
    public static final int MARCH = 3;
    
    // Проблемы:
    // 1. Можно передать любое int значение
    // 2. Нет проверки типов (можно передать день вместо месяца)
    // 3. Нет автодополнения и проверки IDE
    // 4. Сложно отлаживать (выводится просто число)
}
```

### С Enum (правильный подход):
```java
// ✅ Типобезопасные перечисления
public enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

public enum Month {
    JANUARY, FEBRUARY, MARCH, APRIL, MAY, JUNE,
    JULY, AUGUST, SEPTEMBER, OCTOBER, NOVEMBER, DECEMBER
}

public class BasicEnumExample {
    public static void main(String[] args) {
        Day today = Day.MONDAY;
        Month currentMonth = Month.SEPTEMBER;
        
        System.out.println("Сегодня: " + today);
        System.out.println("Месяц: " + currentMonth);
        
        // Сравнение перечислений (можно использовать == и equals)
        if (today == Day.MONDAY) {
            System.out.println("Понедельник - тяжелый день!");
        }
        
        // Нельзя передать неправильный тип
        // printDay(currentMonth); // Ошибка компиляции!
        printDay(today); // ✅ Правильно
        
        // Все значения перечисления
        System.out.println("\\nВсе дни недели:");
        for (Day day : Day.values()) {
            System.out.println("- " + day);
        }
        
        // Получение enum по имени
        Day parsedDay = Day.valueOf("FRIDAY");
        System.out.println("\\nПарсинг строки 'FRIDAY': " + parsedDay);
        
        // Порядковый номер
        System.out.println("Порядковый номер " + today + ": " + today.ordinal());
        
        // Сравнение по порядку
        if (Day.FRIDAY.ordinal() > Day.MONDAY.ordinal()) {
            System.out.println("Пятница идет после понедельника");
        }
    }
    
    public static void printDay(Day day) {
        System.out.println("Переданный день: " + day);
    }
}
```

---

## 🔧 Методы и поля в Enum

### Enum с дополнительной логикой
```java
public enum Status {
    NEW("Новый", "Объект только создан"),
    IN_PROGRESS("В обработке", "Объект обрабатывается"),
    COMPLETED("Завершен", "Обработка успешно завершена"),
    FAILED("Ошибка", "Произошла ошибка при обработке"),
    CANCELLED("Отменен", "Операция была отменена");
    
    // Поля перечисления
    private final String displayName;
    private final String description;
    
    // Конструктор (всегда private)
    Status(String displayName, String description) {
        this.displayName = displayName;
        this.description = description;
    }
    
    // Геттеры
    public String getDisplayName() {
        return displayName;
    }
    
    public String getDescription() {
        return description;
    }
    
    // Методы enum
    public boolean isFinished() {
        return this == COMPLETED || this == FAILED || this == CANCELLED;
    }
    
    public boolean canTransitionTo(Status newStatus) {
        switch (this) {
            case NEW:
                return newStatus == IN_PROGRESS || newStatus == CANCELLED;
            case IN_PROGRESS:
                return newStatus == COMPLETED || newStatus == FAILED || newStatus == CANCELLED;
            case COMPLETED:
            case FAILED:
            case CANCELLED:
                return false; // Финальные состояния
            default:
                return false;
        }
    }
    
    // Статический метод для поиска по отображаемому имени
    public static Status findByDisplayName(String displayName) {
        for (Status status : values()) {
            if (status.displayName.equals(displayName)) {
                return status;
            }
        }
        throw new IllegalArgumentException("Неизвестный статус: " + displayName);
    }
    
    @Override
    public String toString() {
        return displayName;
    }
}

// Использование enum с дополнительной логикой
public class StatusExample {
    public static void main(String[] args) {
        Status currentStatus = Status.NEW;
        System.out.println("Текущий статус: " + currentStatus);
        System.out.println("Описание: " + currentStatus.getDescription());
        System.out.println("Завершен? " + currentStatus.isFinished());
        
        // Попытка перехода к следующему статусу
        Status nextStatus = Status.IN_PROGRESS;
        if (currentStatus.canTransitionTo(nextStatus)) {
            System.out.println("Переход " + currentStatus + " -> " + nextStatus + " разрешен");
            currentStatus = nextStatus;
        } else {
            System.out.println("Переход " + currentStatus + " -> " + nextStatus + " запрещен");
        }
        
        // Поиск по отображаемому имени
        try {
            Status found = Status.findByDisplayName("В обработке");
            System.out.println("Найден статус: " + found + " (" + found.name() + ")");
        } catch (IllegalArgumentException e) {
            System.out.println("Ошибка: " + e.getMessage());
        }
        
        // Вывод всех статусов с описаниями
        System.out.println("\\nВсе доступные статусы:");
        for (Status status : Status.values()) {
            System.out.printf("%-15s - %s%n", status.getDisplayName(), status.getDescription());
        }
    }
}
```

### Enum с абстрактными методами
```java
public enum Operation {
    PLUS("+") {
        @Override
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        @Override
        public double apply(double x, double y) {
            return x - y;
        }
    },
    MULTIPLY("*") {
        @Override
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE("/") {
        @Override
        public double apply(double x, double y) {
            if (y == 0) {
                throw new ArithmeticException("Деление на ноль");
            }
            return x / y;
        }
    },
    POWER("^") {
        @Override
        public double apply(double x, double y) {
            return Math.pow(x, y);
        }
    };
    
    private final String symbol;
    
    Operation(String symbol) {
        this.symbol = symbol;
    }
    
    public String getSymbol() {
        return symbol;
    }
    
    // Абстрактный метод - должен быть реализован в каждом элементе
    public abstract double apply(double x, double y);
    
    // Статический метод для поиска по символу
    public static Operation fromSymbol(String symbol) {
        for (Operation op : values()) {
            if (op.symbol.equals(symbol)) {
                return op;
            }
        }
        throw new IllegalArgumentException("Неизвестная операция: " + symbol);
    }
    
    @Override
    public String toString() {
        return symbol;
    }
}

// Калькулятор с использованием enum
public class Calculator {
    
    public static double calculate(double x, String operatorSymbol, double y) {
        Operation operation = Operation.fromSymbol(operatorSymbol);
        return operation.apply(x, y);
    }
    
    public static void main(String[] args) {
        double a = 10.0;
        double b = 3.0;
        
        System.out.println("Калькулятор с enum операциями:");
        for (Operation op : Operation.values()) {
            try {
                double result = op.apply(a, b);
                System.out.printf("%.1f %s %.1f = %.2f%n", a, op.getSymbol(), b, result);
            } catch (ArithmeticException e) {
                System.out.printf("%.1f %s %.1f = %s%n", a, op.getSymbol(), b, e.getMessage());
            }
        }
        
        // Использование через строковый символ
        System.out.println("\\nВычисления через символы:");
        String[] expressions = {"5 + 3", "10 - 4", "6 * 7", "15 / 3", "2 ^ 8"};
        
        for (String expr : expressions) {
            String[] parts = expr.split(" ");
            if (parts.length == 3) {
                try {
                    double x = Double.parseDouble(parts[0]);
                    String operator = parts[1];
                    double y = Double.parseDouble(parts[2]);
                    
                    double result = calculate(x, operator, y);
                    System.out.printf("%s = %.2f%n", expr, result);
                } catch (Exception e) {
                    System.out.printf("%s = Ошибка: %s%n", expr, e.getMessage());
                }
            }
        }
    }
}
```

---

## 🏗️ Enum с конструкторами

### Сложный enum с несколькими конструкторами
```java
public enum Planet {
    MERCURY(3.303e+23, 2.4397e6),
    VENUS(4.869e+24, 6.0518e6),
    EARTH(5.976e+24, 6.37814e6),
    MARS(6.421e+23, 3.3972e6),
    JUPITER(1.9e+27, 7.1492e7),
    SATURN(5.688e+26, 6.0268e7),
    URANUS(8.686e+25, 2.5559e7),
    NEPTUNE(1.024e+26, 2.4746e7);
    
    private final double mass;   // в килограммах
    private final double radius; // в метрах
    
    // Конструктор enum (всегда private)
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }
    
    public double getMass() { return mass; }
    public double getRadius() { return radius; }
    
    // Универсальная гравитационная постоянная
    private static final double G = 6.67300E-11;
    
    // Вычисление силы гравитации на поверхности
    public double surfaceGravity() {
        return G * mass / (radius * radius);
    }
    
    // Вес объекта на данной планете
    public double surfaceWeight(double otherMass) {
        return otherMass * surfaceGravity();
    }
    
    // Получение информации о планете
    public String getInfo() {
        return String.format("%s: масса=%.2e кг, радиус=%.2e м, гравитация=%.2f м/с²", 
                           name(), mass, radius, surfaceGravity());
    }
    
    // Сравнение планет по размеру
    public static Planet getLargestByRadius() {
        Planet largest = MERCURY;
        for (Planet p : values()) {
            if (p.radius > largest.radius) {
                largest = p;
            }
        }
        return largest;
    }
    
    public static Planet getHeaviest() {
        Planet heaviest = MERCURY;
        for (Planet p : values()) {
            if (p.mass > heaviest.mass) {
                heaviest = p;
            }
        }
        return heaviest;
    }
}

public class PlanetExample {
    public static void main(String[] args) {
        double earthWeight = 70.0; // кг
        
        System.out.println("Вес объекта 70 кг на разных планетах:");
        for (Planet p : Planet.values()) {
            double weight = p.surfaceWeight(earthWeight);
            System.out.printf("На %s: %.1f кг (гравитация: %.1f м/с²)%n", 
                             p.name(), weight, p.surfaceGravity());
        }
        
        System.out.println("\\nИнформация о планетах:");
        for (Planet p : Planet.values()) {
            System.out.println(p.getInfo());
        }
        
        System.out.println("\\nСтатистика:");
        Planet largest = Planet.getLargestByRadius();
        Planet heaviest = Planet.getHeaviest();
        
        System.out.println("Самая большая планета по радиусу: " + largest.name());
        System.out.println("Самая тяжелая планета: " + heaviest.name());
        
        // Сортировка по гравитации
        System.out.println("\\nПланеты по убыванию гравитации:");
        Arrays.stream(Planet.values())
              .sorted((p1, p2) -> Double.compare(p2.surfaceGravity(), p1.surfaceGravity()))
              .forEach(p -> System.out.printf("%s: %.1f м/с²%n", p.name(), p.surfaceGravity()));
    }
}
```

### Enum для конфигурации
```java
public enum DatabaseType {
    MYSQL("com.mysql.cj.jdbc.Driver", "jdbc:mysql://", 3306) {
        @Override
        public String buildConnectionUrl(String host, String database) {
            return getUrlPrefix() + host + ":" + getDefaultPort() + "/" + database + 
                   "?useSSL=false&serverTimezone=UTC";
        }
        
        @Override
        public String getDialect() {
            return "MySQL";
        }
    },
    
    POSTGRESQL("org.postgresql.Driver", "jdbc:postgresql://", 5432) {
        @Override
        public String buildConnectionUrl(String host, String database) {
            return getUrlPrefix() + host + ":" + getDefaultPort() + "/" + database;
        }
        
        @Override
        public String getDialect() {
            return "PostgreSQL";
        }
    },
    
    ORACLE("oracle.jdbc.driver.OracleDriver", "jdbc:oracle:thin:@", 1521) {
        @Override
        public String buildConnectionUrl(String host, String database) {
            return getUrlPrefix() + host + ":" + getDefaultPort() + ":" + database;
        }
        
        @Override
        public String getDialect() {
            return "Oracle";
        }
    },
    
    H2("org.h2.Driver", "jdbc:h2:", 0) {
        @Override
        public String buildConnectionUrl(String host, String database) {
            // H2 может работать в embedded режиме
            if ("embedded".equals(host)) {
                return getUrlPrefix() + "mem:" + database + ";DB_CLOSE_DELAY=-1";
            }
            return getUrlPrefix() + "tcp://" + host + "/" + database;
        }
        
        @Override
        public String getDialect() {
            return "H2";
        }
    };
    
    private final String driverClassName;
    private final String urlPrefix;
    private final int defaultPort;
    
    DatabaseType(String driverClassName, String urlPrefix, int defaultPort) {
        this.driverClassName = driverClassName;
        this.urlPrefix = urlPrefix;
        this.defaultPort = defaultPort;
    }
    
    public String getDriverClassName() { return driverClassName; }
    public String getUrlPrefix() { return urlPrefix; }
    public int getDefaultPort() { return defaultPort; }
    
    // Абстрактные методы для реализации в каждом типе БД
    public abstract String buildConnectionUrl(String host, String database);
    public abstract String getDialect();
    
    // Общие методы
    public boolean isEmbedded() {
        return this == H2;
    }
    
    public boolean needsPort() {
        return defaultPort > 0;
    }
    
    // Фабричный метод для создания соединения
    public DatabaseConnection createConnection(String host, String database, String username, String password) {
        String url = buildConnectionUrl(host, database);
        return new DatabaseConnection(this, url, username, password);
    }
}

// Класс соединения с БД
class DatabaseConnection {
    private final DatabaseType type;
    private final String url;
    private final String username;
    private final String password;
    
    public DatabaseConnection(DatabaseType type, String url, String username, String password) {
        this.type = type;
        this.url = url;
        this.username = username;
        this.password = password;
    }
    
    public void connect() {
        System.out.printf("Подключение к %s БД%n", type.getDialect());
        System.out.printf("Драйвер: %s%n", type.getDriverClassName());
        System.out.printf("URL: %s%n", url);
        System.out.printf("Пользователь: %s%n", username);
        System.out.println("Соединение установлено успешно!");
    }
    
    @Override
    public String toString() {
        return String.format("DatabaseConnection{type=%s, url='%s', username='%s'}", 
                           type.name(), url, username);
    }
}

public class DatabaseConfigExample {
    public static void main(String[] args) {
        System.out.println("=== Конфигурация различных БД ===");
        
        // Создаем соединения с разными БД
        DatabaseConnection mysql = DatabaseType.MYSQL.createConnection("localhost", "myapp", "user", "pass");
        DatabaseConnection postgres = DatabaseType.POSTGRESQL.createConnection("db.example.com", "production", "admin", "secret");
        DatabaseConnection h2 = DatabaseType.H2.createConnection("embedded", "testdb", "sa", "");
        
        // Подключаемся
        mysql.connect();
        System.out.println();
        
        postgres.connect();
        System.out.println();
        
        h2.connect();
        System.out.println();
        
        // Информация о всех типах БД
        System.out.println("=== Поддерживаемые типы БД ===");
        for (DatabaseType dbType : DatabaseType.values()) {
            System.out.printf("%-12s - %s (порт: %s, встроенная: %b)%n",
                             dbType.name(),
                             dbType.getDialect(),
                             dbType.needsPort() ? String.valueOf(dbType.getDefaultPort()) : "N/A",
                             dbType.isEmbedded());
        }
    }
}
```

---

## 🤖 Enum как конечный автомат

### State Machine с использованием Enum
```java
public enum OrderState {
    DRAFT("Черновик") {
        @Override
        public OrderState nextState(OrderEvent event) {
            switch (event) {
                case SUBMIT: return PENDING;
                case CANCEL: return CANCELLED;
                default: throw new IllegalStateException("Недопустимое событие " + event + " для состояния " + this);
            }
        }
        
        @Override
        public boolean canEdit() { return true; }
        
        @Override
        public String getAvailableActions() { return "Редактировать, Отправить, Отменить"; }
    },
    
    PENDING("Ожидает подтверждения") {
        @Override
        public OrderState nextState(OrderEvent event) {
            switch (event) {
                case APPROVE: return CONFIRMED;
                case REJECT: return REJECTED;
                case CANCEL: return CANCELLED;
                default: throw new IllegalStateException("Недопустимое событие " + event + " для состояния " + this);
            }
        }
        
        @Override
        public boolean canEdit() { return false; }
        
        @Override
        public String getAvailableActions() { return "Подтвердить, Отклонить, Отменить"; }
    },
    
    CONFIRMED("Подтвержден") {
        @Override
        public OrderState nextState(OrderEvent event) {
            switch (event) {
                case SHIP: return SHIPPED;
                case CANCEL: return CANCELLED;
                default: throw new IllegalStateException("Недопустимое событие " + event + " для состояния " + this);
            }
        }
        
        @Override
        public boolean canEdit() { return false; }
        
        @Override
        public String getAvailableActions() { return "Отправить, Отменить"; }
    },
    
    SHIPPED("Отправлен") {
        @Override
        public OrderState nextState(OrderEvent event) {
            switch (event) {
                case DELIVER: return DELIVERED;
                case RETURN: return RETURNED;
                default: throw new IllegalStateException("Недопустимое событие " + event + " для состояния " + this);
            }
        }
        
        @Override
        public boolean canEdit() { return false; }
        
        @Override
        public String getAvailableActions() { return "Доставить, Вернуть"; }
    },
    
    DELIVERED("Доставлен") {
        @Override
        public OrderState nextState(OrderEvent event) {
            switch (event) {
                case RETURN: return RETURNED;
                default: throw new IllegalStateException("Недопустимое событие " + event + " для состояния " + this);
            }
        }
        
        @Override
        public boolean canEdit() { return false; }
        
        @Override
        public String getAvailableActions() { return "Вернуть"; }
    },
    
    CANCELLED("Отменен") {
        @Override
        public OrderState nextState(OrderEvent event) {
            throw new IllegalStateException("Заказ отменен, никакие действия невозможны");
        }
        
        @Override
        public boolean canEdit() { return false; }
        
        @Override
        public String getAvailableActions() { return "Нет доступных действий"; }
    },
    
    REJECTED("Отклонен") {
        @Override
        public OrderState nextState(OrderEvent event) {
            switch (event) {
                case RESUBMIT: return PENDING;
                default: throw new IllegalStateException("Недопустимое событие " + event + " для состояния " + this);
            }
        }
        
        @Override
        public boolean canEdit() { return true; }
        
        @Override
        public String getAvailableActions() { return "Редактировать, Повторно отправить"; }
    },
    
    RETURNED("Возвращен") {
        @Override
        public OrderState nextState(OrderEvent event) {
            throw new IllegalStateException("Заказ возвращен, никакие действия невозможны");
        }
        
        @Override
        public boolean canEdit() { return false; }
        
        @Override
        public String getAvailableActions() { return "Нет доступных действий"; }
    };
    
    private final String displayName;
    
    OrderState(String displayName) {
        this.displayName = displayName;
    }
    
    public String getDisplayName() { return displayName; }
    
    // Абстрактные методы для каждого состояния
    public abstract OrderState nextState(OrderEvent event);
    public abstract boolean canEdit();
    public abstract String getAvailableActions();
    
    // Общие методы
    public boolean isTerminal() {
        return this == CANCELLED || this == DELIVERED || this == RETURNED;
    }
    
    public boolean isActive() {
        return !isTerminal() && this != DRAFT;
    }
    
    @Override
    public String toString() {
        return displayName;
    }
}

// События для изменения состояния заказа
enum OrderEvent {
    SUBMIT("Отправить"),
    APPROVE("Подтвердить"),
    REJECT("Отклонить"),
    SHIP("Отправить"),
    DELIVER("Доставить"),
    CANCEL("Отменить"),
    RETURN("Вернуть"),
    RESUBMIT("Повторно отправить");
    
    private final String displayName;
    
    OrderEvent(String displayName) {
        this.displayName = displayName;
    }
    
    public String getDisplayName() { return displayName; }
    
    @Override
    public String toString() {
        return displayName;
    }
}

// Класс заказа с состоянием
class Order {
    private String id;
    private OrderState state;
    private String content;
    private List<String> history;
    
    public Order(String id, String content) {
        this.id = id;
        this.content = content;
        this.state = OrderState.DRAFT;
        this.history = new ArrayList<>();
        addToHistory("Заказ создан в состоянии: " + state.getDisplayName());
    }
    
    public void processEvent(OrderEvent event) {
        OrderState oldState = state;
        try {
            state = state.nextState(event);
            addToHistory(String.format("Событие '%s': %s -> %s", 
                       event.getDisplayName(), oldState.getDisplayName(), state.getDisplayName()));
            System.out.printf("Заказ %s: %s%n", id, getLastHistoryEntry());
        } catch (IllegalStateException e) {
            System.err.printf("Ошибка для заказа %s: %s%n", id, e.getMessage());
        }
    }
    
    public void editContent(String newContent) {
        if (state.canEdit()) {
            this.content = newContent;
            addToHistory("Содержимое заказа изменено");
            System.out.printf("Заказ %s отредактирован%n", id);
        } else {
            System.err.printf("Заказ %s нельзя редактировать в состоянии '%s'%n", 
                            id, state.getDisplayName());
        }
    }
    
    private void addToHistory(String entry) {
        history.add(LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")) + " - " + entry);
    }
    
    private String getLastHistoryEntry() {
        return history.isEmpty() ? "" : history.get(history.size() - 1);
    }
    
    // Геттеры
    public String getId() { return id; }
    public OrderState getState() { return state; }
    public String getContent() { return content; }
    public List<String> getHistory() { return new ArrayList<>(history); }
    
    public void printStatus() {
        System.out.println("=== Статус заказа " + id + " ===");
        System.out.println("Содержимое: " + content);
        System.out.println("Текущее состояние: " + state.getDisplayName());
        System.out.println("Можно редактировать: " + (state.canEdit() ? "Да" : "Нет"));
        System.out.println("Доступные действия: " + state.getAvailableActions());
        System.out.println("Терминальное состояние: " + (state.isTerminal() ? "Да" : "Нет"));
        System.out.println("\\nИстория:");
        for (String entry : history) {
            System.out.println("  " + entry);
        }
        System.out.println();
    }
}

// Демонстрация state machine
public class StateMachineExample {
    public static void main(String[] args) {
        Order order1 = new Order("ORD-001", "Ноутбук Dell XPS 13");
        Order order2 = new Order("ORD-002", "Мышь Logitech MX Master");
        
        System.out.println("=== Обработка заказа 1 (успешный путь) ===");
        order1.processEvent(OrderEvent.SUBMIT);
        order1.processEvent(OrderEvent.APPROVE);
        order1.processEvent(OrderEvent.SHIP);
        order1.processEvent(OrderEvent.DELIVER);
        order1.printStatus();
        
        System.out.println("=== Обработка заказа 2 (с отклонением и повторной отправкой) ===");
        order2.processEvent(OrderEvent.SUBMIT);
        order2.processEvent(OrderEvent.REJECT);
        order2.editContent("Мышь Logitech MX Master 3 (обновленная версия)");
        order2.processEvent(OrderEvent.RESUBMIT);
        order2.processEvent(OrderEvent.APPROVE);
        order2.processEvent(OrderEvent.CANCEL);
        order2.printStatus();
        
        System.out.println("=== Попытки недопустимых операций ===");
        // Попытка редактировать неизменяемый заказ
        order1.editContent("Другое содержимое");
        
        // Попытка применить недопустимое событие
        order1.processEvent(OrderEvent.SUBMIT);
        
        // Демонстрация всех состояний
        System.out.println("=== Все состояния заказа ===");
        for (OrderState state : OrderState.values()) {
            System.out.printf("%-20s - %-25s (редактируемый: %-5s, терминальный: %-5s)%n",
                             state.name(),
                             state.getDisplayName(),
                             state.canEdit(),
                             state.isTerminal());
        }
    }
}
```

---

## 📦 EnumSet и EnumMap

### Эффективные коллекции для Enum
```java
import java.util.*;

// Enum для прав доступа
enum Permission {
    READ("Чтение"),
    WRITE("Запись"),
    EXECUTE("Выполнение"),
    DELETE("Удаление"),
    ADMIN("Администрирование");
    
    private final String description;
    
    Permission(String description) {
        this.description = description;
    }
    
    public String getDescription() { return description; }
    
    @Override
    public String toString() {
        return description;
    }
}

// Enum для ролей пользователей
enum UserRole {
    GUEST("Гость", EnumSet.of(Permission.READ)),
    USER("Пользователь", EnumSet.of(Permission.READ, Permission.WRITE)),
    MODERATOR("Модератор", EnumSet.of(Permission.READ, Permission.WRITE, Permission.DELETE)),
    ADMIN("Администратор", EnumSet.allOf(Permission.class));
    
    private final String displayName;
    private final EnumSet<Permission> permissions;
    
    UserRole(String displayName, EnumSet<Permission> permissions) {
        this.displayName = displayName;
        this.permissions = permissions;
    }
    
    public String getDisplayName() { return displayName; }
    public EnumSet<Permission> getPermissions() { return permissions; }
    
    public boolean hasPermission(Permission permission) {
        return permissions.contains(permission);
    }
    
    @Override
    public String toString() {
        return displayName;
    }
}

class User {
    private String name;
    private UserRole role;
    
    public User(String name, UserRole role) {
        this.name = name;
        this.role = role;
    }
    
    public String getName() { return name; }
    public UserRole getRole() { return role; }
    
    public boolean canPerform(Permission permission) {
        return role.hasPermission(permission);
    }
    
    public void setRole(UserRole newRole) {
        this.role = newRole;
    }
    
    @Override
    public String toString() {
        return String.format("User{name='%s', role=%s}", name, role);
    }
}

public class EnumCollectionsExample {
    
    public static void main(String[] args) {
        demonstrateEnumSet();
        demonstrateEnumMap();
        demonstrateUserPermissions();
    }
    
    private static void demonstrateEnumSet() {
        System.out.println("=== EnumSet примеры ===");
        
        // Создание EnumSet различными способами
        EnumSet<Permission> allPermissions = EnumSet.allOf(Permission.class);
        EnumSet<Permission> noPermissions = EnumSet.noneOf(Permission.class);
        EnumSet<Permission> basicPermissions = EnumSet.of(Permission.READ, Permission.WRITE);
        EnumSet<Permission> advancedPermissions = EnumSet.range(Permission.DELETE, Permission.ADMIN);
        
        System.out.println("Все разрешения: " + allPermissions);
        System.out.println("Нет разрешений: " + noPermissions);
        System.out.println("Базовые разрешения: " + basicPermissions);
        System.out.println("Продвинутые разрешения: " + advancedPermissions);
        
        // Операции с EnumSet
        EnumSet<Permission> readWrite = EnumSet.of(Permission.READ, Permission.WRITE);
        EnumSet<Permission> writeDelete = EnumSet.of(Permission.WRITE, Permission.DELETE);
        
        // Объединение
        EnumSet<Permission> union = EnumSet.copyOf(readWrite);
        union.addAll(writeDelete);
        System.out.println("Объединение: " + union);
        
        // Пересечение
        EnumSet<Permission> intersection = EnumSet.copyOf(readWrite);
        intersection.retainAll(writeDelete);
        System.out.println("Пересечение: " + intersection);
        
        // Дополнение
        EnumSet<Permission> complement = EnumSet.complementOf(basicPermissions);
        System.out.println("Дополнение к базовым: " + complement);
        
        // Производительность EnumSet vs HashSet
        measurePerformance();
        
        System.out.println();
    }
    
    private static void measurePerformance() {
        System.out.println("\\n--- Сравнение производительности ---");
        
        // EnumSet
        long start = System.nanoTime();
        EnumSet<Permission> enumSet = EnumSet.allOf(Permission.class);
        for (int i = 0; i < 100000; i++) {
            enumSet.contains(Permission.READ);
            enumSet.add(Permission.WRITE);
            enumSet.remove(Permission.EXECUTE);
        }
        long enumSetTime = System.nanoTime() - start;
        
        // HashSet
        start = System.nanoTime();
        Set<Permission> hashSet = new HashSet<>(Arrays.asList(Permission.values()));
        for (int i = 0; i < 100000; i++) {
            hashSet.contains(Permission.READ);
            hashSet.add(Permission.WRITE);
            hashSet.remove(Permission.EXECUTE);
        }
        long hashSetTime = System.nanoTime() - start;
        
        System.out.printf("EnumSet время: %.2f мс%n", enumSetTime / 1_000_000.0);
        System.out.printf("HashSet время: %.2f мс%n", hashSetTime / 1_000_000.0);
        System.out.printf("EnumSet быстрее в %.1f раз%n", (double) hashSetTime / enumSetTime);
    }
    
    private static void demonstrateEnumMap() {
        System.out.println("=== EnumMap примеры ===");
        
        // Создание EnumMap
        EnumMap<Permission, String> permissionDescriptions = new EnumMap<>(Permission.class);
        permissionDescriptions.put(Permission.READ, "Может просматривать содержимое");
        permissionDescriptions.put(Permission.WRITE, "Может изменять содержимое");
        permissionDescriptions.put(Permission.EXECUTE, "Может запускать программы");
        permissionDescriptions.put(Permission.DELETE, "Может удалять файлы");
        permissionDescriptions.put(Permission.ADMIN, "Полный доступ к системе");
        
        System.out.println("Описания разрешений:");
        permissionDescriptions.forEach((perm, desc) -> 
            System.out.printf("  %-15s: %s%n", perm, desc));
        
        // Статистика использования разрешений
        EnumMap<Permission, Integer> usageStats = new EnumMap<>(Permission.class);
        
        // Инициализация нулями
        for (Permission p : Permission.values()) {
            usageStats.put(p, 0);
        }
        
        // Имитация использования
        Random random = new Random();
        Permission[] permissions = Permission.values();
        for (int i = 0; i < 1000; i++) {
            Permission randomPerm = permissions[random.nextInt(permissions.length)];
            usageStats.put(randomPerm, usageStats.get(randomPerm) + 1);
        }
        
        System.out.println("\\nСтатистика использования разрешений:");
        usageStats.entrySet().stream()
                  .sorted(Map.Entry.<Permission, Integer>comparingByValue().reversed())
                  .forEach(entry -> 
                      System.out.printf("  %-15s: %d раз%n", entry.getKey(), entry.getValue()));
        
        System.out.println();
    }
    
    private static void demonstrateUserPermissions() {
        System.out.println("=== Система разрешений пользователей ===");
        
        // Создание пользователей с разными ролями
        List<User> users = Arrays.asList(
            new User("Анна", UserRole.GUEST),
            new User("Борис", UserRole.USER),
            new User("Владимир", UserRole.MODERATOR),
            new User("Галина", UserRole.ADMIN)
        );
        
        // Показываем разрешения для каждой роли
        System.out.println("Разрешения по ролям:");
        for (UserRole role : UserRole.values()) {
            System.out.printf("%-15s: %s%n", role.getDisplayName(), role.getPermissions());
        }
        
        // Тестируем разрешения пользователей
        System.out.println("\\nТестирование разрешений:");
        Permission[] testPermissions = {Permission.READ, Permission.WRITE, Permission.DELETE, Permission.ADMIN};
        
        for (User user : users) {
            System.out.printf("\\n%s (%s):%n", user.getName(), user.getRole());
            for (Permission permission : testPermissions) {
                boolean canPerform = user.canPerform(permission);
                String status = canPerform ? "✓" : "✗";
                System.out.printf("  %s %s%n", status, permission);
            }
        }
        
        // Аудит безопасности: находим пользователей с опасными разрешениями
        System.out.println("\\n=== Аудит безопасности ===");
        EnumSet<Permission> dangerousPermissions = EnumSet.of(Permission.DELETE, Permission.ADMIN);
        
        System.out.println("Пользователи с опасными разрешениями:");
        for (User user : users) {
            EnumSet<Permission> userPermissions = user.getRole().getPermissions();
            EnumSet<Permission> intersection = EnumSet.copyOf(userPermissions);
            intersection.retainAll(dangerousPermissions);
            
            if (!intersection.isEmpty()) {
                System.out.printf("  %s: %s%n", user.getName(), intersection);
            }
        }
        
        // Матрица доступа
        System.out.println("\\n=== Матрица доступа ===");
        System.out.printf("%-15s", "Роль/Разрешение");
        for (Permission permission : Permission.values()) {
            System.out.printf("%-8s", permission.name().substring(0, Math.min(7, permission.name().length())));
        }
        System.out.println();
        
        for (UserRole role : UserRole.values()) {
            System.out.printf("%-15s", role.getDisplayName());
            for (Permission permission : Permission.values()) {
                String marker = role.hasPermission(permission) ? "   ✓   " : "   ✗   ";
                System.out.printf("%-8s", marker);
            }
            System.out.println();
        }
    }
}
```

---

## 💡 Лучшие практики Enum

### 1. Используй Enum вместо констант
```java
// ❌ Плохо - обычные константы
public class BadHttpStatus {
    public static final int OK = 200;
    public static final int NOT_FOUND = 404;
    public static final int SERVER_ERROR = 500;
}

// ✅ Хорошо - enum с дополнительной информацией
public enum HttpStatus {
    OK(200, "OK"),
    NOT_FOUND(404, "Not Found"),
    SERVER_ERROR(500, "Internal Server Error");
    
    private final int code;
    private final String message;
    
    HttpStatus(int code, String message) {
        this.code = code;
        this.message = message;
    }
    
    public int getCode() { return code; }
    public String getMessage() { return message; }
    
    public static HttpStatus fromCode(int code) {
        for (HttpStatus status : values()) {
            if (status.code == code) return status;
        }
        throw new IllegalArgumentException("Unknown status code: " + code);
    }
}
```

### 2. Применяй Enum для состояний и конфигурации
```java
// ✅ Enum для настроек среды
public enum Environment {
    DEVELOPMENT("dev", "localhost", 8080, true),
    TESTING("test", "test.example.com", 8080, true),
    PRODUCTION("prod", "api.example.com", 443, false);
    
    private final String shortName;
    private final String host;
    private final int port;
    private final boolean debugMode;
    
    Environment(String shortName, String host, int port, boolean debugMode) {
        this.shortName = shortName;
        this.host = host;
        this.port = port;
        this.debugMode = debugMode;
    }
    
    public String getApiUrl() {
        String protocol = (port == 443) ? "https" : "http";
        return String.format("%s://%s:%d", protocol, host, port);
    }
    
    // Геттеры...
}
```

---

## 🔗 Связанные темы
- [[OOP-Principles-Code|Принципы ООП]] - инкапсуляция в enum
- [[Collections-Overview|Collections Framework]] - EnumSet и EnumMap
- [[Best-Practices-Code|Лучшие практики]] - когда использовать enum
- [[Switch-Case-Code|Switch-Case-Code]] - работа с enum в switch