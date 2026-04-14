## 🔹 equals() и hashCode()

### Контракт equals

Объект должен:

- быть равен самому себе
- не быть равен null
- не быть равен другому классу
- быть симметричным
- быть транзитивным
---

### Контракт hashCode

Если:

```java
a.equals(b) == true
```

то:

```java
a.hashCode() == b.hashCode()
```

---

## 🔸 Что тестировать

### equals

- same object → true
    
- null → false
    
- другой класс → false
    
- одинаковые поля → true
    
- разные поля → false
    

---

### hashCode

- одинаковые объекты → одинаковый hashCode
    

---

## 🔥 Частая ошибка

Тестируют только equals  
❌ забывают про hashCode

---

## 💡 Для Student

Проверить:

- одинаковое имя + оценки → equals true
    
- разные оценки → false
    

---
