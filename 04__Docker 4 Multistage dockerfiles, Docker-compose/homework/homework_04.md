# Docker & Cloud Tech: Домашнее задание 4

Источник: <https://lms.itcareerhub.de/mod/assign/view.php?id=15597>

Создайте многоконтейнерное приложение с помощью Docker Compose: Nginx, Redis и
MySQL с volume для хранения файлов базы данных.

- `web`: `nginx:latest`, порт хоста `8080` на порт контейнера `80`;
- `redis`: `redis:latest`;
- `mysql`: `mysql:8.0`, переменные окружения и volume `mysql_data`;
- общая сеть `mynetwork` для всех контейнеров.

## Решение

### 1. Файл `compose.yaml`

```yaml
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    networks:
      - mynetwork

  redis:
    image: redis:latest
    networks:
      - mynetwork

  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: app_database
      MYSQL_USER: app_user
      MYSQL_PASSWORD: app_password
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - mynetwork

volumes:
  mysql_data:

networks:
  mynetwork:
    driver: bridge
```

В Compose-файле описаны три сервиса:

- `web` запускает Nginx и публикует контейнерный порт `80` на порт `8080` хост-машины;
- `redis` запускает Redis и доступен другим сервисам по имени `redis` внутри общей сети;
- `mysql` запускает MySQL 8.0, получает параметры инициализации через переменные окружения, а данные сохраняет в именованном томе `mysql_data`.

Сервисы подключены к общей bridge-сети `mynetwork`. Именованный том не удаляется обычной командой `docker compose down`, поэтому данные MySQL сохраняются между перезапусками стека.

### 2. Проверка конфигурации

Команды выполняются из каталога, в котором находится `compose.yaml`:

```bash
docker compose config
```

Команда проверяет и разворачивает Compose-конфигурацию без запуска контейнеров.

### 3. Запуск приложения

```bash
docker compose up -d
docker compose ps
```

После запуска веб-сервер доступен по адресу:

```text
http://localhost:8080
```

Проверка ответа Nginx из терминала:

```bash
curl http://localhost:8080
```

Проверка логов сервисов:

```bash
docker compose logs --tail=50 web redis mysql
```

### 4. Остановка приложения

```bash
docker compose down
```

Команда останавливает и удаляет контейнеры и сеть Compose-проекта, но сохраняет именованный том `mysql_data`.
