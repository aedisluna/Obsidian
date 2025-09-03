# Comparator в Java

*Интерфейс для сравнения объектов и задания пользовательского порядка сортировки*

#java #Comparator #сортировка #сравнение #коллекции

## 📋 Содержание

- [[#🎯 Что такое Comparator]]
- [[#🛠️ Основные методы интерфейса]]
- [[#📚 Использование Comparator]]
- [[#✨ Примеры кода]]
- [[#🚀 Все методы Comparator с пояснениями]]
- [[#💡 Советы и рекомендации]]


## 🎯 Что такое Comparator

Comparator — функциональный интерфейс, который предоставляет способ сравнения объектов и определения порядка сортировки. Очень удобен, когда нужно определить пользовательский порядок, отличный от естественного порядка или если класс не реализует Comparable.

## 🛠️ Основные методы интерфейса

- `int compare(T o1, T o2)` — сравнивает два объекта, возвращая отрицательное число, если o1 меньше o2, 0 если равны, и положительное — если o1 больше o2.


## 📚 Использование Comparator

Comparator можно использовать с:

- `Collections.sort(List<T> list, Comparator<? super T> c)`
- `List.sort(Comparator<? super T> c)`
- `Stream.sorted(Comparator<? super T> c)`


## ✨ Примеры кода

```java
class Person {
    String name;
    int age;
    // Конструктор, геттеры и сеттеры
}

Comparator<Person> byAge = (p1, p2) -> Integer.compare(p1.age, p2.age);

List<Person> people = Arrays.asList(new Person("Иван", 30), new Person("Анна", 25));
people.sort(byAge);
```


## 🚀 Все методы Comparator с пояснениями

### Основной метод интерфейса

- `int compare(T o1, T o2)`
Сравнивает два объекта и возвращает отрицательное число, ноль или положительное число в зависимости от порядка объектов.


### Статические методы (Java 8+)

- `static <T, U extends Comparable<? super U>> Comparator<T> comparing(Function<? super T, ? extends U> keyExtractor)`
Создаёт компаратор, сравнивающий объекты по значению, возвращённому функцией-ключом (keyExtractor). Удобен для создания компараторов по полям объекта.
- `static <T, U> Comparator<T> comparing(Function<? super T, ? extends U> keyExtractor, Comparator<? super U> keyComparator)`
Создаёт компаратор по ключу с использованием указанного компаратора для значения ключа.
- `static <T> Comparator<T> reverseOrder()`
Возвращает компаратор, который сортирует элементы в обратном естественном порядке.
- `static <T> Comparator<T> naturalOrder()`
Возвращает компаратор, который сортирует элементы в естественном порядке (требуется, чтобы объект реализовывал Comparable).
- `static <T> Comparator<T> nullsFirst(Comparator<? super T> comparator)`
Создаёт компаратор, который ставит null-значения перед непустыми элементами, используя заданный компаратор для непустых.
- `static <T> Comparator<T> nullsLast(Comparator<? super T> comparator)`
Создаёт компаратор, который ставит null-значения после непустых элементов.


### Дефолтные методы (Java 8+)

- `default Comparator<T> reversed()`
Возвращает компаратор с обратным порядком текущего.
- `default Comparator<T> thenComparing(Comparator<? super T> other)`
Возвращает компаратор, который сначала использует текущий, а при равенстве сравнивает вторым компаратором.
- `default <U extends Comparable<? super U>> Comparator<T> thenComparing(Function<? super T, ? extends U> keyExtractor)`
Возвращает компаратор, который сначала сравнивает текущим, а при равенстве сравнивает объекты по ключу, с естественным порядком ключа.
- `default <U> Comparator<T> thenComparing(Function<? super T, ? extends U> keyExtractor, Comparator<? super U> keyComparator)`
Аналогично предыдущему, но с заданным компаратором для ключа.
- `default Comparator<T> thenComparingInt(ToIntFunction<? super T> keyExtractor)`
Цепочка сравнения по int ключу.
- `default Comparator<T> thenComparingLong(ToLongFunction<? super T> keyExtractor)`
Цепочка сравнения по long ключу.
- `default Comparator<T> thenComparingDouble(ToDoubleFunction<? super T> keyExtractor)`
Цепочка сравнения по double ключу.
- `default boolean equals(Object obj)`
Проверка на равенство компараторов.


### Пример использования всех методов в цепочке

```java
Comparator<Person> comp = Comparator
    .comparing(Person::getAge)                      // сравнить по возрасту
    .reversed()                                     // перевернуть порядок
    .thenComparing(Person::getName)                 // потом по имени
    .thenComparingInt(Person::getId);               // потом по id (int)
```


## 💡 Советы и рекомендации

- Используй статические методы `comparing`, `nullsFirst`, `nullsLast`, чтобы быстро создавать компараторы.
- Для компактности и читаемости кода применяй цепочку методов `thenComparing`.
- Всегда учитывай возможные null-значения при сравнении.
- Метод `reversed` удобен для разворота порядка без дублирования логики.
- Компаратор можно создавать и как лямбду, и как класс, реализующий интерфейс.

***