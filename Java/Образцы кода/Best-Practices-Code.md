# Лучшие практики Java
*Рекомендации по написанию качественного и maintainable кода*

#java #best-practices #code-quality #patterns #performance

---

## 📋 Содержание
- [[#Общие принципы]]
- [[#Именование]]
- [[#Классы и методы]]
- [[#Обработка исключений]]
- [[#Производительность]]
- [[#Многопоточность]]
- [[#Тестирование]]

---

## 🎯 Общие принципы

### SOLID принципы
```java
// ✅ Single Responsibility Principle - один класс = одна ответственность
public class UserValidator {
    public boolean isValidEmail(String email) {
        return email != null && email.matches("^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$");
    }
    
    public boolean isValidAge(int age) {
        return age >= 0 && age <= 150;
    }
}

public class UserRepository {
    public void save(User user) { /* сохранение */ }
    public User findById(Long id) { /* поиск */ }
}

// ❌ Нарушение SRP - слишком много ответственностей
public class BadUserService {
    public boolean validateEmail(String email) { /* валидация */ }
    public void saveUser(User user) { /* сохранение */ }
    public void sendNotification(User user) { /* отправка уведомлений */ }
    public String generateReport(List<User> users) { /* генерация отчетов */ }
}

// ✅ Open/Closed Principle - открыт для расширения, закрыт для изменения
public abstract class Shape {
    public abstract double calculateArea();
}

public class Rectangle extends Shape {
    private double width, height;
    
    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }
    
    @Override
    public double calculateArea() {
        return width * height;
    }
}

public class Circle extends Shape {
    private double radius;
    
    public Circle(double radius) {
        this.radius = radius;
    }
    
    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
}

// ✅ Liskov Substitution Principle - подклассы должны заменять базовые классы
public class Bird {
    public void eat() { /* все птицы едят */ }
    public void sleep() { /* все птицы спят */ }
}

public class FlyingBird extends Bird {
    public void fly() { /* только летающие птицы летают */ }
}

public class Penguin extends Bird {
    // Пингвин не летает, но это корректно - он не наследует fly()
    public void swim() { /* пингвины умеют плавать */ }
}

// ✅ Interface Segregation Principle - много специализированных интерфейсов
public interface Readable {
    String read();
}

public interface Writable {
    void write(String data);
}

public interface Closeable {
    void close();
}

// Клиенты реализуют только нужные интерфейсы
public class File implements Readable, Writable, Closeable {
    // реализация всех методов
}

public class ReadOnlyFile implements Readable, Closeable {
    // только чтение и закрытие
}

// ✅ Dependency Inversion Principle - зависимости от абстракций
public interface NotificationService {
    void sendNotification(String message, String recipient);
}

public class EmailService implements NotificationService {
    @Override
    public void sendNotification(String message, String recipient) {
        System.out.println("Email to " + recipient + ": " + message);
    }
}

public class OrderService {
    private final NotificationService notificationService;
    
    // Зависимость от абстракции, а не от конкретного класса
    public OrderService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }
    
    public void processOrder(Order order) {
        // обработка заказа
        notificationService.sendNotification("Order processed", order.getCustomerEmail());
    }
}
```

### DRY (Don't Repeat Yourself)
```java
// ❌ Дублирование кода
public class BadUserService {
    public void createUser(String name, String email) {
        if (name == null || name.trim().isEmpty()) {
            throw new IllegalArgumentException("Name cannot be empty");
        }
        if (email == null || !email.matches("^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$")) {
            throw new IllegalArgumentException("Invalid email");
        }
        // создание пользователя
    }
    
    public void updateUser(User user, String name, String email) {
        if (name == null || name.trim().isEmpty()) {
            throw new IllegalArgumentException("Name cannot be empty");
        }
        if (email == null || !email.matches("^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$")) {
            throw new IllegalArgumentException("Invalid email");
        }
        // обновление пользователя
    }
}

// ✅ Вынесение общей логики
public class GoodUserService {
    private void validateName(String name) {
        if (name == null || name.trim().isEmpty()) {
            throw new IllegalArgumentException("Name cannot be empty");
        }
    }
    
    private void validateEmail(String email) {
        if (email == null || !email.matches("^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$")) {
            throw new IllegalArgumentException("Invalid email");
        }
    }
    
    public void createUser(String name, String email) {
        validateName(name);
        validateEmail(email);
        // создание пользователя
    }
    
    public void updateUser(User user, String name, String email) {
        validateName(name);
        validateEmail(email);
        // обновление пользователя
    }
}
```

---

## 📝 Именование

### Правила именования
```java
// ✅ Хорошие имена - описывают назначение
public class CustomerOrderProcessor {
    private static final int MAX_RETRY_ATTEMPTS = 3;
    private static final long CONNECTION_TIMEOUT_MILLIS = 30000;
    
    private List<Order> pendingOrders;
    private DatabaseConnection databaseConnection;
    
    public void processCustomerOrders(List<Order> orders) {
        for (Order order : orders) {
            if (isValidOrder(order)) {
                processOrder(order);
            }
        }
    }
    
    private boolean isValidOrder(Order order) {
        return order != null && 
               order.getCustomerId() != null && 
               order.getAmount() > 0;
    }
    
    private void processOrder(Order order) {
        int attemptCount = 0;
        while (attemptCount < MAX_RETRY_ATTEMPTS) {
            try {
                saveOrderToDatabase(order);
                sendConfirmationEmail(order);
                break;
            } catch (DatabaseException e) {
                attemptCount++;
                if (attemptCount >= MAX_RETRY_ATTEMPTS) {
                    handleProcessingFailure(order, e);
                }
            }
        }
    }
}

// ❌ Плохие имена - неинформативные
public class COProc {
    private static final int MAX = 3;
    private List<Order> list;
    private Connection conn;
    
    public void proc(List<Order> orders) {
        for (Order o : orders) {
            if (check(o)) {
                handle(o);
            }
        }
    }
    
    private boolean check(Order o) {
        return o != null && o.getId() != null;
    }
}
```

### Соглашения по именованию
```java
// ✅ Правильные конвенции
public class NamingConventions {
    // Константы - UPPER_SNAKE_CASE
    public static final String DEFAULT_ENCODING = "UTF-8";
    public static final int MAX_CONNECTIONS = 100;
    
    // Переменные и методы - camelCase
    private String userName;
    private int connectionTimeout;
    
    public String getUserName() {
        return userName;
    }
    
    public void setConnectionTimeout(int timeoutInMillis) {
        this.connectionTimeout = timeoutInMillis;
    }
    
    // Boolean методы - начинаются с is/has/can
    public boolean isEmpty() {
        return userName == null || userName.isEmpty();
    }
    
    public boolean hasValidConnection() {
        return connectionTimeout > 0;
    }
    
    public boolean canProcess() {
        return !isEmpty() && hasValidConnection();
    }
    
    // Коллекции - множественное число
    private List<String> activeUserNames;
    private Set<Integer> processedOrderIds;
    private Map<String, UserProfile> userProfiles;
}

// Enum - PascalCase с описательными именами
public enum OrderStatus {
    PENDING_PAYMENT,
    PAYMENT_CONFIRMED,
    PROCESSING,
    SHIPPED,
    DELIVERED,
    CANCELLED,
    REFUNDED
}
```

---

## 🏗️ Классы и методы

### Размер и ответственность классов
```java
// ✅ Компактный, focused класс
public class EmailValidator {
    private static final String EMAIL_PATTERN = 
        "^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$";
    
    private final Pattern pattern;
    
    public EmailValidator() {
        this.pattern = Pattern.compile(EMAIL_PATTERN);
    }
    
    public ValidationResult validate(String email) {
        if (email == null || email.trim().isEmpty()) {
            return ValidationResult.invalid("Email cannot be empty");
        }
        
        if (!pattern.matcher(email).matches()) {
            return ValidationResult.invalid("Invalid email format");
        }
        
        return ValidationResult.valid();
    }
}

// ✅ Методы должны делать одну вещь
public class OrderProcessor {
    
    public void processOrder(Order order) {
        validateOrder(order);
        calculateTotals(order);
        applyDiscounts(order);
        reserveInventory(order);
        processPayment(order);
        createShipment(order);
        sendConfirmation(order);
    }
    
    private void validateOrder(Order order) {
        if (order == null) {
            throw new IllegalArgumentException("Order cannot be null");
        }
        if (order.getItems().isEmpty()) {
            throw new IllegalArgumentException("Order must contain items");
        }
    }
    
    private void calculateTotals(Order order) {
        BigDecimal total = order.getItems().stream()
            .map(item -> item.getPrice().multiply(BigDecimal.valueOf(item.getQuantity())))
            .reduce(BigDecimal.ZERO, BigDecimal::add);
        order.setTotal(total);
    }
    
    // остальные методы...
}

// ❌ Слишком большой метод с множественной ответственностью
public class BadOrderProcessor {
    public void processOrder(Order order) {
        // Валидация (должна быть в отдельном методе)
        if (order == null) throw new IllegalArgumentException("Order cannot be null");
        if (order.getItems().isEmpty()) throw new IllegalArgumentException("No items");
        
        // Расчеты (должны быть в отдельном методе)
        BigDecimal total = BigDecimal.ZERO;
        for (OrderItem item : order.getItems()) {
            BigDecimal itemTotal = item.getPrice().multiply(BigDecimal.valueOf(item.getQuantity()));
            total = total.add(itemTotal);
        }
        
        // Применение скидок (должно быть в отдельном методе)
        if (order.getCustomer().isPremium()) {
            total = total.multiply(BigDecimal.valueOf(0.9)); // 10% скидка
        }
        
        // Резервирование товара (должно быть в отдельном методе)
        for (OrderItem item : order.getItems()) {
            // код резервирования...
        }
        
        // И так далее - слишком много ответственностей в одном методе
    }
}
```

### Инкапсуляция и иммутабельность
```java
// ✅ Правильная инкапсуляция
public class BankAccount {
    private final String accountNumber;
    private BigDecimal balance;
    private final List<Transaction> transactions;
    
    public BankAccount(String accountNumber, BigDecimal initialBalance) {
        this.accountNumber = Objects.requireNonNull(accountNumber);
        this.balance = Objects.requireNonNull(initialBalance);
        this.transactions = new ArrayList<>();
    }
    
    public String getAccountNumber() {
        return accountNumber;
    }
    
    public BigDecimal getBalance() {
        return balance;
    }
    
    // Defensive copy для коллекций
    public List<Transaction> getTransactions() {
        return new ArrayList<>(transactions);
    }
    
    public synchronized void deposit(BigDecimal amount) {
        validateAmount(amount);
        balance = balance.add(amount);
        transactions.add(new Transaction(TransactionType.DEPOSIT, amount));
    }
    
    public synchronized boolean withdraw(BigDecimal amount) {
        validateAmount(amount);
        if (balance.compareTo(amount) >= 0) {
            balance = balance.subtract(amount);
            transactions.add(new Transaction(TransactionType.WITHDRAWAL, amount));
            return true;
        }
        return false;
    }
    
    private void validateAmount(BigDecimal amount) {
        if (amount == null || amount.compareTo(BigDecimal.ZERO) <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
    }
}

// ✅ Immutable класс
public final class Money {
    private final BigDecimal amount;
    private final Currency currency;
    
    public Money(BigDecimal amount, Currency currency) {
        this.amount = Objects.requireNonNull(amount);
        this.currency = Objects.requireNonNull(currency);
        
        if (amount.compareTo(BigDecimal.ZERO) < 0) {
            throw new IllegalArgumentException("Amount cannot be negative");
        }
    }
    
    public BigDecimal getAmount() { return amount; }
    public Currency getCurrency() { return currency; }
    
    public Money add(Money other) {
        validateSameCurrency(other);
        return new Money(amount.add(other.amount), currency);
    }
    
    public Money subtract(Money other) {
        validateSameCurrency(other);
        return new Money(amount.subtract(other.amount), currency);
    }
    
    private void validateSameCurrency(Money other) {
        if (!currency.equals(other.currency)) {
            throw new IllegalArgumentException("Cannot operate on different currencies");
        }
    }
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Money money = (Money) obj;
        return Objects.equals(amount, money.amount) && Objects.equals(currency, money.currency);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(amount, currency);
    }
    
    @Override
    public String toString() {
        return String.format("%s %s", amount, currency.getCurrencyCode());
    }
}
```

### Builder Pattern для сложных объектов
```java
// ✅ Builder для объектов с множеством параметров
public class HttpRequest {
    private final String url;
    private final String method;
    private final Map<String, String> headers;
    private final String body;
    private final int timeout;
    
    private HttpRequest(Builder builder) {
        this.url = builder.url;
        this.method = builder.method;
        this.headers = Map.copyOf(builder.headers);
        this.body = builder.body;
        this.timeout = builder.timeout;
    }
    
    // Геттеры...
    public String getUrl() { return url; }
    public String getMethod() { return method; }
    public Map<String, String> getHeaders() { return headers; }
    
    public static class Builder {
        private String url;
        private String method = "GET";
        private Map<String, String> headers = new HashMap<>();
        private String body;
        private int timeout = 30000;
        
        public Builder url(String url) {
            this.url = url;
            return this;
        }
        
        public Builder method(String method) {
            this.method = method;
            return this;
        }
        
        public Builder header(String name, String value) {
            this.headers.put(name, value);
            return this;
        }
        
        public Builder body(String body) {
            this.body = body;
            return this;
        }
        
        public Builder timeout(int timeout) {
            this.timeout = timeout;
            return this;
        }
        
        public HttpRequest build() {
            if (url == null || url.trim().isEmpty()) {
                throw new IllegalStateException("URL is required");
            }
            return new HttpRequest(this);
        }
    }
    
    // Использование:
    public static void main(String[] args) {
        HttpRequest request = new HttpRequest.Builder()
            .url("https://api.example.com/users")
            .method("POST")
            .header("Content-Type", "application/json")
            .header("Authorization", "Bearer token123")
            .body("{\"name\":\"John\", \"email\":\"john@example.com\"}")
            .timeout(5000)
            .build();
    }
}
```

---

## ⚠️ Обработка исключений

### Правильная обработка исключений
```java
// ✅ Специфичные исключения
public class UserService {
    
    public User createUser(String email, String password) throws UserCreationException {
        try {
            validateEmail(email);
            validatePassword(password);
            
            if (userRepository.existsByEmail(email)) {
                throw new UserAlreadyExistsException("User with email " + email + " already exists");
            }
            
            User user = new User(email, hashPassword(password));
            return userRepository.save(user);
            
        } catch (ValidationException e) {
            throw new UserCreationException("Validation failed: " + e.getMessage(), e);
        } catch (DatabaseException e) {
            logger.error("Database error while creating user: {}", email, e);
            throw new UserCreationException("Failed to create user due to database error", e);
        }
    }
    
    private void validateEmail(String email) throws ValidationException {
        if (email == null || !email.matches("^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$")) {
            throw new ValidationException("Invalid email format: " + email);
        }
    }
    
    private void validatePassword(String password) throws ValidationException {
        if (password == null || password.length() < 8) {
            throw new ValidationException("Password must be at least 8 characters long");
        }
        if (!password.matches(".*[A-Z].*") || !password.matches(".*[a-z].*") || !password.matches(".*\\d.*")) {
            throw new ValidationException("Password must contain uppercase, lowercase and digit");
        }
    }
}

// ✅ Иерархия исключений
public class UserCreationException extends Exception {
    public UserCreationException(String message) {
        super(message);
    }
    
    public UserCreationException(String message, Throwable cause) {
        super(message, cause);
    }
}

public class UserAlreadyExistsException extends UserCreationException {
    public UserAlreadyExistsException(String message) {
        super(message);
    }
}

public class ValidationException extends Exception {
    public ValidationException(String message) {
        super(message);
    }
}

// ✅ Try-with-resources для автоматического закрытия ресурсов
public class FileProcessor {
    
    public String processFile(String filename) throws FileProcessingException {
        try (BufferedReader reader = Files.newBufferedReader(Paths.get(filename));
             StringWriter writer = new StringWriter()) {
            
            String line;
            while ((line = reader.readLine()) != null) {
                String processed = processLine(line);
                writer.write(processed);
                writer.write(System.lineSeparator());
            }
            
            return writer.toString();
            
        } catch (IOException e) {
            throw new FileProcessingException("Failed to process file: " + filename, e);
        }
    }
    
    private String processLine(String line) {
        return line.trim().toUpperCase();
    }
}

// ❌ Плохая обработка исключений
public class BadExceptionHandling {
    
    public void badMethod() {
        try {
            // какой-то код
        } catch (Exception e) {
            // Проглатывание исключения - очень плохо!
        }
        
        try {
            // какой-то код
        } catch (Exception e) {
            e.printStackTrace(); // Логирование в консоль - плохо для production
            throw new RuntimeException("Something went wrong"); // Потеря контекста
        }
    }
    
    public String readFile(String filename) {
        FileReader reader = null;
        try {
            reader = new FileReader(filename);
            // чтение файла
            return "content";
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            // Ручное закрытие ресурсов - подвержено ошибкам
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    // еще одно место для ошибки
                }
            }
        }
    }
}
```

---

## ⚡ Производительность

### Эффективное использование коллекций
```java
public class PerformanceOptimizations {
    
    // ✅ Используй правильные типы коллекций
    public void demonstrateCollectionChoice() {
        // ArrayList для частых обращений по индексу
        List<String> frequentIndexAccess = new ArrayList<>();
        
        // LinkedList для частых вставок/удалений в середине
        List<String> frequentInsertions = new LinkedList<>();
        
        // HashSet для быстрого поиска уникальных элементов
        Set<String> uniqueElements = new HashSet<>();
        
        // TreeSet для отсортированных уникальных элементов
        Set<String> sortedUniqueElements = new TreeSet<>();
        
        // HashMap для быстрого поиска по ключу
        Map<String, User> userLookup = new HashMap<>();
        
        // LinkedHashMap для сохранения порядка вставки
        Map<String, User> orderedUserLookup = new LinkedHashMap<>();
    }
    
    // ✅ Задавай правильную начальную емкость
    public Map<String, Integer> createOptimizedMap(int expectedSize) {
        // Правильная формула: expectedSize / loadFactor + 1
        // loadFactor по умолчанию 0.75
        int initialCapacity = (int) (expectedSize / 0.75f) + 1;
        return new HashMap<>(initialCapacity);
    }
    
    // ✅ Переиспользуй объекты
    private static final DateTimeFormatter DATE_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd");
    private final StringBuilder stringBuilder = new StringBuilder();
    
    public List<String> formatDates(List<LocalDate> dates) {
        List<String> result = new ArrayList<>(dates.size());
        
        for (LocalDate date : dates) {
            // Переиспользуем formatter вместо создания нового каждый раз
            result.add(date.format(DATE_FORMATTER));
        }
        
        return result;
    }
    
    public String concatenateStrings(List<String> strings) {
        stringBuilder.setLength(0); // Очищаем, но не создаем новый
        
        for (String str : strings) {
            stringBuilder.append(str).append(" ");
        }
        
        return stringBuilder.toString().trim();
    }
    
    // ✅ Ленивые вычисления
    public class ExpensiveDataProcessor {
        private List<ProcessedData> cachedData;
        
        public List<ProcessedData> getProcessedData() {
            if (cachedData == null) {
                cachedData = computeExpensiveData();
            }
            return cachedData;
        }
        
        private List<ProcessedData> computeExpensiveData() {
            // Дорогие вычисления выполняются только при необходимости
            return new ArrayList<>();
        }
    }
    
    // ✅ Используй Stream API эффективно
    public List<String> processLargeDataset(List<DataItem> items) {
        return items.parallelStream() // parallelStream для больших объемов данных
            .filter(item -> item.isActive()) // Фильтрация на раннем этапе
            .filter(item -> item.getScore() > 0.7) // Множественные фильтры
            .map(DataItem::getName)
            .distinct()
            .sorted()
            .collect(Collectors.toList());
    }
    
    // ❌ Неэффективные практики
    public void badPerformancePractices() {
        // Создание объектов в цикле
        List<String> items = Arrays.asList("a", "b", "c");
        for (int i = 0; i < items.size(); i++) {
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd"); // Плохо!
            StringBuilder sb = new StringBuilder(); // Плохо!
            // ...
        }
        
        // Конкатенация строк в цикле
        String result = "";
        for (String item : items) {
            result += item; // Создает новую строку каждый раз!
        }
        
        // Неправильный размер коллекций
        List<String> list = new ArrayList<>(); // Размер по умолчанию 10, может потребоваться resize
        for (int i = 0; i < 1000; i++) {
            list.add("item" + i);
        }
    }
}

class DataItem {
    private boolean active;
    private double score;
    private String name;
    
    // Конструктор и методы
    public boolean isActive() { return active; }
    public double getScore() { return score; }
    public String getName() { return name; }
}

class ProcessedData {
    // Данные
}
```

### Работа с базой данных
```java
// ✅ Эффективная работа с БД
public class DatabaseOptimizations {
    private static final String BATCH_INSERT_SQL = 
        "INSERT INTO users (name, email, created_at) VALUES (?, ?, ?)";
    
    private final DataSource dataSource;
    
    // Batch операции для множественных вставок
    public void insertUsersBatch(List<User> users) throws SQLException {
        try (Connection conn = dataSource.getConnection();
             PreparedStatement stmt = conn.prepareStatement(BATCH_INSERT_SQL)) {
            
            conn.setAutoCommit(false);
            
            for (User user : users) {
                stmt.setString(1, user.getName());
                stmt.setString(2, user.getEmail());
                stmt.setTimestamp(3, Timestamp.valueOf(user.getCreatedAt()));
                stmt.addBatch();
            }
            
            stmt.executeBatch();
            conn.commit();
        }
    }
    
    // Пагинация для больших результатов
    public List<User> getUsersPaginated(int page, int pageSize) throws SQLException {
        String sql = "SELECT * FROM users ORDER BY id LIMIT ? OFFSET ?";
        
        try (Connection conn = dataSource.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            
            stmt.setInt(1, pageSize);
            stmt.setInt(2, page * pageSize);
            
            try (ResultSet rs = stmt.executeQuery()) {
                List<User> users = new ArrayList<>();
                while (rs.next()) {
                    users.add(mapResultSetToUser(rs));
                }
                return users;
            }
        }
    }
    
    // Кэширование для часто используемых данных
    private final Map<Integer, User> userCache = new ConcurrentHashMap<>();
    
    public User getUserWithCache(int id) throws SQLException {
        return userCache.computeIfAbsent(id, this::fetchUserFromDatabase);
    }
    
    private User fetchUserFromDatabase(int id) {
        try {
            String sql = "SELECT * FROM users WHERE id = ?";
            try (Connection conn = dataSource.getConnection();
                 PreparedStatement stmt = conn.prepareStatement(sql)) {
                
                stmt.setInt(1, id);
                try (ResultSet rs = stmt.executeQuery()) {
                    if (rs.next()) {
                        return mapResultSetToUser(rs);
                    }
                }
            }
        } catch (SQLException e) {
            throw new RuntimeException("Failed to fetch user", e);
        }
        return null;
    }
    
    private User mapResultSetToUser(ResultSet rs) throws SQLException {
        return new User(
            rs.getInt("id"),
            rs.getString("name"),
            rs.getString("email"),
            rs.getTimestamp("created_at").toLocalDateTime()
        );
    }
}
```

---

## 🧵 Многопоточность

### Thread-safe практики
```java
// ✅ Правильная многопоточность
public class ThreadSafePractices {
    
    // Immutable объекты - всегда thread-safe
    public static final class ImmutableCounter {
        private final int value;
        
        public ImmutableCounter(int value) {
            this.value = value;
        }
        
        public int getValue() { return value; }
        
        public ImmutableCounter increment() {
            return new ImmutableCounter(value + 1);
        }
    }
    
    // Thread-safe singleton
    public enum Singleton {
        INSTANCE;
        
        public void doSomething() {
            System.out.println("Thread-safe singleton operation");
        }
    }
    
    // Правильное использование volatile
    public class ConnectionManager {
        private volatile boolean connected = false;
        
        public void connect() {
            // Дорогая операция подключения
            establishConnection();
            connected = true; // Volatile гарантирует видимость изменений
        }
        
        public boolean isConnected() {
            return connected; // Всегда видим актуальное значение
        }
        
        private void establishConnection() {
            // Имитация подключения
        }
    }
    
    // Atomic операции для счетчиков
    public class Statistics {
        private final AtomicLong requestCount = new AtomicLong(0);
        private final AtomicLong errorCount = new AtomicLong(0);
        
        public void recordRequest() {
            requestCount.incrementAndGet();
        }
        
        public void recordError() {
            errorCount.incrementAndGet();
        }
        
        public double getErrorRate() {
            long requests = requestCount.get();
            long errors = errorCount.get();
            return requests > 0 ? (double) errors / requests : 0.0;
        }
    }
    
    // Правильное использование synchronized
    public class BankAccount {
        private BigDecimal balance;
        private final Object lock = new Object();
        
        public void withdraw(BigDecimal amount) {
            synchronized (lock) {
                if (balance.compareTo(amount) >= 0) {
                    balance = balance.subtract(amount);
                }
            }
        }
        
        public void deposit(BigDecimal amount) {
            synchronized (lock) {
                balance = balance.add(amount);
            }
        }
        
        public BigDecimal getBalance() {
            synchronized (lock) {
                return balance;
            }
        }
    }
    
    // Использование concurrent коллекций
    private final Map<String, User> userCache = new ConcurrentHashMap<>();
    private final BlockingQueue<Task> taskQueue = new LinkedBlockingQueue<>();
    
    // Правильное завершение ExecutorService
    public void processTasksWithExecutor() {
        ExecutorService executor = Executors.newFixedThreadPool(4);
        
        try {
            // Отправляем задачи
            for (int i = 0; i < 10; i++) {
                final int taskId = i;
                executor.submit(() -> {
                    System.out.println("Processing task " + taskId);
                });
            }
        } finally {
            // Правильное завершение
            executor.shutdown();
            try {
                if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                    executor.shutdownNow();
                }
            } catch (InterruptedException e) {
                executor.shutdownNow();
                Thread.currentThread().interrupt();
            }
        }
    }
}

// ❌ Распространенные ошибки
public class ThreadingMistakes {
    private boolean flag = false; // Должно быть volatile!
    private int counter = 0; // Race condition без синхронизации!
    
    // Double-checked locking без volatile
    private static ThreadingMistakes instance;
    
    public static ThreadingMistakes getInstance() {
        if (instance == null) {
            synchronized (ThreadingMistakes.class) {
                if (instance == null) {
                    instance = new ThreadingMistakes(); // Может быть проблема без volatile!
                }
            }
        }
        return instance;
    }
    
    // Неправильная синхронизация на изменяемом объекте
    private String lock = "lock";
    
    public void badSynchronization() {
        synchronized (lock) { // Плохо - строки могут интернироваться!
            // критическая секция
        }
    }
}

class Task {
    // Задача для выполнения
}

class User {
    private int id;
    private String name;
    private String email;
    private LocalDateTime createdAt;
    
    public User(int id, String name, String email, LocalDateTime createdAt) {
        this.id = id;
        this.name = name;
        this.email = email;
        this.createdAt = createdAt;
    }
    
    // Геттеры
    public int getId() { return id; }
    public String getName() { return name; }
    public String getEmail() { return email; }
    public LocalDateTime getCreatedAt() { return createdAt; }
}
```

---

## 🧪 Тестирование

### Качественные тесты
```java
// ✅ Хорошие юнит-тесты
public class UserServiceTest {
    
    @Mock
    private UserRepository userRepository;
    
    @Mock
    private EmailService emailService;
    
    @InjectMocks
    private UserService userService;
    
    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }
    
    @Test
    @DisplayName("Should create user when valid data provided")
    void shouldCreateUserWhenValidDataProvided() {
        // Given
        String email = "john@example.com";
        String name = "John Doe";
        User expectedUser = new User(1, name, email);
        
        when(userRepository.existsByEmail(email)).thenReturn(false);
        when(userRepository.save(any(User.class))).thenReturn(expectedUser);
        
        // When
        User actualUser = userService.createUser(name, email);
        
        // Then
        assertThat(actualUser)
            .isNotNull()
            .hasFieldOrPropertyWithValue("name", name)
            .hasFieldOrPropertyWithValue("email", email);
        
        verify(userRepository).existsByEmail(email);
        verify(userRepository).save(any(User.class));
        verify(emailService).sendWelcomeEmail(email);
    }
    
    @Test
    @DisplayName("Should throw exception when user already exists")
    void shouldThrowExceptionWhenUserAlreadyExists() {
        // Given
        String email = "existing@example.com";
        String name = "Existing User";
        
        when(userRepository.existsByEmail(email)).thenReturn(true);
        
        // When & Then
        assertThatThrownBy(() -> userService.createUser(name, email))
            .isInstanceOf(UserAlreadyExistsException.class)
            .hasMessageContaining("User with email existing@example.com already exists");
        
        verify(userRepository).existsByEmail(email);
        verify(userRepository, never()).save(any(User.class));
        verify(emailService, never()).sendWelcomeEmail(anyString());
    }
    
    @ParameterizedTest
    @DisplayName("Should throw exception for invalid emails")
    @ValueSource(strings = {"invalid-email", "@example.com", "user@", "user@domain"})
    void shouldThrowExceptionForInvalidEmails(String invalidEmail) {
        // When & Then
        assertThatThrownBy(() -> userService.createUser("John", invalidEmail))
            .isInstanceOf(IllegalArgumentException.class)
            .hasMessageContaining("Invalid email format");
    }
    
    @Test
    @DisplayName("Should handle repository exception gracefully")
    void shouldHandleRepositoryExceptionGracefully() {
        // Given
        String email = "john@example.com";
        String name = "John";
        
        when(userRepository.existsByEmail(email)).thenReturn(false);
        when(userRepository.save(any(User.class)))
            .thenThrow(new RuntimeException("Database connection failed"));
        
        // When & Then
        assertThatThrownBy(() -> userService.createUser(name, email))
            .isInstanceOf(UserCreationException.class)
            .hasCauseInstanceOf(RuntimeException.class);
    }
}

// ✅ Интеграционные тесты
@SpringBootTest
@TestPropertySource(locations = "classpath:application-test.properties")
@Transactional
class UserServiceIntegrationTest {
    
    @Autowired
    private UserService userService;
    
    @Autowired
    private TestEntityManager entityManager;
    
    @Test
    @DisplayName("Should create and persist user in database")
    void shouldCreateAndPersistUserInDatabase() {
        // Given
        String name = "Integration Test User";
        String email = "integration@test.com";
        
        // When
        User createdUser = userService.createUser(name, email);
        entityManager.flush();
        entityManager.clear();
        
        // Then
        User foundUser = entityManager.find(User.class, createdUser.getId());
        assertThat(foundUser)
            .isNotNull()
            .hasFieldOrPropertyWithValue("name", name)
            .hasFieldOrPropertyWithValue("email", email);
    }
}

// ✅ Test Data Builders для читаемости
public class UserTestDataBuilder {
    private String name = "Default Name";
    private String email = "default@example.com";
    private LocalDateTime createdAt = LocalDateTime.now();
    private boolean active = true;
    
    public static UserTestDataBuilder aUser() {
        return new UserTestDataBuilder();
    }
    
    public UserTestDataBuilder withName(String name) {
        this.name = name;
        return this;
    }
    
    public UserTestDataBuilder withEmail(String email) {
        this.email = email;
        return this;
    }
    
    public UserTestDataBuilder inactive() {
        this.active = false;
        return this;
    }
    
    public UserTestDataBuilder createdAt(LocalDateTime createdAt) {
        this.createdAt = createdAt;
        return this;
    }
    
    public User build() {
        return new User(name, email, createdAt, active);
    }
}

// Использование в тестах:
class UserTestDataBuilderExample {
    @Test
    void testUserCreation() {
        // Given
        User user = UserTestDataBuilder.aUser()
            .withName("John Doe")
            .withEmail("john@example.com")
            .inactive()
            .build();
        
        // Test logic...
    }
}
```

---

## 📊 Мониторинг и логирование

### Правильное логирование
```java
// ✅ Эффективное логирование
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.slf4j.MDC;

public class UserService {
    private static final Logger logger = LoggerFactory.getLogger(UserService.class);
    
    public User createUser(String name, String email) {
        // Structured logging с контекстом
        MDC.put("operation", "createUser");
        MDC.put("email", email);
        
        try {
            logger.info("Creating user with email: {}", email);
            
            validateInput(name, email);
            
            User user = new User(name, email);
            user = userRepository.save(user);
            
            logger.info("User created successfully with ID: {}", user.getId());
            return user;
            
        } catch (ValidationException e) {
            logger.warn("User creation failed due to validation error: {}", e.getMessage());
            throw e;
        } catch (Exception e) {
            logger.error("Unexpected error during user creation", e);
            throw new UserCreationException("Failed to create user", e);
        } finally {
            MDC.clear(); // Очищаем контекст
        }
    }
    
    // Производительное логирование - проверяем уровень
    public void processLargeDataset(List<DataItem> items) {
        if (logger.isDebugEnabled()) {
            logger.debug("Processing dataset with {} items", items.size());
        }
        
        for (DataItem item : items) {
            processItem(item);
            
            // Логируем только каждый 1000-й элемент
            if (item.getId() % 1000 == 0 && logger.isInfoEnabled()) {
                logger.info("Processed {} items", item.getId());
            }
        }
    }
    
    private void processItem(DataItem item) {
        // Обработка элемента
    }
}

// ✅ Метрики производительности
public class MetricsCollector {
    private static final MeterRegistry meterRegistry = Metrics.globalRegistry;
    
    private final Counter userCreationCounter = Counter.builder("users.created")
        .description("Total number of users created")
        .register(meterRegistry);
    
    private final Timer userCreationTimer = Timer.builder("users.creation.time")
        .description("Time taken to create a user")
        .register(meterRegistry);
    
    private final Gauge activeUsersGauge = Gauge.builder("users.active")
        .description("Number of active users")
        .register(meterRegistry, this, MetricsCollector::getActiveUsersCount);
    
    public User createUserWithMetrics(String name, String email) {
        return Timer.Sample.start(meterRegistry)
            .stop(userCreationTimer.time(() -> {
                User user = createUser(name, email);
                userCreationCounter.increment();
                return user;
            }));
    }
    
    private double getActiveUsersCount() {
        // Подсчет активных пользователей
        return 42.0; // Заглушка
    }
    
    private User createUser(String name, String email) {
        // Логика создания пользователя
        return new User(name, email);
    }
}

// ❌ Плохое логирование
public class BadLoggingExamples {
    private static final Logger logger = LoggerFactory.getLogger(BadLoggingExamples.class);
    
    public void badLoggingPractices(String input) {
        // Конкатенация строк - медленно!
        logger.info("Processing input: " + input + " with length: " + input.length());
        
        // Логирование в циклах без проверки уровня
        for (int i = 0; i < 10000; i++) {
            logger.debug("Processing item: " + i); // Создает 10000 строк даже если debug отключен!
        }
        
        // Логирование исключений без контекста
        try {
            processInput(input);
        } catch (Exception e) {
            logger.error("Error occurred"); // Нет информации об ошибке!
        }
        
        // Использование System.out.println в production коде
        System.out.println("This should not be in production code!");
    }
    
    private void processInput(String input) throws Exception {
        // Обработка
    }
}
```

---

## 🔗 Связанные темы
- [[OOP-Principles-Code|Принципы ООП]] - основа качественного кода
- [[Exception-Handling-Code|Обработка исключений]] - правильная работа с ошибками
- [[Multithreading-Code|Многопоточность]] - безопасное параллельное программирование
- [[JUnit-Basics-Code|JUnit]] - тестирование кода
- [[Collections-Overview|Collections Framework]] - эффективное использование коллекций