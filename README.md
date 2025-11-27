# Домашнее задание к занятию 4 «Работа с roles»

## ` Дмитрий Климов `

## Основная часть

### Ваша цель — разбить ваш playbook на отдельные roles.

### Задача — сделать roles для ClickHouse, Vector и LightHouse и написать playbook для использования этих ролей.

### Ожидаемый результат — существуют три ваших репозитория: два с roles и один с playbook.

### Что нужно сделать

1. Создайте в старой версии playbook файл `requirements.yml` и заполните его содержимым:
```yml
---
  - src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
    scm: git
    version: "1.13"
    name: clickhouse
```
2. При помощи ansible-galaxy скачайте себе эту роль.

3. Создайте новый каталог с ролью при помощи ansible-galaxy role init vector-role.

4. На основе tasks из старого playbook заполните новую role. Разнесите переменные между vars и default.

5. Перенести нужные шаблоны конфигов в templates.

6. Опишите в README.md обе роли и их параметры. Пример качественной документации ansible role по ссылке.

7. Повторите шаги 3–6 для LightHouse. Помните, что одна роль должна настраивать один продукт.

8. Выложите все roles в репозитории. Проставьте теги, используя семантическую нумерацию. Добавьте roles в requirements.yml в    playbook.

9. Переработайте playbook на использование roles. Не забудьте про зависимости LightHouse и возможности совмещения roles с       tasks.

10. Выложите playbook в репозиторий.

11. В ответе дайте ссылки на оба репозитория с roles и одну ссылку на репозиторий с playbook.

Как оформить решение задания
Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.

## Ответ:

# Домашнее задание к занятию 4: Работа с roles

Цель: Разбить монолитный playbook на три отдельные, версионированные roles (ClickHouse, Vector, LightHouse) и создать главный playbook для их оркестрации.

---

## 1. Структура Решения

Проект был разбит на три публичных репозитория, согласно требованиям задания:
1.  Две кастомные роли (`vector-role`, `lighthouse-role`).
2.  Главный плейбук, использующий эти роли, а также внешнюю роль ClickHouse, через `requirements.yml`.

### Ссылки на Репозитории

| Назначение | Репозиторий | Версионирование (Теги) |
| :--- | :--- | :--- |
| **Главный Playbook** | [Dmitriy-py/ansible-data-stack-playbook](https://github.com/Dmitriy-py/ansible-data-stack-playbook) | N/A |
| **Vector Role** | [Dmitriy-py/vector-role](https://github.com/Dmitriy-py/vector-role) | `v1.0.0` |
| **LightHouse Role** | [Dmitriy-py/lighthouse-role](https://github.com/Dmitriy-py/lighthouse-role) | `v1.0.0` |

---

## 2. Реализация Roles

Обе кастомные роли (`vector` и `lighthouse`) были созданы с использованием `ansible-galaxy role init` и включают следующие компоненты:

### 2.1. Vector Role

*   **Назначение:** Установка Vector и настройка его на сбор хостовых метрик и отправку их в ClickHouse.
*   **Tasks:** Установка зависимостей, добавление APT репозитория, установка пакета.
*   **Templates:** `vector.toml.j2` (использует переменные `vector_sink_host` и `vector_sink_port`).
*   **Defaults:** Определены `vector_sink_host` (`127.0.0.1`) и `vector_sink_port` (`8123`).
*   **Документация:** README.md содержит полное описание параметров.

### 2.2. LightHouse Role

*   **Назначение:** Установка Nginx, PHP-FPM, клонирование LightHouse и настройка веб-сервера.
*   **Tasks:** Установка веб-сервера и PHP, клонирование Git-репозитория LightHouse.
*   **Templates:** `config.php.j2` (для подключения к ClickHouse) и `nginx_lighthouse.conf.j2` (для настройки Nginx VHost).
*   **Defaults:** Определены параметры подключения к ClickHouse (`lighthouse_ch_host`, `lighthouse_ch_port`).
*   **Документация:** README.md содержит полное описание параметров.

---

## 3. Оркестрация Playbook

Файл `ansible-data-stack-playbook/requirements.yml` был переработан для использования HTTPS-ссылок (из-за проблем с SSH-аутентификацией), чтобы обеспечить скачивание всех ролей, включая ClickHouse v1.13.

### `requirements.yml`

Роли скачиваются по тегам, используя семантическую нумерацию:
```yaml
---
- src: https://github.com/AlexeySetevoi/ansible-clickhouse.git
  version: "1.13"
  name: clickhouse 

- src: https://github.com/Dmitriy-py/vector-role.git
  version: v1.0.0
  name: vector
  
- src: https://github.com/Dmitriy-py/lighthouse-role.git
  version: v1.0.0
  name: lighthouse
```
