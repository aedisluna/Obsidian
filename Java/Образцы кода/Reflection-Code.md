# Reflection в Java
*Исследование и модификация программы во время выполнения*

#java #reflection #runtime #метаданные #dynamic

---

## 📋 Содержание
- [[#Основы Reflection]]
- [[#Работа с классами]]
- [[#Поля и методы]]
- [[#Конструкторы]]
- [[#Динамическое создание объектов]]
- [[#Производительность и безопасность]]
- [[#Практические примеры]]

---

## 🎯 Основы Reflection

> [!info] Reflection - это возможность программы исследовать и модифицировать свою структуру и поведение во время выполнения

### Зачем нужна рефлексия?
- ✅ **Frameworks** - Spring, Hibernate используют reflection для DI и ORM
- ✅ **Тестирование** - доступ к private методам и полям
- ✅ **Сериализация** - JSON/XML библиотеки
- ✅ **Конфигурация** - динамическая настройка приложений
- ✅ **Анализ кода** - IDE, debuggers, profilers
- ✅ **Метапrogramming** - создание кода во время выполнения

### Основные классы Reflection API
```java
import java.lang.reflect.*;

public class ReflectionBasics {
    
    public static void exploreBasicReflection() {
        // Получение объекта Class
        Class<String> stringClass1 = String.class;                    // Через литерал класса
        Class<?> stringClass2 = "Hello".getClass();                   // Через объект
        
        try {
            Class<?> stringClass3 = Class.forName("java.lang.String"); // Через имя класса
            
            System.out.println("=== Basic Class Information ===");
            System.out.println("Class name: " + stringClass1.getName());
            System.out.println("Simple name: " + stringClass1.getSimpleName());
            System.out.println("Package: " + stringClass1.getPackage().getName());
            System.out.println("Is interface: " + stringClass1.isInterface());
            System.out.println("Is array: " + stringClass1.isArray());
            System.out.println("Is primitive: " + stringClass1.isPrimitive());
            System.out.println("Modifiers: " + Modifier.toString(stringClass1.getModifiers()));
            
            // Суперкласс и интерфейсы
            Class<?> superClass = stringClass1.getSuperclass();
            if (superClass != null) {
                System.out.println("Superclass: " + superClass.getName());
            }
            
            Class<?>[] interfaces = stringClass1.getInterfaces();
            System.out.println("Interfaces:");
            for (Class<?> iface : interfaces) {
                System.out.println("  - " + iface.getName());
            }
            
        } catch (ClassNotFoundException e) {
            System.err.println("Class not found: " + e.getMessage());
        }
    }
    
    public static void main(String[] args) {
        exploreBasicReflection();
    }
}
```

---

## 🏗️ Работа с классами

### Анализ структуры класса
```java
import java.lang.reflect.*;
import java.util.*;

// Тестовый класс для демонстрации
class Person {
    public static final String SPECIES = "Homo sapiens";
    private static int instanceCount = 0;
    
    private String name;
    private int age;
    protected String email;
    public String phoneNumber;
    
    static {
        System.out.println("Static block executed");
    }
    
    public Person() {
        this("Unknown", 0);
    }
    
    public Person(String name, int age) {
        this(name, age, "");
    }
    
    public Person(String name, int age, String email) {
        this.name = name;
        this.age = age;
        this.email = email;
        instanceCount++;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    protected String getEmail() { return email; }
    
    public void setName(String name) { this.name = name; }
    public void setAge(int age) { this.age = age; }
    
    private void secretMethod() {
        System.out.println("This is a private method!");
    }
    
    public static int getInstanceCount() {
        return instanceCount;
    }
    
    public static void resetInstanceCount() {
        instanceCount = 0;
    }
    
    @Override
    public String toString() {
        return String.format("Person{name='%s', age=%d, email='%s'}", name, age, email);
    }
}

public class ClassAnalysis {
    
    public static void analyzeClass(Class<?> clazz) {
        System.out.println("=== Detailed Class Analysis: " + clazz.getSimpleName() + " ===");
        
        analyzeClassHierarchy(clazz);
        analyzeFields(clazz);
        analyzeMethods(clazz);
        analyzeConstructors(clazz);
    }
    
    private static void analyzeClassHierarchy(Class<?> clazz) {
        System.out.println("\\n--- Class Hierarchy ---");
        
        // Построение иерархии наследования
        List<Class<?>> hierarchy = new ArrayList<>();
        Class<?> current = clazz;
        while (current != null) {
            hierarchy.add(current);
            current = current.getSuperclass();
        }
        
        System.out.println("Inheritance chain:");
        for (int i = hierarchy.size() - 1; i >= 0; i--) {
            String indent = "  ".repeat(hierarchy.size() - 1 - i);
            System.out.println(indent + hierarchy.get(i).getSimpleName());
            if (i > 0) {
                System.out.println(indent + "  ↑");
            }
        }
        
        // Анализ интерфейсов
        Class<?>[] interfaces = clazz.getInterfaces();
        if (interfaces.length > 0) {
            System.out.println("\\nDirect interfaces:");
            for (Class<?> iface : interfaces) {
                System.out.println("  - " + iface.getName());
            }
        }
        
        // Все интерфейсы (включая унаследованные)
        Set<Class<?>> allInterfaces = getAllInterfaces(clazz);
        if (!allInterfaces.isEmpty()) {
            System.out.println("\\nAll interfaces (including inherited):");
            allInterfaces.forEach(iface -> System.out.println("  - " + iface.getName()));
        }
    }
    
    private static Set<Class<?>> getAllInterfaces(Class<?> clazz) {
        Set<Class<?>> interfaces = new LinkedHashSet<>();
        
        while (clazz != null) {
            for (Class<?> iface : clazz.getInterfaces()) {
                interfaces.add(iface);
                interfaces.addAll(getAllInterfaces(iface));
            }
            clazz = clazz.getSuperclass();
        }
        
        return interfaces;
    }
    
    private static void analyzeFields(Class<?> clazz) {
        System.out.println("\\n--- Fields ---");
        
        Field[] allFields = clazz.getDeclaredFields();
        if (allFields.length == 0) {
            System.out.println("No declared fields");
            return;
        }
        
        System.out.printf("%-15s %-20s %-15s %s%n", "Modifiers", "Type", "Name", "Value (if static)");
        System.out.println("-".repeat(70));
        
        for (Field field : allFields) {
            String modifiers = Modifier.toString(field.getModifiers());
            String type = field.getType().getSimpleName();
            String name = field.getName();
            
            String value = "";
            if (Modifier.isStatic(field.getModifiers())) {
                try {
                    field.setAccessible(true);
                    Object staticValue = field.get(null);
                    value = staticValue != null ? staticValue.toString() : "null";
                } catch (Exception e) {
                    value = "Error: " + e.getMessage();
                }
            }
            
            System.out.printf("%-15s %-20s %-15s %s%n", modifiers, type, name, value);
        }
    }
    
    private static void analyzeMethods(Class<?> clazz) {
        System.out.println("\\n--- Methods ---");
        
        Method[] allMethods = clazz.getDeclaredMethods();
        if (allMethods.length == 0) {
            System.out.println("No declared methods");
            return;
        }
        
        // Группируем методы по типам
        List<Method> publicMethods = new ArrayList<>();
        List<Method> protectedMethods = new ArrayList<>();
        List<Method> privateMethods = new ArrayList<>();
        List<Method> staticMethods = new ArrayList<>();
        
        for (Method method : allMethods) {
            if (Modifier.isStatic(method.getModifiers())) {
                staticMethods.add(method);
            } else if (Modifier.isPublic(method.getModifiers())) {
                publicMethods.add(method);
            } else if (Modifier.isProtected(method.getModifiers())) {
                protectedMethods.add(method);
            } else if (Modifier.isPrivate(method.getModifiers())) {
                privateMethods.add(method);
            }
        }
        
        printMethodGroup("Public Methods", publicMethods);
        printMethodGroup("Protected Methods", protectedMethods);
        printMethodGroup("Private Methods", privateMethods);
        printMethodGroup("Static Methods", staticMethods);
    }
    
    private static void printMethodGroup(String groupName, List<Method> methods) {
        if (methods.isEmpty()) return;
        
        System.out.println("\\n" + groupName + ":");
        for (Method method : methods) {
            String signature = buildMethodSignature(method);
            System.out.println("  " + signature);
        }
    }
    
    private static String buildMethodSignature(Method method) {
        StringBuilder sb = new StringBuilder();
        
        // Модификаторы
        String modifiers = Modifier.toString(method.getModifiers());
        if (!modifiers.isEmpty()) {
            sb.append(modifiers).append(" ");
        }
        
        // Возвращаемый тип
        sb.append(method.getReturnType().getSimpleName()).append(" ");
        
        // Имя метода
        sb.append(method.getName()).append("(");
        
        // Параметры
        Parameter[] parameters = method.getParameters();
        for (int i = 0; i < parameters.length; i++) {
            if (i > 0) sb.append(", ");
            sb.append(parameters[i].getType().getSimpleName());
            sb.append(" ").append(parameters[i].getName());
        }
        
        sb.append(")");
        
        // Исключения
        Class<?>[] exceptions = method.getExceptionTypes();
        if (exceptions.length > 0) {
            sb.append(" throws ");
            for (int i = 0; i < exceptions.length; i++) {
                if (i > 0) sb.append(", ");
                sb.append(exceptions[i].getSimpleName());
            }
        }
        
        return sb.toString();
    }
    
    private static void analyzeConstructors(Class<?> clazz) {
        System.out.println("\\n--- Constructors ---");
        
        Constructor<?>[] constructors = clazz.getDeclaredConstructors();
        if (constructors.length == 0) {
            System.out.println("No declared constructors");
            return;
        }
        
        for (Constructor<?> constructor : constructors) {
            StringBuilder sb = new StringBuilder();
            
            String modifiers = Modifier.toString(constructor.getModifiers());
            if (!modifiers.isEmpty()) {
                sb.append(modifiers).append(" ");
            }
            
            sb.append(clazz.getSimpleName()).append("(");
            
            Parameter[] parameters = constructor.getParameters();
            for (int i = 0; i < parameters.length; i++) {
                if (i > 0) sb.append(", ");
                sb.append(parameters[i].getType().getSimpleName());
                sb.append(" ").append(parameters[i].getName());
            }
            
            sb.append(")");
            
            Class<?>[] exceptions = constructor.getExceptionTypes();
            if (exceptions.length > 0) {
                sb.append(" throws ");
                for (int i = 0; i < exceptions.length; i++) {
                    if (i > 0) sb.append(", ");
                    sb.append(exceptions[i].getSimpleName());
                }
            }
            
            System.out.println("  " + sb.toString());
        }
    }
    
    public static void main(String[] args) {
        analyzeClass(Person.class);
        analyzeClass(String.class);
        analyzeClass(ArrayList.class);
    }
}
```

---

## 🔧 Поля и методы

### Работа с полями через рефлексию
```java
import java.lang.reflect.Field;

public class FieldReflectionExample {
    
    // Различные типы полей для тестирования
    public static final String PUBLIC_STATIC_FINAL = "Constant Value";
    private static int privateStaticField = 42;
    
    private String privateField = "private data";
    protected int protectedField = 10;
    public double publicField = 3.14;
    
    public static void demonstrateFieldAccess() {
        FieldReflectionExample instance = new FieldReflectionExample();
        Class<?> clazz = instance.getClass();
        
        System.out.println("=== Field Access Examples ===");
        
        try {
            // Чтение публичного поля
            Field publicField = clazz.getField("publicField");
            double publicValue = (Double) publicField.get(instance);
            System.out.println("Public field value: " + publicValue);
            
            // Изменение публичного поля
            publicField.set(instance, 2.71);
            System.out.println("Public field after change: " + publicField.get(instance));
            
            // Чтение приватного поля
            Field privateField = clazz.getDeclaredField("privateField");
            privateField.setAccessible(true); // Снимаем ограничение доступа
            String privateValue = (String) privateField.get(instance);
            System.out.println("Private field value: " + privateValue);
            
            // Изменение приватного поля
            privateField.set(instance, "modified private data");
            System.out.println("Private field after change: " + privateField.get(instance));
            
            // Работа со статическими полями
            Field staticField = clazz.getDeclaredField("privateStaticField");
            staticField.setAccessible(true);
            int staticValue = (Integer) staticField.get(null); // null для статических полей
            System.out.println("Static field value: " + staticValue);
            
            staticField.set(null, 100);
            System.out.println("Static field after change: " + staticField.get(null));
            
        } catch (NoSuchFieldException | IllegalAccessException e) {
            System.err.println("Field access error: " + e.getMessage());
        }
    }
    
    // Универсальный метод для получения значения поля
    public static Object getFieldValue(Object obj, String fieldName) {
        try {
            Class<?> clazz = obj.getClass();
            Field field = findField(clazz, fieldName);
            
            if (field != null) {
                field.setAccessible(true);
                return field.get(obj);
            } else {
                throw new NoSuchFieldException("Field '" + fieldName + "' not found in " + clazz.getName());
            }
        } catch (Exception e) {
            throw new RuntimeException("Failed to get field value", e);
        }
    }
    
    // Универсальный метод для установки значения поля
    public static void setFieldValue(Object obj, String fieldName, Object value) {
        try {
            Class<?> clazz = obj.getClass();
            Field field = findField(clazz, fieldName);
            
            if (field != null) {
                field.setAccessible(true);
                field.set(obj, value);
            } else {
                throw new NoSuchFieldException("Field '" + fieldName + "' not found in " + clazz.getName());
            }
        } catch (Exception e) {
            throw new RuntimeException("Failed to set field value", e);
        }
    }
    
    // Поиск поля в иерархии классов
    private static Field findField(Class<?> clazz, String fieldName) {
        while (clazz != null) {
            try {
                return clazz.getDeclaredField(fieldName);
            } catch (NoSuchFieldException e) {
                clazz = clazz.getSuperclass();
            }
        }
        return null;
    }
    
    // Копирование значений полей между объектами
    public static <T> T copyFields(T source, T target) {
        Class<?> clazz = source.getClass();
        
        while (clazz != null) {
            Field[] fields = clazz.getDeclaredFields();
            
            for (Field field : fields) {
                if (!Modifier.isStatic(field.getModifiers()) && !Modifier.isFinal(field.getModifiers())) {
                    try {
                        field.setAccessible(true);
                        Object value = field.get(source);
                        field.set(target, value);
                    } catch (IllegalAccessException e) {
                        System.err.println("Cannot copy field " + field.getName() + ": " + e.getMessage());
                    }
                }
            }
            
            clazz = clazz.getSuperclass();
        }
        
        return target;
    }
    
    public static void main(String[] args) {
        demonstrateFieldAccess();
        
        System.out.println("\\n=== Universal Field Access ===");
        
        FieldReflectionExample obj1 = new FieldReflectionExample();
        FieldReflectionExample obj2 = new FieldReflectionExample();
        
        // Изменяем поля первого объекта
        setFieldValue(obj1, "privateField", "changed by reflection");
        setFieldValue(obj1, "protectedField", 99);
        
        System.out.println("Object 1 private field: " + getFieldValue(obj1, "privateField"));
        System.out.println("Object 1 protected field: " + getFieldValue(obj1, "protectedField"));
        
        System.out.println("\\n=== Field Copying ===");
        System.out.println("Before copy - Object 2 private field: " + getFieldValue(obj2, "privateField"));
        
        copyFields(obj1, obj2);
        System.out.println("After copy - Object 2 private field: " + getFieldValue(obj2, "privateField"));
        System.out.println("After copy - Object 2 protected field: " + getFieldValue(obj2, "protectedField"));
    }
}
```

### Динамический вызов методов
```java
import java.lang.reflect.Method;
import java.lang.reflect.Parameter;

public class MethodReflectionExample {
    
    // Тестовые методы различных типов
    public String publicMethod(String input) {
        return "Public method called with: " + input;
    }
    
    private int privateMethod(int a, int b) {
        return a + b;
    }
    
    protected void protectedMethod() {
        System.out.println("Protected method called");
    }
    
    public static String staticMethod(String prefix, String suffix) {
        return prefix + " STATIC " + suffix;
    }
    
    // Метод с исключением
    public void methodWithException() throws RuntimeException {
        throw new RuntimeException("This method always throws an exception");
    }
    
    // Overloaded методы
    public String overloadedMethod(String str) {
        return "String version: " + str;
    }
    
    public String overloadedMethod(int num) {
        return "Integer version: " + num;
    }
    
    public String overloadedMethod(String str, int num) {
        return "String + Integer version: " + str + " + " + num;
    }
    
    public static void demonstrateMethodInvocation() {
        System.out.println("=== Method Invocation Examples ===");
        
        MethodReflectionExample instance = new MethodReflectionExample();
        Class<?> clazz = instance.getClass();
        
        try {
            // Вызов публичного метода
            Method publicMethod = clazz.getMethod("publicMethod", String.class);
            Object result1 = publicMethod.invoke(instance, "Hello World");
            System.out.println("Public method result: " + result1);
            
            // Вызов приватного метода
            Method privateMethod = clazz.getDeclaredMethod("privateMethod", int.class, int.class);
            privateMethod.setAccessible(true);
            Object result2 = privateMethod.invoke(instance, 5, 3);
            System.out.println("Private method result: " + result2);
            
            // Вызов защищенного метода
            Method protectedMethod = clazz.getDeclaredMethod("protectedMethod");
            protectedMethod.setAccessible(true);
            protectedMethod.invoke(instance);
            
            // Вызов статического метода
            Method staticMethod = clazz.getMethod("staticMethod", String.class, String.class);
            Object result3 = staticMethod.invoke(null, "START", "END");
            System.out.println("Static method result: " + result3);
            
            // Вызов перегруженных методов
            Method overloaded1 = clazz.getMethod("overloadedMethod", String.class);
            Method overloaded2 = clazz.getMethod("overloadedMethod", int.class);
            Method overloaded3 = clazz.getMethod("overloadedMethod", String.class, int.class);
            
            System.out.println("Overloaded 1: " + overloaded1.invoke(instance, "Test"));
            System.out.println("Overloaded 2: " + overloaded2.invoke(instance, 42));
            System.out.println("Overloaded 3: " + overloaded3.invoke(instance, "Test", 42));
            
        } catch (Exception e) {
            System.err.println("Method invocation error: " + e.getMessage());
        }
        
        // Обработка исключений в вызываемых методах
        System.out.println("\\n=== Exception Handling ===");
        try {
            Method exceptionMethod = clazz.getMethod("methodWithException");
            exceptionMethod.invoke(instance);
        } catch (Exception e) {
            System.out.println("Caught exception from reflected method: " + e.getCause().getMessage());
        }
    }
    
    // Универсальный метод для поиска и вызова метода
    public static Object invokeMethod(Object obj, String methodName, Object... args) {
        try {
            Class<?> clazz = obj.getClass();
            Class<?>[] paramTypes = new Class[args.length];
            
            // Определяем типы параметров
            for (int i = 0; i < args.length; i++) {
                paramTypes[i] = args[i] != null ? args[i].getClass() : Object.class;
            }
            
            // Ищем подходящий метод
            Method method = findCompatibleMethod(clazz, methodName, paramTypes);
            
            if (method != null) {
                method.setAccessible(true);
                return method.invoke(obj, args);
            } else {
                throw new NoSuchMethodException("No compatible method found: " + methodName);
            }
            
        } catch (Exception e) {
            throw new RuntimeException("Failed to invoke method: " + methodName, e);
        }
    }
    
    // Поиск совместимого метода (учитывая наследование и boxing/unboxing)
    private static Method findCompatibleMethod(Class<?> clazz, String methodName, Class<?>[] paramTypes) {
        Method[] methods = clazz.getDeclaredMethods();
        
        for (Method method : methods) {
            if (method.getName().equals(methodName) && isCompatible(method.getParameterTypes(), paramTypes)) {
                return method;
            }
        }
        
        // Ищем в родительских классах
        Class<?> superClass = clazz.getSuperclass();
        if (superClass != null) {
            return findCompatibleMethod(superClass, methodName, paramTypes);
        }
        
        return null;
    }
    
    // Проверка совместимости типов параметров
    private static boolean isCompatible(Class<?>[] methodParams, Class<?>[] callParams) {
        if (methodParams.length != callParams.length) {
            return false;
        }
        
        for (int i = 0; i < methodParams.length; i++) {
            if (!isAssignable(methodParams[i], callParams[i])) {
                return false;
            }
        }
        
        return true;
    }
    
    // Проверка возможности присваивания (с учетом boxing/unboxing)
    private static boolean isAssignable(Class<?> target, Class<?> source) {
        if (target.isAssignableFrom(source)) {
            return true;
        }
        
        // Проверяем boxing/unboxing для примитивных типов
        if (target.isPrimitive()) {
            if (target == int.class && (source == Integer.class)) return true;
            if (target == long.class && (source == Long.class)) return true;
            if (target == double.class && (source == Double.class)) return true;
            if (target == float.class && (source == Float.class)) return true;
            if (target == boolean.class && (source == Boolean.class)) return true;
            if (target == byte.class && (source == Byte.class)) return true;
            if (target == short.class && (source == Short.class)) return true;
            if (target == char.class && (source == Character.class)) return true;
        }
        
        if (source.isPrimitive()) {
            if (source == int.class && (target == Integer.class || target == Object.class)) return true;
            if (source == long.class && (target == Long.class || target == Object.class)) return true;
            if (source == double.class && (target == Double.class || target == Object.class)) return true;
            if (source == float.class && (target == Float.class || target == Object.class)) return true;
            if (source == boolean.class && (target == Boolean.class || target == Object.class)) return true;
            if (source == byte.class && (target == Byte.class || target == Object.class)) return true;
            if (source == short.class && (target == Short.class || target == Object.class)) return true;
            if (source == char.class && (target == Character.class || target == Object.class)) return true;
        }
        
        return false;
    }
    
    // Получение информации о параметрах метода
    public static void analyzeMethodParameters() {
        System.out.println("\\n=== Method Parameter Analysis ===");
        
        Class<?> clazz = MethodReflectionExample.class;
        
        try {
            Method method = clazz.getMethod("overloadedMethod", String.class, int.class);
            
            System.out.println("Method: " + method.getName());
            System.out.println("Parameter count: " + method.getParameterCount());
            
            Parameter[] parameters = method.getParameters();
            for (int i = 0; i < parameters.length; i++) {
                Parameter param = parameters[i];
                System.out.printf("Parameter %d: %s %s%n", 
                                i, param.getType().getSimpleName(), param.getName());
            }
            
        } catch (NoSuchMethodException e) {
            System.err.println("Method not found: " + e.getMessage());
        }
    }
    
    public static void main(String[] args) {
        demonstrateMethodInvocation();
        
        System.out.println("\\n=== Universal Method Invocation ===");
        
        MethodReflectionExample instance = new MethodReflectionExample();
        
        // Тестируем универсальный вызов методов
        Object result1 = invokeMethod(instance, "publicMethod", "Universal call");
        System.out.println("Universal call result: " + result1);
        
        Object result2 = invokeMethod(instance, "overloadedMethod", "Test", 123);
        System.out.println("Universal overloaded call result: " + result2);
        
        analyzeMethodParameters();
    }
}
```

---

## 🏗️ Конструкторы

### Динамическое создание объектов
```java
import java.lang.reflect.Constructor;
import java.lang.reflect.Parameter;

// Тестовые классы для демонстрации
class Product {
    private String name;
    private double price;
    private String category;
    
    // Различные конструкторы
    public Product() {
        this("Unknown", 0.0, "General");
    }
    
    public Product(String name) {
        this(name, 0.0, "General");
    }
    
    public Product(String name, double price) {
        this(name, price, "General");
    }
    
    public Product(String name, double price, String category) {
        this.name = name;
        this.price = price;
        this.category = category;
    }
    
    // Приватный конструктор
    private Product(double price) {
        this("Special", price, "Private");
    }
    
    // Геттеры
    public String getName() { return name; }
    public double getPrice() { return price; }
    public String getCategory() { return category; }
    
    @Override
    public String toString() {
        return String.format("Product{name='%s', price=%.2f, category='%s'}", name, price, category);
    }
}

public class ConstructorReflectionExample {
    
    public static void demonstrateConstructorUsage() {
        System.out.println("=== Constructor Reflection Examples ===");
        
        Class<?> productClass = Product.class;
        
        try {
            // Создание объекта через конструктор по умолчанию
            Constructor<?> defaultConstructor = productClass.getConstructor();
            Object product1 = defaultConstructor.newInstance();
            System.out.println("Default constructor: " + product1);
            
            // Создание объекта через конструктор с одним параметром
            Constructor<?> oneParamConstructor = productClass.getConstructor(String.class);
            Object product2 = oneParamConstructor.newInstance("Laptop");
            System.out.println("One parameter constructor: " + product2);
            
            // Создание объекта через конструктор с двумя параметрами
            Constructor<?> twoParamConstructor = productClass.getConstructor(String.class, double.class);
            Object product3 = twoParamConstructor.newInstance("Smartphone", 599.99);
            System.out.println("Two parameter constructor: " + product3);
            
            // Создание объекта через полный конструктор
            Constructor<?> fullConstructor = productClass.getConstructor(String.class, double.class, String.class);
            Object product4 = fullConstructor.newInstance("Tablet", 399.99, "Electronics");
            System.out.println("Full constructor: " + product4);
            
            // Создание объекта через приватный конструктор
            Constructor<?> privateConstructor = productClass.getDeclaredConstructor(double.class);
            privateConstructor.setAccessible(true);
            Object product5 = privateConstructor.newInstance(999.99);
            System.out.println("Private constructor: " + product5);
            
        } catch (Exception e) {
            System.err.println("Constructor usage error: " + e.getMessage());
            e.printStackTrace();
        }
    }
    
    // Анализ всех конструкторов класса
    public static void analyzeConstructors(Class<?> clazz) {
        System.out.println("\\n=== Constructor Analysis for " + clazz.getSimpleName() + " ===");
        
        Constructor<?>[] constructors = clazz.getDeclaredConstructors();
        
        for (int i = 0; i < constructors.length; i++) {
            Constructor<?> constructor = constructors[i];
            
            System.out.printf("\\nConstructor %d:%n", i + 1);
            System.out.printf("  Modifiers: %s%n", Modifier.toString(constructor.getModifiers()));
            System.out.printf("  Parameter count: %d%n", constructor.getParameterCount());
            
            if (constructor.getParameterCount() > 0) {
                System.out.println("  Parameters:");
                Parameter[] parameters = constructor.getParameters();
                for (int j = 0; j < parameters.length; j++) {
                    Parameter param = parameters[j];
                    System.out.printf("    %d: %s %s%n", 
                                    j + 1, param.getType().getSimpleName(), param.getName());
                }
            }
            
            Class<?>[] exceptions = constructor.getExceptionTypes();
            if (exceptions.length > 0) {
                System.out.println("  Declared exceptions:");
                for (Class<?> exception : exceptions) {
                    System.out.println("    - " + exception.getSimpleName());
                }
            }
        }
    }
    
    // Универсальная фабрика объектов
    public static <T> T createInstance(Class<T> clazz, Object... args) {
        try {
            if (args.length == 0) {
                // Пытаемся найти конструктор по умолчанию
                Constructor<T> constructor = clazz.getDeclaredConstructor();
                constructor.setAccessible(true);
                return constructor.newInstance();
            } else {
                // Ищем подходящий конструктор по типам аргументов
                Constructor<T> constructor = findBestConstructor(clazz, args);
                if (constructor != null) {
                    constructor.setAccessible(true);
                    return constructor.newInstance(args);
                } else {
                    throw new IllegalArgumentException("No suitable constructor found for arguments");
                }
            }
        } catch (Exception e) {
            throw new RuntimeException("Failed to create instance of " + clazz.getSimpleName(), e);
        }
    }
    
    // Поиск наиболее подходящего конструктора
    @SuppressWarnings("unchecked")
    private static <T> Constructor<T> findBestConstructor(Class<T> clazz, Object[] args) {
        Constructor<?>[] constructors = clazz.getDeclaredConstructors();
        
        for (Constructor<?> constructor : constructors) {
            if (isConstructorCompatible(constructor, args)) {
                return (Constructor<T>) constructor;
            }
        }
        
        return null;
    }
    
    // Проверка совместимости конструктора с аргументами
    private static boolean isConstructorCompatible(Constructor<?> constructor, Object[] args) {
        Class<?>[] paramTypes = constructor.getParameterTypes();
        
        if (paramTypes.length != args.length) {
            return false;
        }
        
        for (int i = 0; i < paramTypes.length; i++) {
            if (args[i] != null && !isAssignableFrom(paramTypes[i], args[i].getClass())) {
                return false;
            }
        }
        
        return true;
    }
    
    // Проверка возможности присваивания с учетом boxing/unboxing
    private static boolean isAssignableFrom(Class<?> target, Class<?> source) {
        if (target.isAssignableFrom(source)) {
            return true;
        }
        
        // Проверяем boxing/unboxing
        Map<Class<?>, Class<?>> primitiveToWrapper = Map.of(
            boolean.class, Boolean.class,
            byte.class, Byte.class,
            char.class, Character.class,
            double.class, Double.class,
            float.class, Float.class,
            int.class, Integer.class,
            long.class, Long.class,
            short.class, Short.class
        );
        
        Map<Class<?>, Class<?>> wrapperToPrimitive = Map.of(
            Boolean.class, boolean.class,
            Byte.class, byte.class,
            Character.class, char.class,
            Double.class, double.class,
            Float.class, float.class,
            Integer.class, int.class,
            Long.class, long.class,
            Short.class, short.class
        );
        
        return (primitiveToWrapper.containsKey(target) && primitiveToWrapper.get(target) == source) ||
               (wrapperToPrimitive.containsKey(target) && wrapperToPrimitive.get(target) == source);
    }
    
    // Клонирование объектов через рефлексию
    public static <T> T deepClone(T original) {
        if (original == null) {
            return null;
        }
        
        try {
            @SuppressWarnings("unchecked")
            Class<T> clazz = (Class<T>) original.getClass();
            
            // Создаем новый экземпляр
            T clone = clazz.getDeclaredConstructor().newInstance();
            
            // Копируем все поля
            copyAllFields(original, clone, clazz);
            
            return clone;
            
        } catch (Exception e) {
            throw new RuntimeException("Failed to clone object", e);
        }
    }
    
    private static void copyAllFields(Object source, Object target, Class<?> clazz) 
            throws IllegalAccessException {
        while (clazz != null) {
            Field[] fields = clazz.getDeclaredFields();
            
            for (Field field : fields) {
                if (!Modifier.isStatic(field.getModifiers())) {
                    field.setAccessible(true);
                    Object value = field.get(source);
                    field.set(target, value);
                }
            }
            
            clazz = clazz.getSuperclass();
        }
    }
    
    public static void main(String[] args) {
        demonstrateConstructorUsage();
        analyzeConstructors(Product.class);
        
        System.out.println("\\n=== Universal Object Creation ===");
        
        // Тестируем универсальную фабрику
        Product product1 = createInstance(Product.class);
        System.out.println("Factory default: " + product1);
        
        Product product2 = createInstance(Product.class, "Factory Product");
        System.out.println("Factory with name: " + product2);
        
        Product product3 = createInstance(Product.class, "Factory Product", 299.99);
        System.out.println("Factory with name and price: " + product3);
        
        Product product4 = createInstance(Product.class, "Factory Product", 299.99, "Factory");
        System.out.println("Factory full: " + product4);
        
        System.out.println("\\n=== Object Cloning ===");
        
        Product original = new Product("Original Product", 199.99, "Original");
        Product cloned = deepClone(original);
        
        System.out.println("Original: " + original);
        System.out.println("Cloned: " + cloned);
        System.out.println("Are they the same object? " + (original == cloned));
        System.out.println("Are they equal? " + original.toString().equals(cloned.toString()));
    }
}
```

---

## ⚡ Производительность и безопасность

### Кэширование и оптимизация
```java
import java.lang.reflect.*;
import java.util.concurrent.ConcurrentHashMap;

public class ReflectionOptimization {
    
    // Кэш для часто используемых метаданных
    private static final Map<String, Class<?>> classCache = new ConcurrentHashMap<>();
    private static final Map<String, Method> methodCache = new ConcurrentHashMap<>();
    private static final Map<String, Field> fieldCache = new ConcurrentHashMap<>();
    private static final Map<String, Constructor<?>> constructorCache = new ConcurrentHashMap<>();
    
    // Кэшированное получение класса
    public static Class<?> getCachedClass(String className) throws ClassNotFoundException {
        return classCache.computeIfAbsent(className, name -> {
            try {
                return Class.forName(name);
            } catch (ClassNotFoundException e) {
                throw new RuntimeException(e);
            }
        });
    }
    
    // Кэшированное получение метода
    public static Method getCachedMethod(Class<?> clazz, String methodName, Class<?>... paramTypes) {
        String key = buildMethodKey(clazz, methodName, paramTypes);
        return methodCache.computeIfAbsent(key, k -> {
            try {
                Method method = clazz.getDeclaredMethod(methodName, paramTypes);
                method.setAccessible(true);
                return method;
            } catch (NoSuchMethodException e) {
                throw new RuntimeException(e);
            }
        });
    }
    
    // Кэшированное получение поля
    public static Field getCachedField(Class<?> clazz, String fieldName) {
        String key = clazz.getName() + "." + fieldName;
        return fieldCache.computeIfAbsent(key, k -> {
            try {
                Field field = clazz.getDeclaredField(fieldName);
                field.setAccessible(true);
                return field;
            } catch (NoSuchFieldException e) {
                throw new RuntimeException(e);
            }
        });
    }
    
    // Кэшированное получение конструктора
    public static Constructor<?> getCachedConstructor(Class<?> clazz, Class<?>... paramTypes) {
        String key = buildConstructorKey(clazz, paramTypes);
        return constructorCache.computeIfAbsent(key, k -> {
            try {
                Constructor<?> constructor = clazz.getDeclaredConstructor(paramTypes);
                constructor.setAccessible(true);
                return constructor;
            } catch (NoSuchMethodException e) {
                throw new RuntimeException(e);
            }
        });
    }
    
    private static String buildMethodKey(Class<?> clazz, String methodName, Class<?>[] paramTypes) {
        StringBuilder sb = new StringBuilder();
        sb.append(clazz.getName()).append(".").append(methodName).append("(");
        for (int i = 0; i < paramTypes.length; i++) {
            if (i > 0) sb.append(",");
            sb.append(paramTypes[i].getName());
        }
        sb.append(")");
        return sb.toString();
    }
    
    private static String buildConstructorKey(Class<?> clazz, Class<?>[] paramTypes) {
        StringBuilder sb = new StringBuilder();
        sb.append(clazz.getName()).append(".<init>(");
        for (int i = 0; i < paramTypes.length; i++) {
            if (i > 0) sb.append(",");
            sb.append(paramTypes[i].getName());
        }
        sb.append(")");
        return sb.toString();
    }
    
    // Производительный вызов метода с кэшированием
    public static Object invokeOptimized(Object obj, String methodName, Object... args) {
        try {
            Class<?> clazz = obj.getClass();
            Class<?>[] paramTypes = getParameterTypes(args);
            Method method = getCachedMethod(clazz, methodName, paramTypes);
            return method.invoke(obj, args);
        } catch (Exception e) {
            throw new RuntimeException("Optimized method invocation failed", e);
        }
    }
    
    private static Class<?>[] getParameterTypes(Object[] args) {
        Class<?>[] types = new Class[args.length];
        for (int i = 0; i < args.length; i++) {
            types[i] = args[i] != null ? args[i].getClass() : Object.class;
        }
        return types;
    }
    
    // Бенчмарк для сравнения производительности
    public static void performanceBenchmark() {
        System.out.println("=== Reflection Performance Benchmark ===");
        
        // Подготовка тестовых данных
        List<String> testStrings = Arrays.asList("test1", "test2", "test3", "test4", "test5");
        int iterations = 1000000;
        
        // Прогрев JVM
        for (int i = 0; i < 10000; i++) {
            testStrings.get(0).length();
            try {
                Method lengthMethod = String.class.getMethod("length");
                lengthMethod.invoke(testStrings.get(0));
            } catch (Exception e) {
                // ignore
            }
        }
        
        // Тест 1: Прямой вызов
        long startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            testStrings.get(i % testStrings.size()).length();
        }
        long directCallTime = System.nanoTime() - startTime;
        
        // Тест 2: Reflection без кэширования
        startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            try {
                String str = testStrings.get(i % testStrings.size());
                Method method = str.getClass().getMethod("length");
                method.invoke(str);
            } catch (Exception e) {
                // ignore
            }
        }
        long uncachedReflectionTime = System.nanoTime() - startTime;
        
        // Тест 3: Reflection с кэшированием
        startTime = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            try {
                String str = testStrings.get(i % testStrings.size());
                invokeOptimized(str, "length");
            } catch (Exception e) {
                // ignore
            }
        }
        long cachedReflectionTime = System.nanoTime() - startTime;
        
        // Результаты
        System.out.printf("Direct call: %.2f ms%n", directCallTime / 1_000_000.0);
        System.out.printf("Uncached reflection: %.2f ms%n", uncachedReflectionTime / 1_000_000.0);
        System.out.printf("Cached reflection: %.2f ms%n", cachedReflectionTime / 1_000_000.0);
        
        System.out.printf("Uncached reflection is %.1fx slower than direct call%n", 
                         (double) uncachedReflectionTime / directCallTime);
        System.out.printf("Cached reflection is %.1fx slower than direct call%n", 
                         (double) cachedReflectionTime / directCallTime);
        System.out.printf("Cached reflection is %.1fx faster than uncached%n", 
                         (double) uncachedReflectionTime / cachedReflectionTime);
        
        // Информация о кэше
        System.out.printf("\\nCache sizes: Classes=%d, Methods=%d, Fields=%d, Constructors=%d%n",
                         classCache.size(), methodCache.size(), fieldCache.size(), constructorCache.size());
    }
    
    // Безопасность и проверки
    public static void securityConsiderations() {
        System.out.println("\\n=== Security Considerations ===");
        
        // Пример потенциально опасного кода
        try {
            // Получение доступа к System.exit через рефлексию
            Class<?> systemClass = System.class;
            Method exitMethod = systemClass.getMethod("exit", int.class);
            
            System.out.println("Found System.exit method: " + exitMethod);
            System.out.println("This could be dangerous if called!");
            
            // НЕ вызываем exitMethod.invoke(null, 0) !
            
        } catch (Exception e) {
            System.err.println("Security example error: " + e.getMessage());
        }
        
        // Безопасная проверка доступности класса
        if (isClassAccessible("java.lang.System")) {
            System.out.println("System class is accessible");
        }
        
        // Безопасная проверка доступности метода
        if (isMethodAccessible(System.class, "exit", int.class)) {
            System.out.println("System.exit method is accessible - be careful!");
        }
    }
    
    private static boolean isClassAccessible(String className) {
        try {
            Class.forName(className);
            return true;
        } catch (ClassNotFoundException | SecurityException e) {
            return false;
        }
    }
    
    private static boolean isMethodAccessible(Class<?> clazz, String methodName, Class<?>... paramTypes) {
        try {
            clazz.getMethod(methodName, paramTypes);
            return true;
        } catch (NoSuchMethodException | SecurityException e) {
            return false;
        }
    }
    
    public static void main(String[] args) {
        performanceBenchmark();
        securityConsiderations();
    }
}
```

---

## 💼 Практические примеры

### Простой Object-Relational Mapping
```java
import java.lang.annotation.*;
import java.lang.reflect.Field;
import java.sql.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

// Аннотации для ORM
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Table {
    String name();
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Column {
    String name() default "";
    boolean primaryKey() default false;
    boolean autoIncrement() default false;
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Transient {
}

// Пример entity класса
@Table(name = "users")
class User {
    @Column(name = "id", primaryKey = true, autoIncrement = true)
    private Long id;
    
    @Column(name = "username")
    private String username;
    
    @Column(name = "email")
    private String email;
    
    @Column(name = "age")
    private Integer age;
    
    @Transient
    private String temporaryData;
    
    // Конструкторы
    public User() {}
    
    public User(String username, String email, Integer age) {
        this.username = username;
        this.email = email;
        this.age = age;
    }
    
    // Геттеры и сеттеры
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    
    public String getUsername() { return username; }
    public void setUsername(String username) { this.username = username; }
    
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    
    public Integer getAge() { return age; }
    public void setAge(Integer age) { this.age = age; }
    
    public String getTemporaryData() { return temporaryData; }
    public void setTemporaryData(String temporaryData) { this.temporaryData = temporaryData; }
    
    @Override
    public String toString() {
        return String.format("User{id=%d, username='%s', email='%s', age=%d}", 
                           id, username, email, age);
    }
}

// Простой ORM класс
public class SimpleORM {
    
    // Метаданные о таблице
    public static class EntityMetadata {
        public String tableName;
        public List<FieldMetadata> fields = new ArrayList<>();
        public FieldMetadata primaryKeyField;
    }
    
    public static class FieldMetadata {
        public Field field;
        public String columnName;
        public boolean isPrimaryKey;
        public boolean isAutoIncrement;
        public boolean isTransient;
    }
    
    private static final Map<Class<?>, EntityMetadata> metadataCache = new HashMap<>();
    
    // Получение метаданных с кэшированием
    public static EntityMetadata getEntityMetadata(Class<?> entityClass) {
        return metadataCache.computeIfAbsent(entityClass, SimpleORM::buildEntityMetadata);
    }
    
    private static EntityMetadata buildEntityMetadata(Class<?> entityClass) {
        EntityMetadata metadata = new EntityMetadata();
        
        // Получаем имя таблицы
        Table tableAnnotation = entityClass.getAnnotation(Table.class);
        if (tableAnnotation != null) {
            metadata.tableName = tableAnnotation.name();
        } else {
            metadata.tableName = entityClass.getSimpleName().toLowerCase();
        }
        
        // Анализируем поля
        Field[] fields = entityClass.getDeclaredFields();
        for (Field field : fields) {
            field.setAccessible(true);
            
            // Пропускаем @Transient поля
            if (field.isAnnotationPresent(Transient.class)) {
                continue;
            }
            
            FieldMetadata fieldMeta = new FieldMetadata();
            fieldMeta.field = field;
            
            Column columnAnnotation = field.getAnnotation(Column.class);
            if (columnAnnotation != null) {
                fieldMeta.columnName = columnAnnotation.name().isEmpty() ? 
                                     field.getName() : columnAnnotation.name();
                fieldMeta.isPrimaryKey = columnAnnotation.primaryKey();
                fieldMeta.isAutoIncrement = columnAnnotation.autoIncrement();
            } else {
                fieldMeta.columnName = field.getName();
            }
            
            metadata.fields.add(fieldMeta);
            
            if (fieldMeta.isPrimaryKey) {
                metadata.primaryKeyField = fieldMeta;
            }
        }
        
        return metadata;
    }
    
    // Генерация SQL запросов
    public static String generateSelectSQL(Class<?> entityClass) {
        EntityMetadata metadata = getEntityMetadata(entityClass);
        
        StringBuilder sql = new StringBuilder("SELECT ");
        
        for (int i = 0; i < metadata.fields.size(); i++) {
            if (i > 0) sql.append(", ");
            sql.append(metadata.fields.get(i).columnName);
        }
        
        sql.append(" FROM ").append(metadata.tableName);
        
        return sql.toString();
    }
    
    public static String generateInsertSQL(Class<?> entityClass) {
        EntityMetadata metadata = getEntityMetadata(entityClass);
        
        StringBuilder sql = new StringBuilder("INSERT INTO ");
        sql.append(metadata.tableName).append(" (");
        
        StringBuilder values = new StringBuilder("VALUES (");
        
        boolean first = true;
        for (FieldMetadata field : metadata.fields) {
            if (field.isAutoIncrement) continue; // Пропускаем auto increment поля
            
            if (!first) {
                sql.append(", ");
                values.append(", ");
            }
            
            sql.append(field.columnName);
            values.append("?");
            first = false;
        }
        
        sql.append(") ").append(values.toString()).append(")");
        
        return sql.toString();
    }
    
    public static String generateUpdateSQL(Class<?> entityClass) {
        EntityMetadata metadata = getEntityMetadata(entityClass);
        
        StringBuilder sql = new StringBuilder("UPDATE ");
        sql.append(metadata.tableName).append(" SET ");
        
        boolean first = true;
        for (FieldMetadata field : metadata.fields) {
            if (field.isPrimaryKey) continue; // Пропускаем PK в SET части
            
            if (!first) sql.append(", ");
            sql.append(field.columnName).append(" = ?");
            first = false;
        }
        
        if (metadata.primaryKeyField != null) {
            sql.append(" WHERE ").append(metadata.primaryKeyField.columnName).append(" = ?");
        }
        
        return sql.toString();
    }
    
    public static String generateDeleteSQL(Class<?> entityClass) {
        EntityMetadata metadata = getEntityMetadata(entityClass);
        
        StringBuilder sql = new StringBuilder("DELETE FROM ");
        sql.append(metadata.tableName);
        
        if (metadata.primaryKeyField != null) {
            sql.append(" WHERE ").append(metadata.primaryKeyField.columnName).append(" = ?");
        }
        
        return sql.toString();
    }
    
    // Маппинг ResultSet в объект
    public static <T> T mapResultSetToEntity(ResultSet rs, Class<T> entityClass) throws Exception {
        T entity = entityClass.getDeclaredConstructor().newInstance();
        EntityMetadata metadata = getEntityMetadata(entityClass);
        
        for (FieldMetadata fieldMeta : metadata.fields) {
            Object value = rs.getObject(fieldMeta.columnName);
            fieldMeta.field.set(entity, value);
        }
        
        return entity;
    }
    
    // Получение значений из объекта для INSERT/UPDATE
    public static Object[] getFieldValues(Object entity, boolean includeAutoIncrement) throws Exception {
        EntityMetadata metadata = getEntityMetadata(entity.getClass());
        List<Object> values = new ArrayList<>();
        
        for (FieldMetadata fieldMeta : metadata.fields) {
            if (!includeAutoIncrement && fieldMeta.isAutoIncrement) {
                continue;
            }
            
            Object value = fieldMeta.field.get(entity);
            values.add(value);
        }
        
        return values.toArray();
    }
    
    public static void main(String[] args) {
        // Демонстрация работы простого ORM
        System.out.println("=== Simple ORM Demo ===");
        
        Class<User> userClass = User.class;
        
        // Получаем метаданные
        EntityMetadata metadata = getEntityMetadata(userClass);
        System.out.println("Table name: " + metadata.tableName);
        System.out.println("Fields:");
        for (FieldMetadata field : metadata.fields) {
            System.out.printf("  %s -> %s (PK: %b, Auto: %b)%n", 
                             field.field.getName(), field.columnName, 
                             field.isPrimaryKey, field.isAutoIncrement);
        }
        
        // Генерируем SQL запросы
        System.out.println("\\nGenerated SQL:");
        System.out.println("SELECT: " + generateSelectSQL(userClass));
        System.out.println("INSERT: " + generateInsertSQL(userClass));
        System.out.println("UPDATE: " + generateUpdateSQL(userClass));
        System.out.println("DELETE: " + generateDeleteSQL(userClass));
        
        // Тестируем получение значений полей
        System.out.println("\\n=== Field Values Test ===");
        User testUser = new User("john_doe", "john@example.com", 30);
        testUser.setId(1L);
        testUser.setTemporaryData("This should be ignored");
        
        try {
            Object[] values = getFieldValues(testUser, true);
            System.out.println("Field values (with auto-increment): " + Arrays.toString(values));
            
            Object[] valuesNoAuto = getFieldValues(testUser, false);
            System.out.println("Field values (without auto-increment): " + Arrays.toString(valuesNoAuto));
            
        } catch (Exception e) {
            System.err.println("Error getting field values: " + e.getMessage());
        }
        
        // Демонстрация создания таблицы на основе метаданных
        System.out.println("\\n=== CREATE TABLE SQL ===");
        System.out.println(generateCreateTableSQL(userClass));
    }
    
    // Дополнительный метод для генерации CREATE TABLE
    public static String generateCreateTableSQL(Class<?> entityClass) {
        EntityMetadata metadata = getEntityMetadata(entityClass);
        
        StringBuilder sql = new StringBuilder("CREATE TABLE ");
        sql.append(metadata.tableName).append(" (\\n");
        
        for (int i = 0; i < metadata.fields.size(); i++) {
            FieldMetadata field = metadata.fields.get(i);
            
            if (i > 0) sql.append(",\\n");
            sql.append("  ").append(field.columnName);
            
            // Простое определение типов данных
            Class<?> fieldType = field.field.getType();
            if (fieldType == String.class) {
                sql.append(" VARCHAR(255)");
            } else if (fieldType == Integer.class || fieldType == int.class) {
                sql.append(" INTEGER");
            } else if (fieldType == Long.class || fieldType == long.class) {
                sql.append(" BIGINT");
            } else if (fieldType == Double.class || fieldType == double.class) {
                sql.append(" DOUBLE");
            } else {
                sql.append(" VARCHAR(255)"); // По умолчанию
            }
            
            if (field.isPrimaryKey) {
                sql.append(" PRIMARY KEY");
            }
            
            if (field.isAutoIncrement) {
                sql.append(" AUTO_INCREMENT");
            }
        }
        
        sql.append("\\n)");
        
        return sql.toString();
    }
}
```

---

## 🔗 Связанные темы
- [[Annotations-Code|Аннотации]] - работа с метаданными через рефлексию
- [[Best-Practices-Code|Лучшие практики]] - безопасное использование reflection
- [[Exception-Handling-Code|Обработка исключений]] - исключения в reflection
- [[Multithreading-Code|Многопоточность]] - thread safety при использовании reflection