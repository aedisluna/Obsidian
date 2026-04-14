## 🔹 JUnit 5

### Базовые аннотации

```java
@Test
@BeforeEach
@AfterEach
```

---

## 🔸 Assertions

### Проверки

```java
assertEquals(expected, actual);
assertTrue(condition);
assertFalse(condition);
```

---

### Исключения

```java
assertThrows(IllegalArgumentException.class, () -> {
    method();
});
```

---

### Ссылки

```java
assertSame(a, b);
assertNotSame(a, b);
```

---

## 🔸 Структура теста

```java
// arrange
// act
// assert
```

---

## 💡 Хорошая практика

- 1 тест = 1 поведение
    
- понятные имена тестов
    
- не тестировать реализацию, тестировать поведение
    
