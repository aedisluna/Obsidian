# Многопоточность в Java
*Concurrent и Parallel программирование*

#java #multithreading #concurrency #threads #synchronization #parallel

---

## 📋 Содержание
- [[#Основы многопоточности]]
- [[#Создание потоков]]
- [[#Синхронизация]]
- [[#Concurrent коллекции]]
- [[#ExecutorService и пулы потоков]]
- [[#Проблемы многопоточности]]
- [[#Лучшие практики]]

---

## 🎯 Основы многопоточности

> [!info] Многопоточность позволяет программе выполнять несколько задач одновременно, что повышает производительность и отзывчивость приложения

### Зачем нужна многопоточность?
- ✅ **Параллельные вычисления** - использование всех ядер процессора
- ✅ **Асинхронная обработка** - не блокировать основной поток
- ✅ **Отзывчивый UI** - интерфейс остается активным
- ✅ **Эффективное I/O** - пока один поток ждет, другие работают
- ✅ **Масштабируемость** - обработка множества клиентов

### Процесс vs Поток
```java
public class ProcessVsThread {
    
    public static void main(String[] args) {
        // Получение информации о текущем процессе
        ProcessHandle currentProcess = ProcessHandle.current();
        System.out.println("PID: " + currentProcess.pid());
        System.out.println("Команда: " + currentProcess.info().command().orElse("Unknown"));
        
        // Информация о потоках
        ThreadGroup mainGroup = Thread.currentThread().getThreadGroup();
        System.out.println("Главная группа потоков: " + mainGroup.getName());
        System.out.println("Активных потоков: " + mainGroup.activeCount());
        
        // Информация о текущем потоке
        Thread currentThread = Thread.currentThread();
        System.out.println("Текущий поток: " + currentThread.getName());
        System.out.println("ID потока: " + currentThread.getId());
        System.out.println("Приоритет: " + currentThread.getPriority());
        System.out.println("Состояние: " + currentThread.getState());
        
        // Информация о процессоре
        int availableProcessors = Runtime.getRuntime().availableProcessors();
        System.out.println("Доступно процессоров: " + availableProcessors);
        
        // Создание нового потока для демонстрации
        Thread demoThread = new Thread(() -> {
            System.out.println("Демо поток: " + Thread.currentThread().getName());
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }, "Demo-Thread");
        
        demoThread.start();
        
        try {
            demoThread.join(); // Ждем завершения демо потока
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        System.out.println("Программа завершена");
    }
}
```

### Жизненный цикл потока
```java
public class ThreadLifecycleDemo {
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== Жизненный цикл потока ===");
        
        Thread thread = new Thread(new LifecycleTask(), "Lifecycle-Thread");
        
        // NEW - поток создан, но не запущен
        System.out.println("1. NEW: " + thread.getState());
        
        thread.start();
        
        // RUNNABLE - поток может выполняться
        Thread.sleep(100);
        System.out.println("2. RUNNABLE: " + thread.getState());
        
        // Даем потоку время войти в состояние TIMED_WAITING
        Thread.sleep(2000);
        System.out.println("3. TIMED_WAITING: " + thread.getState());
        
        // Прерываем поток
        thread.interrupt();
        
        // Ждем завершения
        thread.join();
        System.out.println("4. TERMINATED: " + thread.getState());
    }
}

class LifecycleTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Поток начал выполнение");
        
        try {
            // Активная работа
            for (int i = 0; i < 3; i++) {
                System.out.println("Работаем... " + i);
                Thread.sleep(500);
            }
            
            // TIMED_WAITING
            System.out.println("Входим в TIMED_WAITING на 5 секунд");
            Thread.sleep(5000);
            
        } catch (InterruptedException e) {
            System.out.println("Поток прерван");
            Thread.currentThread().interrupt(); // Восстанавливаем флаг прерывания
        }
        
        System.out.println("Поток завершил выполнение");
    }
}
```

---

## 🚀 Создание потоков

### Способы создания потоков
```java
// Способ 1: Наследование от Thread
class MyThread extends Thread {
    private String taskName;
    
    public MyThread(String taskName) {
        this.taskName = taskName;
    }
    
    @Override
    public void run() {
        System.out.println("Поток " + taskName + " начал работу");
        
        for (int i = 1; i <= 5; i++) {
            System.out.println(taskName + " - итерация " + i);
            
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                System.out.println(taskName + " прерван");
                Thread.currentThread().interrupt();
                return;
            }
        }
        
        System.out.println("Поток " + taskName + " завершил работу");
    }
}

// Способ 2: Реализация Runnable
class MyTask implements Runnable {
    private String taskName;
    
    public MyTask(String taskName) {
        this.taskName = taskName;
    }
    
    @Override
    public void run() {
        System.out.println("Задача " + taskName + " начата");
        
        for (int i = 1; i <= 3; i++) {
            System.out.println(taskName + " выполняется... " + i);
            
            try {
                Thread.sleep(800);
            } catch (InterruptedException e) {
                System.out.println(taskName + " прервана");
                Thread.currentThread().interrupt();
                return;
            }
        }
        
        System.out.println("Задача " + taskName + " завершена");
    }
}

// Способ 3: Lambda выражения
public class ThreadCreationExample {
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== Создание потоков ===");
        
        // Способ 1: Наследование от Thread
        MyThread thread1 = new MyThread("Thread-1");
        thread1.start();
        
        // Способ 2: Runnable интерфейс
        MyTask task = new MyTask("Task-1");
        Thread thread2 = new Thread(task);
        thread2.start();
        
        // Способ 3: Lambda выражение
        Thread thread3 = new Thread(() -> {
            String name = Thread.currentThread().getName();
            System.out.println("Lambda поток " + name + " запущен");
            
            for (int i = 1; i <= 4; i++) {
                System.out.println(name + " - шаг " + i);
                try {
                    Thread.sleep(600);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
            
            System.out.println("Lambda поток " + name + " завершен");
        }, "Lambda-Thread");
        
        thread3.start();
        
        // Способ 4: Анонимный класс
        Thread thread4 = new Thread(new Runnable() {
            @Override
            public void run() {
                String name = Thread.currentThread().getName();
                System.out.println("Анонимный поток " + name + " работает");
                
                try {
                    Thread.sleep(2000);
                    System.out.println("Анонимный поток " + name + " завершился");
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        }, "Anonymous-Thread");
        
        thread4.start();
        
        // Ждем завершения всех потоков
        thread1.join();
        thread2.join();
        thread3.join();
        thread4.join();
        
        System.out.println("Все потоки завершены");
        
        // Демонстрация Daemon потоков
        demonstrateDaemonThreads();
        
        // Демонстрация приоритетов потоков
        demonstrateThreadPriorities();
    }
    
    private static void demonstrateDaemonThreads() throws InterruptedException {
        System.out.println("\n=== Daemon потоки ===");
        
        // Обычный поток
        Thread userThread = new Thread(() -> {
            for (int i = 1; i <= 3; i++) {
                System.out.println("User thread: " + i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        }, "User-Thread");
        
        // Daemon поток
        Thread daemonThread = new Thread(() -> {
            int counter = 1;
            while (!Thread.currentThread().isInterrupted()) {
                System.out.println("Daemon thread: " + counter++);
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        }, "Daemon-Thread");
        
        daemonThread.setDaemon(true); // Устанавливаем как daemon
        
        userThread.start();
        daemonThread.start();
        
        userThread.join(); // Ждем только user thread
        
        System.out.println("User thread завершен, daemon thread завершится автоматически");
        Thread.sleep(100); // Небольшая задержка для демонстрации
    }
    
    private static void demonstrateThreadPriorities() throws InterruptedException {
        System.out.println("\n=== Приоритеты потоков ===");
        
        Thread highPriority = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("HIGH PRIORITY: " + i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        }, "High-Priority");
        
        Thread lowPriority = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("low priority: " + i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        }, "Low-Priority");
        
        highPriority.setPriority(Thread.MAX_PRIORITY);
        lowPriority.setPriority(Thread.MIN_PRIORITY);
        
        System.out.println("Высокий приоритет: " + highPriority.getPriority());
        System.out.println("Низкий приоритет: " + lowPriority.getPriority());
        
        lowPriority.start();
        highPriority.start();
        
        highPriority.join();
        lowPriority.join();
    }
}
```

### Callable и Future
```java
import java.util.concurrent.*;
import java.util.*;

// Callable возвращает результат и может выбрасывать исключения
class CalculationTask implements Callable<Integer> {
    private final int number;
    
    public CalculationTask(int number) {
        this.number = number;
    }
    
    @Override
    public Integer call() throws Exception {
        System.out.println("Вычисляем факториал " + number + " в потоке: " + 
                          Thread.currentThread().getName());
        
        // Имитация сложного вычисления
        Thread.sleep(1000);
        
        if (number < 0) {
            throw new IllegalArgumentException("Число не может быть отрицательным");
        }
        
        int result = 1;
        for (int i = 1; i <= number; i++) {
            result *= i;
        }
        
        System.out.println("Факториал " + number + " = " + result);
        return result;
    }
}

class PrimeCheckTask implements Callable<Boolean> {
    private final int number;
    
    public PrimeCheckTask(int number) {
        this.number = number;
    }
    
    @Override
    public Boolean call() throws Exception {
        System.out.println("Проверяем, является ли " + number + " простым числом");
        
        if (number <= 1) return false;
        if (number <= 3) return true;
        if (number % 2 == 0 || number % 3 == 0) return false;
        
        // Имитация времени обработки
        Thread.sleep(500);
        
        for (int i = 5; i * i <= number; i += 6) {
            if (number % i == 0 || number % (i + 2) == 0) {
                return false;
            }
        }
        
        return true;
    }
}

public class CallableAndFutureExample {
    
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(3);
        
        try {
            demonstrateBasicCallable(executor);
            demonstrateMultipleCallables(executor);
            demonstrateFutureTimeout(executor);
            demonstrateCompletableFuture();
        } finally {
            executor.shutdown();
        }
    }
    
    private static void demonstrateBasicCallable(ExecutorService executor) {
        System.out.println("=== Базовое использование Callable ===");
        
        // Отправляем задачу на выполнение
        Future<Integer> future = executor.submit(new CalculationTask(5));
        
        try {
            // Получаем результат (блокирующий вызов)
            Integer result = future.get();
            System.out.println("Результат вычисления: " + result);
            
        } catch (InterruptedException | ExecutionException e) {
            System.err.println("Ошибка при выполнении задачи: " + e.getMessage());
        }
    }
    
    private static void demonstrateMultipleCallables(ExecutorService executor) {
        System.out.println("\n=== Множественные Callable ===");
        
        List<Future<Integer>> futures = new ArrayList<>();
        
        // Запускаем несколько задач
        for (int i = 1; i <= 5; i++) {
            Future<Integer> future = executor.submit(new CalculationTask(i));
            futures.add(future);
        }
        
        // Собираем результаты
        System.out.println("Собираем результаты:");
        for (int i = 0; i < futures.size(); i++) {
            try {
                Integer result = futures.get(i).get();
                System.out.println("Факториал " + (i + 1) + "! = " + result);
            } catch (InterruptedException | ExecutionException e) {
                System.err.println("Ошибка в задаче " + i + ": " + e.getMessage());
            }
        }
        
        // Демонстрация invokeAll
        demonstrateInvokeAll(executor);
    }
    
    private static void demonstrateInvokeAll(ExecutorService executor) {
        System.out.println("\n--- invokeAll демонстрация ---");
        
        List<Callable<Boolean>> tasks = new ArrayList<>();
        int[] numbersToCheck = {17, 18, 19, 20, 21, 22, 23};
        
        for (int num : numbersToCheck) {
            tasks.add(new PrimeCheckTask(num));
        }
        
        try {
            // Выполняем все задачи и ждем завершения всех
            List<Future<Boolean>> results = executor.invokeAll(tasks);
            
            System.out.println("Результаты проверки на простоту:");
            for (int i = 0; i < results.size(); i++) {
                Boolean isPrime = results.get(i).get();
                System.out.printf("%d - %s%n", numbersToCheck[i], 
                                isPrime ? "простое" : "составное");
            }
            
        } catch (InterruptedException | ExecutionException e) {
            System.err.println("Ошибка при выполнении invokeAll: " + e.getMessage());
        }
    }
    
    private static void demonstrateFutureTimeout(ExecutorService executor) {
        System.out.println("\n=== Future с таймаутом ===");
        
        // Долгая задача
        Future<Integer> longTask = executor.submit(() -> {
            Thread.sleep(5000); // 5 секунд
            return 42;
        });
        
        try {
            // Ждем результат максимум 2 секунды
            Integer result = longTask.get(2, TimeUnit.SECONDS);
            System.out.println("Результат: " + result);
            
        } catch (TimeoutException e) {
            System.out.println("Задача не завершилась за 2 секунды");
            longTask.cancel(true); // Прерываем задачу
            
        } catch (InterruptedException | ExecutionException e) {
            System.err.println("Ошибка: " + e.getMessage());
        }
        
        // Проверяем состояние задачи
        System.out.println("Задача отменена: " + longTask.isCancelled());
        System.out.println("Задача завершена: " + longTask.isDone());
    }
    
    private static void demonstrateCompletableFuture() {
        System.out.println("\n=== CompletableFuture ===");
        
        // Асинхронное выполнение
        CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            return "Hello";
        });
        
        CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(800);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            return "World";
        });
        
        // Объединение результатов
        CompletableFuture<String> combinedFuture = future1.thenCombine(future2, (s1, s2) -> s1 + " " + s2);
        
        try {
            String result = combinedFuture.get();
            System.out.println("Объединенный результат: " + result);
        } catch (InterruptedException | ExecutionException e) {
            System.err.println("Ошибка: " + e.getMessage());
        }
        
        // Цепочка обработки
        CompletableFuture<String> chainedFuture = CompletableFuture
                .supplyAsync(() -> "Java")
                .thenApply(String::toUpperCase)
                .thenApply(s -> s + " Programming")
                .thenCompose(s -> CompletableFuture.supplyAsync(() -> s + " is awesome!"));
        
        try {
            System.out.println("Цепочка обработки: " + chainedFuture.get());
        } catch (InterruptedException | ExecutionException e) {
            System.err.println("Ошибка в цепочке: " + e.getMessage());
        }
        
        // Обработка исключений
        CompletableFuture<Integer> faultyFuture = CompletableFuture
                .supplyAsync(() -> {
                    if (Math.random() > 0.5) {
                        throw new RuntimeException("Случайная ошибка");
                    }
                    return 100;
                })
                .exceptionally(throwable -> {
                    System.out.println("Обрабатываем исключение: " + throwable.getMessage());
                    return -1; // Значение по умолчанию
                });
        
        try {
            Integer result = faultyFuture.get();
            System.out.println("Результат с обработкой исключений: " + result);
        } catch (InterruptedException | ExecutionException e) {
            System.err.println("Ошибка: " + e.getMessage());
        }
    }
}
```

---

## 🔒 Синхронизация

### Synchronized и блокировки
```java
// Проблема race condition без синхронизации
class UnsafeCounter {
    private int count = 0;
    
    public void increment() {
        count++; // Не атомарная операция!
    }
    
    public int getCount() {
        return count;
    }
}

// Безопасный счетчик с synchronized методами
class SafeCounter {
    private int count = 0;
    
    public synchronized void increment() {
        count++;
    }
    
    public synchronized int getCount() {
        return count;
    }
}

// Использование synchronized блоков
class BankAccount {
    private double balance;
    private final Object lock = new Object(); // Приватная блокировка
    
    public BankAccount(double initialBalance) {
        this.balance = initialBalance;
    }
    
    public void deposit(double amount) {
        synchronized (lock) {
            System.out.println("Депозит " + amount + " от потока " + Thread.currentThread().getName());
            balance += amount;
            System.out.println("Новый баланс после депозита: " + balance);
        }
    }
    
    public void withdraw(double amount) {
        synchronized (lock) {
            if (balance >= amount) {
                System.out.println("Снятие " + amount + " потоком " + Thread.currentThread().getName());
                balance -= amount;
                System.out.println("Новый баланс после снятия: " + balance);
            } else {
                System.out.println("Недостаточно средств для снятия " + amount);
            }
        }
    }
    
    public synchronized double getBalance() {
        return balance;
    }
}

public class SynchronizationExample {
    
    public static void main(String[] args) throws InterruptedException {
        demonstrateRaceCondition();
        demonstrateSynchronizedMethods();
        demonstrateSynchronizedBlocks();
        demonstrateDeadlock();
    }
    
    private static void demonstrateRaceCondition() throws InterruptedException {
        System.out.println("=== Race Condition Demo ===");
        
        UnsafeCounter unsafeCounter = new UnsafeCounter();
        int numThreads = 10;
        int incrementsPerThread = 1000;
        
        Thread[] threads = new Thread[numThreads];
        
        // Создаем потоки, которые инкрементируют счетчик
        for (int i = 0; i < numThreads; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < incrementsPerThread; j++) {
                    unsafeCounter.increment();
                }
            }, "Unsafe-Thread-" + i);
        }
        
        // Запускаем все потоки
        long startTime = System.currentTimeMillis();
        for (Thread thread : threads) {
            thread.start();
        }
        
        // Ждем завершения всех потоков
        for (Thread thread : threads) {
            thread.join();
        }
        
        long endTime = System.currentTimeMillis();
        
        System.out.println("Ожидаемый результат: " + (numThreads * incrementsPerThread));
        System.out.println("Фактический результат: " + unsafeCounter.getCount());
        System.out.println("Время выполнения: " + (endTime - startTime) + "ms");
        System.out.println("Race condition " + 
                         (unsafeCounter.getCount() == numThreads * incrementsPerThread ? "НЕ произошла" : "произошла"));
    }
    
    private static void demonstrateSynchronizedMethods() throws InterruptedException {
        System.out.println("\n=== Synchronized Methods Demo ===");
        
        SafeCounter safeCounter = new SafeCounter();
        int numThreads = 10;
        int incrementsPerThread = 1000;
        
        Thread[] threads = new Thread[numThreads];
        
        for (int i = 0; i < numThreads; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < incrementsPerThread; j++) {
                    safeCounter.increment();
                }
            }, "Safe-Thread-" + i);
        }
        
        long startTime = System.currentTimeMillis();
        for (Thread thread : threads) {
            thread.start();
        }
        
        for (Thread thread : threads) {
            thread.join();
        }
        
        long endTime = System.currentTimeMillis();
        
        System.out.println("Ожидаемый результат: " + (numThreads * incrementsPerThread));
        System.out.println("Фактический результат: " + safeCounter.getCount());
        System.out.println("Время выполнения: " + (endTime - startTime) + "ms");
        System.out.println("Синхронизация " + 
                         (safeCounter.getCount() == numThreads * incrementsPerThread ? "работает" : "НЕ работает"));
    }
    
    private static void demonstrateSynchronizedBlocks() throws InterruptedException {
        System.out.println("\n=== Synchronized Blocks Demo ===");
        
        BankAccount account = new BankAccount(1000.0);
        
        // Создаем потоки для депозитов
        Thread[] depositThreads = new Thread[3];
        for (int i = 0; i < depositThreads.length; i++) {
            final int threadId = i;
            depositThreads[i] = new Thread(() -> {
                account.deposit(100.0 * (threadId + 1));
            }, "Deposit-Thread-" + i);
        }
        
        // Создаем потоки для снятий
        Thread[] withdrawThreads = new Thread[2];
        for (int i = 0; i < withdrawThreads.length; i++) {
            final int threadId = i;
            withdrawThreads[i] = new Thread(() -> {
                account.withdraw(200.0 * (threadId + 1));
            }, "Withdraw-Thread-" + i);
        }
        
        // Запускаем все потоки
        for (Thread thread : depositThreads) {
            thread.start();
        }
        for (Thread thread : withdrawThreads) {
            thread.start();
        }
        
        // Ждем завершения
        for (Thread thread : depositThreads) {
            thread.join();
        }
        for (Thread thread : withdrawThreads) {
            thread.join();
        }
        
        System.out.println("Итоговый баланс: " + account.getBalance());
    }
    
    private static void demonstrateDeadlock() {
        System.out.println("\n=== Deadlock Demo ===");
        
        final Object lock1 = new Object();
        final Object lock2 = new Object();
        
        Thread thread1 = new Thread(() -> {
            synchronized (lock1) {
                System.out.println("Thread 1: Держу lock1");
                
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
                
                System.out.println("Thread 1: Пытаюсь получить lock2");
                synchronized (lock2) {
                    System.out.println("Thread 1: Держу оба замка");
                }
            }
        }, "Deadlock-Thread-1");
        
        Thread thread2 = new Thread(() -> {
            synchronized (lock2) {
                System.out.println("Thread 2: Держу lock2");
                
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
                
                System.out.println("Thread 2: Пытаюсь получить lock1");
                synchronized (lock1) {
                    System.out.println("Thread 2: Держу оба замка");
                }
            }
        }, "Deadlock-Thread-2");
        
        thread1.start();
        thread2.start();
        
        try {
            // Ждем немного, чтобы увидеть deadlock
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        if (thread1.isAlive() || thread2.isAlive()) {
            System.out.println("DEADLOCK обнаружен! Потоки заблокированы.");
            // В реальном приложении здесь была бы логика обнаружения и разрешения deadlock
        }
    }
}
```

### Wait, Notify и Notify All
```java
// Producer-Consumer паттерн
class Buffer<T> {
    private final Queue<T> queue = new LinkedList<>();
    private final int maxSize;
    
    public Buffer(int maxSize) {
        this.maxSize = maxSize;
    }
    
    public synchronized void put(T item) throws InterruptedException {
        // Ждем, пока в буфере появится место
        while (queue.size() == maxSize) {
            System.out.println("Буфер полон. Производитель " + 
                             Thread.currentThread().getName() + " ждет...");
            wait(); // Освобождаем блокировку и ждем
        }
        
        queue.offer(item);
        System.out.println("Произведен: " + item + " (размер буфера: " + queue.size() + ")");
        
        // Уведомляем всех ожидающих потребителей
        notifyAll();
    }
    
    public synchronized T take() throws InterruptedException {
        // Ждем, пока в буфере появятся элементы
        while (queue.isEmpty()) {
            System.out.println("Буфер пуст. Потребитель " + 
                             Thread.currentThread().getName() + " ждет...");
            wait(); // Освобождаем блокировку и ждем
        }
        
        T item = queue.poll();
        System.out.println("Потреблен: " + item + " (размер буфера: " + queue.size() + ")");
        
        // Уведомляем всех ожидающих производителей
        notifyAll();
        return item;
    }
    
    public synchronized int size() {
        return queue.size();
    }
}

class Producer implements Runnable {
    private final Buffer<String> buffer;
    private final String producerName;
    private final int itemCount;
    
    public Producer(Buffer<String> buffer, String producerName, int itemCount) {
        this.buffer = buffer;
        this.producerName = producerName;
        this.itemCount = itemCount;
    }
    
    @Override
    public void run() {
        try {
            for (int i = 1; i <= itemCount; i++) {
                String item = producerName + "-Item-" + i;
                buffer.put(item);
                
                // Имитируем время производства
                Thread.sleep((int)(Math.random() * 1000));
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            System.out.println("Производитель " + producerName + " прерван");
        }
        
        System.out.println("Производитель " + producerName + " завершил работу");
    }
}

class Consumer implements Runnable {
    private final Buffer<String> buffer;
    private final String consumerName;
    private final int itemCount;
    
    public Consumer(Buffer<String> buffer, String consumerName, int itemCount) {
        this.buffer = buffer;
        this.consumerName = consumerName;
        this.itemCount = itemCount;
    }
    
    @Override
    public void run() {
        try {
            for (int i = 1; i <= itemCount; i++) {
                String item = buffer.take();
                
                // Имитируем время обработки
                Thread.sleep((int)(Math.random() * 1500));
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            System.out.println("Потребитель " + consumerName + " прерван");
        }
        
        System.out.println("Потребитель " + consumerName + " завершил работу");
    }
}

public class WaitNotifyExample {
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== Producer-Consumer Pattern ===");
        
        Buffer<String> buffer = new Buffer<>(5); // Буфер размером 5
        
        // Создаем производителей
        Thread producer1 = new Thread(new Producer(buffer, "Producer1", 8), "Producer-1");
        Thread producer2 = new Thread(new Producer(buffer, "Producer2", 7), "Producer-2");
        
        // Создаем потребителей
        Thread consumer1 = new Thread(new Consumer(buffer, "Consumer1", 10), "Consumer-1");
        Thread consumer2 = new Thread(new Consumer(buffer, "Consumer2", 5), "Consumer-2");
        
        // Запускаем все потоки
        producer1.start();
        producer2.start();
        consumer1.start();
        consumer2.start();
        
        // Ждем завершения всех потоков
        producer1.join();
        producer2.join();
        consumer1.join();
        consumer2.join();
        
        System.out.println("Все потоки завершены. Размер буфера: " + buffer.size());
        
        // Демонстрация собственной реализации семафора
        demonstrateCustomSemaphore();
    }
    
    private static void demonstrateCustomSemaphore() throws InterruptedException {
        System.out.println("\n=== Custom Semaphore ===");
        
        CustomSemaphore semaphore = new CustomSemaphore(2); // Максимум 2 потока
        
        // Создаем несколько задач, которые будут конкурировать за ресурс
        for (int i = 1; i <= 5; i++) {
            final int taskId = i;
            Thread task = new Thread(() -> {
                try {
                    System.out.println("Задача " + taskId + " ожидает разрешения...");
                    semaphore.acquire();
                    
                    System.out.println("Задача " + taskId + " получила доступ к ресурсу");
                    Thread.sleep(2000); // Имитация работы с ресурсом
                    System.out.println("Задача " + taskId + " освобождает ресурс");
                    
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                } finally {
                    semaphore.release();
                }
            }, "Task-" + i);
            
            task.start();
        }
        
        Thread.sleep(10000); // Даем время всем задачам завершиться
    }
}

// Собственная реализация семафора
class CustomSemaphore {
    private int permits;
    private final int maxPermits;
    
    public CustomSemaphore(int permits) {
        this.permits = permits;
        this.maxPermits = permits;
    }
    
    public synchronized void acquire() throws InterruptedException {
        while (permits <= 0) {
            wait(); // Ждем освобождения разрешения
        }
        permits--;
        System.out.println("Разрешение получено. Осталось: " + permits);
    }
    
    public synchronized void release() {
        if (permits < maxPermits) {
            permits++;
            System.out.println("Разрешение освобождено. Доступно: " + permits);
            notify(); // Уведомляем один ожидающий поток
        }
    }
    
    public synchronized int availablePermits() {
        return permits;
    }
}
```

---

## 🗃️ Concurrent коллекции

### Thread-safe коллекции
```java
import java.util.concurrent.*;
import java.util.*;

public class ConcurrentCollectionsExample {
    
    public static void main(String[] args) throws InterruptedException {
        demonstrateConcurrentHashMap();
        demonstrateCopyOnWriteArrayList();
        demonstrateBlockingQueue();
        demonstrateConcurrentLinkedQueue();
        demonstratePerformanceComparison();
    }
    
    private static void demonstrateConcurrentHashMap() throws InterruptedException {
        System.out.println("=== ConcurrentHashMap Demo ===");
        
        ConcurrentHashMap<String, Integer> concurrentMap = new ConcurrentHashMap<>();
        int numThreads = 10;
        int operationsPerThread = 1000;
        
        // Потоки для записи
        Thread[] writeThreads = new Thread[numThreads];
        for (int i = 0; i < numThreads; i++) {
            final int threadId = i;
            writeThreads[i] = new Thread(() -> {
                for (int j = 0; j < operationsPerThread; j++) {
                    String key = "key-" + threadId + "-" + j;
                    concurrentMap.put(key, threadId * operationsPerThread + j);
                }
            }, "Writer-" + i);
        }
        
        // Потоки для чтения
        Thread[] readThreads = new Thread[numThreads / 2];
        for (int i = 0; i < readThreads.length; i++) {
            final int threadId = i;
            readThreads[i] = new Thread(() -> {
                for (int j = 0; j < operationsPerThread; j++) {
                    // Читаем случайный ключ
                    String key = "key-" + (j % numThreads) + "-" + (j / numThreads);
                    Integer value = concurrentMap.get(key);
                    if (j % 100 == 0) { // Логируем каждую 100-ю операцию
                        System.out.println("Reader-" + threadId + " прочитал: " + key + " = " + value);
                    }
                }
            }, "Reader-" + i);
        }
        
        long startTime = System.currentTimeMillis();
        
        // Запускаем все потоки
        for (Thread thread : writeThreads) {
            thread.start();
        }
        for (Thread thread : readThreads) {
            thread.start();
        }
        
        // Ждем завершения
        for (Thread thread : writeThreads) {
            thread.join();
        }
        for (Thread thread : readThreads) {
            thread.join();
        }
        
        long endTime = System.currentTimeMillis();
        
        System.out.println("Размер ConcurrentHashMap: " + concurrentMap.size());
        System.out.println("Время выполнения: " + (endTime - startTime) + "ms");
        
        // Демонстрация атомарных операций
        concurrentMap.clear();
        concurrentMap.put("counter", 0);
        
        Thread[] incrementThreads = new Thread[5];
        for (int i = 0; i < incrementThreads.length; i++) {
            incrementThreads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    concurrentMap.compute("counter", (key, val) -> val == null ? 1 : val + 1);
                }
            }, "Incrementer-" + i);
        }
        
        for (Thread thread : incrementThreads) {
            thread.start();
        }
        for (Thread thread : incrementThreads) {
            thread.join();
        }
        
        System.out.println("Итоговое значение счетчика: " + concurrentMap.get("counter"));
        System.out.println("Ожидалось: " + (incrementThreads.length * 1000));
    }
    
    private static void demonstrateCopyOnWriteArrayList() throws InterruptedException {
        System.out.println("\n=== CopyOnWriteArrayList Demo ===");
        
        CopyOnWriteArrayList<String> copyOnWriteList = new CopyOnWriteArrayList<>();
        
        // Заполняем список начальными данными
        for (int i = 0; i < 10; i++) {
            copyOnWriteList.add("Initial-" + i);
        }
        
        // Поток для добавления элементов
        Thread writerThread = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                copyOnWriteList.add("Added-" + i);
                System.out.println("Добавлен: Added-" + i);
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        }, "Writer");
        
        // Поток для чтения элементов
        Thread readerThread = new Thread(() -> {
            for (int iteration = 0; iteration < 10; iteration++) {
                System.out.println("Итерация " + iteration + ", размер списка: " + copyOnWriteList.size());
                
                // Безопасная итерация - snapshot на момент создания итератора
                for (String item : copyOnWriteList) {
                    System.out.print(item + " ");
                }
                System.out.println();
                
                try {
                    Thread.sleep(300);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        }, "Reader");
        
        writerThread.start();
        readerThread.start();
        
        writerThread.join();
        readerThread.join();
        
        System.out.println("Финальный размер списка: " + copyOnWriteList.size());
    }
    
    private static void demonstrateBlockingQueue() throws InterruptedException {
        System.out.println("\n=== BlockingQueue Demo ===");
        
        BlockingQueue<String> queue = new ArrayBlockingQueue<>(5);
        
        // Producer
        Thread producer = new Thread(() -> {
            try {
                for (int i = 1; i <= 10; i++) {
                    String item = "Item-" + i;
                    queue.put(item); // Блокируется если очередь полна
                    System.out.println("Произведен: " + item + " (размер очереди: " + queue.size() + ")");
                    Thread.sleep(200);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }, "Producer");
        
        // Consumer
        Thread consumer = new Thread(() -> {
            try {
                for (int i = 1; i <= 10; i++) {
                    String item = queue.take(); // Блокируется если очередь пуста
                    System.out.println("Потреблен: " + item + " (размер очереди: " + queue.size() + ")");
                    Thread.sleep(500); // Потребитель медленнее производителя
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }, "Consumer");
        
        producer.start();
        consumer.start();
        
        producer.join();
        consumer.join();
        
        // Демонстрация других типов BlockingQueue
        demonstrateOtherBlockingQueues();
    }
    
    private static void demonstrateOtherBlockingQueues() throws InterruptedException {
        System.out.println("\n--- Другие типы BlockingQueue ---");
        
        // LinkedBlockingQueue - неограниченная очередь (по умолчанию)
        BlockingQueue<Integer> linkedQueue = new LinkedBlockingQueue<>(3);
        
        // PriorityBlockingQueue - очередь с приоритетом
        BlockingQueue<Task> priorityQueue = new PriorityBlockingQueue<>();
        
        // Добавляем задачи с разными приоритетами
        priorityQueue.put(new Task("Low Priority", 3));
        priorityQueue.put(new Task("High Priority", 1));
        priorityQueue.put(new Task("Medium Priority", 2));
        priorityQueue.put(new Task("Urgent", 0));
        
        System.out.println("Обработка задач по приоритету:");
        while (!priorityQueue.isEmpty()) {
            Task task = priorityQueue.take();
            System.out.println("Обрабатываем: " + task);
        }
        
        // DelayQueue - очередь с задержкой
        DelayQueue<DelayedTask> delayQueue = new DelayQueue<>();
        
        long currentTime = System.currentTimeMillis();
        delayQueue.put(new DelayedTask("Task 1", currentTime + 1000)); // +1 секунда
        delayQueue.put(new DelayedTask("Task 2", currentTime + 500));  // +0.5 секунды
        delayQueue.put(new DelayedTask("Task 3", currentTime + 1500)); // +1.5 секунды
        
        System.out.println("Обработка задач с задержкой:");
        while (!delayQueue.isEmpty()) {
            DelayedTask task = delayQueue.take(); // Блокируется до наступления времени
            System.out.println("Выполняем: " + task + " в " + System.currentTimeMillis());
        }
    }
    
    private static void demonstrateConcurrentLinkedQueue() {
        System.out.println("\n=== ConcurrentLinkedQueue Demo ===");
        
        ConcurrentLinkedQueue<String> queue = new ConcurrentLinkedQueue<>();
        
        // Неблокирующие операции
        queue.offer("First");
        queue.offer("Second");
        queue.offer("Third");
        
        System.out.println("Размер очереди: " + queue.size());
        
        String head = queue.poll(); // Неблокирующее извлечение
        System.out.println("Извлечен: " + head);
        
        String peek = queue.peek(); // Просмотр без извлечения
        System.out.println("Следующий элемент: " + peek);
        
        System.out.println("Оставшиеся элементы:");
        queue.forEach(System.out::println);
    }
    
    private static void demonstratePerformanceComparison() throws InterruptedException {
        System.out.println("\n=== Сравнение производительности ===");
        
        int numThreads = 4;
        int operationsPerThread = 10000;
        
        // Тест synchronized HashMap
        Map<Integer, Integer> syncMap = Collections.synchronizedMap(new HashMap<>());
        long syncTime = testMapPerformance(syncMap, numThreads, operationsPerThread, "Synchronized HashMap");
        
        // Тест ConcurrentHashMap
        ConcurrentHashMap<Integer, Integer> concurrentMap = new ConcurrentHashMap<>();
        long concurrentTime = testMapPerformance(concurrentMap, numThreads, operationsPerThread, "ConcurrentHashMap");
        
        System.out.printf("ConcurrentHashMap быстрее на %.1f%%\n", 
                         ((double)(syncTime - concurrentTime) / syncTime) * 100);
    }
    
    private static long testMapPerformance(Map<Integer, Integer> map, int numThreads, 
                                         int operationsPerThread, String mapType) throws InterruptedException {
        Thread[] threads = new Thread[numThreads];
        
        long startTime = System.currentTimeMillis();
        
        for (int i = 0; i < numThreads; i++) {
            final int threadId = i;
            threads[i] = new Thread(() -> {
                for (int j = 0; j < operationsPerThread; j++) {
                    int key = threadId * operationsPerThread + j;
                    map.put(key, key * 2);
                    map.get(key);
                }
            });
        }
        
        for (Thread thread : threads) {
            thread.start();
        }
        
        for (Thread thread : threads) {
            thread.join();
        }
        
        long endTime = System.currentTimeMillis();
        long duration = endTime - startTime;
        
        System.out.printf("%s: %d операций за %d мс (размер: %d)\n", 
                         mapType, numThreads * operationsPerThread, duration, map.size());
        
        return duration;
    }
}

// Вспомогательные классы
class Task implements Comparable<Task> {
    private final String name;
    private final int priority;
    
    public Task(String name, int priority) {
        this.name = name;
        this.priority = priority;
    }
    
    @Override
    public int compareTo(Task other) {
        return Integer.compare(this.priority, other.priority);
    }
    
    @Override
    public String toString() {
        return String.format("%s (приоритет: %d)", name, priority);
    }
}

class DelayedTask implements Delayed {
    private final String name;
    private final long executeTime;
    
    public DelayedTask(String name, long executeTime) {
        this.name = name;
        this.executeTime = executeTime;
    }
    
    @Override
    public long getDelay(TimeUnit unit) {
        long remaining = executeTime - System.currentTimeMillis();
        return unit.convert(remaining, TimeUnit.MILLISECONDS);
    }
    
    @Override
    public int compareTo(Delayed other) {
        return Long.compare(this.getDelay(TimeUnit.MILLISECONDS), 
                          other.getDelay(TimeUnit.MILLISECONDS));
    }
    
    @Override
    public String toString() {
        return name;
    }
}
```

---

## ⚙️ ExecutorService и пулы потоков

### Типы пулов потоков
```java
import java.util.concurrent.*;
import java.util.*;

public class ExecutorServiceExample {
    
    public static void main(String[] args) {
        demonstrateFixedThreadPool();
        demonstrateCachedThreadPool();
        demonstrateScheduledThreadPool();
        demonstrateSingleThreadExecutor();
        demonstrateCustomThreadPool();
        demonstrateForkJoinPool();
    }
    
    private static void demonstrateFixedThreadPool() {
        System.out.println("=== Fixed Thread Pool ===");
        
        ExecutorService fixedPool = Executors.newFixedThreadPool(3);
        
        // Отправляем больше задач чем потоков
        for (int i = 1; i <= 10; i++) {
            final int taskId = i;
            fixedPool.submit(() -> {
                System.out.printf("Задача %d выполняется в потоке %s%n", 
                                taskId, Thread.currentThread().getName());
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
                System.out.printf("Задача %d завершена%n", taskId);
            });
        }
        
        shutdownExecutor(fixedPool, "Fixed Thread Pool");
    }
    
    private static void demonstrateCachedThreadPool() {
        System.out.println("\n=== Cached Thread Pool ===");
        
        ExecutorService cachedPool = Executors.newCachedThreadPool();
        
        // Отправляем задачи с разными интервалами
        for (int i = 1; i <= 8; i++) {
            final int taskId = i;
            
            cachedPool.submit(() -> {
                System.out.printf("Cached задача %d в потоке %s%n", 
                                taskId, Thread.currentThread().getName());
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });
            
            // Небольшая задержка между отправкой задач
            if (i <= 4) {
                try {
                    Thread.sleep(200);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        }
        
        shutdownExecutor(cachedPool, "Cached Thread Pool");
    }
    
    private static void demonstrateScheduledThreadPool() {
        System.out.println("\n=== Scheduled Thread Pool ===");
        
        ScheduledExecutorService scheduledPool = Executors.newScheduledThreadPool(2);
        
        // Задача с задержкой
        System.out.println("Планируем задачу на выполнение через 2 секунды...");
        scheduledPool.schedule(() -> {
            System.out.println("Отложенная задача выполнена в " + 
                             Thread.currentThread().getName());
        }, 2, TimeUnit.SECONDS);
        
        // Периодическая задача с фиксированной частотой
        System.out.println("Запускаем периодическую задачу каждые 1 секунду...");
        ScheduledFuture<?> periodicTask = scheduledPool.scheduleAtFixedRate(() -> {
            System.out.printf("Периодическая задача в %s в %tT%n", 
                            Thread.currentThread().getName(), 
                            System.currentTimeMillis());
        }, 1, 1, TimeUnit.SECONDS);
        
        // Задача с фиксированной задержкой между выполнениями
        ScheduledFuture<?> delayedTask = scheduledPool.scheduleWithFixedDelay(() -> {
            System.out.printf("Задача с задержкой в %s%n", 
                            Thread.currentThread().getName());
            try {
                Thread.sleep(500); // Имитация работы
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }, 0, 2, TimeUnit.SECONDS);
        
        // Даем поработать 6 секунд, затем отменяем
        try {
            Thread.sleep(6000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        periodicTask.cancel(false);
        delayedTask.cancel(false);
        
        shutdownExecutor(scheduledPool, "Scheduled Thread Pool");
    }
    
    private static void demonstrateSingleThreadExecutor() {
        System.out.println("\n=== Single Thread Executor ===");
        
        ExecutorService singleThreadPool = Executors.newSingleThreadExecutor();
        
        // Все задачи выполнятся последовательно в одном потоке
        for (int i = 1; i <= 5; i++) {
            final int taskId = i;
            singleThreadPool.submit(() -> {
                System.out.printf("Single thread задача %d в %s%n", 
                                taskId, Thread.currentThread().getName());
                try {
                    Thread.sleep(800);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });
        }
        
        shutdownExecutor(singleThreadPool, "Single Thread Executor");
    }
    
    private static void demonstrateCustomThreadPool() {
        System.out.println("\n=== Custom Thread Pool ===");
        
        // Создаем кастомный пул с настройками
        ThreadPoolExecutor customPool = new ThreadPoolExecutor(
            2,          // corePoolSize - минимальное количество потоков
            4,          // maximumPoolSize - максимальное количество потоков
            60L,        // keepAliveTime - время жизни "лишних" потоков
            TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(10), // рабочая очередь
            new CustomThreadFactory("Custom"), // фабрика потоков
            new ThreadPoolExecutor.CallerRunsPolicy() // политика отклонения
        );
        
        // Отправляем много задач чтобы увидеть масштабирование
        for (int i = 1; i <= 20; i++) {
            final int taskId = i;
            
            customPool.submit(() -> {
                System.out.printf("Custom задача %d в %s (активных потоков: %d)%n", 
                                taskId, Thread.currentThread().getName(),
                                customPool.getActiveCount());
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });
            
            // Показываем статистику пула
            if (i % 5 == 0) {
                System.out.printf("Статистика пула: размер=%d, активных=%d, очередь=%d%n",
                                customPool.getPoolSize(),
                                customPool.getActiveCount(),
                                customPool.getQueue().size());
            }
            
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        
        shutdownExecutor(customPool, "Custom Thread Pool");
    }
    
    private static void demonstrateForkJoinPool() {
        System.out.println("\n=== Fork-Join Pool ===");
        
        ForkJoinPool forkJoinPool = new ForkJoinPool();
        
        // Вычисляем сумму массива через Fork-Join
        int[] array = new int[1000];
        for (int i = 0; i < array.length; i++) {
            array[i] = i + 1;
        }
        
        SumTask task = new SumTask(array, 0, array.length);
        Long result = forkJoinPool.invoke(task);
        
        System.out.println("Сумма элементов массива: " + result);
        System.out.println("Проверка: " + (array.length * (array.length + 1) / 2));
        
        // Параллельная обработка с использованием parallelStream
        long parallelSum = Arrays.stream(array)
                                .parallel()
                                .mapToLong(i -> i)
                                .sum();
        
        System.out.println("Сумма через parallel stream: " + parallelSum);
        
        forkJoinPool.shutdown();
    }
    
    private static void shutdownExecutor(ExecutorService executor, String name) {
        executor.shutdown();
        try {
            if (!executor.awaitTermination(10, TimeUnit.SECONDS)) {
                System.out.println(name + " не завершился за 10 секунд, принудительное завершение...");
                executor.shutdownNow();
                if (!executor.awaitTermination(5, TimeUnit.SECONDS)) {
                    System.err.println(name + " не удалось завершить!");
                }
            } else {
                System.out.println(name + " успешно завершен");
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
            Thread.currentThread().interrupt();
        }
    }
}

// Кастомная фабрика потоков
class CustomThreadFactory implements ThreadFactory {
    private final String namePrefix;
    private final AtomicInteger threadNumber = new AtomicInteger(1);
    
    public CustomThreadFactory(String namePrefix) {
        this.namePrefix = namePrefix;
    }
    
    @Override
    public Thread newThread(Runnable r) {
        Thread thread = new Thread(r, namePrefix + "-Thread-" + threadNumber.getAndIncrement());
        thread.setDaemon(false); // Не daemon поток
        thread.setPriority(Thread.NORM_PRIORITY);
        return thread;
    }
}

// RecursiveTask для Fork-Join
class SumTask extends RecursiveTask<Long> {
    private final int[] array;
    private final int start;
    private final int end;
    private static final int THRESHOLD = 100; // Порог для разделения задачи
    
    public SumTask(int[] array, int start, int end) {
        this.array = array;
        this.start = start;
        this.end = end;
    }
    
    @Override
    protected Long compute() {
        int length = end - start;
        
        // Если задача маленькая, вычисляем напрямую
        if (length <= THRESHOLD) {
            long sum = 0;
            for (int i = start; i < end; i++) {
                sum += array[i];
            }
            return sum;
        }
        
        // Иначе разделяем задачу
        int middle = start + length / 2;
        SumTask leftTask = new SumTask(array, start, middle);
        SumTask rightTask = new SumTask(array, middle, end);
        
        // Запускаем левую задачу асинхронно
        leftTask.fork();
        
        // Вычисляем правую задачу в текущем потоке
        Long rightResult = rightTask.compute();
        
        // Ждем результат левой задачи
        Long leftResult = leftTask.join();
        
        return leftResult + rightResult;
    }
}

// Пример работы с CompletableFuture в ExecutorService
class CompletableFutureExample {
    
    public static void demonstrateAsyncProcessing() {
        System.out.println("\n=== CompletableFuture with ExecutorService ===");
        
        ExecutorService executor = Executors.newFixedThreadPool(4);
        
        try {
            // Цепочка асинхронных операций
            CompletableFuture<String> future = CompletableFuture
                .supplyAsync(() -> {
                    System.out.println("Шаг 1: Получение данных в " + Thread.currentThread().getName());
                    sleep(1000);
                    return "Raw Data";
                }, executor)
                .thenApplyAsync(data -> {
                    System.out.println("Шаг 2: Обработка данных в " + Thread.currentThread().getName());
                    sleep(500);
                    return "Processed " + data;
                }, executor)
                .thenApplyAsync(processed -> {
                    System.out.println("Шаг 3: Финальная обработка в " + Thread.currentThread().getName());
                    sleep(300);
                    return processed.toUpperCase();
                }, executor);
            
            // Параллельная обработка нескольких задач
            CompletableFuture<String> task1 = CompletableFuture.supplyAsync(() -> {
                sleep(800);
                return "Task 1 Result";
            }, executor);
            
            CompletableFuture<String> task2 = CompletableFuture.supplyAsync(() -> {
                sleep(600);
                return "Task 2 Result";
            }, executor);
            
            CompletableFuture<String> task3 = CompletableFuture.supplyAsync(() -> {
                sleep(1000);
                return "Task 3 Result";
            }, executor);
            
            // Объединение результатов
            CompletableFuture<String> combinedResult = CompletableFuture
                .allOf(task1, task2, task3)
                .thenApply(ignored -> {
                    try {
                        return String.join(", ", task1.get(), task2.get(), task3.get());
                    } catch (InterruptedException | ExecutionException e) {
                        throw new RuntimeException(e);
                    }
                });
            
            // Получение результатов
            try {
                String result1 = future.get();
                String result2 = combinedResult.get();
                
                System.out.println("Результат цепочки: " + result1);
                System.out.println("Объединенный результат: " + result2);
                
            } catch (InterruptedException | ExecutionException e) {
                System.err.println("Ошибка: " + e.getMessage());
            }
            
        } finally {
            executor.shutdown();
        }
    }
    
    private static void sleep(long millis) {
        try {
            Thread.sleep(millis);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            throw new RuntimeException(e);
        }
    }
}
```

---

## ⚠️ Проблемы многопоточности

### Race Conditions и Deadlocks
```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

// Демонстрация race condition
class RaceConditionExample {
    private static int counter = 0;
    
    public static void demonstrateRaceCondition() throws InterruptedException {
        System.out.println("=== Race Condition Demo ===");
        
        Thread[] threads = new Thread[10];
        
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    counter++; // Не атомарная операция!
                }
            });
        }
        
        for (Thread thread : threads) {
            thread.start();
        }
        
        for (Thread thread : threads) {
            thread.join();
        }
        
        System.out.println("Ожидаемое значение: " + (threads.length * 1000));
        System.out.println("Фактическое значение: " + counter);
        System.out.println("Race condition " + 
                         (counter == threads.length * 1000 ? "НЕ произошла" : "произошла"));
    }
}

// Демонстрация различных типов Deadlock
class DeadlockExamples {
    
    // Классический deadlock с двумя ресурсами
    public static void demonstrateClassicDeadlock() {
        System.out.println("\n=== Classic Deadlock Demo ===");
        
        Object resource1 = new Object();
        Object resource2 = new Object();
        
        Thread thread1 = new Thread(() -> {
            synchronized (resource1) {
                System.out.println("Thread 1: Получил resource1");
                
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
                
                System.out.println("Thread 1: Пытаюсь получить resource2");
                synchronized (resource2) {
                    System.out.println("Thread 1: Получил оба ресурса");
                }
            }
        }, "DeadlockThread1");
        
        Thread thread2 = new Thread(() -> {
            synchronized (resource2) {
                System.out.println("Thread 2: Получил resource2");
                
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
                
                System.out.println("Thread 2: Пытаюсь получить resource1");
                synchronized (resource1) {
                    System.out.println("Thread 2: Получил оба ресурса");
                }
            }
        }, "DeadlockThread2");
        
        thread1.start();
        thread2.start();
        
        // Ждем немного чтобы увидеть deadlock
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        if (thread1.isAlive() || thread2.isAlive()) {
            System.out.println("DEADLOCK обнаружен!");
            // Принудительно прерываем потоки
            thread1.interrupt();
            thread2.interrupt();
        }
    }
    
    // Решение deadlock через упорядочивание ресурсов
    public static void demonstrateDeadlockSolution() {
        System.out.println("\n=== Deadlock Solution Demo ===");
        
        Object resource1 = new Object();
        Object resource2 = new Object();
        
        // Всегда захватываем ресурсы в одном и том же порядке
        Thread thread1 = new Thread(() -> {
            acquireResourcesInOrder(resource1, resource2, "Thread 1");
        }, "SolutionThread1");
        
        Thread thread2 = new Thread(() -> {
            acquireResourcesInOrder(resource1, resource2, "Thread 2");
        }, "SolutionThread2");
        
        thread1.start();
        thread2.start();
        
        try {
            thread1.join(3000);
            thread2.join(3000);
            
            if (!thread1.isAlive() && !thread2.isAlive()) {
                System.out.println("Deadlock успешно предотвращен!");
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
    
    private static void acquireResourcesInOrder(Object first, Object second, String threadName) {
        synchronized (first) {
            System.out.println(threadName + ": Получил первый ресурс");
            
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                return;
            }
            
            synchronized (second) {
                System.out.println(threadName + ": Получил второй ресурс");
                // Работаем с ресурсами
                try {
                    Thread.sleep(200);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        }
        System.out.println(threadName + ": Освободил все ресурсы");
    }
}

// Демонстрация Livelock
class LivelockExample {
    private static class Spoon {
        private Diner owner;
        
        public Spoon(Diner owner) {
            this.owner = owner;
        }
        
        public Diner getOwner() { return owner; }
        public void setOwner(Diner owner) { this.owner = owner; }
        
        public synchronized void use() {
            System.out.printf("%s использует ложку%n", owner.getName());
        }
    }
    
    private static class Diner {
        private String name;
        private boolean isHungry;
        
        public Diner(String name) {
            this.name = name;
            this.isHungry = true;
        }
        
        public String getName() { return name; }
        public boolean isHungry() { return isHungry; }
        
        public void eatWith(Spoon spoon, Diner spouse) {
            while (isHungry) {
                // Если ложка не моя
                if (spoon.getOwner() != this) {
                    try {
                        Thread.sleep(1);
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                        return;
                    }
                    continue;
                }
                
                // Если супруг голоден, отдаю ложку
                if (spouse.isHungry()) {
                    System.out.printf("%s: %s выглядит голодным, отдаю ложку%n", 
                                    name, spouse.getName());
                    spoon.setOwner(spouse);
                    continue;
                }
                
                // Ем
                spoon.use();
                isHungry = false;
                System.out.printf("%s наелся!%n", name);
                spoon.setOwner(spouse);
            }
        }
    }
    
    public static void demonstrateLivelock() {
        System.out.println("\n=== Livelock Demo ===");
        
        Diner husband = new Diner("Муж");
        Diner wife = new Diner("Жена");
        Spoon spoon = new Spoon(husband);
        
        Thread husbandThread = new Thread(() -> husband.eatWith(spoon, wife));
        Thread wifeThread = new Thread(() -> wife.eatWith(spoon, husband));
        
        husbandThread.start();
        wifeThread.start();
        
        try {
            Thread.sleep(5000); // Ждем 5 секунд
            
            if (husband.isHungry() && wife.isHungry()) {
                System.out.println("LIVELOCK обнаружен! Оба остались голодными.");
                husbandThread.interrupt();
                wifeThread.interrupt();
            }
            
            husbandThread.join(1000);
            wifeThread.join(1000);
            
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

// Демонстрация Starvation
class StarvationExample {
    
    public static void demonstrateStarvation() {
        System.out.println("\n=== Starvation Demo ===");
        
        Object resource = new Object();
        
        // Высокоприоритетные потоки
        for (int i = 0; i < 5; i++) {
            Thread highPriority = new Thread(() -> {
                while (!Thread.currentThread().isInterrupted()) {
                    synchronized (resource) {
                        System.out.println("Высокоприоритетный поток " + 
                                         Thread.currentThread().getName() + " работает");
                        try {
                            Thread.sleep(100);
                        } catch (InterruptedException e) {
                            Thread.currentThread().interrupt();
                            break;
                        }
                    }
                }
            }, "HighPriority-" + i);
            
            highPriority.setPriority(Thread.MAX_PRIORITY);
            highPriority.start();
        }
        
        // Низкоприоритетный поток
        Thread lowPriority = new Thread(() -> {
            int attempts = 0;
            while (!Thread.currentThread().isInterrupted() && attempts < 3) {
                synchronized (resource) {
                    System.out.println("Низкоприоритетный поток наконец получил ресурс! Попытка: " + (++attempts));
                    try {
                        Thread.sleep(500);
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                        break;
                    }
                }
                
                try {
                    Thread.sleep(50);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        }, "LowPriority");
        
        lowPriority.setPriority(Thread.MIN_PRIORITY);
        lowPriority.start();
        
        try {
            Thread.sleep(3000); // Ждем 3 секунды
            
            // Останавливаем все потоки
            ThreadGroup currentGroup = Thread.currentThread().getThreadGroup();
            Thread[] threads = new Thread[currentGroup.activeCount()];
            currentGroup.enumerate(threads);
            
            for (Thread thread : threads) {
                if (thread != null && thread != Thread.currentThread() && 
                    (thread.getName().startsWith("HighPriority") || 
                     thread.getName().startsWith("LowPriority"))) {
                    thread.interrupt();
                }
            }
            
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

public class ConcurrencyProblemsDemo {
    
    public static void main(String[] args) throws InterruptedException {
        RaceConditionExample.demonstrateRaceCondition();
        DeadlockExamples.demonstrateClassicDeadlock();
        DeadlockExamples.demonstrateDeadlockSolution();
        LivelockExample.demonstrateLivelock();
        StarvationExample.demonstrateStarvation();
        
        // Демонстрация решений с помощью Lock API
        demonstrateLockAPI();
    }
    
    private static void demonstrateLockAPI() throws InterruptedException {
        System.out.println("\n=== Lock API Demo ===");
        
        Lock lock1 = new ReentrantLock();
        Lock lock2 = new ReentrantLock();
        
        Thread thread1 = new Thread(() -> {
            try {
                if (lock1.tryLock(1000, TimeUnit.MILLISECONDS)) {
                    try {
                        System.out.println("Thread 1: Получил lock1");
                        Thread.sleep(500);
                        
                        if (lock2.tryLock(1000, TimeUnit.MILLISECONDS)) {
                            try {
                                System.out.println("Thread 1: Получил lock2");
                                // Работаем с ресурсами
                            } finally {
                                lock2.unlock();
                            }
                        } else {
                            System.out.println("Thread 1: Не смог получить lock2, избегаю deadlock");
                        }
                    } finally {
                        lock1.unlock();
                    }
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }, "LockThread1");
        
        Thread thread2 = new Thread(() -> {
            try {
                if (lock2.tryLock(1000, TimeUnit.MILLISECONDS)) {
                    try {
                        System.out.println("Thread 2: Получил lock2");
                        Thread.sleep(500);
                        
                        if (lock1.tryLock(1000, TimeUnit.MILLISECONDS)) {
                            try {
                                System.out.println("Thread 2: Получил lock1");
                                // Работаем с ресурсами
                            } finally {
                                lock1.unlock();
                            }
                        } else {
                            System.out.println("Thread 2: Не смог получить lock1, избегаю deadlock");
                        }
                    } finally {
                        lock2.unlock();
                    }
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }, "LockThread2");
        
        thread1.start();
        thread2.start();
        
        thread1.join();
        thread2.join();
        
        System.out.println("Lock API демонстрация завершена успешно");
    }
}
```

---

## 🏆 Лучшие практики

### Thread-safe программирование
```java
import java.util.concurrent.*;
import java.util.concurrent.atomic.*;
import java.util.concurrent.locks.*;

// Лучшие практики многопоточности
public class MultithreadingBestPractices {
    
    public static void main(String[] args) throws InterruptedException {
        demonstrateImmutability();
        demonstrateAtomicOperations();
        demonstrateVolatileUsage();
        demonstrateThreadLocal();
        demonstrateProperExceptionHandling();
    }
    
    // Принцип 1: Используйте неизменяемые объекты когда возможно
    private static void demonstrateImmutability() {
        System.out.println("=== Immutability Demo ===");
        
        ImmutablePerson person = new ImmutablePerson("John Doe", 30, 
                                                    Arrays.asList("Java", "Python", "Go"));
        
        // Создаем несколько потоков, которые читают данные
        ExecutorService executor = Executors.newFixedThreadPool(3);
        
        for (int i = 0; i < 5; i++) {
            final int threadId = i;
            executor.submit(() -> {
                System.out.printf("Thread %d: %s, возраст %d, навыки: %s%n",
                                threadId, person.getName(), person.getAge(), 
                                person.getSkills());
            });
        }
        
        executor.shutdown();
        try {
            executor.awaitTermination(2, TimeUnit.SECONDS);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
    
    // Принцип 2: Используйте атомарные операции для простых операций
    private static void demonstrateAtomicOperations() throws InterruptedException {
        System.out.println("\n=== Atomic Operations Demo ===");
        
        AtomicCounter atomicCounter = new AtomicCounter();
        int numThreads = 5;
        int incrementsPerThread = 1000;
        
        Thread[] threads = new Thread[numThreads];
        
        for (int i = 0; i < numThreads; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < incrementsPerThread; j++) {
                    atomicCounter.increment();
                    atomicCounter.add(1);
                }
            }, "AtomicThread-" + i);
        }
        
        long startTime = System.currentTimeMillis();
        
        for (Thread thread : threads) {
            thread.start();
        }
        
        for (Thread thread : threads) {
            thread.join();
        }
        
        long endTime = System.currentTimeMillis();
        
        System.out.printf("Ожидаемое значение: %d%n", numThreads * incrementsPerThread * 2);
        System.out.printf("Фактическое значение: %d%n", atomicCounter.getValue());
        System.out.printf("Время выполнения: %d мс%n", endTime - startTime);
    }
    
    // Принцип 3: Правильное использование volatile
    private static void demonstrateVolatileUsage() throws InterruptedException {
        System.out.println("\n=== Volatile Usage Demo ===");
        
        VolatileExample volatileExample = new VolatileExample();
        
        // Поток-писатель
        Thread writer = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                volatileExample.updateData(i * 10);
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
            volatileExample.stop();
        }, "Writer");
        
        // Поток-читатель
        Thread reader = new Thread(() -> {
            while (volatileExample.isRunning()) {
                int data = volatileExample.getData();
                System.out.println("Прочитано: " + data);
                try {
                    Thread.sleep(200);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        }, "Reader");
        
        writer.start();
        reader.start();
        
        writer.join();
        reader.join();
    }
    
    // Принцип 4: Используйте ThreadLocal для thread-specific данных
    private static void demonstrateThreadLocal() throws InterruptedException {
        System.out.println("\n=== ThreadLocal Demo ===");
        
        ThreadLocalExample threadLocalExample = new ThreadLocalExample();
        
        Thread[] threads = new Thread[3];
        
        for (int i = 0; i < threads.length; i++) {
            final int threadId = i;
            threads[i] = new Thread(() -> {
                // Каждый поток имеет свою копию данных
                threadLocalExample.setUserId("User-" + threadId);
                threadLocalExample.setRequestId("Request-" + threadId + "-" + System.currentTimeMillis());
                
                // Имитация работы
                for (int j = 0; j < 3; j++) {
                    threadLocalExample.processRequest();
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                        break;
                    }
                }
                
                // Важно очистить ThreadLocal данные
                threadLocalExample.cleanup();
            }, "ThreadLocal-" + i);
        }
        
        for (Thread thread : threads) {
            thread.start();
        }
        
        for (Thread thread : threads) {
            thread.join();
        }
    }
    
    // Принцип 5: Правильная обработка исключений в многопоточной среде
    private static void demonstrateProperExceptionHandling() {
        System.out.println("\n=== Exception Handling Demo ===");
        
        ExecutorService executor = Executors.newFixedThreadPool(2);
        
        // Обработка исключений с Future
        Future<String> future = executor.submit(() -> {
            if (Math.random() > 0.5) {
                throw new RuntimeException("Случайное исключение");
            }
            return "Успешный результат";
        });
        
        try {
            String result = future.get(2, TimeUnit.SECONDS);
            System.out.println("Результат: " + result);
        } catch (ExecutionException e) {
            System.out.println("Исключение в задаче: " + e.getCause().getMessage());
        } catch (TimeoutException e) {
            System.out.println("Задача не завершилась вовремя");
            future.cancel(true);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            System.out.println("Поток прерван");
        }
        
        // Установка обработчика неперехваченных исключений
        Thread.setDefaultUncaughtExceptionHandler((thread, exception) -> {
            System.err.printf("Неперехваченное исключение в потоке %s: %s%n", 
                            thread.getName(), exception.getMessage());
            // Логирование, уведомления и т.д.
        });
        
        // Поток который выбросит неперехваченное исключение
        Thread faultyThread = new Thread(() -> {
            throw new RuntimeException("Неперехваченное исключение");
        }, "Faulty-Thread");
        
        faultyThread.start();
        
        try {
            faultyThread.join();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        executor.shutdown();
    }
}

// Неизменяемый класс
final class ImmutablePerson {
    private final String name;
    private final int age;
    private final List<String> skills;
    
    public ImmutablePerson(String name, int age, List<String> skills) {
        this.name = name;
        this.age = age;
        // Создаем неизменяемую копию списка
        this.skills = Collections.unmodifiableList(new ArrayList<>(skills));
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public List<String> getSkills() { return skills; }
    
    @Override
    public String toString() {
        return String.format("Person{name='%s', age=%d, skills=%s}", name, age, skills);
    }
}

// Класс с атомарными операциями
class AtomicCounter {
    private final AtomicInteger counter = new AtomicInteger(0);
    
    public void increment() {
        counter.incrementAndGet();
    }
    
    public void add(int value) {
        counter.addAndGet(value);
    }
    
    public int getValue() {
        return counter.get();
    }
    
    public boolean compareAndSet(int expect, int update) {
        return counter.compareAndSet(expect, update);
    }
}

// Правильное использование volatile
class VolatileExample {
    private volatile int data = 0;
    private volatile boolean running = true;
    
    public void updateData(int newData) {
        this.data = newData;
        System.out.println("Данные обновлены: " + newData);
    }
    
    public int getData() {
        return data;
    }
    
    public boolean isRunning() {
        return running;
    }
    
    public void stop() {
        running = false;
        System.out.println("Остановка работы");
    }
}

// ThreadLocal пример
class ThreadLocalExample {
    private static final ThreadLocal<String> userIdThreadLocal = new ThreadLocal<>();
    private static final ThreadLocal<String> requestIdThreadLocal = new ThreadLocal<>();
    
    public void setUserId(String userId) {
        userIdThreadLocal.set(userId);
    }
    
    public void setRequestId(String requestId) {
        requestIdThreadLocal.set(requestId);
    }
    
    public void processRequest() {
        String userId = userIdThreadLocal.get();
        String requestId = requestIdThreadLocal.get();
        
        System.out.printf("Обработка запроса %s для пользователя %s в потоке %s%n",
                         requestId, userId, Thread.currentThread().getName());
    }
    
    public void cleanup() {
        userIdThreadLocal.remove();
        requestIdThreadLocal.remove();
        System.out.println("ThreadLocal данные очищены для потока " + 
                          Thread.currentThread().getName());
    }
}

// Дополнительные паттерны и практики
class AdvancedPatterns {
    
    // Double-checked locking для ленивой инициализации
    private static class LazySingleton {
        private static volatile LazySingleton instance;
        
        private LazySingleton() {}
        
        public static LazySingleton getInstance() {
            if (instance == null) { // Первая проверка без синхронизации
                synchronized (LazySingleton.class) {
                    if (instance == null) { // Вторая проверка в синхронизированном блоке
                        instance = new LazySingleton();
                    }
                }
            }
            return instance;
        }
    }
    
    // Enum singleton (лучший способ для singleton)
    private enum EnumSingleton {
        INSTANCE;
        
        public void doSomething() {
            System.out.println("Enum singleton работает");
        }
    }
    
    // Producer-Consumer с лимитированной очередью
    public static void demonstrateProducerConsumerWithLimit() throws InterruptedException {
        BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(5);
        CountDownLatch latch = new CountDownLatch(2);
        
        // Producer
        Thread producer = new Thread(() -> {
            try {
                for (int i = 1; i <= 10; i++) {
                    queue.put(i);
                    System.out.println("Произведено: " + i);
                    Thread.sleep(100);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            } finally {
                latch.countDown();
            }
        }, "Producer");
        
        // Consumer
        Thread consumer = new Thread(() -> {
            try {
                for (int i = 1; i <= 10; i++) {
                    Integer item = queue.take();
                    System.out.println("Потреблено: " + item);
                    Thread.sleep(200);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            } finally {
                latch.countDown();
            }
        }, "Consumer");
        
        producer.start();
        consumer.start();
        
        // Ждем завершения обоих потоков
        latch.await();
    }
}
```

---

## 🔗 Связанные темы
- [[Lambda-Expressions-Code|Lambda выражения]] - в многопоточном программировании
- [[Collections-Overview|Collections Framework]] - потокобезопасные коллекции
- [[Exception-Handling-Code|Обработка исключений]] - в многопоточной среде
- [[Best-Practices-Code|Лучшие практики]] - thread-safe код