# Домашнее задание к занятию «Работа с Playbook» - Некрасов Александр

## Что делает playbook

Playbook `site.yml` устанавливает и настраивает два сервиса:

1. **ClickHouse** — колоночная СУБД для аналитики:
   - Скачивает RPM-пакеты с официального репозитория
   - Устанавливает `clickhouse-server`, `clickhouse-client`, `clickhouse-common-static`
   - Настраивает runtime-директории с правильным владельцем
   - Запускает сервер под пользователем `clickhouse`
   - Создаёт базу данных `logs`

2. **Vector** — сборщик и трансформер логов:
   - Скачивает дистрибутив
   - Распаковывает в `/opt/vector`
   - Деплоит конфиг `/etc/vector/vector.toml` через Jinja2-шаблон
   - Запускает Vector
   - Перезапускает при изменении конфига (handler)

## Параметры (переменные)

### group_vars/clickhouse/vars.yml

| Переменная | Описание | Значение по умолчанию |
|------------|----------|----------------------|
| `clickhouse_version` | Версия ClickHouse | `22.3.3.44` |
| `clickhouse_packages` | Список пакетов для скачивания | `[clickhouse-client, clickhouse-server, clickhouse-common-static]` |

### group_vars/vector/vars.yml

| Переменная | Описание | Значение по умолчанию |
|------------|----------|----------------------|
| `vector_version` | Версия Vector | `0.21.1` |
| `vector_install_dir` | Директория установки | `/opt/vector` |

## Теги

Playbook использует **два play** (без явных тегов):
- `Install Clickhouse` — для группы `clickhouse`
- `Install and configure Vector` — для группы `vector`

## Inventory

### inventory/prod.yml

```yaml
---
clickhouse:
  hosts:
    clickhouse-01:
      ansible_connection: docker
      ansible_host: centos7
vector:
  hosts:
    vector-01:
      ansible_connection: docker
      ansible_host: ubuntu

```
Обычный запуск
ansible-playbook -i inventory/prod.yml site.yml

Проверка синтаксиса

```ansible-playbook -i inventory/prod.yml site.yml --syntax-check```

Проверка (dry-run)

```ansible-playbook -i inventory/prod.yml site.yml --check```

С показом diff

```ansible-playbook -i inventory/prod.yml site.yml --diff```

Линтинг

```ansible-lint site.yml```