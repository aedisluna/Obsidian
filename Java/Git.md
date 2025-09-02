# Основные команды Git

## Инициализация и копирование

- git init — создать новый репозиторий в текущей папке
- git clone <URL> — клонировать удалённый репозиторий. Примеры: git clone https://... или git clone git@...


## Базовый цикл: статус, индексация, коммиты

- git status — текущее состояние (изменённые/новые/удалённые файлы, ветка).
- git add <файл> — добавить файл в индекс; git add . — добавить все изменения.
- git commit -m "сообщение" — создать коммит; git commit -am "msg" — добавить отслеживаемые + коммит.
- git log — история; варианты: git log --oneline --graph --decorate --all.
- git diff — показать различия; git diff --staged — разница, подготовленная к коммиту.


## Ветки и переключение

- git branch — список веток; git branch <name> — создать ветку; git branch -d <name> — удалить локальную.
- git checkout <branch> — переключиться; git checkout -b <name> — создать и переключиться.
- git switch <branch> — новый синтаксис переключения; git switch -c <name> — создать и переключиться.
- git merge <branch> — слить указанную ветку в текущую.
- git rebase <base> — перебазировать текущую ветку поверх <base>; интерактивно: git rebase -i HEAD~N.


## Удалённые репозитории

- git remote -v — список remotes; git remote add origin <URL> — добавить удалённый.
- git fetch — забрать ссылки и объекты без слияния.
- git pull — fetch + merge; git pull --rebase — fetch + rebase для линейной истории.
- git push — отправить коммиты; git push -u origin <branch> — установить апстрим.


## Отмена и исправления

- git restore <file> — отменить изменения в рабочем каталоге; git restore --staged <file> — убрать из индекса.
- git reset --soft HEAD~1 — откатить коммит, оставить изменения в индексе; --mixed (по умолчанию) — в рабочую копию; --hard — стереть изменения.
- git revert <commit> — создать «обратный» коммит, отменяющий указанный; безопасно для опубликованных веток.
- git commit --amend — поправить последний коммит (сообщение/содержимое).
- git cherry-pick <commit> — применить конкретный коммит в текущей ветке.


## Stash: временно спрятать изменения

- git stash push -m "msg" — сохранить незакоммиченные изменения; по умолчанию только отслеживаемые.
- git stash list — список; git stash show -p — дифф в stash.
- git stash apply — применить, сохранив в списке; git stash pop — применить и удалить.
- Полезно: git stash -u — захватить и неотслеживаемые файлы.


## Теги релизов

- git tag — список; git tag -a v1.0 -m "rel" — аннотированный тег.
- git push --tags — отправить теги; удалить на сервере: git push origin :v1.0.


## Просмотр и навигация

- git show <obj> — показать коммит/тег/файл в коммите.
- git blame <file> — кто и когда менял строки.
- git shortlog -sn — сводка по авторам.


## Частые сценарии с примерами

- Создать ветку от main и запушить:
    - git checkout -b feature/x; git push -u origin feature/x.
- Обновить текущую ветку поверх origin/main:
    - git fetch origin; git rebase origin/main.
- Аккуратно подтянуть изменения без merge-коммита:
    - git pull --rebase --autostash.
- Откатить последний коммит, но оставить правки:
    - git reset --soft HEAD~1.
- Отменить опубликованный коммит:
    - git revert <hash>; git push.


## Безопасный откат: что выбрать

- Локально и «для себя»: git reset (soft/mixed/hard). Не пушить в общий репозиторий.
- В общей ветке: git revert, чтобы не переписывать историю.
- Поправить последний коммит: git commit --amend, если ещё не пушил.


## Полезные настройки и алиасы

- Конфигурация пользователя:
    - git config --global user.name "Имя"; git config --global user.email "mail@example.com".
- Цветной вывод и редактор:
    - git config --global color.ui auto; git config --global core.editor "code --wait".
- Алиасы:
    - git config --global alias.lg "log --oneline --graph --decorate --all".


## Подводные камни и советы

- Не делай git push -f в общие ветки; если нужен force, используй --force-with-lease.
- Разрешай конфликты осознанно; после merge/rebase используй git status и git add для пометки решённых файлов.
- Перед rebase опубликовнной ветки согласуй с командой; rebase переписывает историю.


<span style="display:none"></span>

<div style="text-align: center">⁂</div>

: https://git-scm.com/book/ru/v2/Приложение-C:-Команды-Git-Внесение-исправлений

: https://hmarketing.ru/blog/git/shpargalka/

: https://www.atlassian.com/ru/git/tutorials/resetting-checking-out-and-reverting

: https://www.atlassian.com/ru/git/glossary

: https://habr.com/ru/articles/918386/

: https://zobzn.com/git

: https://htmlacademy.ru/blog/git/first-aid-git

: https://github.com/cyberspacedk/Git-commands

: https://kolesnikof.gitbooks.io/git-basics/base.html

: https://habr.com/ru/articles/522078/

