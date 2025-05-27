# cbr_airflow
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
 


## Технологии

- **Backend**: Apache Airflow
- **База данных**: PostgreSQL
- **Асинхронные задачи**: Redis + Airflow-Sheduler
- **Парсинг данных**: requests, xml.etree.ElementTree
- **WEB:** Airflow-webserver на localhost


## Требования
### Windows
- Windows 10/11 (64-bit)
- Docker Desktop 4.12+
- WSL 2 (Windows Subsystem for Linux)
- 4+ GB оперативной памяти
- 10+ GB свободного места
### Linux
- Ubuntu 20.04+/Debian 10+/CentOS 7+
- 4+ GB RAM
- 10+ GB HDD
- Python 3.8+


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

### 3. Настройка Docker

```bash
docker-compose build
```







