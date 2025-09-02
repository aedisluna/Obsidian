---
tags:
  - QA
  - "#Tools"
---
[[Бэкэнд]]

Штука для нагрузочного тестирования апишек

Jmeter:
Для нагрузочного теста нам понадобятся: Thread Group, Sampler, Listener
Добавить Thread Group: Test Plan > Add > Threads (Users) > Thread Group
Добавить Sampler: Thread Group > Add > Sampler > HTTP Request
Добавить Listener: HTTP Request > Add > Listener > View Results Tree