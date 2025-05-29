# CBR Airflow
This project implements an automated ETL pipeline using Apache Airflow to regularly fetch and process financial data from the Central Bank of Russia (CBR). The system extracts currency exchange rates and banking institution data, transforms it, and loads it into a PostgreSQL database.
---
Этот проект реализует автоматизированную загрузку данных о банках и курсах валют с сайта Центрального Банка Российской Федерации в базу данных PostgreSQL с использованием Apache Airflow.

![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)
![Airflow](https://img.shields.io/badge/Airflow-2.5+-green.svg)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-13+-blue.svg)
![Docker](https://img.shields.io/badge/Docker-20.10+-yellowgreen.svg)

## Содержание
- [Функциональность](#функциональность)
- [Технологии](#технологии)
- [Требования](#требования)
- [Установка](#установка)
- [Настройка](#настройка)
- [Запуск](#запуск)

- [Результат](#результат)

## Функциональность

- **Парсинг курсов валют**
  - Ежедневное получение данных с ЦБ РФ
  - Парсинг XML и сохранение данных в таблицу
  - Хранение исторических данных с пометками
  - Поддержка множества валют (USD, EUR, CNY и др.)

- **Импорт данных о банках**
  - Ежедневное скачивание ZIP-архива с сайта ЦБ РФ
  - Извлечение и парсинг XML-файла из архива
  - Сохранение в свою таблицу БД
  - Хранение полной информации о банках (БИК, название, корр. счет и др.)
  - Автоматическое обновление по расписанию

- **Дополнительные возможности**
  - Логирование всех операций
  - Админ-панель Airflow для управления
  - Гибкое расписание с возможностью ручного запуска
 


## Технологии

### Основной стек
- **Apache Airflow 2.5+**: Оркестрация ETL-процессов
- **PostgreSQL 13+**: Основное хранилище данных
- **Redis**: Очередь задач для Airflow
- **Docker**: Контейнеризация сервисов

### Библиотеки Python
- `requests`: HTTP-запросы к API ЦБ РФ
- `xml.etree.ElementTree`: Парсинг XML-ответов


## Требования

### Минимальные системные требования
- **ОС**: Linux/Windows 10+ (64-bit)
- **CPU**: 2+ ядра
- **RAM**: 4+ GB
- **HDD**: 20+ GB свободного места
- **Docker**: 20.10+
- **Docker Compose**: 1.29+

### Рекомендуемые параметры
- **CPU**: 4+ ядра
- **RAM**: 8+ GB
- **SSD**: Для лучшей производительности БД
- **Интернет**: Стабильное соединение для загрузки данных


## Установка
Windows
---
### 1. Установка Docker
Установите [Docker Desktop](https://www.docker.com/products/docker-desktop/)
Возможно придётся установить WSL. В PowerShell:
```powershell
wsl --install
```
После установки может потребоваться перезагрузка устройства

### 2.  Клонирование репозитория
Вызов CMD в Windows: Win + R --> cmd --> Enter

Нужен установленный Git. Проверка, есть ли он в системе:
```bash
git --version
```
При отсутствии Git скачайте с [официального сайта](https://git-scm.com/downloads/win)

Следуйте инструкциям установщика (При предложенных настройках по умолчанию Git как нам и необходимо добавится в PATH)

После правильной установки:
```bash
cd C:\Projects # выбираем папку, в которую будем клонировать проект
git clone https://github.com/Maffin0666/cbr_airflow.git
cd cbr_airflow
```

## Настройка

### 1. Переменные
Перед запуском проверим корректность переменных, которые будем передавать в систему. Это файлы `.env` (`.env_example` в данном репозитории представлен для примера наполнения), `config/variables.json`, `config/connections.json`.

`.env` - содержит переменные для решения ошибки данной версии Airflow для Linux-машин и правильной связи Docker и Airflow.

`variables.json` - содержит переменные информации о БД для работы DAGs и ссылки на ресурсы, с которых берутся данные о курсах и банках.

`connections.json` - данные для описания нового соединения с Airflow.

Импорт переменных из файлов должен осуществляться при сборке контейнера. Если нужно сделать это принудительно - используем команду в CMD уже после сборки и запуска Docker:
```bash
docker-compose exec airflow-webserver airflow variables import /opt/airflow/config/variables.json
```

## Запуск

### 1. Активация Docker
В файле `docker-compose.yaml` содержатся сведения о наших контейнерах: система Airflow, PostgreSQL, Redis, Rabbitmq.

В командной строке запустим сборку и активацию контейнеров
```bash
docker-compose up -d
```
После запуска будет доступен Airflow UI: http://localhost:8080
- Логин: airflow
- Пароль: airflow

В Docker Desktop отобразится статус контейнеров и иная информация. Можно взаимодействовать с ними (запуск, пауза и т.п.)

А в папке проекта должна создаться нужная структура папок: появятся `dags`, `logs`, `config`, `plugins`.

Для проверки состояния сервисов
```bash
docker-compose ps
```

Остановка системы через CMD:
```bash
docker-compose down
```
Можно остановить через сам Docker Desktop


### 2. Соединение с PostgreSQL

Перейдём в Airflow (открыть в браузере http://localhost:8080 , либо зайти в Docker Desktop и нажать на порт, указанный у airflow-webserver)

Вводим логин и пароль, если требуется (если не делали это выше)

Перейдём в Admin -> Connections

Проверим список соединений, из важного: наличие `postgres` (или `postgres_default`, или обоих), `redis_default`.

Подключения можно проверить через CMD:
```bash
# Проверить подключение к Redis
docker-compose exec airflow-worker celery -A airflow.executors.celery_executor.app inspect ping

# Проверить подключение к PostgreSQL
docker-compose exec airflow-webserver airflow db check

# Либо так. При успешном подключении мы сможем писать SQL запросы. (Увидим airflow=# вместо пути (\q - для выхода))
docker-compose exec postgres psql -U airflow -d airflow
```

### 3. Переменные Aiflow

Перейдём в Admin -> Variables

В списке должны отобразиться переменные, указанные в файле `variables.json`


### 3. DAGs

Проверим, что файл `cbr_etl.py` находится в папке `dags`. Если на главной странице видно два DAG: `cbr_bank_data` и `cbr_currency_rates` - всё хорошо

`cbr_bank_data` - ежедневно загружает данные банков

`cbr_currency_rates` - ежедневно загружает курсы валют

Изменяя в файле `cbr_etl.py` такие переменные как `schedule_interval`, можно менять расписание активации определённого DAGа

Также можно поставить выполнение на паузу, либо запустить выполнение задания раньше времени, нажав "trigger DAG".


## Результат

После успешного запуска Docker и настройки переменных Airflow система будет:
- Предоставлять доступ к Airflow-панели:
  - Анализ логов задач
  - Просмотр статуса выполнения DAG
  - Графическое представление зависимостей
- Обновлять данные банков по расписанию на актуальные - не сохраняя исторические данные
- Добавлять сегодняшние курсы валют следуя заданному расписанию
- Хранить данные в БД (банки, курсы и прочие системные данные)
- Логировать операции


Для просмотра данных можно подключиться через pgAgmin к нашему серверу (зарегистрировать новый сервер с нашими данными).

Либо подключиться к PostgreSQL через CMD:
```bash
cd cbr_airflow
docker-compose exec postgres psql -U airflow -d airflow

# как только путь сменится на airflow=# - можем вводить SQL запросы
# например, на выборку:

SELECT * FROM banks;
# вывод всех данных таблицы банков
SELECT * FROM currency_rates;
# вывод всех данных таблицы курсов

# для выхода из результата запроса нажимаем q
# для выхода из режима запросов (psql) \q
```
Запросы и вход в режим можно писать сразу одной строкой:
```bash
docker-compose exec postgres psql -U airflow -d airflow -c "SELECT * FROM currency_rates;"
docker-compose exec postgres psql -U airflow -d airflow -c "SELECT * FROM banks;"

# для более "красивого" и понятного вывода курсов можно добавить в запрос сортировку по дате
docker-compose exec postgres psql -U airflow -d airflow -c "SELECT * FROM currency_rates ORDER BY conversion_date DESC, from_currency;"
```
Экспорт в .csv курсов сегодняшней даты:
```bash
docker-compose exec postgres psql -U airflow -d airflow -c "COPY (SELECT * FROM currency_rates WHERE conversion_date = CURRENT_DATE) TO STDOUT WITH CSV HEADER" > rates_temp.csv
```


Наслаждайтесь работой системы

```
 /\_/\  
( o.o )  
 > ^ <

　　　　　／＞　 フ
　　　　　| 　_　 _|
　 　　　／`ミ _x 彡 
　　 　 /　　　 　 |
　　　 /　  ヽ　　 ﾉ
　／￣|　　 |　|　|
　| (￣ヽ＿_ヽ_)_)
　＼二つ
```





