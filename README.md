# Домашнее задание к занятию «Репликация и масштабирование. Часть 2» - `Корниенко Кирилл`


### Задание 1. 
На лекции рассматривались режимы репликации master-slave, master-master, опишите их различия.

*Ответить в свободной форме.*

### *Ответ:*

Master-Slave масштабирование: основные плюсы

1. Master + один Slave (Passive)

Отказоустойчивость: Есть "горячая" копия на случай падения основного сервера.

Резервное копирование: Можно делать бэкапы с реплики, не нагружая основной сервер.

Обслуживание: Возможность обновлять или чинить slave без простоя системы.

2. Master + много Slaves

Масштабирование чтения: Основная нагрузка (чтение данных) распределяется между несколькими серверами, что повышает производительность.

География: Можно разнести серверы по разным странам для ускорения доступа локальных пользователей.

Специализация: Разные реплики можно использовать под разные задачи: одну под аналитику,

### Главный минус обеих схем — 
Задержка репликации . Если данные критически важны и должны быть актуальны «прямо сейчас», читать их нужно обязательно с master-сервера. Slave-серверы могут показывать устаревшие данные на доли секунды или даже секунды, если нагрузка велика

### Задание 2. 

Разработайте план для выполнения горизонтального и вертикального шаринга базы данных. База данных состоит из трёх таблиц:

- пользователи,
- книги,
- магазины (столбцы произвольно).
- 
Опишите принципы построения системы и их разграничение или разбивку между базами данных.

*Пришлите блоксхему, где и что будет располагаться. Опишите, в каких режимах будут работать сервера.*

### Ответ

Архитектура решения
Cоздадим:

Вертикальный шардинг: разные таблицы на разных серверах

Горизонтальный шардинг: таблица users разбита по user_id (3 шарда)

Мастер-сервер: единая точка входа с представлением (VIEW) и правилами маршрутизации

1. Создание структуры проекта

```bash
# Создаем директорию проекта
mkdir ~/sharding-project
cd ~/sharding-project

# Создаем структуру папок
mkdir -p conf/{master,shard1,shard2,shard3}
mkdir -p data/{master,shard1,shard2,shard3}
```
2. Создаем Docker Compose файл

```bash:
nano docker-compose.yml:
```
```yaml:
version: '3.8'

networks:
  sharding_network:
    driver: bridge

services:
  # Мастер-сервер (точка входа для приложения)
  master:
    image: postgres:15
    container_name: postgres_master
    restart: unless-stopped
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: postgres
    ports:
      - "5432:5432"
    volumes:
      - ./conf/master/init.sql:/docker-entrypoint-initdb.d/init.sql
      - ./data/master:/var/lib/postgresql/data
    networks:
      - sharding_network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Шард 1 (пользователи с user_id % 3 = 0)
  shard1:
    image: postgres:15
    container_name: postgres_shard1
    restart: unless-stopped
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: books
    ports:
      - "5433:5432"
    volumes:
      - ./conf/shard1/init.sql:/docker-entrypoint-initdb.d/init.sql
      - ./data/shard1:/var/lib/postgresql/data
    networks:
      - sharding_network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Шард 2 (пользователи с user_id % 3 = 1)
  shard2:
    image: postgres:15
    container_name: postgres_shard2
    restart: unless-stopped
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: books
    ports:
      - "5434:5432"
    volumes:
      - ./conf/shard2/init.sql:/docker-entrypoint-initdb.d/init.sql
      - ./data/shard2:/var/lib/postgresql/data
    networks:
      - sharding_network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Шард 3 (пользователи с user_id % 3 = 2)
  shard3:
    image: postgres:15
    container_name: postgres_shard3
    restart: unless-stopped
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: books
    ports:
      - "5435:5432"
    volumes:
      - ./conf/shard3/init.sql:/docker-entrypoint-initdb.d/init.sql
      - ./data/shard3:/var/lib/postgresql/data
    networks:
      - sharding_network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
``` 
