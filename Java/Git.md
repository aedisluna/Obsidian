# Основные команды Git

*Система контроля версий для разработчика*

\#git \#version-control \#development \#collaboration \#commands

---

## 📋 Содержание

- [[#🎯 Основы Git]]
- [[#⚙️ Настройка и инициализация]]
- [[#📁 Базовые операции]]
- [[#🌿 Ветки (Branches)]]
- [[#🌐 Удаленные репозитории]]
- [[#📜 История и логи]]
- [[#⚔️ Решение конфликтов]]
- [[#💡 Полезные команды]]

---
## 🎯 Основы Git

> [!info] Git - распределенная система контроля версий, позволяющая отслеживать изменения в коде, работать в команде и управлять версиями проекта

### Основные концепции

- **Repository (репозиторий)** - папка с проектом под контролем Git
- **Commit (коммит)** - снимок состояния проекта
- **Branch (ветка)** - независимая линия разработки
- **Merge (слияние)** - объединение изменений из разных веток
- **Remote (удаленный)** - репозиторий на сервере (GitHub, GitLab)

> 
## ⚙️ Настройка и инициализация

### Первоначальная настройка

```bash
# Настройка имени и email (обязательно!)
git config --global user.name "Твое Имя"
git config --global user.email "твой@email.com"

# Настройка редактора по умолчанию
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "idea --wait"  # IntelliJ IDEA

# Просмотр всех настроек
git config --list

# Настройка автопроталкивания
git config --global push.default simple
```

### Инициализация и копирование

- `git init` — создать новый репозиторий
- `git clone` <URL\> — скопировать (клонировать) репозиторий по адресу

## 📁 Базовые операции

### Индексация и коммиты

- `git add <файл>` — добавить файл в индекс для коммита
- `git add .` — добавить все файлы
- `git commit -m "сообщение"` — создать коммит с комментарием
- `git status` — посмотреть текущее состояние репозитория
- `git log` — показать историю коммитов
- `git diff` — посмотреть различия между файлами/коммитами
- `git reset` — отменить изменения или откатить коммит

### Примеры

```bash
# Добавить файл в индекс (staging area)
git add filename.txt
git add *.java                # Все файлы .java
git add .                     # Все файлы в текущей папке
git add -A                    # Все файлы включая удаленные

# Убрать файл из индекса
git reset filename.txt
git reset                     # Убрать все файлы из индекса

# Посмотреть изменения
git diff                      # Изменения в рабочей директории
git diff --staged            # Изменения в индексе
git diff HEAD                # Все изменения относительно последнего коммита
```

#### Создание коммитов

```bash
# Создать коммит с сообщением
git commit -m "Добавлен метод для расчета суммы"

# Создать коммит и добавить все измененные файлы
git commit -am "Исправлена ошибка в алгоритме сортировки"

# Изменить последний коммит
git commit --amend -m "Новое сообщение коммита"

# Создать пустой коммит
git commit --allow-empty -m "Пустой коммит для триггера CI"
```

#### Отмена изменений

```bash
# Отменить изменения в файле (до добавления в индекс)
git checkout -- filename.txt
git restore filename.txt      # Новая команда в Git 2.23+

# Отменить изменения во всех файлах
git checkout -- .
git restore .

# Отменить последний коммит (но сохранить изменения)
git reset HEAD~1

# Отменить последний коммит полностью
git reset --hard HEAD~1

# Создать коммит, отменяющий указанный коммит
git revert abc1234
```


## 🌿 Ветки (Branches)

- `git branch` — список всех веток
- `git branch <имя_ветки>` — создать ветку
- `git checkout <ветка>` — перейти на ветку
- `git merge <ветка>` — слить ветку с текущей
- `git branch -d <ветка>` — удалить ветку

### Примеры

#### Работа с ветками

```bash
# Посмотреть все ветки
git branch                    # Локальные ветки
git branch -a                # Все ветки (включая удаленные)
git branch -r                # Только удаленные ветки

# Создать новую ветку
git branch feature-новая-функция
git checkout -b feature-новая-функция    # Создать и переключиться
git switch -c feature-новая-функция      # Новая команда в Git 2.23+

# Переключиться на ветку
git checkout main
git switch main              # Новая команда

# Переименовать ветку
git branch -m старое-имя новое-имя
git branch -m новое-имя      # Переименовать текущую ветку

# Удалить ветку
git branch -d feature-готовая-функция    # Безопасное удаление
git branch -D feature-сломанная-функция  # Принудительное удаление
```


#### Слияние веток

```bash
# Слить ветку в текущую
git merge feature-ветка

# Слияние без создания merge-коммита (fast-forward)
git merge --ff-only feature-ветка

# Принудительное создание merge-коммита
git merge --no-ff feature-ветка

# Отменить слияние
git merge --abort
```



## 🌐 Удаленные репозитории

- `git remote add <имя> <URL\>` — добавить удалённый репозиторий
- `git remote -v` — список удалённых репозиториев
- `git pull` — забрать изменения с удалённого репозитория
- `git push` — отправить локальные коммиты в удалённый репозиторий
- `git fetch` — забрать ссылки и объекты без слияния.

### Примеры

#### Работа с remote

```bash
# Добавить удаленный репозиторий
git remote add origin https://github.com/username/repo.git

# Посмотреть удаленные репозитории
git remote -v

# Изменить URL удаленного репозитория
git remote set-url origin https://github.com/username/новый-repo.git

# Удалить удаленный репозиторий
git remote remove origin
```


#### Push и Pull

```bash
# Отправить изменения в удаленный репозиторий
git push origin main
git push origin feature-ветка

# Установить upstream для текущей ветки
git push -u origin main
git push --set-upstream origin feature-ветка

# После установки upstream достаточно
git push
git pull

# Получить изменения из удаленного репозитория
git pull origin main         # Fetch + merge
git fetch origin            # Только скачать изменения
git fetch --all             # Скачать из всех удаленных репозиториев

# Принудительная отправка (осторожно!)
git push --force
git push --force-with-lease  # Более безопасный вариант
```


## Отмена и исправления

- `git restore <file>` — отменить изменения в рабочем каталоге; git restore --staged <file\> — убрать из индекса.
- `git reset --soft HEAD~1` — откатить коммит, оставить изменения в индексе; --mixed (по умолчанию) — в рабочую копию; --hard — стереть изменения.
- `git revert <commit>` — создать «обратный» коммит, отменяющий указанный; безопасно для опубликованных веток.
- `git commit --amend` — поправить последний коммит (сообщение/содержимое).
- `git cherry-pick <commit>` — применить конкретный коммит в текущей ветке.

## Stash: временно спрятать изменения

- `git stash push -m "msg"` — сохранить незакоммиченные изменения; по умолчанию только отслеживаемые.
- `git stash list` — список; 
- `git stash show -p` — дифф в stash.
- `git stash apply` — применить, сохранив в списке; 
- `git stash pop` — применить и удалить.
- `git stash -u` — захватить и неотслеживаемые файлы.


## Теги релизов

- `git tag` — список; 
- `git tag -a v1.0 -m "rel"` — аннотированный тег.
- `git push --tags` — отправить теги; удалить на сервере: `git push origin :v1.0.`

## Просмотр и навигация

- `git show <obj>` — показать коммит/тег/файл в коммите.
- `git blame <file>` — кто и когда менял строки.
- `git shortlog -sn` — сводка по авторам.


## Частые сценарии с примерами

```bash
# Создать ветку от main и запушить:
git checkout -b feature/x; git push -u origin feature/x.

#Обновить текущую ветку поверх origin/main:
git fetch origin; git rebase origin/main.

#Аккуратно подтянуть изменения без merge-коммита:
git pull --rebase --autostash.

#Откатить последний коммит, но оставить правки:
git reset --soft HEAD~1.

#Отменить опубликованный коммит:
git revert <hash\>; git push.
```


## Безопасный откат: что выбрать

- Локально и «для себя»: git reset (soft/mixed/hard). Не пушить в общий репозиторий.
- В общей ветке: git revert, чтобы не переписывать историю.
- Поправить последний коммит: git commit --amend, если ещё не пушил.


## Полезные настройки и алиасы

```bash
#Конфигурация пользователя:
git config --global user.name "Имя"; git config --global user.email "mail@example.com".

#Цветной вывод и редактор:
git config --global color.ui auto; git config --global core.editor "code --wait".

#Алиасы:
git config --global alias.lg "log --oneline --graph --decorate --all".
```


## Подводные камни и советы

- Не делай git push -f в общие ветки; если нужен force, используй --force-with-lease.
- Разрешай конфликты осознанно; после merge/rebase используй git status и git add для пометки решённых файлов.
- Перед rebase опубликовнной ветки согласуй с командой; rebase переписывает историю.



---

