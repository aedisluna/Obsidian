# JUnit основы
*Unit тестирование в Java*

#java #junit #testing #unit-tests #tdd

---

## 📋 Содержание
- [[#Основы JUnit]]
- [[#Аннотации JUnit]]
- [[#Assertions (утверждения)]]
- [[#Жизненный цикл тестов]]
- [[#Parameterized тесты]]
- [[#Mocking с Mockito]]
- [[#Лучшие практики]]

---

## 🎯 Основы JUnit

> [!info] JUnit - это фреймворк для unit-тестирования Java приложений, который помогает писать и выполнять повторяемые тесты

### Зачем нужны тесты?
- ✅ **Обнаружение багов** - найти ошибки на раннем этапе
- ✅ **Документация** - тесты показывают, как должен работать код
- ✅ **Рефакторинг** - безопасное изменение кода
- ✅ **Регрессионное тестирование** - убедиться, что новые изменения не ломают существующий функционал
- ✅ **TDD** - разработка через тестирование

### Структура проекта с тестами
```
src/
├── main/
│   └── java/
│       └── com/example/
│           ├── Calculator.java
│           ├── User.java
│           └── UserService.java
└── test/
    └── java/
        └── com/example/
            ├── CalculatorTest.java
            ├── UserTest.java
            └── UserServiceTest.java
```

### Maven зависимости
```xml
<dependencies>
    <!-- JUnit 5 -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.10.0</version>
        <scope>test</scope>
    </dependency>
    
    <!-- Mockito для моков -->
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-core</artifactId>
        <version>5.5.0</version>
        <scope>test</scope>
    </dependency>
    
    <!-- Mockito JUnit Jupiter integration -->
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-junit-jupiter</artifactId>
        <version>5.5.0</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### Простой пример теста
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
            throw new IllegalArgumentException("Division by zero");
        }
        return (double) a / b;
    }
    
    public boolean isEven(int number) {
        return number % 2 == 0;
    }
}

// Тестовый класс
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class CalculatorTest {
    
    private final Calculator calculator = new Calculator();
    
    @Test
    public void testAdd() {
        // Given (Arrange)
        int a = 5;
        int b = 3;
        
        // When (Act)
        int result = calculator.add(a, b);
        
        // Then (Assert)
        assertEquals(8, result);
    }
    
    @Test
    public void testSubtract() {
        assertEquals(2, calculator.subtract(5, 3));
        assertEquals(-2, calculator.subtract(3, 5));
        assertEquals(0, calculator.subtract(5, 5));
    }
    
    @Test
    public void testMultiply() {
        assertEquals(15, calculator.multiply(5, 3));
        assertEquals(0, calculator.multiply(5, 0));
        assertEquals(-15, calculator.multiply(-5, 3));
    }
    
    @Test
    public void testDivide() {
        assertEquals(2.5, calculator.divide(5, 2), 0.001);
        assertEquals(1.0, calculator.divide(5, 5), 0.001);
    }
    
    @Test
    public void testDivideByZero() {
        // Проверяем, что выбрасывается исключение
        IllegalArgumentException exception = assertThrows(
            IllegalArgumentException.class,
            () -> calculator.divide(5, 0)
        );
        
        assertEquals("Division by zero", exception.getMessage());
    }
    
    @Test
    public void testIsEven() {
        assertTrue(calculator.isEven(4));
        assertTrue(calculator.isEven(0));
        assertFalse(calculator.isEven(3));
        assertFalse(calculator.isEven(-1));
    }
}
```

---

## 🏷️ Аннотации JUnit

### Основные аннотации JUnit 5
```java
import org.junit.jupiter.api.*;
import org.junit.jupiter.api.condition.*;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;
import java.time.Duration;

public class JUnitAnnotationsExample {
    
    private static StringBuilder output;
    
    // Выполняется один раз перед всеми тестами в классе
    @BeforeAll
    static void setUpBeforeClass() {
        output = new StringBuilder();
        System.out.println("@BeforeAll - настройка для всего класса");
    }
    
    // Выполняется один раз после всех тестов в классе
    @AfterAll
    static void tearDownAfterClass() {
        System.out.println("@AfterAll - очистка после всех тестов");
        System.out.println("Результат: " + output.toString());
    }
    
    // Выполняется перед каждым тестом
    @BeforeEach
    void setUp() {
        output.append("setup-");
        System.out.println("@BeforeEach - настройка перед тестом");
    }
    
    // Выполняется после каждого теста
    @AfterEach
    void tearDown() {
        output.append("-cleanup");
        System.out.println("@AfterEach - очистка после теста");
    }
    
    // Обычный тест
    @Test
    @DisplayName("Простой тест с красивым именем")
    void simpleTest() {
        output.append("test1");
        assertEquals(4, 2 + 2);
    }
    
    // Тест с тайм-аутом
    @Test
    @Timeout(2) // секунды
    void testWithTimeout() throws InterruptedException {
        output.append("timeout-test");
        Thread.sleep(1000); // Не должно превышать 2 секунды
        assertTrue(true);
    }
    
    // Тест с проверкой времени выполнения
    @Test
    void testExecutionTime() {
        assertTimeout(Duration.ofMillis(100), () -> {
            output.append("time-test");
            // Быстрая операция
            return "result";
        });
    }
    
    // Повторяющийся тест
    @RepeatedTest(value = 3, name = "Повтор {currentRepetition} из {totalRepetitions}")
    void repeatedTest() {
        output.append("repeat-");
        assertTrue(Math.random() >= 0); // Всегда true
    }
    
    // Условное выполнение тестов
    @Test
    @EnabledOnOs(OS.WINDOWS)
    @DisplayName("Только на Windows")
    void windowsOnlyTest() {
        output.append("windows");
        assertTrue(System.getProperty("os.name").toLowerCase().contains("windows"));
    }
    
    @Test
    @EnabledOnJre(JRE.JAVA_17)
    @DisplayName("Только на Java 17")
    void java17OnlyTest() {
        output.append("java17");
        assertTrue(true);
    }
    
    @Test
    @EnabledIfSystemProperty(named = "env", matches = "test")
    @DisplayName("Только если системное свойство env=test")
    void conditionalTest() {
        output.append("conditional");
        assertTrue(true);
    }
    
    @Test
    @EnabledIfEnvironmentVariable(named = "ENV", matches = "TEST")
    @DisplayName("Только если переменная окружения ENV=TEST")
    void environmentTest() {
        output.append("env");
        assertTrue(true);
    }
    
    // Отключенный тест
    @Test
    @Disabled("Пока не реализовано")
    void disabledTest() {
        output.append("disabled");
        fail("Этот тест не должен выполняться");
    }
    
    // Вложенные тесты для группировки
    @Nested
    @DisplayName("Группа математических операций")
    class MathOperations {
        
        @Test
        @DisplayName("Сложение положительных чисел")
        void addPositiveNumbers() {
            assertEquals(5, 2 + 3);
        }
        
        @Test
        @DisplayName("Сложение отрицательных чисел")
        void addNegativeNumbers() {
            assertEquals(-5, -2 + (-3));
        }
    }
    
    // Динамические тесты
    @TestFactory
    @DisplayName("Динамические тесты")
    Stream<DynamicTest> dynamicTests() {
        return Stream.of("apple", "banana", "cherry")
                    .map(fruit -> DynamicTest.dynamicTest(
                        "Тест для " + fruit,
                        () -> assertTrue(fruit.length() > 0)
                    ));
    }
    
    // Параметризованный тест
    @ParameterizedTest
    @ValueSource(ints = {1, 3, 5, 7, 9})
    @DisplayName("Проверка нечетных чисел")
    void testOddNumbers(int number) {
        assertTrue(number % 2 != 0);
    }
}
```

### Теги для группировки тестов
```java
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

public class TaggedTests {
    
    @Test
    @Tag("fast")
    @Tag("unit")
    void fastUnitTest() {
        // Быстрый unit тест
        assertEquals(4, 2 + 2);
    }
    
    @Test
    @Tag("slow")
    @Tag("integration")
    void slowIntegrationTest() throws InterruptedException {
        // Медленный интеграционный тест
        Thread.sleep(1000);
        assertTrue(true);
    }
    
    @Test
    @Tag("database")
    @Tag("integration")
    void databaseTest() {
        // Тест базы данных
        assertTrue(true);
    }
}

// Запуск только быстрых тестов:
// mvn test -Dgroups="fast"

// Запуск всех кроме медленных:
// mvn test -DexcludedGroups="slow"
```

---

## ✅ Assertions (утверждения)

### Базовые assertions
```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;
import java.time.Duration;
import java.util.*;

public class AssertionsExample {
    
    @Test
    void basicAssertions() {
        // Проверка равенства
        assertEquals(4, 2 + 2, "2 + 2 должно равняться 4");
        assertEquals(4, 2 + 2); // Без сообщения
        
        // Проверка неравенства
        assertNotEquals(5, 2 + 2);
        
        // Проверка истинности/ложности
        assertTrue(5 > 3, "5 должно быть больше 3");
        assertFalse(5 < 3, "5 не должно быть меньше 3");
        
        // Проверка null
        String nullString = null;
        String notNullString = "Hello";
        assertNull(nullString, "Строка должна быть null");
        assertNotNull(notNullString, "Строка не должна быть null");
        
        // Проверка того же объекта
        String str1 = "Hello";
        String str2 = str1;
        String str3 = new String("Hello");
        assertSame(str1, str2, "Должны быть одним объектом");
        assertNotSame(str1, str3, "Должны быть разными объектами");
    }
    
    @Test
    void arrayAssertions() {
        int[] expected = {1, 2, 3, 4, 5};
        int[] actual = {1, 2, 3, 4, 5};
        int[] different = {1, 2, 3, 4, 6};
        
        // Проверка массивов
        assertArrayEquals(expected, actual, "Массивы должны быть одинаковыми");
        
        // Проверка с tolerance для float/double
        double[] expectedDoubles = {1.0, 2.1, 3.14};
        double[] actualDoubles = {1.0, 2.1, 3.14};
        assertArrayEquals(expectedDoubles, actualDoubles, 0.01);
    }
    
    @Test
    void iterableAssertions() {
        List<String> expectedList = Arrays.asList("apple", "banana", "cherry");
        List<String> actualList = Arrays.asList("apple", "banana", "cherry");
        List<String> differentList = Arrays.asList("apple", "banana", "date");
        
        // Проверка итерируемых коллекций
        assertIterableEquals(expectedList, actualList, "Списки должны содержать одинаковые элементы в том же порядке");
        
        // Проверка содержания всех элементов (порядок не важен)
        List<String> actualUnordered = Arrays.asList("banana", "apple", "cherry");
        // Для этого нужна библиотека AssertJ или собственная логика
        assertTrue(actualUnordered.containsAll(expectedList) && 
                  expectedList.containsAll(actualUnordered));
    }
    
    @Test
    void exceptionAssertions() {
        // Проверка, что исключение выбрасывается
        ArithmeticException exception = assertThrows(
            ArithmeticException.class,
            () -> divide(10, 0),
            "Деление на ноль должно выбрасывать ArithmeticException"
        );
        
        assertEquals("/ by zero", exception.getMessage());
        
        // Проверка, что исключение НЕ выбрасывается
        assertDoesNotThrow(
            () -> divide(10, 2),
            "Нормальное деление не должно выбрасывать исключение"
        );
        
        // Проверка конкретного типа исключения с проверкой содержимого
        IllegalArgumentException illegalArg = assertThrows(
            IllegalArgumentException.class,
            () -> validateAge(-1)
        );
        
        assertTrue(illegalArg.getMessage().contains("negative"));
    }
    
    @Test
    void timeoutAssertions() {
        // Проверка, что операция выполняется в заданное время
        assertTimeout(Duration.ofMillis(500), () -> {
            Thread.sleep(100);
            return "Быстрая операция";
        });
        
        // Preemptive timeout - прерывает выполнение при превышении времени
        assertTimeoutPreemptively(Duration.ofMillis(200), () -> {
            Thread.sleep(100);
            return "Операция";
        });
    }
    
    @Test
    void complexAssertions() {
        Person person = new Person("John", "Doe", 30);
        
        // Группировка нескольких проверок
        assertAll("person",
            () -> assertEquals("John", person.getFirstName()),
            () -> assertEquals("Doe", person.getLastName()),
            () -> assertTrue(person.getAge() > 0),
            () -> assertTrue(person.getAge() < 150)
        );
        
        // Проверка с пользовательским сообщением
        assertEquals("John Doe", person.getFullName(), 
                    () -> "Полное имя должно быть 'John Doe', но было: " + person.getFullName());
    }
    
    @Test
    void conditionalAssertions() {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        
        // Проверка условий
        assertTrue(numbers.size() > 0, "Список не должен быть пустым");
        assertFalse(numbers.isEmpty(), "Список не должен быть пустым");
        
        // Проверка с предикатом
        assertTrue(numbers.stream().allMatch(n -> n > 0), 
                  "Все числа должны быть положительными");
        
        assertTrue(numbers.stream().anyMatch(n -> n % 2 == 0), 
                  "Должно быть хотя бы одно четное число");
        
        assertFalse(numbers.stream().noneMatch(n -> n > 3), 
                   "Должны быть числа больше 3");
    }
    
    // Вспомогательные методы
    private int divide(int a, int b) {
        return a / b;
    }
    
    private void validateAge(int age) {
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
    }
}

// Вспомогательный класс для тестов
class Person {
    private String firstName;
    private String lastName;
    private int age;
    
    public Person(String firstName, String lastName, int age) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
    }
    
    public String getFirstName() { return firstName; }
    public String getLastName() { return lastName; }
    public int getAge() { return age; }
    
    public String getFullName() {
        return firstName + " " + lastName;
    }
}
```

### Custom Assertions
```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class CustomAssertions {
    
    // Пользовательские assertion методы
    public static void assertEmail(String email) {
        assertNotNull(email, "Email не должен быть null");
        assertTrue(email.contains("@"), "Email должен содержать @");
        assertTrue(email.matches("^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$"), 
                  "Email должен иметь правильный формат");
    }
    
    public static void assertPhoneNumber(String phone) {
        assertNotNull(phone, "Телефон не должен быть null");
        assertTrue(phone.matches("\\+?[0-9\\-\\s\\(\\)]+"), 
                  "Телефон должен содержать только цифры и допустимые символы");
        assertTrue(phone.replaceAll("[^0-9]", "").length() >= 10, 
                  "Телефон должен содержать минимум 10 цифр");
    }
    
    public static void assertPassword(String password) {
        assertNotNull(password, "Пароль не должен быть null");
        assertTrue(password.length() >= 8, "Пароль должен быть минимум 8 символов");
        assertTrue(password.matches(".*[A-Z].*"), "Пароль должен содержать заглавные буквы");
        assertTrue(password.matches(".*[a-z].*"), "Пароль должен содержать строчные буквы");
        assertTrue(password.matches(".*[0-9].*"), "Пароль должен содержать цифры");
    }
    
    public static void assertRange(int value, int min, int max) {
        assertTrue(value >= min, String.format("Значение %d должно быть >= %d", value, min));
        assertTrue(value <= max, String.format("Значение %d должно быть <= %d", value, max));
    }
    
    @Test
    void testCustomAssertions() {
        // Тестируем email
        assertEmail("user@example.com");
        assertEmail("test.email+tag@domain.co.uk");
        
        // Тестируем некорректный email
        assertThrows(AssertionError.class, () -> assertEmail("invalid-email"));
        assertThrows(AssertionError.class, () -> assertEmail("user@"));
        assertThrows(AssertionError.class, () -> assertEmail("@domain.com"));
        
        // Тестируем телефон
        assertPhoneNumber("+7-123-456-78-90");
        assertPhoneNumber("8 (123) 456-78-90");
        assertPhoneNumber("1234567890");
        
        // Тестируем некорректный телефон
        assertThrows(AssertionError.class, () -> assertPhoneNumber("123")); // Слишком короткий
        assertThrows(AssertionError.class, () -> assertPhoneNumber("abc")); // Не цифры
        
        // Тестируем пароль
        assertPassword("MyPassword123");
        assertPassword("Strong#Pass1");
        
        // Тестируем некорректный пароль
        assertThrows(AssertionError.class, () -> assertPassword("weak")); // Слишком короткий
        assertThrows(AssertionError.class, () -> assertPassword("onlylowercase123")); // Нет заглавных
        assertThrows(AssertionError.class, () -> assertPassword("ONLYUPPERCASE123")); // Нет строчных
        assertThrows(AssertionError.class, () -> assertPassword("OnlyLetters")); // Нет цифр
        
        // Тестируем диапазон
        assertRange(50, 0, 100);
        assertRange(0, 0, 100);
        assertRange(100, 0, 100);
        
        // Тестируем выход за диапазон
        assertThrows(AssertionError.class, () -> assertRange(-1, 0, 100));
        assertThrows(AssertionError.class, () -> assertRange(101, 0, 100));
    }
}
```

---

## 🔄 Жизненный цикл тестов

### Порядок выполнения
```java
import org.junit.jupiter.api.*;

@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
public class TestLifecycleExample {
    
    private static int classCounter = 0;
    private int instanceCounter = 0;
    
    @BeforeAll
    static void initAll() {
        System.out.println("@BeforeAll - выполняется один раз перед всеми тестами");
        classCounter = 100;
    }
    
    @BeforeEach
    void init() {
        instanceCounter = 10;
        System.out.println("@BeforeEach - выполняется перед каждым тестом. Instance counter: " + instanceCounter);
    }
    
    @Test
    @Order(1)
    void firstTest() {
        System.out.println("Первый тест. Class counter: " + classCounter + ", Instance counter: " + instanceCounter);
        instanceCounter = 20;
        assertEquals(100, classCounter);
        assertEquals(10, instanceCounter); // instanceCounter был сброшен к 20, но assertion проверяет исходное значение
    }
    
    @Test
    @Order(2)
    void secondTest() {
        System.out.println("Второй тест. Class counter: " + classCounter + ", Instance counter: " + instanceCounter);
        // instanceCounter снова равен 10, так как создался новый экземпляр класса
        assertEquals(100, classCounter);
        assertEquals(10, instanceCounter);
    }
    
    @AfterEach
    void tearDown() {
        System.out.println("@AfterEach - выполняется после каждого теста. Instance counter: " + instanceCounter);
    }
    
    @AfterAll
    static void tearDownAll() {
        System.out.println("@AfterAll - выполняется один раз после всех тестов");
        classCounter = 0;
    }
}
```

### Управление состоянием между тестами
```java
import org.junit.jupiter.api.*;

// По умолчанию JUnit создает новый экземпляр класса для каждого теста
// Это можно изменить с помощью @TestInstance
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
public class SharedStateExample {
    
    private List<String> sharedList;
    
    // Теперь @BeforeAll может быть non-static
    @BeforeAll
    void setUp() {
        sharedList = new ArrayList<>();
        System.out.println("Инициализация общего состояния");
    }
    
    @Test
    @Order(1)
    void firstTest() {
        sharedList.add("first");
        assertEquals(1, sharedList.size());
        System.out.println("После первого теста: " + sharedList);
    }
    
    @Test
    @Order(2)
    void secondTest() {
        // Состояние сохраняется между тестами
        sharedList.add("second");
        assertEquals(2, sharedList.size());
        assertTrue(sharedList.contains("first"));
        System.out.println("После второго теста: " + sharedList);
    }
    
    @Test
    @Order(3)
    void thirdTest() {
        assertEquals(2, sharedList.size());
        assertTrue(sharedList.containsAll(Arrays.asList("first", "second")));
        System.out.println("В третьем тесте: " + sharedList);
    }
    
    @AfterAll
    void tearDown() {
        sharedList.clear();
        System.out.println("Очистка общего состояния");
    }
}
```

### Условное выполнение тестов
```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.condition.*;
import org.junit.jupiter.api.Assumptions;

public class ConditionalTestsExample {
    
    @Test
    @EnabledOnOs({OS.LINUX, OS.MAC})
    void unixOnlyTest() {
        System.out.println("Этот тест выполняется только на Unix системах");
        assertTrue(true);
    }
    
    @Test
    @EnabledOnOs(OS.WINDOWS)
    void windowsOnlyTest() {
        System.out.println("Этот тест выполняется только на Windows");
        assertTrue(true);
    }
    
    @Test
    @EnabledOnJre({JRE.JAVA_8, JRE.JAVA_11, JRE.JAVA_17})
    void javaVersionTest() {
        System.out.println("Тест для конкретных версий Java");
        assertTrue(true);
    }
    
    @Test
    @DisabledOnJre(JRE.JAVA_8)
    void notOnJava8() {
        System.out.println("Этот тест не выполняется на Java 8");
        assertTrue(true);
    }
    
    @Test
    @EnabledIfSystemProperty(named = "os.arch", matches = ".*64.*")
    void on64BitArchitectures() {
        System.out.println("Тест только для 64-битных архитектур");
        assertTrue(true);
    }
    
    @Test
    @EnabledIfEnvironmentVariable(named = "ENV", matches = "test")
    void onlyInTestEnvironment() {
        System.out.println("Тест только в тестовой среде");
        assertTrue(true);
    }
    
    @Test
    void dynamicAssumptions() {
        // Assumptions позволяют пропустить тест динамически
        Assumptions.assumeTrue("test".equals(System.getProperty("env")), 
                              "Пропускаем тест - не тестовая среда");
        
        // Этот код выполнится только если assumption истинно
        System.out.println("Выполняется только в тестовой среде");
        assertTrue(true);
    }
    
    @Test
    void assumptionsWithLambda() {
        Assumptions.assumingThat(
            System.getProperty("os.name").toLowerCase().contains("linux"),
            () -> {
                // Этот блок выполнится только на Linux
                System.out.println("Специальная логика для Linux");
                assertEquals("/", System.getProperty("file.separator"));
            }
        );
        
        // Этот код всегда выполняется
        System.out.println("Этот код выполняется на всех ОС");
        assertTrue(true);
    }
}
```

---

## 🎛️ Parameterized тесты

### Различные источники параметров
```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.*;
import java.util.stream.Stream;

public class ParameterizedTestsExample {
    
    // Простые значения
    @ParameterizedTest
    @ValueSource(strings = {"apple", "banana", "cherry"})
    void testStringLength(String fruit) {
        assertTrue(fruit.length() > 0);
        assertFalse(fruit.isEmpty());
    }
    
    @ParameterizedTest
    @ValueSource(ints = {1, 3, 5, 7, 9})
    void testOddNumbers(int number) {
        assertTrue(number % 2 != 0);
    }
    
    @ParameterizedTest
    @ValueSource(doubles = {1.0, 2.5, 3.14159, 100.0})
    void testPositiveDoubles(double value) {
        assertTrue(value > 0);
    }
    
    // Null и пустые значения
    @ParameterizedTest
    @NullSource
    void testNullValues(String input) {
        assertNull(input);
    }
    
    @ParameterizedTest
    @EmptySource
    void testEmptyValues(String input) {
        assertTrue(input.isEmpty());
    }
    
    @ParameterizedTest
    @NullAndEmptySource
    void testNullAndEmptyValues(String input) {
        assertTrue(input == null || input.isEmpty());
    }
    
    // Перечисления
    @ParameterizedTest
    @EnumSource(TimeUnit.class)
    void testTimeUnits(TimeUnit timeUnit) {
        assertNotNull(timeUnit);
        assertTrue(timeUnit.name().length() > 0);
    }
    
    @ParameterizedTest
    @EnumSource(value = TimeUnit.class, names = {"DAYS", "HOURS", "MINUTES"})
    void testSpecificTimeUnits(TimeUnit timeUnit) {
        assertTrue(timeUnit.toString().endsWith("S"));
    }
    
    // CSV значения
    @ParameterizedTest
    @CsvSource({
        "apple,     5",
        "banana,    6", 
        "cherry,    6",
        "date,      4"
    })
    void testFruitNameLength(String fruit, int expectedLength) {
        assertEquals(expectedLength, fruit.length());
    }
    
    @ParameterizedTest
    @CsvSource(value = {
        "1,1,2",
        "2,3,5",
        "5,7,12",
        "10,20,30"
    })
    void testAddition(int a, int b, int expected) {
        assertEquals(expected, a + b);
    }
    
    // CSV файл
    @ParameterizedTest
    @CsvFileSource(resources = "/test-data.csv", numLinesToSkip = 1)
    void testFromCsvFile(String name, int age, String country) {
        assertNotNull(name);
        assertTrue(age > 0);
        assertNotNull(country);
    }
    
    // Метод как источник данных
    @ParameterizedTest
    @MethodSource("provideStringsForIsBlank")
    void testIsBlank(String input, boolean expected) {
        assertEquals(expected, isBlank(input));
    }
    
    static Stream<Arguments> provideStringsForIsBlank() {
        return Stream.of(
            Arguments.of(null, true),
            Arguments.of("", true),
            Arguments.of("  ", true),
            Arguments.of("not blank", false),
            Arguments.of("\t", true),
            Arguments.of("\n", true)
        );
    }
    
    // Сложные объекты как параметры
    @ParameterizedTest
    @MethodSource("providePersonsForValidation")
    void testPersonValidation(Person person, boolean expectedValid) {
        assertEquals(expectedValid, isValidPerson(person));
    }
    
    static Stream<Arguments> providePersonsForValidation() {
        return Stream.of(
            Arguments.of(new Person("John", "Doe", 30), true),
            Arguments.of(new Person("", "Doe", 30), false),      // Пустое имя
            Arguments.of(new Person("John", "", 30), false),     // Пустая фамилия
            Arguments.of(new Person("John", "Doe", -5), false),  // Отрицательный возраст
            Arguments.of(new Person("John", "Doe", 0), false),   // Нулевой возраст
            Arguments.of(new Person("A", "B", 150), false)       // Слишком большой возраст
        );
    }
    
    // Кастомный источник аргументов
    @ParameterizedTest
    @ArgumentsSource(CustomArgumentsProvider.class)
    void testWithCustomProvider(String input, int length) {
        assertEquals(length, input.length());
    }
    
    // Именование параметризованных тестов
    @ParameterizedTest(name = "Тест #{index}: {0} имеет длину {1}")
    @CsvSource({
        "Java, 4",
        "Python, 6",
        "JavaScript, 10"
    })
    void testWithCustomName(String language, int expectedLength) {
        assertEquals(expectedLength, language.length());
    }
    
    @ParameterizedTest(name = "{0} -> valid: {1}")
    @MethodSource("provideEmailValidationData")
    void testEmailValidation(String email, boolean expectedValid) {
        assertEquals(expectedValid, isValidEmail(email));
    }
    
    static Stream<Arguments> provideEmailValidationData() {
        return Stream.of(
            Arguments.of("valid@example.com", true),
            Arguments.of("user.name@domain.co.uk", true),
            Arguments.of("invalid-email", false),
            Arguments.of("@domain.com", false),
            Arguments.of("user@", false),
            Arguments.of("", false),
            Arguments.of(null, false)
        );
    }
    
    // Вспомогательные методы
    private boolean isBlank(String input) {
        return input == null || input.trim().isEmpty();
    }
    
    private boolean isValidPerson(Person person) {
        return person != null && 
               !person.getFirstName().isEmpty() && 
               !person.getLastName().isEmpty() && 
               person.getAge() > 0 && 
               person.getAge() < 120;
    }
    
    private boolean isValidEmail(String email) {
        if (email == null || email.isEmpty()) {
            return false;
        }
        return email.matches("^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$");
    }
}

// Кастомный провайдер аргументов
class CustomArgumentsProvider implements ArgumentsProvider {
    
    @Override
    public Stream<? extends Arguments> provideArguments(ExtensionContext context) {
        return Stream.of(
            Arguments.of("Hello", 5),
            Arguments.of("World", 5),
            Arguments.of("Java", 4),
            Arguments.of("Testing", 7)
        );
    }
}

// Для примера test-data.csv:
/*
name,age,country
John,25,USA
Maria,30,Spain
Hiroshi,28,Japan
Anna,35,Germany
*/
```

---

## 🎭 Mocking с Mockito

### Основы мокирования
```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.InjectMocks;
import org.mockito.junit.jupiter.MockitoExtension;
import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

// Интерфейс для демонстрации
interface UserRepository {
    User findById(Long id);
    void save(User user);
    boolean existsById(Long id);
    void deleteById(Long id);
}

interface EmailService {
    void sendWelcomeEmail(String email);
    void sendPasswordResetEmail(String email);
}

// Сервис для тестирования
class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;
    
    public UserService(UserRepository userRepository, EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }
    
    public User createUser(String firstName, String lastName, String email) {
        if (firstName == null || firstName.trim().isEmpty()) {
            throw new IllegalArgumentException("First name is required");
        }
        
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("Valid email is required");
        }
        
        User user = new User(firstName, lastName, email);
        userRepository.save(user);
        emailService.sendWelcomeEmail(email);
        
        return user;
    }
    
    public User getUserById(Long id) {
        if (id == null || id <= 0) {
            throw new IllegalArgumentException("Valid user ID is required");
        }
        
        User user = userRepository.findById(id);
        if (user == null) {
            throw new RuntimeException("User not found: " + id);
        }
        
        return user;
    }
    
    public void deleteUser(Long id) {
        if (!userRepository.existsById(id)) {
            throw new RuntimeException("User not found: " + id);
        }
        
        userRepository.deleteById(id);
    }
}

@ExtendWith(MockitoExtension.class)
public class MockitoBasicsExample {
    
    @Mock
    private UserRepository userRepository;
    
    @Mock
    private EmailService emailService;
    
    @InjectMocks
    private UserService userService;
    
    @Test
    void testCreateUserSuccess() {
        // Given
        String firstName = "John";
        String lastName = "Doe";
        String email = "john@example.com";
        
        // When
        User result = userService.createUser(firstName, lastName, email);
        
        // Then
        assertNotNull(result);
        assertEquals(firstName, result.getFirstName());
        assertEquals(lastName, result.getLastName());
        assertEquals(email, result.getEmail());
        
        // Проверяем, что методы моков были вызваны
        verify(userRepository).save(any(User.class));
        verify(emailService).sendWelcomeEmail(email);
    }
    
    @Test
    void testCreateUserInvalidFirstName() {
        // When & Then
        IllegalArgumentException exception = assertThrows(
            IllegalArgumentException.class,
            () -> userService.createUser("", "Doe", "john@example.com")
        );
        
        assertEquals("First name is required", exception.getMessage());
        
        // Проверяем, что моки НЕ были вызваны
        verify(userRepository, never()).save(any(User.class));
        verify(emailService, never()).sendWelcomeEmail(anyString());
    }
    
    @Test
    void testGetUserById() {
        // Given
        Long userId = 1L;
        User expectedUser = new User("John", "Doe", "john@example.com");
        when(userRepository.findById(userId)).thenReturn(expectedUser);
        
        // When
        User result = userService.getUserById(userId);
        
        // Then
        assertSame(expectedUser, result);
        verify(userRepository).findById(userId);
    }
    
    @Test
    void testGetUserByIdNotFound() {
        // Given
        Long userId = 1L;
        when(userRepository.findById(userId)).thenReturn(null);
        
        // When & Then
        RuntimeException exception = assertThrows(
            RuntimeException.class,
            () -> userService.getUserById(userId)
        );
        
        assertTrue(exception.getMessage().contains("User not found"));
    }
    
    @Test
    void testDeleteUser() {
        // Given
        Long userId = 1L;
        when(userRepository.existsById(userId)).thenReturn(true);
        
        // When
        userService.deleteUser(userId);
        
        // Then
        verify(userRepository).existsById(userId);
        verify(userRepository).deleteById(userId);
    }
    
    @Test
    void testDeleteUserNotFound() {
        // Given
        Long userId = 1L;
        when(userRepository.existsById(userId)).thenReturn(false);
        
        // When & Then
        RuntimeException exception = assertThrows(
            RuntimeException.class,
            () -> userService.deleteUser(userId)
        );
        
        assertTrue(exception.getMessage().contains("User not found"));
        verify(userRepository, never()).deleteById(userId);
    }
}
```

### Продвинутые техники мокирования
```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;
import org.mockito.MockedStatic;
import org.mockito.ArgumentCaptor;
import static org.mockito.Mockito.*;

public class AdvancedMockitoExample {
    
    private UserRepository userRepository;
    private EmailService emailService;
    private UserService userService;
    
    @BeforeEach
    void setUp() {
        userRepository = mock(UserRepository.class);
        emailService = mock(EmailService.class);
        userService = new UserService(userRepository, emailService);
    }
    
    @Test
    void testWithArgumentCaptor() {
        // Given
        ArgumentCaptor<User> userCaptor = ArgumentCaptor.forClass(User.class);
        ArgumentCaptor<String> emailCaptor = ArgumentCaptor.forClass(String.class);
        
        // When
        userService.createUser("John", "Doe", "john@example.com");
        
        // Then
        verify(userRepository).save(userCaptor.capture());
        verify(emailService).sendWelcomeEmail(emailCaptor.capture());
        
        User capturedUser = userCaptor.getValue();
        String capturedEmail = emailCaptor.getValue();
        
        assertEquals("John", capturedUser.getFirstName());
        assertEquals("Doe", capturedUser.getLastName());
        assertEquals("john@example.com", capturedUser.getEmail());
        assertEquals("john@example.com", capturedEmail);
    }
    
    @Test
    void testWithAnswer() {
        // Given - мок возвращает пользователя с ID, установленным в callback
        when(userRepository.save(any(User.class))).thenAnswer(invocation -> {
            User user = invocation.getArgument(0);
            user.setId(123L); // Имитируем установку ID базой данных
            return user;
        });
        
        // When
        User result = userService.createUser("John", "Doe", "john@example.com");
        
        // Then
        assertNotNull(result.getId());
        assertEquals(123L, result.getId());
    }
    
    @Test
    void testWithException() {
        // Given
        when(userRepository.save(any(User.class)))
            .thenThrow(new RuntimeException("Database connection failed"));
        
        // When & Then
        RuntimeException exception = assertThrows(
            RuntimeException.class,
            () -> userService.createUser("John", "Doe", "john@example.com")
        );
        
        assertEquals("Database connection failed", exception.getMessage());
        
        // Проверяем, что email не отправился из-за исключения
        verify(emailService, never()).sendWelcomeEmail(anyString());
    }
    
    @Test
    void testMultipleInvocations() {
        // Given - разные значения при повторных вызовах
        when(userRepository.findById(1L))
            .thenReturn(new User("John", "Doe", "john@example.com"))
            .thenReturn(new User("John", "Smith", "john.smith@example.com"))
            .thenThrow(new RuntimeException("Too many calls"));
        
        // When & Then
        User firstCall = userService.getUserById(1L);
        assertEquals("Doe", firstCall.getLastName());
        
        User secondCall = userService.getUserById(1L);
        assertEquals("Smith", secondCall.getLastName());
        
        assertThrows(RuntimeException.class, () -> userService.getUserById(1L));
    }
    
    @Test
    void testVerifyingInteractions() {
        // Given
        User user = new User("John", "Doe", "john@example.com");
        
        // When
        userService.createUser("John", "Doe", "john@example.com");
        
        // Then - различные способы верификации
        verify(userRepository).save(user); // Точное совпадение
        verify(userRepository).save(argThat(u -> "John".equals(u.getFirstName()))); // С предикатом
        verify(emailService).sendWelcomeEmail(eq("john@example.com")); // С матчером
        
        // Проверка количества вызовов
        verify(userRepository, times(1)).save(any(User.class));
        verify(emailService, atLeastOnce()).sendWelcomeEmail(anyString());
        verify(emailService, atMost(1)).sendWelcomeEmail(anyString());
        
        // Проверка порядка вызовов
        InOrder inOrder = inOrder(userRepository, emailService);
        inOrder.verify(userRepository).save(any(User.class));
        inOrder.verify(emailService).sendWelcomeEmail(anyString());
    }
    
    @Test
    void testSpying() {
        // Given - создаем spy (частичный мок) реального объекта
        List<String> realList = new ArrayList<>();
        List<String> spyList = spy(realList);
        
        // When - часть методов работает как обычно, часть - мокается
        spyList.add("one");
        spyList.add("two");
        
        when(spyList.size()).thenReturn(100); // Мокаем размер
        
        // Then
        assertEquals(100, spyList.size()); // Мокированное значение
        assertEquals("one", spyList.get(0)); // Реальное поведение
        assertEquals("two", spyList.get(1)); // Реальное поведение
        
        verify(spyList).add("one");
        verify(spyList).add("two");
    }
    
    @Test
    void testStaticMocking() {
        // Given - мокаем статический метод
        try (MockedStatic<LocalDateTime> mockedLocalDateTime = mockStatic(LocalDateTime.class)) {
            LocalDateTime fixedDateTime = LocalDateTime.of(2023, 12, 1, 10, 30);
            mockedLocalDateTime.when(LocalDateTime::now).thenReturn(fixedDateTime);
            
            // When
            LocalDateTime result = LocalDateTime.now();
            
            // Then
            assertEquals(fixedDateTime, result);
            mockedLocalDateTime.verify(LocalDateTime::now);
        }
        
        // После блока try статический метод работает нормально
        LocalDateTime normalDateTime = LocalDateTime.now();
        assertNotNull(normalDateTime);
    }
    
    @Test
    void testVoidMethodsWithDoNothing() {
        // Given - настраиваем void метод
        doNothing().when(emailService).sendWelcomeEmail(anyString());
        
        // Или выбрасываем исключение
        doThrow(new RuntimeException("Email service down"))
            .when(emailService).sendWelcomeEmail("bad@example.com");
        
        // When & Then
        assertDoesNotThrow(() -> 
            userService.createUser("John", "Doe", "john@example.com"));
        
        assertThrows(RuntimeException.class, () -> 
            userService.createUser("Bad", "User", "bad@example.com"));
    }
}
```

---

## 🏆 Лучшие практики

### Структура и организация тестов
```java
import org.junit.jupiter.api.*;

public class TestBestPracticesExample {
    
    // ✅ Хорошие практики именования
    @Test
    @DisplayName("Should calculate total price when items are added to cart")
    void shouldCalculateTotalPriceWhenItemsAreAddedToCart() {
        // Given-When-Then структура
        
        // Given (Arrange) - настройка начальных условий
        ShoppingCart cart = new ShoppingCart();
        Product laptop = new Product("Laptop", 1000.0);
        Product mouse = new Product("Mouse", 25.0);
        
        // When (Act) - выполнение тестируемого действия
        cart.addItem(laptop, 1);
        cart.addItem(mouse, 2);
        double totalPrice = cart.getTotalPrice();
        
        // Then (Assert) - проверка результата
        assertEquals(1050.0, totalPrice, 0.01);
    }
    
    // ✅ Тест одного поведения
    @Test
    void shouldThrowExceptionWhenDivisionByZero() {
        Calculator calculator = new Calculator();
        
        ArithmeticException exception = assertThrows(
            ArithmeticException.class,
            () -> calculator.divide(10, 0)
        );
        
        assertEquals("Division by zero", exception.getMessage());
    }
    
    // ✅ Группировка связанных тестов
    @Nested
    @DisplayName("User validation tests")
    class UserValidationTests {
        
        private UserValidator validator;
        
        @BeforeEach
        void setUp() {
            validator = new UserValidator();
        }
        
        @Test
        @DisplayName("Should accept valid user")
        void shouldAcceptValidUser() {
            User user = new User("John", "Doe", "john@example.com", 25);
            
            ValidationResult result = validator.validate(user);
            
            assertTrue(result.isValid());
            assertTrue(result.getErrors().isEmpty());
        }
        
        @Test
        @DisplayName("Should reject user with invalid email")
        void shouldRejectUserWithInvalidEmail() {
            User user = new User("John", "Doe", "invalid-email", 25);
            
            ValidationResult result = validator.validate(user);
            
            assertFalse(result.isValid());
            assertTrue(result.getErrors().contains("Invalid email format"));
        }
        
        @Test
        @DisplayName("Should reject user with negative age")
        void shouldRejectUserWithNegativeAge() {
            User user = new User("John", "Doe", "john@example.com", -5);
            
            ValidationResult result = validator.validate(user);
            
            assertFalse(result.isValid());
            assertTrue(result.getErrors().contains("Age must be positive"));
        }
    }
    
    // ✅ Тестирование граничных случаев
    @ParameterizedTest
    @DisplayName("Should handle boundary values correctly")
    @ValueSource(ints = {Integer.MIN_VALUE, -1, 0, 1, Integer.MAX_VALUE})
    void shouldHandleBoundaryValues(int value) {
        NumberClassifier classifier = new NumberClassifier();
        
        // Не должно выбрасывать исключений
        assertDoesNotThrow(() -> classifier.classify(value));
    }
    
    // ✅ Использование Test Data Builders для сложных объектов
    @Test
    void shouldProcessComplexOrder() {
        // Используем паттерн Builder для создания тестовых данных
        Order order = OrderTestDataBuilder.anOrder()
                .withCustomer("John Doe")
                .withItem("Laptop", 1000.0, 1)
                .withItem("Mouse", 25.0, 2)
                .withShippingAddress("123 Main St, City")
                .withExpressDelivery()
                .build();
        
        OrderProcessor processor = new OrderProcessor();
        
        ProcessingResult result = processor.process(order);
        
        assertTrue(result.isSuccessful());
        assertEquals(1050.0, result.getTotalAmount(), 0.01);
        assertTrue(result.isExpressDelivery());
    }
    
    // ✅ Тестирование асинхронного кода
    @Test
    @Timeout(5) // Максимум 5 секунд
    void shouldHandleAsyncOperation() throws Exception {
        AsyncService asyncService = new AsyncService();
        
        CompletableFuture<String> future = asyncService.processAsync("test data");
        
        // Ждем завершения с timeout
        String result = future.get(3, TimeUnit.SECONDS);
        
        assertEquals("Processed: test data", result);
    }
    
    // ✅ Правильное использование моков
    @Test
    void shouldSendNotificationWhenOrderIsProcessed(@Mock EmailService emailService,
                                                   @Mock OrderRepository orderRepository) {
        // Given
        Order order = new Order("customer@example.com", "Product", 100.0);
        OrderService orderService = new OrderService(orderRepository, emailService);
        
        when(orderRepository.save(order)).thenReturn(order);
        
        // When
        orderService.processOrder(order);
        
        // Then
        verify(orderRepository).save(order);
        verify(emailService).sendOrderConfirmation(eq("customer@example.com"), any(Order.class));
    }
    
    // ❌ Анти-паттерны которых нужно избегать
    
    @Test
    void badTest_DoesntFollowGWT() {
        // Плохо: смешивание arrange, act и assert
        Calculator calc = new Calculator();
        assertEquals(4, calc.add(2, 2)); // act и assert в одной строке
        assertEquals(6, calc.add(2, 4)); // еще один тест в том же методе
        assertTrue(calc.isEven(4)); // тестирование разного поведения
    }
    
    @Test 
    void badTest_TooManyAssertions() {
        User user = new User("John", "Doe", "john@example.com", 25);
        
        // Плохо: слишком много проверок в одном тесте
        assertEquals("John", user.getFirstName());
        assertEquals("Doe", user.getLastName());
        assertEquals("john@example.com", user.getEmail());
        assertEquals(25, user.getAge());
        assertTrue(user.isAdult());
        assertFalse(user.isSenior());
        assertEquals("John Doe", user.getFullName());
        // ... еще 10 проверок
    }
    
    @Test
    void badTest_HardCodedValues() {
        Order order = new Order();
        order.setId(12345L); // Плохо: магические числа
        order.setTotal(999.99); // Плохо: хардкод значений
        
        assertTrue(order.getId() > 0);
    }
}

// Вспомогательные классы для примеров

class OrderTestDataBuilder {
    private String customer;
    private List<OrderItem> items = new ArrayList<>();
    private String shippingAddress;
    private boolean expressDelivery = false;
    
    public static OrderTestDataBuilder anOrder() {
        return new OrderTestDataBuilder();
    }
    
    public OrderTestDataBuilder withCustomer(String customer) {
        this.customer = customer;
        return this;
    }
    
    public OrderTestDataBuilder withItem(String name, double price, int quantity) {
        items.add(new OrderItem(name, price, quantity));
        return this;
    }
    
    public OrderTestDataBuilder withShippingAddress(String address) {
        this.shippingAddress = address;
        return this;
    }
    
    public OrderTestDataBuilder withExpressDelivery() {
        this.expressDelivery = true;
        return this;
    }
    
    public Order build() {
        Order order = new Order();
        order.setCustomer(customer);
        order.setItems(items);
        order.setShippingAddress(shippingAddress);
        order.setExpressDelivery(expressDelivery);
        return order;
    }
}

// Дополнительные утилиты для тестирования

class TestUtils {
    
    // Утилита для создания временных файлов
    public static Path createTempFile(String content) throws IOException {
        Path tempFile = Files.createTempFile("test", ".txt");
        Files.write(tempFile, content.getBytes());
        // Удаляется автоматически при завершении JVM
        tempFile.toFile().deleteOnExit();
        return tempFile;
    }
    
    // Утилита для проверки JSON
    public static void assertJsonEquals(String expected, String actual) {
        // Простая реализация, в реальности лучше использовать JSONAssert
        assertEquals(
            expected.replaceAll("\\s+", ""), 
            actual.replaceAll("\\s+", "")
        );
    }
    
    // Утилита для ожидания асинхронных операций
    public static <T> T waitForResult(Supplier<T> supplier, Duration timeout) {
        long endTime = System.currentTimeMillis() + timeout.toMillis();
        
        while (System.currentTimeMillis() < endTime) {
            T result = supplier.get();
            if (result != null) {
                return result;
            }
            
            try {
                Thread.sleep(50);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                throw new RuntimeException("Interrupted while waiting", e);
            }
        }
        
        throw new AssertionError("Timeout waiting for result");
    }
}

// Пример использования утилит в тестах
public class TestUtilitiesExample {
    
    @Test
    void testFileProcessing() throws IOException {
        // Given
        String content = "line1\nline2\nline3";
        Path tempFile = TestUtils.createTempFile(content);
        FileProcessor processor = new FileProcessor();
        
        // When
        ProcessingResult result = processor.processFile(tempFile);
        
        // Then
        assertEquals(3, result.getLineCount());
        assertTrue(Files.exists(tempFile));
    }
    
    @Test
    void testAsyncProcessing() {
        // Given
        AsyncProcessor processor = new AsyncProcessor();
        processor.startProcessing();
        
        // When & Then - ждем результат с timeout
        ProcessingStatus status = TestUtils.waitForResult(
            processor::getStatus, 
            Duration.ofSeconds(5)
        );
        
        assertEquals(ProcessingStatus.COMPLETED, status);
    }
}
```

### TDD (Test-Driven Development) пример
```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;
import static org.junit.jupiter.api.Assertions.*;

// TDD Цикл: Red -> Green -> Refactor

public class TDDExample {
    
    private BankAccount account;
    
    @BeforeEach
    void setUp() {
        account = new BankAccount();
    }
    
    // Шаг 1: RED - пишем тест, который не проходит
    @Test
    void shouldReturnZeroBalanceForNewAccount() {
        assertEquals(0.0, account.getBalance(), 0.01);
    }
    
    // Шаг 2: GREEN - пишем минимальный код для прохождения теста
    // (BankAccount с методом getBalance() возвращающим 0)
    
    // Шаг 3: Следующий тест
    @Test
    void shouldIncreaseBalanceAfterDeposit() {
        account.deposit(100.0);
        assertEquals(100.0, account.getBalance(), 0.01);
    }
    
    @Test
    void shouldDecreaseBalanceAfterWithdrawal() {
        account.deposit(100.0);
        account.withdraw(30.0);
        assertEquals(70.0, account.getBalance(), 0.01);
    }
    
    @Test
    void shouldThrowExceptionForNegativeDeposit() {
        IllegalArgumentException exception = assertThrows(
            IllegalArgumentException.class,
            () -> account.deposit(-10.0)
        );
        assertEquals("Amount must be positive", exception.getMessage());
    }
    
    @Test
    void shouldThrowExceptionForWithdrawalExceedingBalance() {
        account.deposit(50.0);
        
        IllegalArgumentException exception = assertThrows(
            IllegalArgumentException.class,
            () -> account.withdraw(100.0)
        );
        assertEquals("Insufficient funds", exception.getMessage());
    }
    
    @Test
    void shouldMaintainTransactionHistory() {
        account.deposit(100.0);
        account.withdraw(30.0);
        account.deposit(20.0);
        
        List<Transaction> history = account.getTransactionHistory();
        
        assertEquals(3, history.size());
        assertEquals(TransactionType.DEPOSIT, history.get(0).getType());
        assertEquals(100.0, history.get(0).getAmount(), 0.01);
        assertEquals(TransactionType.WITHDRAWAL, history.get(1).getType());
        assertEquals(30.0, history.get(1).getAmount(), 0.01);
    }
}

// Реализация после TDD (упрощенная)
class BankAccount {
    private double balance = 0.0;
    private List<Transaction> transactions = new ArrayList<>();
    
    public double getBalance() {
        return balance;
    }
    
    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
        balance += amount;
        transactions.add(new Transaction(TransactionType.DEPOSIT, amount));
    }
    
    public void withdraw(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
        if (amount > balance) {
            throw new IllegalArgumentException("Insufficient funds");
        }
        balance -= amount;
        transactions.add(new Transaction(TransactionType.WITHDRAWAL, amount));
    }
    
    public List<Transaction> getTransactionHistory() {
        return new ArrayList<>(transactions);
    }
}

enum TransactionType {
    DEPOSIT, WITHDRAWAL
}

class Transaction {
    private final TransactionType type;
    private final double amount;
    private final LocalDateTime timestamp;
    
    public Transaction(TransactionType type, double amount) {
        this.type = type;
        this.amount = amount;
        this.timestamp = LocalDateTime.now();
    }
    
    public TransactionType getType() { return type; }
    public double getAmount() { return amount; }
    public LocalDateTime getTimestamp() { return timestamp; }
}
```

---

## 🔗 Связанные темы
- [[Best-Practices-Code|Лучшие практики]] - качественное тестирование
- [[Exception-Handling-Code|Обработка исключений]] - тестирование исключений
- [[Lambda-Expressions-Code|Lambda выражения]] - в тестах и assertions
- [[Collections-Overview|Collections Framework]] - тестирование коллекций