Role Name
=========

Ansible роль для установки и настройки [LightHouse](https://github.com/VKCOM/lighthouse) — легковесного веб-интерфейса для СУБД ClickHouse. Роль устанавливает веб-сервер Nginx, скачивает статические файлы LightHouse из GitHub-репозитория, разворачивает конфигурацию сайта Nginx через Jinja2-шаблоны и запускает веб-сервер.

Requirements
------------

- Ansible 2.10 или выше
- Целевой хост: Ubuntu 20.04 (focal), 22.04 (jammy) или 24.04 (noble)
- Права `sudo` на целевом хосте
- Доступ в интернет для скачивания архива LightHouse с GitHub
- Для полноценной работы LightHouse требуется запущенный экземпляр ClickHouse (сама роль ClickHouse не устанавливает)

Role Variables
--------------

Все переменные определены в `defaults/main.yml` и могут быть переопределены на уровне плейбука или инвентаря.

| Переменная | Описание | Значение по умолчанию |
|------------|----------|-----------------------|
| `lighthouse_repo_url` | Ссылка на архив с исходным кодом LightHouse | `"https://github.com/VKCOM/lighthouse/archive/refs/heads/master.tar.gz"` |
| `lighthouse_install_dir` | Директория, куда будут распакованы файлы сайта | `"/var/www/lighthouse"` |
| `nginx_config_path` | Путь к конфигурации сайта Nginx (sites-available) | `"/etc/nginx/sites-available/lighthouse"` |
| `nginx_enabled_path` | Путь к симлинку для включения сайта в Nginx (sites-enabled) | `"/etc/nginx/sites-enabled/lighthouse"` |

Конфигурация Nginx включает проксирование запросов к ClickHouse (по умолчанию на `http://localhost:8123`). Если ClickHouse находится на другом хосте, необходимо изменить шаблон `lighthouse_nginx.conf.j2`.

Внутренние переменные хранятся в `vars/main.yml` и в данный момент пусты.

Dependencies
------------

Роль зависит от роли `clickhouse`. LightHouse требует работающий сервер ClickHouse для своей функциональности. Зависимость объявлена в `meta/main.yml` и будет автоматически выполнена перед этой ролью, если ClickHouse еще не установлен.

Если ClickHouse и LightHouse находятся на одном хосте, зависимость выполнится один раз. Если на разных — убедитесь, что хост с ClickHouse доступен с хоста LightHouse и обновите настройки проксирования в шаблоне Nginx.

Для установки ClickHouse отдельно можно использовать роль `clickhouse`:

```yaml
- hosts: clickhouse
  roles:
    - role: clickhouse
```

Example Playbook
----------------

```yaml
- hosts: lighthouse
  become: true
  roles:
    - role: lighthouse-role
      lighthouse_repo_url: "https://github.com/VKCOM/lighthouse/archive/refs/heads/master.tar.gz"
      lighthouse_install_dir: "/var/www/lighthouse"
      nginx_config_path: "/etc/nginx/sites-available/lighthouse"
      nginx_enabled_path: "/etc/nginx/sites-enabled/lighthouse"
```

Minimal Example 
----------------

```yaml
- hosts: lighthouse
  become: true
  roles:
    - lighthouse-role
```

Этот пример установит LightHouse с настройками по умолчанию (Nginx на 80 порту, статика в /var/www/lighthouse, прокси на localhost:8123).

License
-------

MIT

Author Information
------------------

Kristina Filimonova - Netology student.
