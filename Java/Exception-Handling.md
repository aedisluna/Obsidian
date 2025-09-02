# Обработка исключений в Java
*Полное руководство по Exception Handling*

#java #exceptions #try-catch #error-handling #исключения

---

## 📋 Содержание
- [[#🎯 Введение в исключения]]
- [[#Иерархия исключений]]
- [[#Try-Catch-Finally]]
- [[#Типы исключений]]
- [[#Создание собственных исключений]]
- [[#Best Practices]]
- [[#Практические примеры]]

---

## 🎯 Введение в исключения

> [!info] Исключение (Exception) - это событие, которое нарушает нормальный ход выполнения программы

### Зачем нужна обработка исключений?
- **Предотвращение краха программы** - корректная обработка ошибок
- **Разделение логики** - отделение обработки ошибок от основной логики
- **Информативность** - подробная информация об ошибке
- **Восстановление** - возможность продолжить работу после ошибки

### Ключевые понятия:
- **Выбрасывание** (`throw`) - создание исключения
- **Перехват** (`catch`) - обработка исключения
- **Распространение** - передача исключения вверх по стеку вызовов
- **Обработчик** - блок кода для обработки исключения

---

## 🌳 Иерархия исключений

```
                    Throwable
                        |
          ┌─────────────┴─────────────┐
          |                         |
        Error                   Exception
          |                         |
    OutOfMemoryError       ┌────────┴────────┐
    StackOverflowError     |                 |
    VirtualMachineError    |           RuntimeException
                           |                 |
                    IOException       NullPointerException
                    SQLException      IllegalArgumentException
                    ClassNotFoundException  ArrayIndexOutOfBoundsException
                    InterruptedException    ArithmeticException
                                           IllegalStateException
```

### Основные категории:

#### 1. **Error** - системные ошибки
```java
// Примеры Error (обычно не обрабатываются)
OutOfMemoryError        // Нехватка памяти
StackOverflowError      // Переполнение стека
VirtualMachineError     // Ошибка JVM
```

#### 2. **Checked Exceptions** - проверяемые исключения
```java
// Примеры Checked Exceptions (обязательна обработка)
IOException             // Ошибки ввода/вывода
SQLException           // Ошибки базы данных
ClassNotFoundException // Класс не найден
InterruptedException   // Прерывание потока
```

#### 3. **Unchecked Exceptions** - непроверяемые исключения
```java
// Примеры Unchecked Exceptions (необязательна обработка)
NullPointerException      // Обращение к null
ArithmeticException       // Арифметические ошибки
IllegalArgumentException  // Неверные аргументы
ArrayIndexOutOfBoundsException // Выход за границы массива
```

---

## 🛠️ Try-Catch-Finally

### Базовый синтаксис
```java
try {
    // Код, который может выбросить исключение
    // Основная логика
} catch (SpecificException e) {
    // Обработка конкретного исключения
} catch (Exception e) {
    // Обработка любого другого исключения
} finally {
    // Код, который выполняется всегда
    // Освобождение ресурсов
}
```

### Простой пример
```java
public class BasicExceptionHandling {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 4, 5};
        
        try {
            System.out.println("Начинаем обработку массива");
            
            // Потенциально опасный код
            int result = numbers[10]; // ArrayIndexOutOfBoundsException
            System.out.println("Элемент: " + result);
            
            int division = 10 / 0; // ArithmeticException
            System.out.println("Результат деления: " + division);
            
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Ошибка: выход за границы массива");
            System.out.println("Детали: " + e.getMessage());
        } catch (ArithmeticException e) {
            System.out.println("Ошибка: арифметическая ошибка");
            System.out.println("Детали: " + e.getMessage());
        } catch (Exception e) {
            System.out.println("Неожиданная ошибка: " + e.getClass().getSimpleName());
            System.out.println("Сообщение: " + e.getMessage());
        } finally {
            System.out.println("Блок finally всегда выполняется");
        }
        
        System.out.println("Программа продолжает работу");
    }
}
```

### Try-with-resources (Java 7+)
```java
import java.io.*;
import java.util.Scanner;

public class TryWithResourcesExample {
    
    // Автоматическое закрытие ресурсов
    public static void readFile(String filename) {
        try (FileInputStream fis = new FileInputStream(filename);
             Scanner scanner = new Scanner(fis)) {
            
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
            
        } catch (FileNotFoundException e) {
            System.out.println("Файл не найден: " + filename);
        } catch (IOException e) {
            System.out.println("Ошибка чтения файла: " + e.getMessage());
        }
        // Ресурсы автоматически закрываются
    }
    
    // Множественные ресурсы
    public static void copyFile(String source, String destination) {
        try (FileInputStream input = new FileInputStream(source);
             FileOutputStream output = new FileOutputStream(destination);
             BufferedInputStream bis = new BufferedInputStream(input);
             BufferedOutputStream bos = new BufferedOutputStream(output)) {
            
            byte[] buffer = new byte[1024];
            int bytesRead;
            
            while ((bytesRead = bis.read(buffer)) != -1) {
                bos.write(buffer, 0, bytesRead);
            }
            
            System.out.println("Файл успешно скопирован");
            
        } catch (IOException e) {
            System.out.println("Ошибка копирования: " + e.getMessage());
        }
    }
}
```

### Множественный catch (Java 7+)
```java
public class MultiCatchExample {
    
    public static void processData(String data) {
        try {
            // Различные операции, которые могут выбросить разные исключения
            int number = Integer.parseInt(data);
            int result = 100 / number;
            String substring = data.substring(10);
            
        } catch (NumberFormatException | ArithmeticException e) {
            // Обработка нескольких типов исключений одинаково
            System.out.println("Ошибка в числовых операциях: " + e.getMessage());
            
        } catch (StringIndexOutOfBoundsException e) {
            System.out.println("Ошибка работы со строкой: " + e.getMessage());
            
        } catch (Exception e) {
            System.out.println("Общая ошибка: " + e.getMessage());
        }
    }
}
```

---

## 📊 Типы исключений

### Checked Exceptions
> [!warning] Checked исключения должны быть обработаны или объявлены в throws

```java
import java.io.*;
import java.sql.*;

public class CheckedExceptionsExample {
    
    // Обработка IOException
    public String readFileContent(String filename) {
        StringBuilder content = new StringBuilder();
        
        try (BufferedReader reader = new BufferedReader(new FileReader(filename))) {
            String line;
            while ((line = reader.readLine()) != null) {
                content.append(line).append("\\n");
            }
        } catch (IOException e) {
            System.err.println("Не удалось прочитать файл: " + e.getMessage());
            return null;
        }
        
        return content.toString();
    }
    
    // Передача исключения вызывающему методу
    public void connectToDatabase() throws SQLException, ClassNotFoundException {
        // Может выбросить SQLException
        Connection conn = DriverManager.getConnection(
            "jdbc:mysql://localhost:3306/testdb", "user", "password"
        );
        
        // Может выбросить ClassNotFoundException
        Class.forName("com.mysql.cj.jdbc.Driver");
    }
    
    // Использование метода с throws
    public void useDatabase() {
        try {
            connectToDatabase();
            System.out.println("Подключение к БД успешно");
        } catch (SQLException e) {
            System.err.println("Ошибка БД: " + e.getMessage());
        } catch (ClassNotFoundException e) {
            System.err.println("Драйвер не найден: " + e.getMessage());
        }
    }
}
```

### Unchecked Exceptions
> [!tip] Unchecked исключения можно не обрабатывать, но лучше предусмотреть обработку

```java
import java.util.*;

public class UncheckedExceptionsExample {
    
    public static void demonstrateCommonExceptions() {
        
        // NullPointerException
        try {
            String str = null;
            int length = str.length(); // NPE
        } catch (NullPointerException e) {
            System.out.println("NPE: Попытка вызвать метод у null объекта");
        }
        
        // ArrayIndexOutOfBoundsException
        try {
            int[] array = {1, 2, 3};
            int value = array[5]; // AIOOBE
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("AIOOBE: Выход за границы массива");
        }
        
        // IllegalArgumentException
        try {
            Thread.sleep(-1000); // Отрицательное время
        } catch (IllegalArgumentException e) {
            System.out.println("IAE: Неверный аргумент");
        } catch (InterruptedException e) {
            System.out.println("Поток был прерван");
        }
        
        // NumberFormatException
        try {
            int number = Integer.parseInt("not_a_number");
        } catch (NumberFormatException e) {
            System.out.println("NFE: Не удалось преобразовать строку в число");
        }
        
        // ClassCastException
        try {
            Object obj = "Hello";
            Integer number = (Integer) obj; // CCE
        } catch (ClassCastException e) {
            System.out.println("CCE: Неверное приведение типов");
        }
    }
    
    // Безопасные методы с проверками
    public static String safeSubstring(String str, int start, int end) {
        if (str == null) {
            throw new IllegalArgumentException("Строка не может быть null");
        }
        if (start < 0 || end > str.length() || start > end) {
            throw new IllegalArgumentException("Неверные границы подстроки");
        }
        
        return str.substring(start, end);
    }
    
    public static int safeDivision(int dividend, int divisor) {
        if (divisor == 0) {
            throw new ArithmeticException("Деление на ноль невозможно");
        }
        return dividend / divisor;
    }
}
```

---

## 🎨 Создание собственных исключений

### Простые пользовательские исключения
```java
// Checked исключение
class InsufficientFundsException extends Exception {
    private double deficit;
    
    public InsufficientFundsException(double deficit) {
        super("Недостаточно средств. Не хватает: " + deficit);
        this.deficit = deficit;
    }
    
    public InsufficientFundsException(String message, double deficit) {
        super(message);
        this.deficit = deficit;
    }
    
    public double getDeficit() {
        return deficit;
    }
}

// Unchecked исключение
class InvalidEmailException extends RuntimeException {
    private String email;
    
    public InvalidEmailException(String email) {
        super("Неверный формат email: " + email);
        this.email = email;
    }
    
    public InvalidEmailException(String message, String email, Throwable cause) {
        super(message, cause);
        this.email = email;
    }
    
    public String getEmail() {
        return email;  
    }
}

// Использование пользовательских исключений
class BankAccount {
    private double balance;
    private String email;
    
    public BankAccount(double initialBalance, String email) {
        if (initialBalance < 0) {
            throw new IllegalArgumentException("Начальный баланс не может быть отрицательным");
        }
        this.balance = initialBalance;
        setEmail(email);
    }
    
    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount <= 0) {
            throw new IllegalArgumentException("Сумма должна быть положительной");
        }
        
        if (amount > balance) {
            double deficit = amount - balance;
            throw new InsufficientFundsException(deficit);
        }
        
        balance -= amount;
        System.out.println("Снято: " + amount + ". Остаток: " + balance);
    }
    
    public void setEmail(String email) {
        if (email == null || !email.contains("@") || !email.contains(".")) {
            throw new InvalidEmailException(email);
        }
        this.email = email;
    }
    
    public double getBalance() {
        return balance;
    }
}
```

### Продвинутые пользовательские исключения
```java
// Базовый класс для исключений приложения
abstract class ApplicationException extends Exception {
    private final String errorCode;
    private final long timestamp;
    
    public ApplicationException(String errorCode, String message) {
        super(message);
        this.errorCode = errorCode;
        this.timestamp = System.currentTimeMillis();
    }
    
    public ApplicationException(String errorCode, String message, Throwable cause) {
        super(message, cause);
        this.errorCode = errorCode;
        this.timestamp = System.currentTimeMillis();
    }
    
    public String getErrorCode() {
        return errorCode;
    }
    
    public long getTimestamp() {
        return timestamp;
    }
    
    @Override
    public String toString() {
        return String.format("%s [%s]: %s (время: %d)", 
                           getClass().getSimpleName(), errorCode, getMessage(), timestamp);
    }
}

// Конкретные исключения для бизнес-логики
class UserNotFoundException extends ApplicationException {
    private final String userId;
    
    public UserNotFoundException(String userId) {
        super("USER_NOT_FOUND", "Пользователь не найден: " + userId);
        this.userId = userId;
    }
    
    public String getUserId() {
        return userId;
    }
}

class ValidationException extends ApplicationException {
    private final Map<String, String> fieldErrors;
    
    public ValidationException(Map<String, String> fieldErrors) {
        super("VALIDATION_ERROR", "Ошибки валидации: " + fieldErrors.size() + " полей");
        this.fieldErrors = new HashMap<>(fieldErrors);
    }
    
    public Map<String, String> getFieldErrors() {
        return new HashMap<>(fieldErrors);
    }
}

// Сервис с обработкой исключений
class UserService {
    private Map<String, String> users = new HashMap<>();
    
    public String getUser(String userId) throws UserNotFoundException {
        if (!users.containsKey(userId)) {
            throw new UserNotFoundException(userId);
        }
        return users.get(userId);
    }
    
    public void createUser(String userId, String name, String email) throws ValidationException {
        Map<String, String> errors = new HashMap<>();
        
        if (userId == null || userId.trim().isEmpty()) {
            errors.put("userId", "ID пользователя не может быть пустым");
        }
        
        if (name == null || name.trim().length() < 2) {
            errors.put("name", "Имя должно содержать минимум 2 символа");
        }
        
        if (email == null || !email.matches("^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$")) {
            errors.put("email", "Неверный формат email");
        }
        
        if (!errors.isEmpty()) {
            throw new ValidationException(errors);
        }
        
        users.put(userId, name);
    }
}
```

---

## 💼 Практические примеры

### Файловый менеджер с обработкой исключений
```java
import java.io.*;
import java.nio.file.*;
import java.util.List;

public class FileManager {
    
    public static class FileOperationException extends Exception {
        public FileOperationException(String message, Throwable cause) {
            super(message, cause);
        }
    }
    
    /**
     * Безопасное чтение файла
     */
    public static String readFileContent(String filePath) throws FileOperationException {
        try {
            return Files.readString(Paths.get(filePath));
        } catch (IOException e) {
            throw new FileOperationException("Не удалось прочитать файл: " + filePath, e);
        } catch (OutOfMemoryError e) {
            throw new FileOperationException("Файл слишком большой для чтения: " + filePath, e);
        }
    }
    
    /**
     * Безопасная запись в файл
     */
    public static void writeFileContent(String filePath, String content) throws FileOperationException {
        try {
            Path path = Paths.get(filePath);
            
            // Создаем директории если они не существуют
            Files.createDirectories(path.getParent());
            
            Files.writeString(path, content);
            System.out.println("Файл успешно записан: " + filePath);
            
        } catch (IOException e) {
            throw new FileOperationException("Не удалось записать файл: " + filePath, e);
        }
    }
    
    /**
     * Безопасное копирование файла
     */
    public static void copyFile(String sourcePath, String destPath) throws FileOperationException {
        try {
            Path source = Paths.get(sourcePath);
            Path dest = Paths.get(destPath);
            
            if (!Files.exists(source)) {
                throw new FileOperationException("Исходный файл не существует: " + sourcePath, null);
            }
            
            // Создаем директории для назначения
            Files.createDirectories(dest.getParent());
            
            Files.copy(source, dest, StandardCopyOption.REPLACE_EXISTING);
            System.out.println("Файл скопирован: " + sourcePath + " -> " + destPath);
            
        } catch (IOException e) {
            throw new FileOperationException("Ошибка копирования файла", e);
        }
    }
    
    /**
     * Получение списка файлов в директории
     */
    public static List<String> listFiles(String directoryPath) throws FileOperationException {
        try {
            Path dir = Paths.get(directoryPath);
            
            if (!Files.exists(dir)) {
                throw new FileOperationException("Директория не существует: " + directoryPath, null);
            }
            
            if (!Files.isDirectory(dir)) {
                throw new FileOperationException("Путь не является директорией: " + directoryPath, null);
            }
            
            return Files.list(dir)
                    .filter(Files::isRegularFile)
                    .map(Path::getFileName)
                    .map(Path::toString)
                    .sorted()
                    .toList();
                    
        } catch (IOException e) {
            throw new FileOperationException("Ошибка чтения директории: " + directoryPath, e);
        }
    }
    
    // Демонстрация использования
    public static void main(String[] args) {
        String testFile = "test.txt";
        String copyFile = "test_copy.txt";
        String testDir = "files";
        
        try {
            // Запись файла
            writeFileContent(testFile, "Hello, World!\\nЭто тестовый файл.");
            
            // Чтение файла
            String content = readFileContent(testFile);
            System.out.println("Содержимое файла:\\n" + content);
            
            // Копирование файла
            copyFile(testFile, copyFile);
            
            // Список файлов в текущей директории
            List<String> files = listFiles(".");
            System.out.println("Файлы в текущей директории:");
            files.forEach(file -> System.out.println("- " + file));
            
        } catch (FileOperationException e) {
            System.err.println("Ошибка файловой операции: " + e.getMessage());
            if (e.getCause() != null) {
                System.err.println("Причина: " + e.getCause().getMessage());
            }
        }
    }
}
```

### Калькулятор с комплексной обработкой ошибок
```java
import java.util.Stack;
import java.util.regex.Pattern;

public class SafeCalculator {
    
    // Пользовательские исключения
    public static class CalculatorException extends Exception {
        public CalculatorException(String message) {
            super(message);
        }
        
        public CalculatorException(String message, Throwable cause) {
            super(message, cause);
        }
    }
    
    public static class DivisionByZeroException extends CalculatorException {
        public DivisionByZeroException() {
            super("Деление на ноль недопустимо");
        }
    }
    
    public static class InvalidExpressionException extends CalculatorException {
        public InvalidExpressionException(String expression) {
            super("Неверное выражение: " + expression);
        }
    }
    
    public static class OverflowException extends CalculatorException {
        public OverflowException(String operation) {
            super("Переполнение при выполнении операции: " + operation);
        }
    }
    
    /**
     * Безопасное деление
     */
    public static double safeDivide(double dividend, double divisor) throws DivisionByZeroException {
        if (Math.abs(divisor) < 1e-10) { // Учитываем погрешности с плавающей точкой
            throw new DivisionByZeroException();
        }
        return dividend / divisor;
    }
    
    /**
     * Безопасное сложение с проверкой переполнения
     */
    public static long safeAdd(long a, long b) throws OverflowException {
        try {
            return Math.addExact(a, b);
        } catch (ArithmeticException e) {
            throw new OverflowException("сложение " + a + " + " + b);
        }
    }
    
    /**
     * Безопасное умножение с проверкой переполнения
     */
    public static long safeMultiply(long a, long b) throws OverflowException {
        try {
            return Math.multiplyExact(a, b);
        } catch (ArithmeticException e) {
            throw new OverflowException("умножение " + a + " * " + b);
        }
    }
    
    /**
     * Вычисление квадратного корня
     */
    public static double safeSqrt(double number) throws CalculatorException {
        if (number < 0) {
            throw new CalculatorException("Квадратный корень из отрицательного числа: " + number);
        }
        return Math.sqrt(number);
    }
    
    /**
     * Вычисление факториала
     */
    public static long factorial(int n) throws CalculatorException {
        if (n < 0) {
            throw new CalculatorException("Факториал отрицательного числа: " + n);
        }
        if (n > 20) {
            throw new OverflowException("факториал " + n + " (слишком большое число)");
        }
        
        long result = 1;
        try {
            for (int i = 2; i <= n; i++) {
                result = safeMultiply(result, i);
            }
        } catch (OverflowException e) {
            throw new OverflowException("факториал " + n);
        }
        
        return result;
    }
    
    /**
     * Парсинг и вычисление простого арифметического выражения
     */
    public static double evaluate(String expression) throws CalculatorException {
        if (expression == null || expression.trim().isEmpty()) {
            throw new InvalidExpressionException("пустое выражение");
        }
        
        // Убираем пробелы
        expression = expression.replaceAll("\\s+", "");
        
        // Проверяем на допустимые символы
        if (!Pattern.matches("[0-9+\\-*/().]+", expression)) {
            throw new InvalidExpressionException(expression);
        }
        
        try {
            return evaluateExpression(expression);
        } catch (Exception e) {
            if (e instanceof CalculatorException) {
                throw e;
            }
            throw new InvalidExpressionException(expression);
        }
    }
    
    // Простая реализация вычисления выражения (стековый алгоритм)
    private static double evaluateExpression(String expr) throws CalculatorException {
        Stack<Double> numbers = new Stack<>();
        Stack<Character> operators = new Stack<>();
        
        for (int i = 0; i < expr.length(); i++) {
            char ch = expr.charAt(i);
            
            if (Character.isDigit(ch) || ch == '.') {
                // Парсинг числа
                StringBuilder sb = new StringBuilder();
                while (i < expr.length() && (Character.isDigit(expr.charAt(i)) || expr.charAt(i) == '.')) {
                    sb.append(expr.charAt(i++));
                }
                i--; // Корректировка индекса
                
                try {
                    numbers.push(Double.parseDouble(sb.toString()));
                } catch (NumberFormatException e) {
                    throw new InvalidExpressionException("неверное число: " + sb.toString());
                }
                
            } else if (ch == '(') {
                operators.push(ch);
                
            } else if (ch == ')') {
                while (!operators.isEmpty() && operators.peek() != '(') {
                    numbers.push(applyOperation(operators.pop(), numbers.pop(), numbers.pop()));
                }
                if (!operators.isEmpty()) {
                    operators.pop(); // Убираем '('  
                }
                
            } else if (ch == '+' || ch == '-' || ch == '*' || ch == '/') {
                while (!operators.isEmpty() && hasPrecedence(ch, operators.peek())) {
                    numbers.push(applyOperation(operators.pop(), numbers.pop(), numbers.pop()));
                }
                operators.push(ch);
            }
        }
        
        // Применяем оставшиеся операции
        while (!operators.isEmpty()) {
            numbers.push(applyOperation(operators.pop(), numbers.pop(), numbers.pop()));
        }
        
        if (numbers.size() != 1) {
            throw new InvalidExpressionException("неверная структура выражения");
        }
        
        return numbers.pop();
    }
    
    private static boolean hasPrecedence(char op1, char op2) {
        if (op2 == '(' || op2 == ')') return false;
        if ((op1 == '*' || op1 == '/') && (op2 == '+' || op2 == '-')) return false;
        return true;
    }
    
    private static double applyOperation(char operator, double b, double a) throws CalculatorException {
        switch (operator) {
            case '+': return a + b;
            case '-': return a - b;
            case '*': return a * b;
            case '/': return safeDivide(a, b);
            default: throw new InvalidExpressionException("неизвестный оператор: " + operator);
        }
    }
    
    // Демонстрация использования
    public static void main(String[] args) {
        String[] testExpressions = {
            "2 + 3 * 4",
            "(2 + 3) * 4",
            "10 / 0",
            "sqrt(16)",
            "10 / 2.5",
            "invalid_expression",
            ""
        };
        
        for (String expr : testExpressions) {
            try {
                double result = evaluate(expr);
                System.out.printf("'%s' = %.2f%n", expr, result);
                
            } catch (DivisionByZeroException e) {
                System.err.printf("'%s': Ошибка - %s%n", expr, e.getMessage());
                
            } catch (InvalidExpressionException e) {
                System.err.printf("'%s': Неверное выражение - %s%n", expr, e.getMessage());
                
            } catch (OverflowException e) {
                System.err.printf("'%s': Переполнение - %s%n", expr, e.getMessage());
                
            } catch (CalculatorException e) {
                System.err.printf("'%s': Ошибка вычисления - %s%n", expr, e.getMessage());
            }
        }
        
        // Демонстрация других методов
        System.out.println("\\n=== Дополнительные функции ===");
        
        try {
            System.out.println("sqrt(25) = " + safeSqrt(25));
            System.out.println("factorial(5) = " + factorial(5));
            System.out.println("safeAdd(Long.MAX_VALUE, 1):");
            safeAdd(Long.MAX_VALUE, 1);
            
        } catch (CalculatorException e) {
            System.err.println("Ошибка: " + e.getMessage());
        }
    }
}
```

---

## 💡 Best Practices

### 1. Выбор типа исключения
```java
// ✅ Используй checked исключения для ошибок, от которых можно восстановиться
public void saveToFile(String data) throws IOException {
    // IOException - checked, потому что можно попробовать другой файл
}

// ✅ Используй unchecked исключения для программных ошибок
public void processUser(User user) {
    if (user == null) {
        throw new IllegalArgumentException("User cannot be null"); // unchecked
    }
}

// ❌ Не используй checked исключения для программных ошибок
public void validateAge(int age) throws InvalidAgeException { // плохо, если unchecked
    if (age < 0) throw new InvalidAgeException("Age cannot be negative");
}
```

### 2. Создание информативных сообщений
```java
// ✅ Подробные сообщения об ошибках
public void transfer(Account from, Account to, double amount) throws InsufficientFundsException {
    if (from.getBalance() < amount) {
        throw new InsufficientFundsException(
            String.format("Недостаточно средств для перевода %.2f с счета %s. Доступно: %.2f",
                         amount, from.getNumber(), from.getBalance())
        );
    }
}

// ❌ Неинформативные сообщения
public void transfer(Account from, Account to, double amount) throws Exception {
    if (from.getBalance() < amount) {
        throw new Exception("Error"); // плохо
    }
}
```

### 3. Обработка исключений
```java
// ✅ Специфичная обработка разных типов исключений
public void processFile(String filename) {
    try {
        String content = Files.readString(Paths.get(filename));
        processContent(content);
        
    } catch (FileNotFoundException e) {
        logger.warn("Файл не найден: {}. Создаю новый файл.", filename);
        createDefaultFile(filename);
        
    } catch (AccessDeniedException e) {
        logger.error("Нет доступа к файлу: {}", filename);
        notifyAdmin("Access denied for file: " + filename);
        
    } catch (IOException e) {
        logger.error("Ошибка чтения файла: {}", filename, e);
        throw new ServiceException("Cannot process file", e);
    }
}

// ❌ Поглощение исключений
public void badProcessFile(String filename) {
    try {
        String content = Files.readString(Paths.get(filename));
        processContent(content);
    } catch (Exception e) {
        // Плохо - исключение игнорируется
    }
}
```

### 4. Освобождение ресурсов
```java
// ✅ Используй try-with-resources
public void processFile(String filename) throws IOException {
    try (BufferedReader reader = Files.newBufferedReader(Paths.get(filename))) {
        reader.lines()
              .forEach(this::processLine);
    } // Ресурс автоматически закрывается
}

// ✅ Или используй finally блок
public void processFileOldWay(String filename) throws IOException {
    BufferedReader reader = null;
    try {
        reader = Files.newBufferedReader(Paths.get(filename));
        reader.lines().forEach(this::processLine);
        
    } finally {
        if (reader != null) {
            try {
                reader.close();
            } catch (IOException e) {
                logger.warn("Не удалось закрыть файл", e);
            }
        }
    }
}
```

### 5. Логирование исключений
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class ServiceWithLogging {
    private static final Logger logger = LoggerFactory.getLogger(ServiceWithLogging.class);
    
    // ✅ Правильное логирование
    public void processData(String data) throws ServiceException {
        try {
            doProcessing(data);
            
        } catch (ValidationException e) {
            logger.warn("Ошибка валидации данных: {}", e.getMessage());
            throw e; // Перебрасываем для обработки на верхнем уровне
            
        } catch (Exception e) {
            logger.error("Неожиданная ошибка при обработке данных: {}", data, e);
            throw new ServiceException("Processing failed", e);
        }
    }
    
    // ✅ Логирование с контекстом
    public void processUserData(String userId, String data) {
        try {
            validateUser(userId);
            processData(data);
            logger.info("Данные пользователя {} успешно обработаны", userId);
            
        } catch (UserNotFoundException e) {
            logger.warn("Пользователь не найден: {}", userId);
            
        } catch (ServiceException e) {
            logger.error("Ошибка обработки данных пользователя {}: {}", userId, e.getMessage(), e);
        }
    }
}
```

### 6. Цепочка исключений
```java
// ✅ Сохраняй оригинальное исключение в cause
public void highlevelOperation() throws ServiceException {
    try {
        lowLevelOperation();
    } catch (SQLException e) {
        // Оборачиваем в исключение верхнего уровня, но сохраняем первоначальную причину
        throw new ServiceException("Database operation failed", e);
    }
}

// ✅ Анализ цепочки исключений
public void analyzeException(Exception e) {
    logger.error("Основное исключение: {}", e.getMessage());
    
    Throwable cause = e.getCause();
    int level = 1;
    while (cause != null) {
        logger.error("Причина уровня {}: {}", level++, cause.getMessage());
        cause = cause.getCause();
    }
}
```

---

## 🚀 Продвинутые техники

### Функциональная обработка исключений
```java
import java.util.Optional;
import java.util.function.Function;
import java.util.function.Supplier;

public class FunctionalExceptionHandling {
    
    // Функциональный интерфейс для операций, которые могут выбросить исключение
    @FunctionalInterface
    public interface ThrowingSupplier<T> {
        T get() throws Exception;
    }
    
    @FunctionalInterface
    public interface ThrowingFunction<T, R> {
        R apply(T t) throws Exception;
    }
    
    // Обертка для безопасного выполнения операций
    public static <T> Optional<T> tryGet(ThrowingSupplier<T> supplier) {
        try {
            return Optional.ofNullable(supplier.get());
        } catch (Exception e) {
            logger.warn("Операция завершилась с ошибкой", e);
            return Optional.empty();
        }
    }
    
    // Обертка с обработчиком ошибок
    public static <T> T tryGet(ThrowingSupplier<T> supplier, T defaultValue) {
        try {
            return supplier.get();
        } catch (Exception e) {
            logger.warn("Операция завершилась с ошибкой, используется значение по умолчанию", e);
            return defaultValue;
        }
    }
    
    // Обертка с кастомным обработчиком
    public static <T> T tryGet(ThrowingSupplier<T> supplier, Function<Exception, T> errorHandler) {
        try {
            return supplier.get();
        } catch (Exception e) {
            return errorHandler.apply(e);
        }
    }
    
    // Примеры использования
    public static void demonstrateFunctionalApproach() {
        // Безопасное чтение файла с Optional
        Optional<String> content = tryGet(() -> Files.readString(Paths.get("config.txt")));
        content.ifPresentOrElse(
            System.out::println,
            () -> System.out.println("Файл не найден")
        );
        
        // Безопасное чтение с значением по умолчанию
        String config = tryGet(
            () -> Files.readString(Paths.get("config.txt")),
            "default config"
        );
        
        // Безопасное чтение с кастомным обработчиком
        String result = tryGet(
            () -> Files.readString(Paths.get("data.txt")),
            ex -> {
                logger.error("Не удалось прочитать файл", ex);
                return "error: " + ex.getMessage();
            }
        );
    }
}
```

---

## 🔗 Связанные темы
- [[JUnit-Basics|JUnit тестирование]] - тестирование исключений в JUnit
- [[Java-Basics|Основы Java]] - базовые концепции языка
- [[Best-Practices|Лучшие практики]] - общие рекомендации по разработке
- [[Logging|Логирование]] - запись информации об ошибках