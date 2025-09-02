

# Обработка исключений в Java: try-catch 
## Что такое try-catch 

- Блок `try` содержит код, который может вызвать исключение (ошибку). 
- Если в блоке `try` возникает исключение, управление передаётся в блок `catch`. 
- В блоке `catch` прописывается логика обработки конкретного типа исключения.
- Блок `finally` (опционально) выполняется всегда после `try/catch` — например, для закрытия ресурсов. 
## Синтаксис

```java
try {  
// код, который может вызвать исключение  
} catch (ExceptionType1 e1) {  
// обработка исключения типа ExceptionType1  
} catch (ExceptionType2 e2) {  
// обработка исключения типа ExceptionType2  
} finally {  
// код, который выполнится в любом случае  
}
```



## Особенности 
- Можно использовать несколько блоков `catch` для разных типов исключений. 
- Исключение ловит **первый подходящий** `catch`, остальные пропускаются. 
- Если исключение не поймали — программа аварийно завершится. 
- Блок `finally` выполняется всегда, даже если в `try` есть `return`. - В Java 7+ можно ловить несколько исключений в одном `catch` через `|`:


```java
catch (IOException | SQLException ex) {  
// обработка  
}
```


## Пример

```java
try {  
	int a = 10 / 0; // ArithmeticException  
	String s = null;  
	System.out.println(s.length()); // NullPointerException  
} catch (ArithmeticException e) {  
	System.out.println("Деление на ноль!");  
} catch (NullPointerException e) {  
	System.out.println("Попытка обращения к null");  
} finally {  
	System.out.println("Блок finally выполнен");  
}

```

## Иерархия исключений

- Checked: IOException, SQLException — требуют обработки или проброса через throws.
- Unchecked: RuntimeException и наследники (NullPointerException, IllegalArgumentException) — можно не объявлять, но обрабатывать разумно.
- Error: OutOfMemoryError, StackOverflowError — системные, обычно не ловят.


## Правила catch

- От частного к общему: сначала специфичные типы, затем более общие (иначе недостижимый код).
- Multi-catch:

```java
try { ... }
catch (IOException | SQLException e) {
  log(e);
}
```

- Переменная в multi-catch эффективно final, переприсваивать нельзя.


## finally: когда и зачем

- Гарантированно выполняется после try/catch, даже при исключении.
- Типичные задачи: закрыть соединение, разблокировать мьютекс, очистить ресурсы.
- Не возвращай из finally и не «глоти» исключения — это скрывает исходную проблему.


## try-with-resources (рекомендуется)

- Авто‑закрытие ресурсов, реализующих AutoCloseable:

```java
try (BufferedReader br = Files.newBufferedReader(path);
     PrintWriter pw = new PrintWriter(Files.newBufferedWriter(out))) {
  pw.println(br.readLine());
} catch (IOException e) {
  e.printStackTrace();
}
```

- Можно объявлять ресурс заранее (Java 9+):

```java
BufferedReader br = Files.newBufferedReader(path);
try (br) {
  System.out.println(br.readLine());
}
```

- Поддерживает «подавленные» исключения: вторичные исключения при закрытии добавляются в primary через e.addSuppressed(...), видны в stacktrace.


## Перебрасывание и оборачивание

- Проброс без изменений:

```java
try { ... }
catch (IOException e) {
  throw e; // сохраняет тип и стек
}
```

- Оборачивание в бизнес-исключение с контекстом:

```java
try { ... }
catch (IOException e) {
  throw new UncheckedIOException("Failed to load config: " + file, e);
}
```

- Сохранение причины исключения через «cause» критично для дебага.


## Лучшие практики

- Лови то, что можешь корректно обработать; не ставь «пустые» catch.
- Логируй с контекстом: параметры, идентификаторы, состояние, но без чувствительных данных.
- Не используй исключения для нормального управления потоком (особенно в циклах).
- Валидацию аргументов делай через IllegalArgumentException/Objects.requireNonNull.
- В публичном API декларируй понятные checked-исключения или оборачивай в доменные runtime.


