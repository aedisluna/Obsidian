# Try-Catch в Java
*Обработка исключений и управление ошибками*

#java #exceptions #error-handling #try-catch #finally #resources

---

## 📋 Содержание
- [[#Основы Try-Catch]]
- [[#Типы исключений]]
- [[#Синтаксис и структура]]
- [[#Try-with-resources]]
- [[#Multi-catch]]
- [[#Лучшие практики]]
- [[#Продвинутые техники]]

---

## 🎯 Основы Try-Catch

> [!info] Try-catch - это механизм обработки исключений, который позволяет перехватывать и обрабатывать ошибки во время выполнения программы

### Зачем нужна обработка исключений?
- **Graceful degradation** - программа не падает при ошибках
- **Пользовательский опыт** - показ понятных сообщений об ошибках
- **Логирование** - сохранение информации о проблемах
- **Восстановление** - попытки исправить ошибку автоматически
- **Ресурсы** - корректная очистка ресурсов

```java
public class TryCatchBasicsDemo {
    
    public static void demonstrateBasicTryCatch() {
        System.out.println("=== Основы try-catch ===");
        
        // Пример без обработки исключений
        demonstrateWithoutTryCatch();
        
        // Пример с обработкой исключений
        demonstrateWithTryCatch();
        
        // Множественные исключения
        demonstrateMultipleExceptions();
    }
    
    private static void demonstrateWithoutTryCatch() {
        System.out.println("\n--- Без обработки исключений ---");
        
        try {
            // Эмулируем код без try-catch
            int[] numbers = {1, 2, 3};
            System.out.println("Элемент с индексом 5: " + numbers[5]); // ArrayIndexOutOfBoundsException
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("❌ Программа упала бы здесь: " + e.getMessage());
        }
    }
    
    private static void demonstrateWithTryCatch() {
        System.out.println("\n--- С обработкой исключений ---");
        
        int[] numbers = {1, 2, 3, 4, 5};
        int index = 10;
        
        try {
            System.out.println("Попытка получить элемент с индексом " + index);
            int value = numbers[index];
            System.out.println("Значение: " + value);
            
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("✅ Обработано исключение: Индекс " + index + " выходит за границы массива");
            System.out.println("Размер массива: " + numbers.length);
            
            // Попытка исправить ситуацию
            if (index < numbers.length) {
                System.out.println("Значение: " + numbers[index]);
            } else {
                System.out.println("Используем последний элемент: " + numbers[numbers.length - 1]);
            }
        }
        
        System.out.println("Программа продолжает работать ✅");
    }
    
    private static void demonstrateMultipleExceptions() {
        System.out.println("\n--- Множественные исключения ---");
        
        String[] testInputs = {"42", "abc", null, "0"};
        
        for (String input : testInputs) {
            System.out.println("\nОбработка входа: " + input);
            
            try {
                // Несколько потенциальных источников исключений
                int length = input.length(); // NullPointerException
                int number = Integer.parseInt(input); // NumberFormatException
                int result = 100 / number; // ArithmeticException
                
                System.out.println("✅ Результат: " + result);
                
            } catch (NullPointerException e) {
                System.out.println("❌ Входная строка null");
            } catch (NumberFormatException e) {
                System.out.println("❌ Не удается преобразовать в число: " + input);
            } catch (ArithmeticException e) {
                System.out.println("❌ Деление на ноль");
            }
        }
    }
    
    public static void main(String[] args) {
        demonstrateBasicTryCatch();
    }
}
```

---

## 🏗️ Типы исключений

### Иерархия исключений
```java
public class ExceptionTypesDemo {
    
    public static void demonstrateExceptionHierarchy() {
        System.out.println("=== Иерархия исключений ===");
        
        // Checked Exceptions - должны быть обработаны
        demonstrateCheckedExceptions();
        
        // Unchecked Exceptions - RuntimeException
        demonstrateUncheckedExceptions();
        
        // Errors - серьезные системные ошибки
        demonstrateErrors();
    }
    
    private static void demonstrateCheckedExceptions() {
        System.out.println("\n--- Checked Exceptions ---");
        
        // FileNotFoundException, IOException и другие должны быть обработаны
        String filename = "nonexistent.txt";
        
        try {
            readFile(filename);
        } catch (java.io.FileNotFoundException e) {
            System.out.println("❌ Файл не найден: " + e.getMessage());
        } catch (java.io.IOException e) {
            System.out.println("❌ Ошибка ввода-вывода: " + e.getMessage());
        }
        
        // Альтернатива - проброс исключения через throws
        try {
            riskyMethodWithThrows();
        } catch (InterruptedException e) {
            System.out.println("❌ Операция прервана: " + e.getMessage());
            Thread.currentThread().interrupt(); // Восстанавливаем флаг прерывания
        }
    }
    
    private static void readFile(String filename) throws java.io.IOException {
        // Эмуляция чтения файла
        if (!filename.equals("existing.txt")) {
            throw new java.io.FileNotFoundException("File not found: " + filename);
        }
        System.out.println("✅ Файл успешно прочитан");
    }
    
    private static void riskyMethodWithThrows() throws InterruptedException {
        System.out.println("Выполнение длительной операции...");
        Thread.sleep(100); // Может выбросить InterruptedException
        System.out.println("Операция завершена");
    }
    
    private static void demonstrateUncheckedExceptions() {
        System.out.println("\n--- Unchecked Exceptions (RuntimeException) ---");
        
        // NullPointerException
        handleNullPointer();
        
        // IllegalArgumentException
        handleIllegalArgument();
        
        // IndexOutOfBoundsException
        handleIndexOutOfBounds();
        
        // ClassCastException
        handleClassCast();
    }
    
    private static void handleNullPointer() {
        String str = null;
        
        try {
            int length = str.length(); // NPE
        } catch (NullPointerException e) {
            System.out.println("❌ NullPointerException: обращение к null объекту");
        }
    }
    
    private static void handleIllegalArgument() {
        try {
            setAge(-5); // Некорректный аргумент
        } catch (IllegalArgumentException e) {
            System.out.println("❌ IllegalArgumentException: " + e.getMessage());
        }
    }
    
    private static void setAge(int age) {
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("Возраст должен быть от 0 до 150, получен: " + age);
        }
        System.out.println("✅ Возраст установлен: " + age);
    }
    
    private static void handleIndexOutOfBounds() {
        java.util.List<String> list = java.util.Arrays.asList("a", "b", "c");
        
        try {
            String element = list.get(10); // IndexOutOfBoundsException
        } catch (IndexOutOfBoundsException e) {
            System.out.println("❌ IndexOutOfBoundsException: " + e.getMessage());
        }
    }
    
    private static void handleClassCast() {
        try {
            Object obj = "Hello";
            Integer number = (Integer) obj; // ClassCastException
        } catch (ClassCastException e) {
            System.out.println("❌ ClassCastException: невозможно привести String к Integer");
        }
    }
    
    private static void demonstrateErrors() {
        System.out.println("\n--- Errors (серьезные системные ошибки) ---");
        
        System.out.println("Примеры Error (не рекомендуется перехватывать):");
        System.out.println("- OutOfMemoryError: нехватка памяти");
        System.out.println("- StackOverflowError: переполнение стека");
        System.out.println("- NoClassDefFoundError: не найден класс");
        System.out.println("- VirtualMachineError: ошибки JVM");
        
        // Демонстрация StackOverflowError (осторожно!)
        try {
            // recursiveMethod(); // Раскомментировать осторожно!
        } catch (StackOverflowError e) {
            System.out.println("❌ StackOverflowError (не рекомендуется перехватывать)");
        }
    }
    
    @SuppressWarnings("unused")
    private static void recursiveMethod() {
        recursiveMethod(); // Бесконечная рекурсия -> StackOverflowError
    }
    
    public static void main(String[] args) {
        demonstrateExceptionHierarchy();
    }
}
```

---

## 🛠️ Синтаксис и структура

### Finally блок
```java
public class TryCatchFinallyDemo {
    
    public static void demonstrateFinallyBlock() {
        System.out.println("=== Finally блок ===");
        
        // Finally выполняется всегда
        demonstrateAlwaysExecute();
        
        // Finally vs return
        demonstrateFinallyWithReturn();
        
        // Finally с исключениями
        demonstrateFinallyWithException();
        
        // Правильная очистка ресурсов
        demonstrateResourceCleanup();
    }
    
    private static void demonstrateAlwaysExecute() {
        System.out.println("\n--- Finally выполняется всегда ---");
        
        for (int scenario = 1; scenario <= 4; scenario++) {
            System.out.println("Сценарий " + scenario + ":");
            
            try {
                switch (scenario) {
                    case 1 -> System.out.println("  Нормальное выполнение");
                    case 2 -> {
                        System.out.println("  Выброс исключения");
                        throw new RuntimeException("Test exception");
                    }
                    case 3 -> {
                        System.out.println("  Return в try");
                        return; // Finally все равно выполнится!
                    }
                    case 4 -> {
                        System.out.println("  Return в catch");
                        throw new RuntimeException("For catch");
                    }
                }
            } catch (RuntimeException e) {
                System.out.println("  Перехвачено в catch: " + e.getMessage());
                if (scenario == 4) {
                    return; // Finally все равно выполнится!
                }
            } finally {
                System.out.println("  ✅ Finally блок выполнен");
            }
            
            System.out.println("  После try-catch-finally");
        }
    }
    
    private static void demonstrateFinallyWithReturn() {
        System.out.println("\n--- Finally и return ---");
        
        String result1 = methodWithFinallyReturn();
        System.out.println("Результат 1: " + result1);
        
        String result2 = methodWithFinallyModification();
        System.out.println("Результат 2: " + result2);
    }
    
    private static String methodWithFinallyReturn() {
        try {
            return "try";
        } finally {
            // ❌ Плохо: return в finally перезаписывает return из try
            return "finally"; 
        }
    }
    
    private static String methodWithFinallyModification() {
        StringBuilder sb = new StringBuilder();
        
        try {
            sb.append("try");
            return sb.toString();
        } finally {
            // Изменение объекта влияет на возвращаемое значение
            sb.append("-finally");
        }
    }
    
    private static void demonstrateFinallyWithException() {
        System.out.println("\n--- Finally с исключениями ---");
        
        try {
            methodWithSuppressedException();
        } catch (Exception e) {
            System.out.println("Перехваченное исключение: " + e.getMessage());
            
            // Проверяем подавленные исключения
            Throwable[] suppressed = e.getSuppressed();
            if (suppressed.length > 0) {
                System.out.println("Подавленные исключения:");
                for (Throwable t : suppressed) {
                    System.out.println("  - " + t.getMessage());
                }
            }
        }
    }
    
    private static void methodWithSuppressedException() throws Exception {
        Exception tryException = null;
        
        try {
            throw new Exception("Исключение из try");
        } catch (Exception e) {
            tryException = e;
            throw e;
        } finally {
            // Исключение в finally "подавляет" исключение из try
            Exception finallyException = new Exception("Исключение из finally");
            if (tryException != null) {
                finallyException.addSuppressed(tryException);
            }
            // throw finallyException; // Раскомментировать для демонстрации
        }
    }
    
    private static void demonstrateResourceCleanup() {
        System.out.println("\n--- Очистка ресурсов в finally ---");
        
        // ❌ Старый способ - ручная очистка
        manualResourceCleanup();
        
        // ✅ Новый способ - try-with-resources (будет рассмотрен далее)
        System.out.println("💡 Для автоматической очистки используйте try-with-resources");
    }
    
    private static void manualResourceCleanup() {
        MockResource resource = null;
        
        try {
            resource = new MockResource("database connection");
            resource.doWork();
            
            // Эмуляция ошибки
            if (Math.random() > 0.5) {
                throw new RuntimeException("Случайная ошибка");
            }
            
        } catch (RuntimeException e) {
            System.out.println("❌ Ошибка: " + e.getMessage());
        } finally {
            // Обязательная очистка ресурсов
            if (resource != null) {
                System.out.println("🧹 Очистка ресурса в finally: " + resource.name);
                resource.close();
            }
        }
    }
    
    // Вспомогательный класс для демонстрации
    static class MockResource {
        final String name;
        
        MockResource(String name) {
            this.name = name;
            System.out.println("📂 Открыт ресурс: " + name);
        }
        
        void doWork() {
            System.out.println("⚡ Работа с ресурсом: " + name);
        }
        
        void close() {
            System.out.println("🔒 Закрыт ресурс: " + name);
        }
    }
    
    public static void main(String[] args) {
        demonstrateFinallyBlock();
    }
}
```

---

## 🔄 Try-with-resources

### Автоматическое управление ресурсами
```java
public class TryWithResourcesDemo {
    
    public static void demonstrateTryWithResources() {
        System.out.println("=== Try-with-resources (Java 7+) ===");
        
        // Основы try-with-resources
        basicTryWithResources();
        
        // Множественные ресурсы
        multipleTryWithResources();
        
        // Java 9+ улучшения
        java9TryWithResources();
        
        // Пользовательские AutoCloseable ресурсы
        customAutoCloseable();
    }
    
    private static void basicTryWithResources() {
        System.out.println("\n--- Основы try-with-resources ---");
        
        // ✅ Автоматическое закрытие ресурса
        try (MockResource resource = new MockResource("file.txt")) {
            resource.doWork();
            
            // Эмуляция исключения
            if (Math.random() > 0.7) {
                throw new RuntimeException("Ошибка при работе с файлом");
            }
            
        } catch (RuntimeException e) {
            System.out.println("❌ Обработана ошибка: " + e.getMessage());
        }
        // Ресурс автоматически закроется, даже если было исключение
        
        System.out.println("💡 Ресурс закрыт автоматически");
    }
    
    private static void multipleTryWithResources() {
        System.out.println("\n--- Множественные ресурсы ---");
        
        try (MockResource resource1 = new MockResource("database");
             MockResource resource2 = new MockResource("network");
             MockResource resource3 = new MockResource("cache")) {
            
            System.out.println("Работаем с тремя ресурсами одновременно");
            resource1.doWork();
            resource2.doWork();
            resource3.doWork();
            
        } catch (Exception e) {
            System.out.println("❌ Ошибка: " + e.getMessage());
        }
        // Все ресурсы закроются в обратном порядке: cache -> network -> database
        
        System.out.println("💡 Все ресурсы закрыты автоматически");
    }
    
    private static void java9TryWithResources() {
        System.out.println("\n--- Java 9+ улучшения ---");
        
        // Java 9: можно использовать существующие переменные
        MockResource existingResource = new MockResource("existing");
        
        try (existingResource) { // Java 9+ - не нужно создавать новую переменную
            existingResource.doWork();
        } catch (Exception e) {
            System.out.println("❌ Ошибка: " + e.getMessage());
        }
        
        System.out.println("💡 Java 9: используем существующую переменную");
        
        // Комбинация новых и существующих ресурсов
        MockResource anotherResource = new MockResource("another");
        
        try (anotherResource;
             MockResource newResource = new MockResource("new")) {
            
            anotherResource.doWork();
            newResource.doWork();
            
        } catch (Exception e) {
            System.out.println("❌ Ошибка: " + e.getMessage());
        }
    }
    
    private static void customAutoCloseable() {
        System.out.println("\n--- Пользовательские AutoCloseable ---");
        
        try (DatabaseConnection db = new DatabaseConnection();
             HttpClient http = new HttpClient();
             MemoryCache cache = new MemoryCache()) {
            
            // Работаем с пользовательскими ресурсами
            db.executeQuery("SELECT * FROM users");
            http.sendRequest("GET /api/data");
            cache.put("key", "value");
            
        } catch (Exception e) {
            System.out.println("❌ Ошибка: " + e.getMessage());
        }
        
        System.out.println("💡 Пользовательские ресурсы закрыты");
    }
    
    // Демонстрация подавленных исключений
    public static void demonstrateSuppressedExceptions() {
        System.out.println("\n=== Подавленные исключения ===");
        
        try (ProblematicResource resource = new ProblematicResource()) {
            resource.doWork();
            throw new RuntimeException("Основное исключение");
            
        } catch (Exception e) {
            System.out.println("Основное исключение: " + e.getMessage());
            
            // Проверяем подавленные исключения
            Throwable[] suppressed = e.getSuppressed();
            if (suppressed.length > 0) {
                System.out.println("Подавленные исключения:");
                for (int i = 0; i < suppressed.length; i++) {
                    System.out.println("  " + (i + 1) + ". " + suppressed[i].getMessage());
                }
            }
        }
    }
    
    // Вспомогательные классы
    static class MockResource implements AutoCloseable {
        final String name;
        
        MockResource(String name) {
            this.name = name;
            System.out.println("📂 Открыт ресурс: " + name);
        }
        
        void doWork() {
            System.out.println("⚡ Работа с ресурсом: " + name);
        }
        
        @Override
        public void close() {
            System.out.println("🔒 Закрыт ресурс: " + name);
        }
    }
    
    static class DatabaseConnection implements AutoCloseable {
        public DatabaseConnection() {
            System.out.println("🗄️  Подключение к базе данных");
        }
        
        public void executeQuery(String sql) {
            System.out.println("🔍 Выполнение запроса: " + sql);
        }
        
        @Override
        public void close() {
            System.out.println("🗄️  Отключение от базы данных");
        }
    }
    
    static class HttpClient implements AutoCloseable {
        public HttpClient() {
            System.out.println("🌐 Создание HTTP клиента");
        }
        
        public void sendRequest(String request) {
            System.out.println("📡 Отправка запроса: " + request);
        }
        
        @Override
        public void close() {
            System.out.println("🌐 Закрытие HTTP клиента");
        }
    }
    
    static class MemoryCache implements AutoCloseable {
        public MemoryCache() {
            System.out.println("🧠 Инициализация кэша");
        }
        
        public void put(String key, String value) {
            System.out.println("💾 Кэширование: " + key + " = " + value);
        }
        
        @Override
        public void close() {
            System.out.println("🧠 Очистка кэша");
        }
    }
    
    static class ProblematicResource implements AutoCloseable {
        public void doWork() {
            System.out.println("⚡ Работа с проблемным ресурсом");
        }
        
        @Override
        public void close() throws Exception {
            System.out.println("🔒 Попытка закрыть проблемный ресурс");
            throw new Exception("Исключение при закрытии ресурса");
        }
    }
    
    public static void main(String[] args) {
        demonstrateTryWithResources();
        demonstrateSuppressedExceptions();
    }
}
```

---

## 🎯 Multi-catch

### Обработка множественных исключений
```java
public class MultiCatchDemo {
    
    public static void demonstrateMultiCatch() {
        System.out.println("=== Multi-catch (Java 7+) ===");
        
        // Старый способ vs новый способ
        oldWayMultipleExceptions();
        newWayMultiCatch();
        
        // Продвинутое использование
        advancedMultiCatch();
        
        // Ограничения multi-catch
        multiCatchLimitations();
    }
    
    private static void oldWayMultipleExceptions() {
        System.out.println("\n--- Старый способ (до Java 7) ---");
        
        String[] testInputs = {"42", "abc", null, "0"};
        
        for (String input : testInputs) {
            System.out.println("\\nВход: " + input);
            
            try {
                processData(input);
            } catch (NumberFormatException e) {
                System.out.println("❌ Ошибка формата числа: " + e.getMessage());
                logError("NumberFormat", e);
                sendAlert("Invalid number format");
            } catch (NullPointerException e) {
                System.out.println("❌ Null pointer: " + e.getMessage());
                logError("NullPointer", e);
                sendAlert("Null value received");
            } catch (ArithmeticException e) {
                System.out.println("❌ Арифметическая ошибка: " + e.getMessage());
                logError("Arithmetic", e);
                sendAlert("Division by zero");
            }
        }
    }
    
    private static void newWayMultiCatch() {
        System.out.println("\n--- Новый способ (Java 7+ multi-catch) ---");
        
        String[] testInputs = {"42", "abc", null, "0"};
        
        for (String input : testInputs) {
            System.out.println("\\nВход: " + input);
            
            try {
                processData(input);
            } catch (NumberFormatException | NullPointerException | ArithmeticException e) {
                // Общая обработка для всех этих исключений
                System.out.println("❌ Ошибка обработки данных: " + e.getClass().getSimpleName() + " - " + e.getMessage());
                
                // e является final в multi-catch
                logError("DataProcessing", e);
                sendAlert("Data processing failed: " + e.getClass().getSimpleName());
                
                // Можно добавить специфичную обработку через instanceof
                if (e instanceof NumberFormatException) {
                    System.out.println("  💡 Подсказка: проверьте формат числа");
                } else if (e instanceof NullPointerException) {
                    System.out.println("  💡 Подсказка: входные данные не должны быть null");
                } else if (e instanceof ArithmeticException) {
                    System.out.println("  💡 Подсказка: избегайте деления на ноль");
                }
            }
        }
    }
    
    private static void advancedMultiCatch() {
        System.out.println("\n--- Продвинутое использование multi-catch ---");
        
        // Группировка похожих исключений
        demonstrateIOExceptions();
        
        // Комбинирование с обычным catch
        demonstrateMixedCatch();
        
        // Nested try-catch с multi-catch
        demonstrateNestedMultiCatch();
    }
    
    private static void demonstrateIOExceptions() {
        System.out.println("\\n--- Группировка I/O исключений ---");
        
        String[] files = {"existing.txt", "protected.txt", "missing.txt"};
        
        for (String filename : files) {
            try {
                processFile(filename);
            } catch (java.io.FileNotFoundException | java.nio.file.NoSuchFileException e) {
                System.out.println("❌ Файл не найден: " + filename);
                createDefaultFile(filename);
            } catch (java.io.IOException | java.nio.file.AccessDeniedException e) {
                System.out.println("❌ Проблемы с доступом к файлу: " + filename);
                logError("FileAccess", e);
            } catch (SecurityException e) {
                System.out.println("❌ Нет прав доступа: " + filename);
                requestPermissions(filename);
            }
        }
    }
    
    private static void demonstrateMixedCatch() {
        System.out.println("\\n--- Комбинирование multi-catch с обычным catch ---");
        
        try {
            complexOperation();
        } catch (IllegalArgumentException | IllegalStateException e) {
            // Multi-catch для связанных исключений
            System.out.println("❌ Некорректное состояние или аргумент: " + e.getMessage());
            resetToDefaultState();
        } catch (SecurityException e) {
            // Отдельная обработка для SecurityException
            System.out.println("❌ Проблемы с безопасностью: " + e.getMessage());
            handleSecurityIssue();
        } catch (Exception e) {
            // Общий catch для всех остальных исключений
            System.out.println("❌ Неожиданная ошибка: " + e.getMessage());
            handleUnexpectedError(e);
        }
    }
    
    private static void demonstrateNestedMultiCatch() {
        System.out.println("\\n--- Вложенные try-catch с multi-catch ---");
        
        try {
            // Внешний try
            performDatabaseOperation();
            
        } catch (java.sql.SQLException | javax.sql.DataSource.class e) {
            System.out.println("❌ Ошибка базы данных: " + e.getMessage());
            
            // Вложенный try для попытки восстановления
            try {
                reconnectToDatabase();
                retryOperation();
            } catch (java.sql.SQLException | java.net.ConnectException retryException) {
                System.out.println("❌ Не удалось восстановить соединение: " + retryException.getMessage());
                switchToOfflineMode();
            }
        }
    }
    
    private static void multiCatchLimitations() {
        System.out.println("\\n--- Ограничения multi-catch ---");
        
        System.out.println("💡 Ограничения multi-catch:");
        System.out.println("1. Переменная исключения является final");
        System.out.println("2. Исключения не могут быть связаны наследованием");
        System.out.println("3. Нельзя переназначить переменную исключения");
        
        try {
            throw new NumberFormatException("Test");
        } catch (NumberFormatException | IllegalArgumentException e) {
            // ❌ Нельзя переназначить e
            // e = new RuntimeException(); // Compilation error
            
            System.out.println("Тип исключения: " + e.getClass().getSimpleName());
        }
        
        // Демонстрация проблемы с наследованием
        demonstrateInheritanceProblem();
    }
    
    private static void demonstrateInheritanceProblem() {
        System.out.println("\\n--- Проблема с наследованием ---");
        
        try {
            throw new NumberFormatException("Test");
        } catch (RuntimeException e) {
            // ✅ Можно поймать родительским классом
            System.out.println("Поймано родительским классом: " + e.getClass().getSimpleName());
        }
        
        // ❌ Такой код не скомпилируется:
        // catch (NumberFormatException | RuntimeException e) {
        //     // Ошибка: NumberFormatException наследуется от RuntimeException
        // }
        
        System.out.println("💡 В multi-catch нельзя использовать классы, связанные наследованием");
    }
    
    // Вспомогательные методы
    private static void processData(String input) {
        if (input == null) {
            throw new NullPointerException("Input is null");
        }
        
        int number = Integer.parseInt(input); // NumberFormatException
        int result = 100 / number; // ArithmeticException if number == 0
        
        System.out.println("✅ Результат: " + result);
    }
    
    private static void processFile(String filename) throws java.io.IOException {
        switch (filename) {
            case "missing.txt" -> throw new java.io.FileNotFoundException("File not found");
            case "protected.txt" -> throw new java.nio.file.AccessDeniedException("Access denied");
            default -> System.out.println("✅ Файл обработан: " + filename);
        }
    }
    
    private static void complexOperation() {
        double random = Math.random();
        if (random < 0.3) {
            throw new IllegalArgumentException("Invalid argument");
        } else if (random < 0.6) {
            throw new IllegalStateException("Invalid state");
        } else if (random < 0.8) {
            throw new SecurityException("Security violation");
        }
        System.out.println("✅ Операция выполнена успешно");
    }
    
    private static void performDatabaseOperation() throws java.sql.SQLException {
        if (Math.random() < 0.7) {
            throw new java.sql.SQLException("Database connection failed");
        }
        System.out.println("✅ Операция с БД выполнена");
    }
    
    // Заглушки для демонстрации
    private static void logError(String type, Throwable e) {
        System.out.println("📝 Логирование: " + type + " - " + e.getMessage());
    }
    
    private static void sendAlert(String message) {
        System.out.println("🚨 Отправка уведомления: " + message);
    }
    
    private static void createDefaultFile(String filename) {
        System.out.println("📄 Создание файла по умолчанию: " + filename);
    }
    
    private static void requestPermissions(String filename) {
        System.out.println("🔑 Запрос разрешений для: " + filename);
    }
    
    private static void resetToDefaultState() {
        System.out.println("🔄 Сброс к состоянию по умолчанию");
    }
    
    private static void handleSecurityIssue() {
        System.out.println("🛡️  Обработка проблем безопасности");
    }
    
    private static void handleUnexpectedError(Exception e) {
        System.out.println("❓ Обработка неожиданной ошибки: " + e.getClass().getSimpleName());
    }
    
    private static void reconnectToDatabase() throws java.sql.SQLException {
        System.out.println("🔄 Попытка переподключения к БД");
        if (Math.random() < 0.5) {
            throw new java.sql.SQLException("Reconnection failed");
        }
    }
    
    private static void retryOperation() {
        System.out.println("🔄 Повторная попытка операции");
    }
    
    private static void switchToOfflineMode() {
        System.out.println("📴 Переключение в автономный режим");
    }
    
    public static void main(String[] args) {
        demonstrateMultiCatch();
    }
}
```

---

## 🏆 Лучшие практики

### Рекомендации по использованию
```java
public class TryCatchBestPractices {
    
    public static void demonstrateBestPractices() {
        System.out.println("=== Лучшие практики Try-Catch ===");
        
        goodPractices();
        badPractices();
        performanceConsiderations();
        loggingAndMonitoring();
    }
    
    private static void goodPractices() {
        System.out.println("\\n✅ Хорошие практики:");
        
        // 1. Конкретные исключения
        demonstrateSpecificExceptions();
        
        // 2. Fail fast принцип
        demonstrateFailFast();
        
        // 3. Правильная очистка ресурсов
        demonstrateProperResourceCleanup();
        
        // 4. Информативные сообщения об ошибках
        demonstrateInformativeMessages();
        
        // 5. Не игнорировать исключения
        demonstrateProperExceptionHandling();
    }
    
    private static void demonstrateSpecificExceptions() {
        System.out.println("\\n--- 1. Ловите конкретные исключения ---");
        
        try {
            processUserData("invalid_email");
        } catch (InvalidEmailException e) {
            // ✅ Конкретная обработка для конкретной ошибки
            System.out.println("❌ Некорректный email: " + e.getEmail());
            System.out.println("💡 Пожалуйста, введите корректный адрес электронной почты");
        } catch (ValidationException e) {
            // ✅ Обработка валидационных ошибок
            System.out.println("❌ Ошибка валидации: " + e.getMessage());
            displayValidationErrors(e.getErrors());
        } catch (ServiceException e) {
            // ✅ Обработка сервисных ошибок
            System.out.println("❌ Ошибка сервиса: " + e.getMessage());
            if (e.isRetryable()) {
                scheduleRetry();
            }
        }
        // ❌ Плохо: catch (Exception e) - слишком общо
    }
    
    private static void demonstrateFailFast() {
        System.out.println("\\n--- 2. Fail Fast принцип ---");
        
        // ✅ Проверки в начале метода
        String email = null;
        Integer age = -5;
        
        try {
            validateUser(email, age);
            System.out.println("✅ Пользователь прошел валидацию");
        } catch (IllegalArgumentException e) {
            System.out.println("❌ Быстрая проверка на входе: " + e.getMessage());
        }
    }
    
    private static void validateUser(String email, Integer age) {
        // ✅ Fail fast - проверяем все условия сразу
        if (email == null || email.trim().isEmpty()) {
            throw new IllegalArgumentException("Email не может быть пустым");
        }
        
        if (age == null || age < 0 || age > 150) {
            throw new IllegalArgumentException("Возраст должен быть от 0 до 150");
        }
        
        if (!email.contains("@")) {
            throw new IllegalArgumentException("Некорректный формат email");
        }
        
        // Дальше основная логика...
    }
    
    private static void demonstrateProperResourceCleanup() {
        System.out.println("\\n--- 3. Правильная очистка ресурсов ---");
        
        // ✅ Try-with-resources для автоматической очистки
        try (DatabaseConnection db = new DatabaseConnection("users");
             CacheManager cache = new CacheManager()) {
            
            db.executeQuery("SELECT * FROM users WHERE active = true");
            cache.put("user_count", "150");
            
            System.out.println("✅ Ресурсы используются корректно");
            
        } catch (SQLException e) {
            System.out.println("❌ Ошибка БД: " + e.getMessage());
            // Ресурсы автоматически закроются
        }
    }
    
    private static void demonstrateInformativeMessages() {
        System.out.println("\\n--- 4. Информативные сообщения об ошибках ---");
        
        String userId = "user123";
        String operation = "updateProfile";
        
        try {
            performUserOperation(userId, operation);
        } catch (UserNotFoundException e) {
            // ✅ Подробное сообщение с контекстом
            String message = String.format(
                "Не удалось найти пользователя '%s' для операции '%s'. " +
                "Возможно, пользователь был удален или не существует.",
                userId, operation
            );
            System.out.println("❌ " + message);
            logError("USER_NOT_FOUND", userId, operation, e);
        }
    }
    
    private static void demonstrateProperExceptionHandling() {
        System.out.println("\\n--- 5. Не игнорируйте исключения ---");
        
        try {
            riskyNetworkOperation();
        } catch (NetworkException e) {
            // ✅ Правильная обработка
            System.out.println("❌ Сетевая ошибка: " + e.getMessage());
            
            // Логируем для разработчиков
            logError("NETWORK_ERROR", e);
            
            // Показываем пользователю понятное сообщение
            showUserFriendlyError("Проблемы с сетью. Пожалуйста, попробуйте позже.");
            
            // Попытка восстановления
            if (e.isRecoverable()) {
                scheduleRetry();
            }
            
            // Метрики для мониторинга
            incrementErrorCounter("network_errors");
        }
        
        // ❌ Плохие практики (никогда не делайте так):
        demonstrateBadExceptionHandling();
    }
    
    private static void badPractices() {
        System.out.println("\\n❌ Плохие практики (избегайте):");
        
        // Эти примеры показывают что НЕ нужно делать
        demonstrateBadExceptionHandling();
        demonstrateOverBroadCatching();
        demonstratePoorResourceManagement();
        demonstrateExceptionSwallowing();
    }
    
    private static void demonstrateBadExceptionHandling() {
        System.out.println("\\n--- ❌ Плохие практики ---");
        
        try {
            riskyOperation();
        } catch (Exception e) {
            // ❌ Плохо: игнорирование исключения
            // (пустой catch блок)
        }
        
        try {
            anotherRiskyOperation();
        } catch (Exception e) {
            // ❌ Плохо: только printStackTrace без логики восстановления
            e.printStackTrace(); // В продакшене лучше использовать логгер
        }
        
        try {
            yetAnotherRiskyOperation();
        } catch (Exception e) {
            // ❌ Плохо: слишком общий catch
            System.out.println("Something went wrong"); // Неинформативно
        }
        
        System.out.println("💡 Избегайте этих практик в реальном коде");
    }
    
    private static void demonstrateOverBroadCatching() {
        System.out.println("\\n--- ❌ Слишком широкие catch блоки ---");
        
        try {
            complexBusinessLogic();
        } catch (Throwable e) { // ❌ Слишком широко - ловит даже Error!
            System.out.println("❌ Плохо: ловим Throwable");
        }
        
        try {
            anotherComplexOperation();
        } catch (Exception e) { // ❌ Слишком общо
            System.out.println("❌ Плохо: общий Exception catch");
            // Теряем возможность специфичной обработки
        }
        
        // ✅ Лучше:
        try {
            betterComplexOperation();
        } catch (BusinessLogicException e) {
            handleBusinessError(e);
        } catch (ValidationException e) {
            handleValidationError(e);
        } catch (SystemException e) {
            handleSystemError(e);
        }
    }
    
    private static void demonstratePoorResourceManagement() {
        System.out.println("\\n--- ❌ Плохое управление ресурсами ---");
        
        // ❌ Плохо: ручное управление ресурсами
        MockResource resource = null;
        try {
            resource = new MockResource("manual");
            resource.doWork();
        } catch (Exception e) {
            System.out.println("❌ Ошибка: " + e.getMessage());
        } finally {
            // ❌ Опасно: может быть NPE или исключение при закрытии
            if (resource != null) {
                try {
                    resource.close();
                } catch (Exception e) {
                    // Что делать с исключением при закрытии?
                    System.err.println("Ошибка при закрытии: " + e.getMessage());
                }
            }
        }
        
        System.out.println("💡 Используйте try-with-resources для автоматического управления");
    }
    
    private static void demonstrateExceptionSwallowing() {
        System.out.println("\\n--- ❌ 'Проглатывание' исключений ---");
        
        for (int i = 0; i < 3; i++) {
            try {
                processItem(i);
            } catch (ProcessingException e) {
                // ❌ Плохо: просто продолжаем без обработки
                continue; // Потеряли информацию об ошибке!
            }
        }
        
        System.out.println("💡 Всегда логируйте или обрабатывайте исключения");
    }
    
    private static void performanceConsiderations() {
        System.out.println("\\n=== Соображения производительности ===");
        
        // 1. Try-catch не должен использоваться для управления потоком
        demonstrateControlFlowAntipattern();
        
        // 2. Создание исключений дорого
        demonstrateExceptionCreationCost();
        
        // 3. Кэширование часто используемых исключений
        demonstrateExceptionCaching();
    }
    
    private static void demonstrateControlFlowAntipattern() {
        System.out.println("\\n--- ❌ Не используйте исключения для управления потоком ---");
        
        // ❌ Плохо: использование исключений для нормального потока
        int[] numbers = {1, 2, 3};
        
        System.out.println("Плохой подход:");
        try {
            for (int i = 0; ; i++) { // Бесконечный цикл!
                System.out.println("Element " + i + ": " + numbers[i]);
            }
        } catch (ArrayIndexOutOfBoundsException e) {
            // ❌ Используем исключение для выхода из цикла
            System.out.println("Конец массива (через исключение)");
        }
        
        // ✅ Правильно: нормальная проверка условий
        System.out.println("\\nХороший подход:");
        for (int i = 0; i < numbers.length; i++) {
            System.out.println("Element " + i + ": " + numbers[i]);
        }
        System.out.println("Конец массива (через нормальную проверку)");
    }
    
    private static void demonstrateExceptionCreationCost() {
        System.out.println("\\n--- Стоимость создания исключений ---");
        
        int iterations = 100000;
        
        // Тест создания исключений
        long startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            Exception e = new Exception("Test exception " + i);
        }
        long exceptionTime = System.nanoTime() - startTime;
        
        // Тест создания обычных объектов
        startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            String s = new String("Test string " + i);
        }
        long stringTime = System.nanoTime() - startTime;
        
        System.out.printf("Создание %d исключений: %.2f мс%n", iterations, exceptionTime / 1_000_000.0);
        System.out.printf("Создание %d строк: %.2f мс%n", iterations, stringTime / 1_000_000.0);
        System.out.printf("Исключения медленнее в %.1f раз%n", (double)exceptionTime / stringTime);
    }
    
    private static void demonstrateExceptionCaching() {
        System.out.println("\\n--- Кэширование исключений ---");
        
        // ✅ Кэширование для часто используемых исключений
        ValidationException cachedNullException = new ValidationException("Value cannot be null");
        ValidationException cachedEmptyException = new ValidationException("Value cannot be empty");
        
        // Использование закэшированных исключений
        for (String input : new String[]{null, "", "valid"}) {
            try {
                if (input == null) {
                    throw cachedNullException; // Переиспользуем исключение
                }
                if (input.isEmpty()) {
                    throw cachedEmptyException; // Переиспользуем исключение
                }
                System.out.println("✅ Валидное значение: " + input);
            } catch (ValidationException e) {
                System.out.println("❌ " + e.getMessage());
            }
        }
        
        System.out.println("💡 Кэширование уменьшает накладные расходы на создание исключений");
    }
    
    private static void loggingAndMonitoring() {
        System.out.println("\\n=== Логирование и мониторинг ===");
        
        try {
            businessOperation();
        } catch (Exception e) {
            // Комплексная обработка исключения
            
            // 1. Логирование с контекстом
            logExceptionWithContext(e, "businessOperation", 
                Map.of("userId", "user123", "operation", "updateProfile"));
            
            // 2. Метрики для мониторинга
            recordExceptionMetrics(e);
            
            // 3. Алерты для критических ошибок
            if (isCriticalException(e)) {
                sendAlert("Critical error in business operation", e);
            }
            
            // 4. Пользовательское сообщение
            displayUserMessage(e);
        }
    }
    
    // Вспомогательные классы и методы
    static class InvalidEmailException extends Exception {
        private final String email;
        
        public InvalidEmailException(String email) {
            super("Invalid email: " + email);
            this.email = email;
        }
        
        public String getEmail() { return email; }
    }
    
    static class ValidationException extends Exception {
        private final java.util.Map<String, String> errors = new java.util.HashMap<>();
        
        public ValidationException(String message) { super(message); }
        
        public void addError(String field, String error) {
            errors.put(field, error);
        }
        
        public java.util.Map<String, String> getErrors() { return errors; }
    }
    
    static class ServiceException extends Exception {
        private final boolean retryable;
        
        public ServiceException(String message, boolean retryable) {
            super(message);
            this.retryable = retryable;
        }
        
        public boolean isRetryable() { return retryable; }
    }
    
    static class NetworkException extends Exception {
        private final boolean recoverable;
        
        public NetworkException(String message, boolean recoverable) {
            super(message);
            this.recoverable = recoverable;
        }
        
        public boolean isRecoverable() { return recoverable; }
    }
    
    // Заглушки методов для демонстрации
    private static void processUserData(String email) throws InvalidEmailException, ValidationException, ServiceException {
        if ("invalid_email".equals(email)) {
            throw new InvalidEmailException(email);
        }
    }
    
    private static void performUserOperation(String userId, String operation) throws UserNotFoundException {
        throw new UserNotFoundException("User not found: " + userId);
    }
    
    private static void riskyNetworkOperation() throws NetworkException {
        throw new NetworkException("Connection timeout", true);
    }
    
    private static void riskyOperation() throws Exception {
        if (Math.random() < 0.5) throw new Exception("Random error");
    }
    
    private static void anotherRiskyOperation() throws Exception {
        throw new Exception("Another error");
    }
    
    private static void yetAnotherRiskyOperation() throws Exception {
        throw new Exception("Yet another error");
    }
    
    private static void complexBusinessLogic() throws Exception {
        throw new BusinessLogicException("Business rule violation");
    }
    
    private static void anotherComplexOperation() throws Exception {
        throw new SystemException("System error");
    }
    
    private static void betterComplexOperation() throws BusinessLogicException, ValidationException, SystemException {
        throw new ValidationException("Validation failed");
    }
    
    private static void processItem(int item) throws ProcessingException {
        throw new ProcessingException("Failed to process item: " + item);
    }
    
    private static void businessOperation() throws Exception {
        throw new RuntimeException("Business operation failed");
    }
    
    // Дополнительные классы исключений
    static class UserNotFoundException extends Exception {
        public UserNotFoundException(String message) { super(message); }
    }
    
    static class BusinessLogicException extends Exception {
        public BusinessLogicException(String message) { super(message); }
    }
    
    static class SystemException extends Exception {
        public SystemException(String message) { super(message); }
    }
    
    static class ProcessingException extends Exception {
        public ProcessingException(String message) { super(message); }
    }
    
    // Заглушки вспомогательных методов
    private static void displayValidationErrors(java.util.Map<String, String> errors) {
        System.out.println("📋 Ошибки валидации: " + errors);
    }
    
    private static void scheduleRetry() {
        System.out.println("🔄 Запланирован повторный запуск");
    }
    
    private static void logError(String type, Object... params) {
        System.out.println("📝 Логирование: " + type + " " + java.util.Arrays.toString(params));
    }
    
    private static void showUserFriendlyError(String message) {
        System.out.println("👤 Пользователю: " + message);
    }
    
    private static void incrementErrorCounter(String metric) {
        System.out.println("📊 Метрика: " + metric + " увеличена");
    }
    
    private static void handleBusinessError(BusinessLogicException e) {
        System.out.println("🏢 Обработка бизнес-ошибки: " + e.getMessage());
    }
    
    private static void handleValidationError(ValidationException e) {
        System.out.println("✅ Обработка ошибки валидации: " + e.getMessage());
    }
    
    private static void handleSystemError(SystemException e) {
        System.out.println("⚙️ Обработка системной ошибки: " + e.getMessage());
    }
    
    private static void logExceptionWithContext(Exception e, String operation, java.util.Map<String, String> context) {
        System.out.println("📝 Исключение в " + operation + " с контекстом " + context + ": " + e.getMessage());
    }
    
    private static void recordExceptionMetrics(Exception e) {
        System.out.println("📊 Запись метрик для: " + e.getClass().getSimpleName());
    }
    
    private static boolean isCriticalException(Exception e) {
        return e instanceof SystemException || e instanceof SecurityException;
    }
    
    private static void sendAlert(String message, Exception e) {
        System.out.println("🚨 АЛЕРТ: " + message + " - " + e.getMessage());
    }
    
    private static void displayUserMessage(Exception e) {
        System.out.println("👤 Пользователю: Произошла ошибка. Мы работаем над её устранением.");
    }
    
    public static void main(String[] args) {
        demonstrateBestPractices();
    }
}
```

---

## 🔗 Связанные темы
- [[Exception-Handling-Code|Exception Handling]] - полное руководство по исключениям
- [[OOP-Principles-Code|ООП принципы]] - создание собственных исключений
- [[Best-Practices-Code|Лучшие практики]] - общие рекомендации по коду
- [[Multithreading-Code|Многопоточность]] - исключения в многопоточном коде