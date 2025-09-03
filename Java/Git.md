### 5. **Git-Commands.md** - Новая заметка
```


# Git - Основные команды

*Система контроля версий для разработчика*

\#git \#version-control \#development \#collaboration \#commands

***

## 📋 Содержание

- [[\#Основы Git]]
- [[\#Настройка и инициализация]]
- [[\#Базовые операции]]
- [[\#Ветки (Branches)]]
- [[\#Удаленные репозитории]]
- [[\#История и логи]]
- [[\#Решение конфликтов]]

***

## 🎯 Основы Git

> [!info] Git - распределенная система контроля версий, позволяющая отслеживать изменения в коде, работать в команде и управлять версиями проекта

### Основные концепции

- **Repository (репозиторий)** - папка с проектом под контролем Git
- **Commit (коммит)** - снимок состояния проекта
- **Branch (ветка)** - независимая линия разработки
- **Merge (слияние)** - объединение изменений из разных веток
- **Remote (удаленный)** - репозиторий на сервере (GitHub, GitLab)


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


### Создание репозитория

```bash
# Создать новый репозиторий в текущей папке
git init

# Клонировать существующий репозиторий
git clone https://github.com/username/repository.git
git clone https://github.com/username/repository.git новое-имя-папки

# Проверить статус репозитория
git status
```


## 📁 Базовые операции

### Отслеживание файлов

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


### Создание коммитов

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


### Отмена изменений

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

### Работа с ветками

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


### Слияние веток

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

### Работа с remote

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


### Push и Pull

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


## 📜 История и логи

### Просмотр истории

```bash
# Посмотреть историю коммитов
git log
git log --oneline            # Краткий формат
git log --graph             # С визуализацией веток
git log --graph --oneline --all  # Полная картина

# История конкретного файла
git log filename.txt
git log -p filename.txt      # С показом изменений

# Поиск в истории
git log --grep="исправление"  # Поиск по сообщениям коммитов
git log --author="Имя"       # Коммиты определенного автора
git log --since="2 weeks ago"  # За последние 2 недели
git log --until="2023-12-01"   # До определенной даты

# Показать конкретный коммит
git show abc1234
git show HEAD               # Последний коммит
git show HEAD~2             # Коммит 2 шага назад
```


## ⚔️ Решение конфликтов

### Обработка конфликтов слияния

```bash
# Когда возник конфликт при merge
git status                  # Посмотреть файлы с конфликтами

# После разрешения конфликтов в файлах
git add файл-с-конфликтом.txt
git commit                  # Завершить merge

# Отменить процесс слияния
git merge --abort

# Использовать стратегию слияния
git merge -X theirs feature-ветка    # Предпочесть их изменения
git merge -X ours feature-ветка      # Предпочесть наши изменения
```


## 💡 Полезные команды

### Временное сохранение изменений

```bash
# Временно сохранить изменения
git stash
git stash push -m "Описание изменений"

# Посмотреть список stash
git stash list

# Применить последний stash
git stash pop               # Применить и удалить
git stash apply             # Применить, но оставить в списке

# Применить конкретный stash
git stash apply stash@{1}

# Удалить stash
git stash drop stash@{0}
git stash clear             # Очистить все stash
```


### Полезные алиасы

```bash
# Настроить алиасы для часто используемых команд
git config --global alias.st status
git config --global alias.co checkout  
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'

# Теперь можно использовать короткие команды
git st                      # вместо git status
git co main                 # вместо git checkout main
git br -a                   # вместо git branch -a
```

> [!warning] **Важные правила**
> - Никогда не изменяй историю в общих ветках (main, develop)
> - Всегда делай `git pull` перед `git push`
> - Пиши понятные сообщения коммитов
> - Не коммить большие бинарные файлы
> - Используй `.gitignore` для исключения ненужных файлов

## 🔗 Связанные темы

- [[IntelliJ-IDEA-Shortcuts|Горячие клавиши IDEA]] - для работы с Git в IDE
- [[Best-Practices|Лучшие практики]] - включая работу с версиями

```