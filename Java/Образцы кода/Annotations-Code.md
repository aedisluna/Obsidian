# Аннотации в Java
*Метаданные для кода и их практическое применение*

#java #annotations #metadata #reflection #обработка

---

## 📋 Содержание
- [[#Основы аннотаций]]
- [[#Стандартные аннотации]]
- [[#Создание собственных аннотаций]]
- [[#Обработка аннотаций]]
- [[#Reflection и аннотации]]
- [[#Практические примеры]]

---

## 🎯 Основы аннотаций

> [!info] Аннотации - это форма метаданных, предоставляющая информацию о программе, но не влияющая на её выполнение

### Зачем нужны аннотации?
- ✅ **Документирование кода** - описание назначения элементов
- ✅ **Конфигурация** - настройка поведения frameworks
- ✅ **Генерация кода** - автоматическое создание boilerplate кода
- ✅ **Валидация** - проверка корректности во время компиляции
- ✅ **Runtime обработка** - динамическое поведение программы

### Синтаксис аннотаций
```java
// Простая аннотация без параметров
@Override
public String toString() {
    return "Example";
}

// Аннотация с одним параметром
@SuppressWarnings("unchecked")
public List<String> getList() {
    return (List<String>) rawList;
}

// Аннотация с несколькими параметрами
@RequestMapping(value = "/users", method = RequestMethod.GET)
public String getUsers() {
    return "users";
}

// Аннотация с массивом значений
@Component(roles = {"ADMIN", "USER"})
public class UserService {
    // ...
}
```

---

## 📚 Стандартные аннотации

### Аннотации для компилятора
```java
public class StandardAnnotationsExample {
    
    // @Override - проверка переопределения метода
    @Override
    public String toString() {
        return "StandardAnnotationsExample{}";
    }
    
    // @Override с ошибкой компиляции
    // @Override
    // public String tostring() { // Ошибка! Метод не переопределяется
    //     return "error";
    // }
    
    // @Deprecated - помечает устаревший код
    @Deprecated
    public void oldMethod() {
        System.out.println("Этот метод устарел, используйте newMethod()");
    }
    
    @Deprecated(since = "2.0", forRemoval = true)
    public void veryOldMethod() {
        System.out.println("Будет удален в следующей версии");
    }
    
    // Новый рекомендуемый метод
    public void newMethod() {
        System.out.println("Новый улучшенный метод");
    }
    
    // @SuppressWarnings - подавление предупреждений компилятора
    @SuppressWarnings("unchecked")
    public List<String> getUntypedList() {
        List rawList = new ArrayList(); // Raw type - обычно вызывает warning
        rawList.add("string");
        return rawList; // Unchecked cast - обычно вызывает warning
    }
    
    @SuppressWarnings({"unused", "deprecation"})
    private void methodWithWarnings() {
        String unusedVariable = "not used"; // unused warning подавлен
        oldMethod(); // deprecation warning подавлен
    }
    
    // @SafeVarargs - безопасные varargs с generic типами
    @SafeVarargs
    public static <T> List<T> createList(T... elements) {
        List<T> list = new ArrayList<>();
        Collections.addAll(list, elements);
        return list;
    }
    
    public static void demonstrateUsage() {
        StandardAnnotationsExample example = new StandardAnnotationsExample();
        
        // Использование deprecated методов вызовет warning
        example.oldMethod();
        example.veryOldMethod();
        
        // Рекомендуемый способ
        example.newMethod();
        
        // SafeVarargs в действии
        List<String> strings = createList("a", "b", "c");
        List<Integer> numbers = createList(1, 2, 3, 4, 5);
        
        System.out.println("Strings: " + strings);
        System.out.println("Numbers: " + numbers);
    }
}
```

### Аннотации для документации и анализа
```java
import javax.annotation.*;

public class DocumentationAnnotations {
    
    // @Nullable - может быть null
    public String processName(@Nullable String input) {
        if (input == null) {
            return "Default Name";
        }
        return input.trim().toUpperCase();
    }
    
    // @Nonnull - не должно быть null
    public String formatName(@Nonnull String name) {
        // IDE предупредит если передать null
        return name.toLowerCase().replace(" ", "_");
    }
    
    // @CheckForNull - результат может быть null, нужно проверять
    @CheckForNull
    public User findUser(long id) {
        // Поиск пользователя может вернуть null
        if (id <= 0) {
            return null;
        }
        return new User("User" + id, "user" + id + "@example.com");
    }
    
    // @CheckReturnValue - результат должен использоваться
    @CheckReturnValue
    public String generateUniqueId() {
        return "ID_" + System.currentTimeMillis() + "_" + Math.random();
    }
    
    public void demonstrateUsage() {
        // Правильное использование @Nullable
        String result1 = processName(null); // OK
        String result2 = processName("  john doe  "); // OK
        
        // Правильное использование @Nonnull
        String formatted = formatName("John Doe"); // OK
        // String formatted2 = formatName(null); // IDE предупредит
        
        // Правильное использование @CheckForNull
        User user = findUser(123);
        if (user != null) { // Проверка обязательна
            System.out.println("Found user: " + user.getName());
        }
        
        // Правильное использование @CheckReturnValue
        String id = generateUniqueId(); // Результат используется - хорошо
        // generateUniqueId(); // Результат игнорируется - IDE предупредит
        
        System.out.println("Generated ID: " + id);
    }
}

// Простой класс User для примера
class User {
    private String name;
    private String email;
    
    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }
    
    public String getName() { return name; }
    public String getEmail() { return email; }
}
```

---

## 🛠️ Создание собственных аннотаций

### Базовые аннотации
```java
import java.lang.annotation.*;

// Простая аннотация-маркер (без параметров)
@Retention(RetentionPolicy.RUNTIME) // Доступна во время выполнения
@Target(ElementType.METHOD) // Применяется только к методам
public @interface Benchmark {
}

// Аннотация с параметрами
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD}) // Применяется к классам и методам
public @interface Author {
    String name(); // Обязательный параметр
    String email() default ""; // Необязательный параметр с значением по умолчанию
    String[] tags() default {}; // Массив строк
    int version() default 1; // Числовой параметр
}

// Аннотация для валидации
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface ValidEmail {
    String message() default "Invalid email format";
    boolean required() default true;
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface Range {
    int min() default 0;
    int max() default Integer.MAX_VALUE;
    String message() default "Value is out of range";
}

// Мета-аннотация для создания составных аннотаций
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Validator {
}

// Составная аннотация
@Validator
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface PersonName {
    int minLength() default 2;
    int maxLength() default 50;
    boolean allowNumbers() default false;
}
```

### Использование собственных аннотаций
```java
@Author(
    name = "John Doe", 
    email = "john@example.com", 
    tags = {"utility", "example"}, 
    version = 2
)
public class UserValidator {
    
    @Benchmark
    @Author(name = "Jane Smith", tags = {"validation"})
    public boolean validateUser(User user) {
        return user != null && 
               !user.getName().isEmpty() && 
               user.getEmail().contains("@");
    }
    
    @Benchmark
    public void processUsers(List<User> users) {
        users.stream()
             .filter(this::validateUser)
             .forEach(user -> System.out.println("Processing: " + user.getName()));
    }
}

// Класс с аннотациями валидации
public class UserForm {
    
    @PersonName(minLength = 2, maxLength = 30)
    private String firstName;
    
    @PersonName(minLength = 2, maxLength = 30)
    private String lastName;
    
    @ValidEmail(required = true)
    private String email;
    
    @Range(min = 18, max = 120, message = "Age must be between 18 and 120")
    private int age;
    
    @Range(min = 0, max = 100000, message = "Salary must be between 0 and 100000")
    private double salary;
    
    // Конструкторы и методы
    public UserForm(String firstName, String lastName, String email, int age, double salary) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.email = email;
        this.age = age;
        this.salary = salary;
    }
    
    // Геттеры
    public String getFirstName() { return firstName; }
    public String getLastName() { return lastName; }
    public String getEmail() { return email; }
    public int getAge() { return age; }
    public double getSalary() { return salary; }
}
```

### Сложные аннотации с конфигурацией
```java
// Аннотация для настройки кэширования
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Cacheable {
    String name() default ""; // Имя кэша
    int expireAfterWrite() default 300; // TTL в секундах
    int maxSize() default 1000; // Максимальный размер кэша
    boolean enabled() default true; // Включено ли кэширование
    CacheStrategy strategy() default CacheStrategy.LRU; // Стратегия кэширования
}

enum CacheStrategy {
    LRU,    // Least Recently Used
    LFU,    // Least Frequently Used
    FIFO    // First In First Out
}

// Аннотация для настройки retry логики
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Retryable {
    int maxAttempts() default 3;
    long delay() default 1000; // Задержка в миллисекундах
    double multiplier() default 1.0; // Множитель для экспоненциальной задержки
    Class<? extends Throwable>[] retryOn() default {RuntimeException.class};
    Class<? extends Throwable>[] noRetryOn() default {};
}

// Аннотация для логирования
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Loggable {
    LogLevel level() default LogLevel.INFO;
    boolean logParameters() default true;
    boolean logResult() default true;
    boolean logExecutionTime() default false;
    String[] excludeParameters() default {}; // Параметры для исключения из логов
}

enum LogLevel {
    TRACE, DEBUG, INFO, WARN, ERROR
}

// Использование сложных аннотаций
public class BusinessService {
    
    @Cacheable(
        name = "userCache",
        expireAfterWrite = 600,
        maxSize = 500,
        strategy = CacheStrategy.LRU
    )
    @Loggable(level = LogLevel.DEBUG, logExecutionTime = true)
    public User getUser(long userId) {
        // Имитация медленного получения пользователя
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        return new User("User" + userId, "user" + userId + "@example.com");
    }
    
    @Retryable(
        maxAttempts = 5,
        delay = 500,
        multiplier = 2.0,
        retryOn = {IllegalStateException.class, RuntimeException.class},
        noRetryOn = {IllegalArgumentException.class}
    )
    @Loggable(level = LogLevel.WARN, excludeParameters = {"password"})
    public boolean authenticate(String username, String password) {
        // Имитация нестабильной аутентификации
        if (Math.random() < 0.7) {
            throw new IllegalStateException("Service temporarily unavailable");
        }
        return username.equals("admin") && password.equals("secret");
    }
    
    @Cacheable(name = "statisticsCache", expireAfterWrite = 60)
    @Benchmark
    public Map<String, Object> getStatistics() {
        Map<String, Object> stats = new HashMap<>();
        stats.put("totalUsers", 1000);
        stats.put("activeUsers", 750);
        stats.put("timestamp", System.currentTimeMillis());
        return stats;
    }
}
```

---

## ⚙️ Обработка аннотаций

### Обработка во время выполнения (Runtime)
```java
import java.lang.annotation.Annotation;
import java.lang.reflect.Method;
import java.lang.reflect.Field;

public class AnnotationProcessor {
    
    // Обработка аннотаций @Author
    public static void printAuthorInfo(Class<?> clazz) {
        System.out.println("=== Author Information ===");
        
        // Аннотации на классе
        if (clazz.isAnnotationPresent(Author.class)) {
            Author author = clazz.getAnnotation(Author.class);
            System.out.printf("Class %s authored by: %s <%s>%n", 
                             clazz.getSimpleName(), author.name(), author.email());
            System.out.println("Tags: " + String.join(", ", author.tags()));
            System.out.println("Version: " + author.version());
        }
        
        // Аннотации на методах
        for (Method method : clazz.getDeclaredMethods()) {
            if (method.isAnnotationPresent(Author.class)) {
                Author author = method.getAnnotation(Author.class);
                System.out.printf("Method %s authored by: %s%n", 
                                 method.getName(), author.name());
            }
        }
        
        System.out.println();
    }
    
    // Простой валидатор на основе аннотаций
    public static ValidationResult validateObject(Object obj) {
        ValidationResult result = new ValidationResult();
        Class<?> clazz = obj.getClass();
        
        for (Field field : clazz.getDeclaredFields()) {
            field.setAccessible(true);
            
            try {
                Object value = field.get(obj);
                
                // Валидация @ValidEmail
                if (field.isAnnotationPresent(ValidEmail.class)) {
                    ValidEmail annotation = field.getAnnotation(ValidEmail.class);
                    if (!validateEmail(value, annotation)) {
                        result.addError(field.getName() + ": " + annotation.message());
                    }
                }
                
                // Валидация @Range
                if (field.isAnnotationPresent(Range.class)) {
                    Range annotation = field.getAnnotation(Range.class);
                    if (!validateRange(value, annotation)) {
                        result.addError(field.getName() + ": " + annotation.message());
                    }
                }
                
                // Валидация @PersonName
                if (field.isAnnotationPresent(PersonName.class)) {
                    PersonName annotation = field.getAnnotation(PersonName.class);
                    if (!validatePersonName(value, annotation)) {
                        result.addError(String.format("%s: Name must be between %d and %d characters", 
                                      field.getName(), annotation.minLength(), annotation.maxLength()));
                    }
                }
                
            } catch (IllegalAccessException e) {
                result.addError("Cannot access field: " + field.getName());
            }
        }
        
        return result;
    }
    
    private static boolean validateEmail(Object value, ValidEmail annotation) {
        if (value == null) {
            return !annotation.required();
        }
        
        if (!(value instanceof String)) {
            return false;
        }
        
        String email = (String) value;
        return email.matches("^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$");
    }
    
    private static boolean validateRange(Object value, Range annotation) {
        if (value == null) {
            return true; // null считается валидным для Range
        }
        
        if (value instanceof Number) {
            double num = ((Number) value).doubleValue();
            return num >= annotation.min() && num <= annotation.max();
        }
        
        return false;
    }
    
    private static boolean validatePersonName(Object value, PersonName annotation) {
        if (value == null) {
            return false;
        }
        
        if (!(value instanceof String)) {
            return false;
        }
        
        String name = (String) value;
        if (name.length() < annotation.minLength() || name.length() > annotation.maxLength()) {
            return false;
        }
        
        if (!annotation.allowNumbers() && name.matches(".*\\d.*")) {
            return false;
        }
        
        return true;
    }
    
    // Обработка @Benchmark аннотаций
    public static void runBenchmarks(Object instance) {
        Class<?> clazz = instance.getClass();
        
        System.out.println("=== Running Benchmarks ===");
        
        for (Method method : clazz.getDeclaredMethods()) {
            if (method.isAnnotationPresent(Benchmark.class)) {
                try {
                    System.out.printf("Benchmarking method: %s%n", method.getName());
                    
                    long startTime = System.nanoTime();
                    method.invoke(instance);
                    long endTime = System.nanoTime();
                    
                    long durationNanos = endTime - startTime;
                    double durationMillis = durationNanos / 1_000_000.0;
                    
                    System.out.printf("  Execution time: %.3f ms%n", durationMillis);
                    
                } catch (Exception e) {
                    System.out.printf("  Error executing %s: %s%n", method.getName(), e.getMessage());
                }
            }
        }
        
        System.out.println();
    }
    
    public static void main(String[] args) {
        // Тестируем обработку аннотаций Author
        printAuthorInfo(UserValidator.class);
        
        // Тестируем валидацию
        UserForm validForm = new UserForm("John", "Doe", "john@example.com", 30, 50000);
        UserForm invalidForm = new UserForm("J", "", "invalid-email", 15, -1000);
        
        System.out.println("=== Validation Results ===");
        
        ValidationResult validResult = validateObject(validForm);
        System.out.println("Valid form: " + validResult.isValid());
        if (!validResult.isValid()) {
            validResult.getErrors().forEach(System.out::println);
        }
        
        ValidationResult invalidResult = validateObject(invalidForm);
        System.out.println("\\nInvalid form: " + invalidResult.isValid());
        if (!invalidResult.isValid()) {
            invalidResult.getErrors().forEach(error -> System.out.println("  " + error));
        }
        
        // Тестируем benchmark
        UserValidator validator = new UserValidator();
        List<User> users = Arrays.asList(
            new User("Alice", "alice@example.com"),
            new User("Bob", "bob@example.com"),
            new User("", "invalid"),
            new User("Charlie", "charlie@example.com")
        );
        
        runBenchmarks(validator);
        
        // Запускаем benchmark методы
        validator.validateUser(users.get(0));
        validator.processUsers(users);
    }
}

// Вспомогательный класс для результатов валидации
class ValidationResult {
    private final List<String> errors = new ArrayList<>();
    
    public void addError(String error) {
        errors.add(error);
    }
    
    public boolean isValid() {
        return errors.isEmpty();
    }
    
    public List<String> getErrors() {
        return new ArrayList<>(errors);
    }
}
```

### Создание прокси с аннотациями
```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.concurrent.ConcurrentHashMap;

// Интерфейс для демонстрации прокси
interface CacheableService {
    String getData(String key);
    void clearCache();
    int calculate(int x, int y);
}

// Реализация сервиса
class CacheableServiceImpl implements CacheableService {
    
    @Override
    @Cacheable(name = "dataCache", expireAfterWrite = 300)
    public String getData(String key) {
        // Имитация медленной операции
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        return "Data for key: " + key + " (loaded at " + System.currentTimeMillis() + ")";
    }
    
    @Override
    public void clearCache() {
        System.out.println("Cache cleared");
    }
    
    @Override
    @Cacheable(name = "calculationCache", maxSize = 100)
    @Loggable(level = LogLevel.DEBUG, logParameters = true, logResult = true)
    public int calculate(int x, int y) {
        // Имитация сложного вычисления
        try {
            Thread.sleep(500);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        return x * x + y * y;
    }
}

// Прокси для обработки кэширования
class CachingInvocationHandler implements InvocationHandler {
    private final Object target;
    private final Map<String, Object> cache = new ConcurrentHashMap<>();
    
    public CachingInvocationHandler(Object target) {
        this.target = target;
    }
    
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // Проверяем наличие аннотации @Cacheable
        if (method.isAnnotationPresent(Cacheable.class)) {
            Cacheable cacheConfig = method.getAnnotation(Cacheable.class);
            
            if (cacheConfig.enabled()) {
                String cacheKey = generateCacheKey(method, args);
                
                // Проверяем кэш
                if (cache.containsKey(cacheKey)) {
                    System.out.println("Cache HIT for " + cacheKey);
                    return cache.get(cacheKey);
                }
                
                // Выполняем метод
                System.out.println("Cache MISS for " + cacheKey);
                Object result = method.invoke(target, args);
                
                // Сохраняем в кэш
                cache.put(cacheKey, result);
                System.out.println("Cached result for " + cacheKey);
                
                return result;
            }
        }
        
        // Обрабатываем логирование
        if (method.isAnnotationPresent(Loggable.class)) {
            Loggable logConfig = method.getAnnotation(Loggable.class);
            
            System.out.printf("[%s] Calling method: %s", logConfig.level(), method.getName());
            if (logConfig.logParameters() && args != null) {
                System.out.print(" with parameters: " + Arrays.toString(args));
            }
            System.out.println();
            
            long startTime = System.currentTimeMillis();
            Object result = method.invoke(target, args);
            long endTime = System.currentTimeMillis();
            
            if (logConfig.logResult()) {
                System.out.printf("[%s] Method result: %s%n", logConfig.level(), result);
            }
            
            if (logConfig.logExecutionTime()) {
                System.out.printf("[%s] Execution time: %d ms%n", logConfig.level(), (endTime - startTime));
            }
            
            return result;
        }
        
        // Обычное выполнение без аннотаций
        return method.invoke(target, args);
    }
    
    private String generateCacheKey(Method method, Object[] args) {
        StringBuilder keyBuilder = new StringBuilder();
        keyBuilder.append(method.getName());
        
        if (args != null) {
            for (Object arg : args) {
                keyBuilder.append("_").append(arg);
            }
        }
        
        return keyBuilder.toString();
    }
}

// Демонстрация работы с прокси
public class ProxyAnnotationExample {
    
    public static void main(String[] args) {
        // Создаем оригинальный объект
        CacheableService originalService = new CacheableServiceImpl();
        
        // Создаем прокси с кэшированием
        CacheableService proxiedService = (CacheableService) Proxy.newProxyInstance(
            CacheableService.class.getClassLoader(),
            new Class[]{CacheableService.class},
            new CachingInvocationHandler(originalService)
        );
        
        System.out.println("=== Testing Caching Proxy ===");
        
        // Первый вызов - должен быть кэш MISS
        String result1 = proxiedService.getData("user123");
        System.out.println("Result 1: " + result1);
        
        System.out.println();
        
        // Второй вызов с тем же параметром - должен быть кэш HIT
        String result2 = proxiedService.getData("user123");
        System.out.println("Result 2: " + result2);
        
        System.out.println();
        
        // Вызов с другим параметром - кэш MISS
        String result3 = proxiedService.getData("user456");
        System.out.println("Result 3: " + result3);
        
        System.out.println("\\n=== Testing Logging with Caching ===");
        
        // Тестируем метод с логированием и кэшированием
        int calculation1 = proxiedService.calculate(3, 4);
        System.out.println("Calculation 1 result: " + calculation1);
        
        System.out.println();
        
        // Повторный вызов - должен использовать кэш
        int calculation2 = proxiedService.calculate(3, 4);
        System.out.println("Calculation 2 result: " + calculation2);
        
        System.out.println();
        
        // Вызов с другими параметрами
        int calculation3 = proxiedService.calculate(5, 6);
        System.out.println("Calculation 3 result: " + calculation3);
    }
}
```

---

## 🔍 Reflection и аннотации

### Глубокий анализ аннотаций
```java
import java.lang.reflect.*;
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class AdvancedAnnotationAnalysis {
    
    public static void analyzeClass(Class<?> clazz) {
        System.out.println("=== Deep Analysis of " + clazz.getSimpleName() + " ===");
        
        // Анализ аннотаций класса
        analyzeClassAnnotations(clazz);
        
        // Анализ полей
        analyzeFields(clazz);
        
        // Анализ методов
        analyzeMethods(clazz);
        
        // Анализ конструкторов
        analyzeConstructors(clazz);
    }
    
    private static void analyzeClassAnnotations(Class<?> clazz) {
        System.out.println("\\n--- Class Annotations ---");
        
        Annotation[] annotations = clazz.getAnnotations();
        if (annotations.length == 0) {
            System.out.println("No annotations on class");
            return;
        }
        
        for (Annotation annotation : annotations) {
            System.out.println("@" + annotation.annotationType().getSimpleName());
            printAnnotationDetails(annotation);
        }
    }
    
    private static void analyzeFields(Class<?> clazz) {
        System.out.println("\\n--- Field Annotations ---");
        
        Field[] fields = clazz.getDeclaredFields();
        for (Field field : fields) {
            Annotation[] annotations = field.getAnnotations();
            if (annotations.length > 0) {
                System.out.printf("Field '%s' (%s):%n", field.getName(), field.getType().getSimpleName());
                
                for (Annotation annotation : annotations) {
                    System.out.println("  @" + annotation.annotationType().getSimpleName());
                    printAnnotationDetails(annotation, "    ");
                }
            }
        }
    }
    
    private static void analyzeMethods(Class<?> clazz) {
        System.out.println("\\n--- Method Annotations ---");
        
        Method[] methods = clazz.getDeclaredMethods();
        for (Method method : methods) {
            Annotation[] annotations = method.getAnnotations();
            if (annotations.length > 0) {
                System.out.printf("Method '%s':%n", method.getName());
                
                for (Annotation annotation : annotations) {
                    System.out.println("  @" + annotation.annotationType().getSimpleName());
                    printAnnotationDetails(annotation, "    ");
                }
                
                // Анализ параметров метода
                analyzeMethodParameters(method);
            }
        }
    }
    
    private static void analyzeMethodParameters(Method method) {
        Parameter[] parameters = method.getParameters();
        if (parameters.length > 0) {
            System.out.println("    Parameters:");
            
            for (int i = 0; i < parameters.length; i++) {
                Parameter param = parameters[i];
                Annotation[] paramAnnotations = param.getAnnotations();
                
                if (paramAnnotations.length > 0) {
                    System.out.printf("      Parameter %d (%s):%n", i, param.getType().getSimpleName());
                    
                    for (Annotation annotation : paramAnnotations) {
                        System.out.println("        @" + annotation.annotationType().getSimpleName());
                        printAnnotationDetails(annotation, "          ");
                    }
                }
            }
        }
    }
    
    private static void analyzeConstructors(Class<?> clazz) {
        System.out.println("\\n--- Constructor Annotations ---");
        
        Constructor<?>[] constructors = clazz.getDeclaredConstructors();
        for (Constructor<?> constructor : constructors) {
            Annotation[] annotations = constructor.getAnnotations();
            if (annotations.length > 0) {
                System.out.printf("Constructor with %d parameters:%n", constructor.getParameterCount());
                
                for (Annotation annotation : annotations) {
                    System.out.println("  @" + annotation.annotationType().getSimpleName());
                    printAnnotationDetails(annotation, "    ");
                }
            }
        }
    }
    
    private static void printAnnotationDetails(Annotation annotation) {
        printAnnotationDetails(annotation, "  ");
    }
    
    private static void printAnnotationDetails(Annotation annotation, String indent) {
        try {
            Method[] methods = annotation.annotationType().getDeclaredMethods();
            
            for (Method method : methods) {
                if (method.getParameterCount() == 0 && method.getReturnType() != void.class) {
                    Object value = method.invoke(annotation);
                    
                    if (value.getClass().isArray()) {
                        System.out.printf("%s%s = %s%n", indent, method.getName(), Arrays.toString((Object[]) value));
                    } else {
                        System.out.printf("%s%s = %s%n", indent, method.getName(), value);
                    }
                }
            }
        } catch (Exception e) {
            System.out.printf("%sError reading annotation details: %s%n", indent, e.getMessage());
        }
    }
    
    // Поиск всех классов с определенной аннотацией
    public static List<Method> findMethodsWithAnnotation(Class<?> clazz, Class<? extends Annotation> annotationClass) {
        return Arrays.stream(clazz.getDeclaredMethods())
                    .filter(method -> method.isAnnotationPresent(annotationClass))
                    .collect(Collectors.toList());
    }
    
    // Поиск всех полей с определенной аннотацией
    public static List<Field> findFieldsWithAnnotation(Class<?> clazz, Class<? extends Annotation> annotationClass) {
        return Arrays.stream(clazz.getDeclaredFields())
                    .filter(field -> field.isAnnotationPresent(annotationClass))
                    .collect(Collectors.toList());
    }
    
    // Получение значения аннотации
    public static <T> T getAnnotationValue(AnnotatedElement element, Class<? extends Annotation> annotationClass, String attributeName) {
        try {
            Annotation annotation = element.getAnnotation(annotationClass);
            if (annotation != null) {
                Method method = annotationClass.getDeclaredMethod(attributeName);
                return (T) method.invoke(annotation);
            }
        } catch (Exception e) {
            System.err.println("Error getting annotation value: " + e.getMessage());
        }
        return null;
    }
    
    public static void main(String[] args) {
        // Анализируем наши тестовые классы
        analyzeClass(UserValidator.class);
        analyzeClass(UserForm.class);
        analyzeClass(BusinessService.class);
        
        // Демонстрируем поиск методов с аннотациями
        System.out.println("\\n=== Methods with @Benchmark ===");
        List<Method> benchmarkMethods = findMethodsWithAnnotation(UserValidator.class, Benchmark.class);
        benchmarkMethods.forEach(method -> System.out.println("- " + method.getName()));
        
        System.out.println("\\n=== Fields with @ValidEmail ===");
        List<Field> emailFields = findFieldsWithAnnotation(UserForm.class, ValidEmail.class);
        emailFields.forEach(field -> System.out.println("- " + field.getName()));
        
        // Демонстрируем получение значений аннотаций
        System.out.println("\\n=== Annotation Values ===");
        for (Field field : findFieldsWithAnnotation(UserForm.class, Range.class)) {
            Integer min = getAnnotationValue(field, Range.class, "min");
            Integer max = getAnnotationValue(field, Range.class, "max");
            String message = getAnnotationValue(field, Range.class, "message");
            
            System.out.printf("Field %s: min=%d, max=%d, message='%s'%n", 
                             field.getName(), min, max, message);
        }
    }
}
```

---

## 💼 Практические примеры

### Система конфигурации на аннотациях
```java
// Аннотации для конфигурации
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface ConfigProperty {
    String key();
    String defaultValue() default "";
    boolean required() default false;
    String description() default "";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Configuration {
    String prefix() default "";
}

// Класс конфигурации приложения
@Configuration(prefix = "app")
public class ApplicationConfig {
    
    @ConfigProperty(key = "database.host", defaultValue = "localhost", required = true)
    private String databaseHost;
    
    @ConfigProperty(key = "database.port", defaultValue = "5432")
    private int databasePort;
    
    @ConfigProperty(key = "database.name", required = true)
    private String databaseName;
    
    @ConfigProperty(key = "cache.enabled", defaultValue = "true")
    private boolean cacheEnabled;
    
    @ConfigProperty(key = "cache.ttl", defaultValue = "3600", description = "Cache TTL in seconds")
    private int cacheTtl;
    
    @ConfigProperty(key = "mail.smtp.host", defaultValue = "localhost")
    private String smtpHost;
    
    @ConfigProperty(key = "mail.smtp.port", defaultValue = "587")
    private int smtpPort;
    
    @ConfigProperty(key = "logging.level", defaultValue = "INFO")
    private String logLevel;
    
    // Геттеры
    public String getDatabaseHost() { return databaseHost; }
    public int getDatabasePort() { return databasePort; }
    public String getDatabaseName() { return databaseName; }
    public boolean isCacheEnabled() { return cacheEnabled; }
    public int getCacheTtl() { return cacheTtl; }
    public String getSmtpHost() { return smtpHost; }
    public int getSmtpPort() { return smtpPort; }
    public String getLogLevel() { return logLevel; }
    
    @Override
    public String toString() {
        return String.format("ApplicationConfig{databaseHost='%s', databasePort=%d, databaseName='%s', " +
                           "cacheEnabled=%s, cacheTtl=%d, smtpHost='%s', smtpPort=%d, logLevel='%s'}",
                           databaseHost, databasePort, databaseName, cacheEnabled, 
                           cacheTtl, smtpHost, smtpPort, logLevel);
    }
}

// Загрузчик конфигурации
public class ConfigurationLoader {
    
    public static <T> T loadConfiguration(Class<T> configClass, Properties properties) {
        try {
            T configInstance = configClass.getDeclaredConstructor().newInstance();
            
            String prefix = getConfigPrefix(configClass);
            
            for (Field field : configClass.getDeclaredFields()) {
                if (field.isAnnotationPresent(ConfigProperty.class)) {
                    loadFieldValue(configInstance, field, properties, prefix);
                }
            }
            
            return configInstance;
            
        } catch (Exception e) {
            throw new RuntimeException("Failed to load configuration", e);
        }
    }
    
    private static String getConfigPrefix(Class<?> configClass) {
        if (configClass.isAnnotationPresent(Configuration.class)) {
            Configuration config = configClass.getAnnotation(Configuration.class);
            String prefix = config.prefix();
            return prefix.isEmpty() ? "" : prefix + ".";
        }
        return "";
    }
    
    private static <T> void loadFieldValue(T configInstance, Field field, Properties properties, String prefix) 
            throws IllegalAccessException {
        ConfigProperty annotation = field.getAnnotation(ConfigProperty.class);
        String propertyKey = prefix + annotation.key();
        
        String value = properties.getProperty(propertyKey, annotation.defaultValue());
        
        if (value.isEmpty() && annotation.required()) {
            throw new IllegalStateException("Required property not found: " + propertyKey);
        }
        
        field.setAccessible(true);
        Object convertedValue = convertValue(value, field.getType());
        field.set(configInstance, convertedValue);
        
        System.out.printf("Loaded %s = %s%n", propertyKey, value);
    }
    
    private static Object convertValue(String value, Class<?> targetType) {
        if (targetType == String.class) {
            return value;
        } else if (targetType == int.class || targetType == Integer.class) {
            return Integer.parseInt(value);
        } else if (targetType == long.class || targetType == Long.class) {
            return Long.parseLong(value);
        } else if (targetType == double.class || targetType == Double.class) {
            return Double.parseDouble(value);
        } else if (targetType == boolean.class || targetType == Boolean.class) {
            return Boolean.parseBoolean(value);
        } else {
            throw new IllegalArgumentException("Unsupported type: " + targetType);
        }
    }
    
    // Генерация документации по конфигурации
    public static void generateConfigDocumentation(Class<?> configClass) {
        System.out.println("=== Configuration Documentation ===");
        System.out.println("Class: " + configClass.getSimpleName());
        
        if (configClass.isAnnotationPresent(Configuration.class)) {
            Configuration config = configClass.getAnnotation(Configuration.class);
            if (!config.prefix().isEmpty()) {
                System.out.println("Prefix: " + config.prefix());
            }
        }
        
        System.out.println("\\nProperties:");
        
        for (Field field : configClass.getDeclaredFields()) {
            if (field.isAnnotationPresent(ConfigProperty.class)) {
                ConfigProperty prop = field.getAnnotation(ConfigProperty.class);
                
                System.out.printf("- %s (%s)%n", prop.key(), field.getType().getSimpleName());
                System.out.printf("  Default: %s%n", prop.defaultValue().isEmpty() ? "none" : prop.defaultValue());
                System.out.printf("  Required: %s%n", prop.required() ? "yes" : "no");
                
                if (!prop.description().isEmpty()) {
                    System.out.printf("  Description: %s%n", prop.description());
                }
                
                System.out.println();
            }
        }
    }
    
    public static void main(String[] args) {
        // Создаем properties файл
        Properties properties = new Properties();
        properties.setProperty("app.database.host", "production-db.example.com");
        properties.setProperty("app.database.port", "3306");
        properties.setProperty("app.database.name", "myapp");
        properties.setProperty("app.cache.enabled", "true");
        properties.setProperty("app.cache.ttl", "7200");
        properties.setProperty("app.mail.smtp.host", "smtp.example.com");
        properties.setProperty("app.logging.level", "DEBUG");
        
        // Загружаем конфигурацию
        System.out.println("=== Loading Configuration ===");
        ApplicationConfig config = loadConfiguration(ApplicationConfig.class, properties);
        
        System.out.println("\\n=== Loaded Configuration ===");
        System.out.println(config);
        
        // Генерируем документацию
        System.out.println("\\n");
        generateConfigDocumentation(ApplicationConfig.class);
        
        // Тест обязательных свойств
        System.out.println("=== Testing Required Properties ===");
        Properties incompleteProperties = new Properties();
        incompleteProperties.setProperty("app.database.host", "localhost");
        // Не устанавливаем database.name (required = true)
        
        try {
            loadConfiguration(ApplicationConfig.class, incompleteProperties);
        } catch (Exception e) {
            System.out.println("Expected error: " + e.getMessage());
        }
    }
}
```

---

## 💡 Лучшие практики аннотаций

### 1. Правильное использование Retention
```java
// ✅ Правильно - выбирай подходящий retention policy
@Retention(RetentionPolicy.SOURCE)   // Только для компилятора
public @interface Generated {
}

@Retention(RetentionPolicy.CLASS)    // Для annotation processors
public @interface Entity {
}

@Retention(RetentionPolicy.RUNTIME)  // Для runtime обработки
public @interface Injectable {
}
```

### 2. Осторожность с производительностью
```java
// ✅ Хорошо - кэшируй результаты reflection
public class PerformantAnnotationProcessor {
    private static final Map<Class<?>, List<Method>> BENCHMARK_METHODS_CACHE = new ConcurrentHashMap<>();
    
    public List<Method> getBenchmarkMethods(Class<?> clazz) {
        return BENCHMARK_METHODS_CACHE.computeIfAbsent(clazz, this::findBenchmarkMethods);
    }
    
    private List<Method> findBenchmarkMethods(Class<?> clazz) {
        return Arrays.stream(clazz.getDeclaredMethods())
                    .filter(method -> method.isAnnotationPresent(Benchmark.class))
                    .collect(Collectors.toList());
    }
}

// ❌ Плохо - каждый раз делаем reflection
public class SlowAnnotationProcessor {
    public void processBenchmarks(Object instance) {
        for (Method method : instance.getClass().getDeclaredMethods()) {
            if (method.isAnnotationPresent(Benchmark.class)) {
                // Обработка каждый раз через reflection
            }
        }
    }
}
```

### 3. Документирование аннотаций
```java
/**
 * Marks a method for performance benchmarking.
 * Methods annotated with @Benchmark will be automatically timed when executed
 * through the BenchmarkProcessor.
 * 
 * <p>Example usage:
 * <pre>
 * {@code
 * @Benchmark
 * public void expensiveOperation() {
 *     // operation to benchmark
 * }
 * }
 * </pre>
 * 
 * @since 1.0
 * @see BenchmarkProcessor
 */
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Benchmark {
}
```

---

## 🔗 Связанные темы
- [[Reflection-Code|Рефлексия]] - работа с аннотациями во время выполнения
- [[Best-Practices-Code|Лучшие практики]] - правильное использование аннотаций
- [[JUnit-Basics-Code|JUnit]] - тестовые аннотации
- [[OOP-Principles-Code|Принципы ООП]] - инкапсуляция метаданных