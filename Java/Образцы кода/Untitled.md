# Класс City - Решение задачи с общим ArrayList

## Основная идея

Для создания общего ArrayList, который будет доступен всем объектам класса City, тебе нужно использовать **статическую переменную** с ключевым словом `static`.

## Полный код класса City

```java
import java.util.ArrayList;
import java.util.List;

public class City {
    private String name;
    private List<Path> connections;
    
    // Статический ArrayList - общий для всех объектов City
    private static ArrayList<String> allCityNames = new ArrayList<>();
    
    // Конструктор с только названием
    public City(String name) {
        this.name = name;
        this.connections = new ArrayList<>();
        
        // Добавляем имя города в общий список при создании объекта
        allCityNames.add(name);
    }
    
    // Конструктор с названием и путями
    public City(String name, List<Path> connections) {
        this.name = name;
        this.connections = connections != null ? connections : new ArrayList<>();
        allCityNames.add(name);
    }
    
    // Метод для получения всех названий городов (статический)
    public static ArrayList<String> getAllCityNames() {
        return allCityNames;
    }
    
    // Добавление пути к другому городу
    public void addPath(City destination, int cost) {
        connections.add(new Path(destination, cost));
    }
    
    public void addPath(Path path) {
        connections.add(path);
    }
    
    // Метод путешествия
    public City travelBy(int n) {
        City current = this;
        for (int i = 0; i < n; i++) {
            if (current.connections.isEmpty()) {
                return null; // Тупик - нет путей
            }
            // Берем первый город из списка путей
            current = current.connections.get(0).getDestination();
        }
        return current;
    }
    
    // Текстовое представление города
    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append(name).append(": ");
        for (int i = 0; i < connections.size(); i++) {
            Path path = connections.get(i);
            sb.append(path.getDestination().getName())
              .append(":")
              .append(path.getCost());
            if (i < connections.size() - 1) {
                sb.append(", ");
            }
        }
        return sb.toString();
    }
    
    // Геттеры
    public String getName() {
        return name;
    }
    
    public List<Path> getConnections() {
        return connections;
    }
    
    // Внутренний класс для представления пути
    public static class Path {
        private City destination;
        private int cost;
        
        public Path(City destination, int cost) {
            this.destination = destination;
            this.cost = cost;
        }
        
        public City getDestination() {
            return destination;
        }
        
        public int getCost() {
            return cost;
        }
    }
}
```

## Пример использования

```java
public class Main {
    public static void main(String[] args) {
        // Создаем города
        City cityA = new City("A");
        City cityB = new City("B");
        City cityC = new City("C");
        City cityD = new City("D");
        City cityF = new City("F");
        
        // Настраиваем соединения согласно заданию
        cityB.addPath(cityA, 10);  // B -> A
        cityB.addPath(cityC, 15);  // B -> C
        
        cityA.addPath(cityB, 10);  // A -> B
        cityC.addPath(cityD, 20);  // C -> D
        cityD.addPath(cityF, 25);  // D -> F
        cityF.addPath(cityA, 30);  // F -> A (для цикла)
        
        // Проверяем общий список городов
        System.out.println("Все города: " + City.getAllCityNames());
        
        // Тестируем метод путешествия
        System.out.println("Путешествие из B на 1 шаг: " + cityB.travelBy(1).getName()); // A
        System.out.println("Путешествие из B на 2 шага: " + cityB.travelBy(2).getName()); // B
        System.out.println("Путешествие из B на 3 шага: " + cityB.travelBy(3).getName()); // A
        
        // Выводим информацию о городах
        System.out.println("\nИнформация о городах:");
        System.out.println(cityA);
        System.out.println(cityB);
        System.out.println(cityC);
    }
}
```

## Ключевые моменты

### 1. Статическая переменная
```java
private static ArrayList<String> allCityNames = new ArrayList<>();
```
- `static` означает, что эта переменная принадлежит классу, а не отдельному объекту
- Она создается один раз и разделяется между всеми экземплярами класса
- Память выделяется в момент первого обращения к классу

### 2. Статический метод для доступа
```java
public static ArrayList<String> getAllCityNames() {
    return allCityNames;
}
```
- Можешь вызывать без создания объекта: `City.getAllCityNames()`

### 3. Добавление в общий список
```java
public City(String name) {
    this.name = name;
    this.connections = new ArrayList<>();
    allCityNames.add(name); // Добавляем в общий список
}
```

## Важные особенности static переменных

1. **Одна копия для всех объектов** - изменения видны всем
2. **Инициализируется при загрузке класса** - не при создании объекта
3. **Доступ через имя класса** - `City.getAllCityNames()`
4. **Память эффективность** - не дублируется для каждого объекта

Теперь у тебя есть общий ArrayList, который автоматически заполняется именами всех созданных городов!