## Частые шаблоны

- Валидация входа:

```java
public Path load(String name) {
  if (name == null || name.isBlank())
    throw new IllegalArgumentException("name is blank");
  ...
}
```

- Перечитывание с fallback:

```java
String loadConfig() {
  try {
    return Files.readString(Path.of("config.yaml"));
  } catch (IOException e) {
    return "defaults: true";
  }
}
```

- Локальные ретраи:

```java
<T> T withRetry(Callable<T> action, int attempts) {
  IOException last = null;
  for (int i = 0; i < attempts; i++) {
    try { return action.call(); }
    catch (IOException e) { last = e; }
  }
  throw new UncheckedIOException("All attempts failed", last);
}
```


## Специфика stacktrace и логирования

- Никогда не теряй стек: передавай cause в конструктор исключения.
- Для временных ошибок (сеть/IO) логируй warn/info с ретраями, для программных багов — error и метрики.
- В проде избегай e.printStackTrace(); используй логгер (slf4j/log4j) с плейсхолдерами.


## Производительность

- Генерация исключений дороже обычного кода: не применяй их для «if/else».
- Try‑блок сам по себе не медленный; дорого бросание и построение трассы стека.
- Для «горячих» участков отделяй контроль потока от обработки ошибок.


## Сниппеты для Obsidian/IDEA

- Try-with-resources для JDBC:

```java
String sql = "select id, name from users where id = ?";
try (Connection c = dataSource.getConnection();
     PreparedStatement ps = c.prepareStatement(sql)) {
  ps.setLong(1, id);
  try (ResultSet rs = ps.executeQuery()) {
    return rs.next() ? rs.getString("name") : null;
  }
} catch (SQLException e) {
  throw new RuntimeException("DB error for id=" + id, e);
}
```

- Гранулированные catch:

```java
try {
  risky();
} catch (FileNotFoundException e) {
  recoverFromMissingFile();
} catch (UnknownHostException e) {
  scheduleRetry();
} catch (IOException e) {
  log.error("IO failure", e);
  throw new UncheckedIOException(e);
}
```

- Обработка «своих» доменных ошибок:

```java
class DomainException extends RuntimeException {
  DomainException(String msg, Throwable cause) { super(msg, cause); }
}
...
try { domainAction(); }
catch (ExternalServiceException e) {
  throw new DomainException("External service failed: " + e.getCode(), e);
}
```


## Мини‑чеклист

- Нужно закрывать ресурсы? Используй try‑with‑resources.
- Есть несколько вариантов ошибок? Лови конкретные типы, затем общий.
- Нужен контекст? Оборачивай и передавай cause.
- Не глоти исключения и не возвращай из finally.
- Разделяй баги (runtime) и ожидаемые сбои (checked) в API.
<span style="display:none">[^1][^2][^3][^4][^5][^6][^7][^8][^9]</span>

<div style="text-align: center">⁂</div>

[^1]: https://javarush.com/groups/posts/isklyucheniya-java

[^2]: https://skillbox.ru/media/base/isklyucheniya-v-java-catch-pod-lupoy-chast-3/

[^3]: https://learn.javascript.ru/try-catch

[^4]: https://ru.hexlet.io/qna/java/questions/kak-rabotaet-try-catch-java

[^5]: https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Statements/try...catch

[^6]: https://habr.com/ru/articles/212759/

[^7]: https://java-blog.ru/collections/obrabotka-isklyucheniy-try-catch-v-java

[^8]: https://timeweb.cloud/tutorials/java/isklyucheniya-v-java-perekhvat-i-obrabotka

[^9]: https://edu.mmcs.sfedu.ru/mod/resource/view.php?id=19882

## Когда использовать 
- Если код может выбросить исключение и нужно корректно на него отреагировать. 
- Для логирования ошибок, освобождения ресурсов, информирования пользователя и предотвращения краха приложения. 
- --- 
- Обработка исключений — мощный инструмент, обеспечивающий устойчивость и безопасность Java-программ. Используй try-catch, чтобы ловить и корректно обрабатывать ошибки

