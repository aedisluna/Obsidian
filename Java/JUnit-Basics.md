# JUnit - Основы тестирования в Java
*Полное руководство по модульному тестированию с JUnit 5*

#java #junit #testing #тестирование #unit-tests

---

## 📋 Содержание
- [[#Введение в JUnit]]
- [[#Настройка проекта]]
- [[#Основные аннотации]]
- [[#Assertions - Утверждения]]
- [[#Жизненный цикл тестов]]
- [[#Параметризованные тесты]]
- [[#Практические примеры]]
- [[#Лучшие практики]]

---

## 🎯 Введение в JUnit

> [!info] JUnit - самый популярный фреймворк для модульного тестирования Java-приложений

### Что такое модульное тестирование?
- **Unit Testing** - тестирование отдельных компонентов программы
- **Автоматизация** - тесты выполняются автоматически
- **Быстрая обратная связь** - мгновенное обнаружение ошибок
- **Регрессионное тестирование** - проверка, что изменения не сломали существующий код

### Преимущества JUnit:
- ✅ **Простота использования** - минимум boilerplate кода
- ✅ **Богатый API** - множество методов для проверок
- ✅ **Интеграция с IDE** - поддержка в IntelliJ IDEA, Eclipse
- ✅ **Интеграция с системами сборки** - Maven, Gradle
- ✅ **Расширяемость** - можно создавать собственные расширения

---

## ⚙️ Настройка проекта

### Maven dependency
```xml
<dependencies>
    <!-- JUnit 5 -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.9.2</version>
        <scope>test</scope>
    </dependency>
    
    <!-- Для параметризованных тестов -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-params</artifactId>
        <version>5.9.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.0.0-M9</version>
        </plugin>
    </plugins>
</build>
```

### Gradle dependency
```gradle
dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter:5.9.2'
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}

test {
    useJUnitPlatform()
}
```

### Структура проекта
```
src/
├── main/
│   └── java/
│       └── com/example/
│           ├── Calculator.java
│           └── BankAccount.java
└── test/
    └── java/
        └── com/example/
            ├── CalculatorTest.java
            └── BankAccountTest.java
```

---

## 📝 Основные аннотации

### Базовые аннотации
```java
import org.junit.jupiter.api.*;
import static org.junit.jupiter.api.Assertions.*;

class BasicAnnotationsTest {
    
    @Test
    void shouldAddTwoNumbers() {
        // Базовый тест
        int result = 2 + 3;
        assertEquals(5, result);
    }
    
    @Test
    @DisplayName("Проверяет деление на ноль")
    void shouldThrowExceptionWhenDivideByZero() {
        Calculator calc = new Calculator();
        assertThrows(ArithmeticException.class, () -> calc.divide(10, 0));
    }
    
    @Test
    @Disabled("Тест временно отключен")
    void disabledTest() {
        fail("Этот тест не должен выполняться");
    }
    
    @BeforeEach
    void setUp() {
        System.out.println("Выполняется перед каждым тестом");
    }
    
    @AfterEach
    void tearDown() {
        System.out.println("Выполняется после каждого теста");
    }
    
    @BeforeAll
    static void setUpAll() {
        System.out.println("Выполняется один раз перед всеми тестами");
    }
    
    @AfterAll
    static void tearDownAll() {
        System.out.println("Выполняется один раз после всех тестов");
    }
}
```

### Условное выполнение тестов
```java
import org.junit.jupiter.api.condition.*;

class ConditionalTestsExample {
    
    @Test
    @EnabledOnOs(OS.WINDOWS)
    void shouldRunOnWindows() {
        // Выполняется только на Windows
    }
    
    @Test
    @DisabledOnOs(OS.MAC)
    void shouldNotRunOnMac() {
        // Не выполняется на Mac
    }
    
    @Test
    @EnabledOnJre(JRE.JAVA_17)
    void shouldRunOnJava17() {
        // Выполняется только на Java 17
    }
    
    @Test
    @EnabledIfSystemProperty(named = "env", matches = "test")
    void shouldRunInTestEnvironment() {
        // Выполняется если системное свойство env=test
    }
    
    @Test
    @EnabledIfEnvironmentVariable(named = "DEBUG", matches = "true")
    void shouldRunInDebugMode() {
        // Выполняется если переменная окружения DEBUG=true
    }
}
```

---

## ✅ Assertions - Утверждения

### Базовые утверждения
```java
import static org.junit.jupiter.api.Assertions.*;

class AssertionsTest {
    
    @Test
    void basicAssertions() {
        // Проверка равенства
        assertEquals(5, 2 + 3);
        assertEquals("Hello", "He" + "llo");
        assertEquals(3.14, Math.PI, 0.01); // с погрешностью
        
        // Проверка неравенства
        assertNotEquals(10, 2 + 3);
        
        // Проверка истинности/ложности
        assertTrue(5 > 3);
        assertFalse(5 < 3);
        
        // Проверка на null
        String str = null;
        assertNull(str);
        
        String nonNullStr = "Hello";
        assertNotNull(nonNullStr);
        
        // Проверка одинаковых объектов (same reference)
        String str1 = "Hello";
        String str2 = str1;
        assertSame(str1, str2);
        
        String str3 = new String("Hello");
        assertNotSame(str1, str3);
    }
    
    @Test
    void arrayAndCollectionAssertions() {
        int[] expected = {1, 2, 3, 4, 5};
        int[] actual = {1, 2, 3, 4, 5};
        
        // Проверка массивов
        assertArrayEquals(expected, actual);
        
        // Проверка коллекций
        List<String> expectedList = Arrays.asList("a", "b", "c");
        List<String> actualList = Arrays.asList("a", "b", "c");
        assertEquals(expectedList, actualList);
        
        // Проверка итерируемых объектов
        assertIterableEquals(expectedList, actualList);
    }
    
    @Test
    void exceptionAssertions() {
        Calculator calc = new Calculator();
        
        // Проверка что исключение выбрасывается
        ArithmeticException exception = assertThrows(
            ArithmeticException.class,
            () -> calc.divide(10, 0)
        );
        
        // Проверка сообщения исключения
        assertEquals("Division by zero", exception.getMessage());
        
        // Проверка что исключение НЕ выбрасывается
        assertDoesNotThrow(() -> calc.divide(10, 2));
    }
    
    @Test
    void timeoutAssertions() {
        // Проверка что метод выполняется за определенное время
        assertTimeout(Duration.ofSeconds(2), () -> {
            // Код, который должен выполниться за 2 секунды
            Thread.sleep(1000);
            return "Готово";
        });
        
        // Прерывает выполнение по таймауту
        assertTimeoutPreemptively(Duration.ofSeconds(1), () -> {
            Thread.sleep(500);
            return "Быстро";
        });
    }
    
    @Test
    void customMessageAssertions() {
        int expected = 10;
        int actual = 5;
        
        // Статическое сообщение
        assertEquals(expected, actual, "Числа должны быть равны");
        
        // Динамическое сообщение (вычисляется только при ошибке)
        assertEquals(expected, actual, 
            () -> "Ожидалось " + expected + ", но получено " + actual);
    }
    
    @Test
    void groupedAssertions() {
        Person person = new Person("Иван", "Петров", 30);
        
        // Все утверждения выполняются, даже если некоторые падают
        assertAll("person",
            () -> assertEquals("Иван", person.getFirstName()),
            () -> assertEquals("Петров", person.getLastName()),
            () -> assertEquals(30, person.getAge()),
            () -> assertNotNull(person.getEmail())
        );
    }
}
```

---

## 🔄 Жизненный цикл тестов

### Пример с полным жизненным циклом
```java
import org.junit.jupiter.api.*;
import java.util.ArrayList;
import java.util.List;

@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
class LifecycleTest {
    
    private static List<String> database;
    private Calculator calculator;
    
    @BeforeAll
    static void initializeDatabase() {
        System.out.println("🚀 Инициализация базы данных");
        database = new ArrayList<>();
        database.add("Начальные данные");
    }
    
    @AfterAll
    static void cleanUpDatabase() {
        System.out.println("🧹 Очистка базы данных");
        database = null;
    }
    
    @BeforeEach
    void setUp() {
        System.out.println("🔧 Подготовка к тесту");
        calculator = new Calculator();
    }
    
    @AfterEach
    void tearDown() {
        System.out.println("🔄 Очистка после теста");
        calculator = null;
    }
    
    @Test
    @Order(1)
    void firstTest() {
        System.out.println("📝 Выполняется первый тест");
        assertEquals(5, calculator.add(2, 3));
    }
    
    @Test
    @Order(2)
    void secondTest() {
        System.out.println("📝 Выполняется второй тест");
        assertEquals(6, calculator.multiply(2, 3));
    }
    
    @Nested
    @DisplayName("Тесты для математических операций")
    class MathOperationsTest {
        
        @BeforeEach
        void setUpNested() {
            System.out.println("🔧 Подготовка вложенного теста");
        }
        
        @Test
        @DisplayName("Сложение положительных чисел")
        void shouldAddPositiveNumbers() {
            assertEquals(8, calculator.add(3, 5));
        }
        
        @Test
        @DisplayName("Вычитание чисел")
        void shouldSubtractNumbers() {
            assertEquals(2, calculator.subtract(5, 3));
        }
    }
}
```

---

## 🎯 Параметризованные тесты

### Базовые параметризованные тесты
```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.*;

class ParameterizedTestsExample {
    
    @ParameterizedTest
    @ValueSource(ints = {1, 2, 3, 4, 5})
    void shouldBePositive(int number) {
        assertTrue(number > 0);
    }
    
    @ParameterizedTest
    @ValueSource(strings = {"hello", "world", "junit"})
    void shouldNotBeEmpty(String word) {
        assertFalse(word.isEmpty());
    }
    
    @ParameterizedTest
    @EnumSource(TimeUnit.class)
    void shouldBeValidTimeUnit(TimeUnit timeUnit) {
        assertNotNull(timeUnit);
    }
    
    @ParameterizedTest
    @NullSource
    @EmptySource
    @ValueSource(strings = {" ", "   ", "\\t", "\\n"})
    void shouldBeBlank(String input) {
        assertTrue(input == null || input.trim().isEmpty());
    }
    
    @ParameterizedTest
    @CsvSource({
        "1, 1, 2",
        "2, 3, 5", 
        "5, 7, 12",
        "-1, 1, 0"
    })
    void shouldAddNumbers(int a, int b, int expected) {
        Calculator calc = new Calculator();
        assertEquals(expected, calc.add(a, b));
    }
    
    @ParameterizedTest
    @CsvFileSource(resources = "/test-data.csv", numLinesToSkip = 1)
    void shouldProcessCsvFile(String name, int age, String email) {
        assertNotNull(name);
        assertTrue(age > 0);
        assertTrue(email.contains("@"));
    }
    
    @ParameterizedTest
    @MethodSource("provideStringsForIsBlank")
    void shouldCheckIfBlank(String input, boolean expected) {
        assertEquals(expected, input == null || input.trim().isEmpty());
    }
    
    static Stream<Arguments> provideStringsForIsBlank() {
        return Stream.of(
            Arguments.of(null, true),
            Arguments.of("", true),
            Arguments.of("  ", true),
            Arguments.of("not blank", false)
        );
    }
}
```

---

## 💼 Практические примеры

### Тестирование класса Calculator
```java
// Класс для тестирования
public class Calculator {
    
    public int add(int a, int b) {
        return a + b;
    }
    
    public int subtract(int a, int b) {
        return a - b;
    }
    
    public int multiply(int a, int b) {
        return a * b;
    }
    
    public double divide(int a, int b) {
        if (b == 0) {
            throw new ArithmeticException("Division by zero");
        }
        return (double) a / b;
    }
    
    public double sqrt(double number) {
        if (number < 0) {
            throw new IllegalArgumentException("Cannot calculate square root of negative number");
        }
        return Math.sqrt(number);
    }
    
    public boolean isPrime(int number) {
        if (number <= 1) return false;
        if (number <= 3) return true;
        if (number % 2 == 0 || number % 3 == 0) return false;
        
        for (int i = 5; i * i <= number; i += 6) {
            if (number % i == 0 || number % (i + 2) == 0) {
                return false;
            }
        }
        return true;
    }
}

// Тесты для Calculator
import org.junit.jupiter.api.*;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.*;
import static org.junit.jupiter.api.Assertions.*;

@DisplayName("Тесты калькулятора")
class CalculatorTest {
    
    private Calculator calculator;
    
    @BeforeEach
    void setUp() {
        calculator = new Calculator();
    }
    
    @Nested
    @DisplayName("Арифметические операции")
    class ArithmeticOperationsTest {
        
        @Test
        @DisplayName("Сложение двух положительных чисел")
        void shouldAddTwoPositiveNumbers() {
            assertEquals(5, calculator.add(2, 3));
        }
        
        @Test
        @DisplayName("Сложение положительного и отрицательного числа")
        void shouldAddPositiveAndNegativeNumber() {
            assertEquals(2, calculator.add(5, -3));
        }
        
        @ParameterizedTest(name = "{0} + {1} = {2}")
        @CsvSource({
            "0, 0, 0",
            "1, 2, 3",
            "-1, 1, 0",
            "100, -50, 50"
        })
        void shouldAddNumbers(int a, int b, int expected) {
            assertEquals(expected, calculator.add(a, b));
        }
        
        @Test
        @DisplayName("Вычитание чисел")
        void shouldSubtractNumbers() {
            assertAll("subtraction",
                () -> assertEquals(2, calculator.subtract(5, 3)),
                () -> assertEquals(-2, calculator.subtract(3, 5)),
                () -> assertEquals(0, calculator.subtract(5, 5))
            );
        }
        
        @Test
        @DisplayName("Умножение чисел")
        void shouldMultiplyNumbers() {
            assertAll("multiplication",
                () -> assertEquals(6, calculator.multiply(2, 3)),
                () -> assertEquals(0, calculator.multiply(0, 5)),
                () -> assertEquals(-10, calculator.multiply(-2, 5))
            );
        }
        
        @Test
        @DisplayName("Деление чисел")
        void shouldDivideNumbers() {
            assertEquals(2.5, calculator.divide(5, 2), 0.001);
            assertEquals(-2.0, calculator.divide(-6, 3), 0.001);
        }
        
        @Test
        @DisplayName("Деление на ноль должно выбрасывать исключение")
        void shouldThrowExceptionWhenDivideByZero() {
            ArithmeticException exception = assertThrows(
                ArithmeticException.class,
                () -> calculator.divide(10, 0),
                "Деление на ноль должно выбрасывать ArithmeticException"
            );
            
            assertEquals("Division by zero", exception.getMessage());
        }
    }
    
    @Nested
    @DisplayName("Специальные функции")
    class SpecialFunctionsTest {
        
        @ParameterizedTest
        @ValueSource(doubles = {0.0, 1.0, 4.0, 9.0, 16.0})
        void shouldCalculateSquareRoot(double input) {
            double expected = Math.sqrt(input);
            assertEquals(expected, calculator.sqrt(input), 0.001);
        }
        
        @Test
        @DisplayName("Квадратный корень из отрицательного числа")
        void shouldThrowExceptionForNegativeSquareRoot() {
            IllegalArgumentException exception = assertThrows(
                IllegalArgumentException.class,
                () -> calculator.sqrt(-1),
                "Квадратный корень из отрицательного числа должен выбрасывать исключение"
            );
            
            assertTrue(exception.getMessage().contains("negative number"));
        }
        
        @ParameterizedTest
        @CsvSource({
            "2, true",
            "3, true",
            "4, false",
            "5, true",
            "6, false",
            "7, true",
            "8, false",
            "9, false",
            "10, false",
            "11, true"
        })
        void shouldCheckIfPrime(int number, boolean expected) {
            assertEquals(expected, calculator.isPrime(number),
                () -> number + " должно быть " + (expected ? "простым" : "составным"));
        }
        
        @ParameterizedTest
        @ValueSource(ints = {-5, -1, 0, 1})
        void shouldReturnFalseForNonPrimeNumbers(int number) {
            assertFalse(calculator.isPrime(number),
                () -> number + " не должно быть простым числом");
        }
    }
}
```

### Тестирование класса BankAccount
```java
// Класс для тестирования
public class BankAccount {
    private String accountNumber;
    private double balance;
    private String ownerName;
    private boolean isActive;
    
    public BankAccount(String accountNumber, String ownerName) {
        if (accountNumber == null || accountNumber.trim().isEmpty()) {
            throw new IllegalArgumentException("Account number cannot be null or empty");
        }
        if (ownerName == null || ownerName.trim().isEmpty()) {
            throw new IllegalArgumentException("Owner name cannot be null or empty");
        }
        
        this.accountNumber = accountNumber;
        this.ownerName = ownerName;
        this.balance = 0.0;
        this.isActive = true;
    }
    
    public boolean deposit(double amount) {
        if (!isActive) {
            throw new IllegalStateException("Account is not active");
        }
        if (amount <= 0) {
            return false;
        }
        
        balance += amount;
        return true;
    }
    
    public boolean withdraw(double amount) {
        if (!isActive) {
            throw new IllegalStateException("Account is not active");
        }
        if (amount <= 0 || amount > balance) {
            return false;
        }
        
        balance -= amount;
        return true;
    }
    
    public double getBalance() {
        return balance;
    }
    
    public String getAccountNumber() {
        return accountNumber;
    }
    
    public String getOwnerName() {
        return ownerName;
    }
    
    public boolean isActive() {
        return isActive;
    }
    
    public void closeAccount() {
        isActive = false;
        balance = 0.0;
    }
}

// Тесты для BankAccount
@DisplayName("Тесты банковского счета")
class BankAccountTest {
    
    private BankAccount account;
    private final String ACCOUNT_NUMBER = "123456789";
    private final String OWNER_NAME = "Иван Петров";
    
    @BeforeEach
    void setUp() {
        account = new BankAccount(ACCOUNT_NUMBER, OWNER_NAME);
    }
    
    @Nested
    @DisplayName("Создание счета")
    class AccountCreationTest {
        
        @Test
        @DisplayName("Создание счета с валидными данными")
        void shouldCreateAccountWithValidData() {
            assertAll("account creation",
                () -> assertEquals(ACCOUNT_NUMBER, account.getAccountNumber()),
                () -> assertEquals(OWNER_NAME, account.getOwnerName()),
                () -> assertEquals(0.0, account.getBalance()),
                () -> assertTrue(account.isActive())
            );
        }
        
        @ParameterizedTest
        @NullAndEmptySource
        @ValueSource(strings = {"   ", "\\t", "\\n"})
        @DisplayName("Создание счета с невалидным номером")
        void shouldThrowExceptionForInvalidAccountNumber(String invalidAccountNumber) {
            IllegalArgumentException exception = assertThrows(
                IllegalArgumentException.class,
                () -> new BankAccount(invalidAccountNumber, OWNER_NAME)
            );
            
            assertTrue(exception.getMessage().contains("Account number"));
        }
        
        @ParameterizedTest
        @NullAndEmptySource
        @ValueSource(strings = {"   ", "\\t", "\\n"})
        @DisplayName("Создание счета с невалидным именем владельца")
        void shouldThrowExceptionForInvalidOwnerName(String invalidOwnerName) {
            IllegalArgumentException exception = assertThrows(
                IllegalArgumentException.class,
                () -> new BankAccount(ACCOUNT_NUMBER, invalidOwnerName)
            );
            
            assertTrue(exception.getMessage().contains("Owner name"));
        }
    }
    
    @Nested
    @DisplayName("Операции пополнения")
    class DepositOperationsTest {
        
        @ParameterizedTest
        @ValueSource(doubles = {100.0, 1000.0, 0.01, 999999.99})
        @DisplayName("Успешное пополнение счета")
        void shouldDepositValidAmount(double amount) {
            double initialBalance = account.getBalance();
            
            assertTrue(account.deposit(amount));
            assertEquals(initialBalance + amount, account.getBalance(), 0.001);
        }
        
        @ParameterizedTest
        @ValueSource(doubles = {-100.0, 0.0, -0.01})
        @DisplayName("Неуспешное пополнение на невалидную сумму")
        void shouldNotDepositInvalidAmount(double amount) {
            double initialBalance = account.getBalance();
            
            assertFalse(account.deposit(amount));
            assertEquals(initialBalance, account.getBalance(), 0.001);
        }
        
        @Test
        @DisplayName("Пополнение закрытого счета должно выбрасывать исключение")
        void shouldThrowExceptionWhenDepositToClosedAccount() {
            account.closeAccount();
            
            IllegalStateException exception = assertThrows(
                IllegalStateException.class,
                () -> account.deposit(100.0)
            );
            
            assertTrue(exception.getMessage().contains("not active"));
        }
    }
    
    @Nested
    @DisplayName("Операции снятия")
    class WithdrawalOperationsTest {
        
        @BeforeEach
        void setUpWithBalance() {
            account.deposit(1000.0); // Пополняем счет для тестов снятия
        }
        
        @ParameterizedTest
        @ValueSource(doubles = {100.0, 500.0, 1000.0})
        @DisplayName("Успешное снятие средств")
        void shouldWithdrawValidAmount(double amount) {
            double initialBalance = account.getBalance();
            
            assertTrue(account.withdraw(amount));
            assertEquals(initialBalance - amount, account.getBalance(), 0.001);
        }
        
        @ParameterizedTest
        @ValueSource(doubles = {1001.0, 2000.0, 9999.0})
        @DisplayName("Неуспешное снятие суммы больше баланса")
        void shouldNotWithdrawMoreThanBalance(double amount) {
            double initialBalance = account.getBalance();
            
            assertFalse(account.withdraw(amount));
            assertEquals(initialBalance, account.getBalance(), 0.001);
        }
        
        @ParameterizedTest
        @ValueSource(doubles = {-100.0, 0.0, -0.01})
        @DisplayName("Неуспешное снятие невалидной суммы")
        void shouldNotWithdrawInvalidAmount(double amount) {
            double initialBalance = account.getBalance();
            
            assertFalse(account.withdraw(amount));
            assertEquals(initialBalance, account.getBalance(), 0.001);
        }
        
        @Test
        @DisplayName("Снятие с закрытого счета должно выбрасывать исключение")
        void shouldThrowExceptionWhenWithdrawFromClosedAccount() {
            account.closeAccount();
            
            IllegalStateException exception = assertThrows(
                IllegalStateException.class,
                () -> account.withdraw(100.0)
            );
            
            assertTrue(exception.getMessage().contains("not active"));
        }
    }
    
    @Nested
    @DisplayName("Закрытие счета")
    class AccountClosureTest {
        
        @Test
        @DisplayName("Закрытие счета с балансом")
        void shouldCloseAccountWithBalance() {
            account.deposit(500.0);
            
            account.closeAccount();
            
            assertAll("closed account",
                () -> assertFalse(account.isActive()),
                () -> assertEquals(0.0, account.getBalance())
            );
        }
        
        @Test
        @DisplayName("Закрытие уже закрытого счета")
        void shouldHandleClosingAlreadyClosedAccount() {
            account.closeAccount();
            account.closeAccount(); // Повторное закрытие
            
            assertFalse(account.isActive());
        }
    }
}
```

---

## 💡 Лучшие практики

### Структура и именование тестов
```java
// ✅ Хорошие имена тестов
@Test
void shouldReturnTrueWhenPasswordIsValid() { }

@Test
void shouldThrowExceptionWhenEmailIsNull() { }

@Test
void shouldCalculateDiscountForPremiumCustomer() { }

// ❌ Плохие имена тестов
@Test
void testPassword() { }

@Test
void test1() { }

@Test 
void emailTest() { }
```

### Организация тестов
```java
// ✅ Используй AAA паттерн: Arrange, Act, Assert
@Test
void shouldCalculateTotalPrice() {
    // Arrange - подготовка данных
    ShoppingCart cart = new ShoppingCart();
    cart.addItem(new Item("Book", 29.99));
    cart.addItem(new Item("Pen", 1.50));
    
    // Act - выполнение действия
    double total = cart.calculateTotal();
    
    // Assert - проверка результата
    assertEquals(31.49, total, 0.01);
}

// ✅ Группируй связанные тесты с @Nested
@Nested
@DisplayName("Тесты валидации email")
class EmailValidationTest {
    
    @Test
    void shouldAcceptValidEmail() { }
    
    @Test 
    void shouldRejectInvalidEmail() { }
}
```

### Тестовые данные
```java
// ✅ Используй константы для тестовых данных
public class TestConstants {
    public static final String VALID_EMAIL = "test@example.com";
    public static final String INVALID_EMAIL = "invalid-email";
    public static final LocalDate TEST_DATE = LocalDate.of(2024, 1, 1);
}

// ✅ Создавай фабричные методы для тестовых объектов
public class TestDataFactory {
    
    public static User createValidUser() {
        return User.builder()
            .name("Test User")
            .email("test@example.com")
            .age(25)
            .build();
    }
    
    public static User createUserWithAge(int age) {
        return createValidUser().withAge(age);
    }
}
```

### Проверка исключений
```java
// ✅ Проверяй тип исключения и сообщение
@Test
void shouldThrowExceptionWithCorrectMessage() {
    IllegalArgumentException exception = assertThrows(
        IllegalArgumentException.class,
        () -> new User(null, "test@example.com")
    );
    
    assertEquals("Name cannot be null", exception.getMessage());
}

// ✅ Используй assertAll для множественных проверок
@Test
void shouldValidateUserData() {
    User user = new User("John", "john@example.com");
    
    assertAll("user validation",
        () -> assertNotNull(user.getName()),
        () -> assertTrue(user.getName().length() > 0),
        () -> assertTrue(user.getEmail().contains("@")),
        () -> assertNotNull(user.getCreatedAt())
    );
}
```

### Что НЕ стоит делать
```java
// ❌ Не тестируй несколько несвязанных вещей в одном тесте
@Test
void badTest() {
    // Тестирует и математику, и валидацию, и сохранение
    Calculator calc = new Calculator();
    assertEquals(4, calc.add(2, 2));
    assertThrows(Exception.class, () -> calc.divide(1, 0));
    
    User user = new User("John");
    userService.save(user);
    assertEquals(1, userRepository.count());
}

// ❌ Не используй System.out.println в тестах
@Test
void badTestWithPrinting() {
    System.out.println("Starting test..."); // ❌
    // тест
    System.out.println("Test completed"); // ❌
}

// ❌ Не делай тесты зависимыми друг от друга
static int counter = 0;

@Test
void firstTest() {
    counter = 1; // ❌ Изменяет глобальное состояние 
}

@Test
void secondTest() {
    assertEquals(1, counter); // ❌ Зависит от firstTest
}
```

---

## 🎯 Запуск тестов

### IntelliJ IDEA
- **Запуск одного теста**: `Ctrl+Shift+F10` (курсор на методе)
- **Запуск класса тестов**: `Ctrl+Shift+F10` (курсор на классе)  
- **Запуск всех тестов**: `Ctrl+Shift+F10` в папке `test`
- **Повторный запуск**: `Shift+F10`

### Командная строка
```bash
# Maven
mvn test                    # Все тесты
mvn test -Dtest=Calculator* # Тесты по маске
mvn test -Dtest=CalculatorTest#shouldAddNumbers # Конкретный тест

# Gradle  
./gradlew test              # Все тесты
./gradlew test --tests "*Calculator*" # Тесты по маске
./gradlew test --tests "CalculatorTest.shouldAddNumbers" # Конкретный тест
```

---

## 🔗 Связанные темы
- [[Mocking|Мокирование]] - использование Mockito для изоляции тестов
- [[TDD|Test-Driven Development]] - разработка через тестирование
- [[Test-Annotations|Аннотации тестов]] - дополнительные аннотации JUnit
- [[Best-Practices|Лучшие практики]] - общие рекомендации по тестированию