# prepend-task-number-from-branch-to-commit-message-hook
For English description, please create an issue.

This script is not POSIX-compliant, so make sure that your `/bin/sh` is not overriden by an another shell. 

# Что делает?
1. Ищет в ветке номер тикета и дописывает его в начало коммит месседжа
2. Если коммит черрипикается - заменяет номер тикета (если он был) черри-пикаемого коммита на номер тикета текущей ветки
3. Капитализирует коммит месседж с исключением на квадратные скобки (оставил эту логику, т.к. она кажется harmless)
4. Не делает вышеперечисленного в ветках `smartgit-temp`, `bitbucket-temp`, `master`, `release`, `release-candidate`, `develop` , в любой другой - валит попытку коммита, если не может дописать (обычно - если в ветке нет номера тикета в нужном формате)

# Как пользоваться?
1. Скачать файл
2. Закинуть в `[repository root]/.git/hooks`
3. Называть рабочие ветки с суффиксом `__CSMS-123456789` - два `_` в начале, затем номер задачи как в урле ютрэка

# Примеры?
Допустим, ветка называется `do-whatever__CSMS-123456`
Тогда
1. `done whatever` -> `CSMS-123456: Done whatever`
2. `CSMS-161234 done whatever` -> `CSMS-123456: Done whatever`
3. `Done whatever CSMS-161234` -> `CSMS-123456: Done whatever CSMS-161234` (замена происходит только в начале сообщения)
4. `[migration] migrate something` -> `CSMS-123456: [migration] Migrate something`

# FAQ
1. У меня другой проект в YouTrack, как научить хук с ним работать?
  - изменить значение переменной `youtrack_project_slug` на слаг нужного проекта
2. Как добавить префикс к номеру задачи, например, писать `ticket #CSMS-123456`?
  - Задать `ticket #` значением переменной `to_insert_prefix`
3. Как отключить капитализацию?
  - задать переменной `capitalization_behaviour` значение `none`. Чтобы вернуть - выставить в `capitalize`
4. Как убрать исключение на капитализацию выражений в квадратных скобках?
  - изменить регулярку в переменной `capitalization_regex`, у меня готовой нет, нужно будет - пишите
5. Как добавить ветку-исключение, например, `gitkraken-temp`?
  - добавить в конец значения переменной `pipe_separated_system_branches` название этой ветки через пайп. Для примера - `|gitkraken-temp`
6. Как сделать, чтобы коммит не падал, если не удаётся дописать номер тикета?
  - задать переменной `forbid_committing_without_ticket_number` значение `0`. Чтобы запретить обратно - значение `1`.
7. Как запретить замену номера тикета при черри-пике?
  - Изменить значение переменной `another_ticket_found_behaviour`. Доступные значения:
    1. `replace` - если находим - заменяем: `CSMS-161234 done whatever` -> `CSMS-123456: Done whatever`
    2. `merge` - если находим - пишем оба тикета через запятую: `CSMS-161234 done whatever` -> `CSMS-161234, CSMS-123456: Done whatever`
    3. `keep` - оставляем старый тикет, форматируем по необходимости: `CSMS-161234 done whatever` -> `CSMS-161234: Done whatever`
