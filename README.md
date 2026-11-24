# Розгортання WordPress веб-аплікації із використанням Docker Compose

Розрахунково-графічна робота з дисципліни **«Операційні системи»**  
**Студент:** Сало Дмитро, група ПП-24  
**Університет:** Національний університет «Львівська політехніка», кафедра САП

## Архітектура проєкту

Багатоконтейнерний стек LEMP складається з трьох ізольованих сервісів у спільній bridge-мережі `app-network`:
- `db-salo` (`mysql:8.0`) — база даних MySQL з іменованим томом `dbdata` та плагіном `mysql_native_password`.
- `wordpress-salo` (`wordpress:5.1.1-fpm-alpine`) — PHP-FPM інтерпретатор та файли CMS WordPress на спільному томі `wordpress`.
- `webserver-salo` (`nginx:1.15.12-alpine`) — реверс-проксі та вебсервер з примусовим перенаправленням HTTP (80) → HTTPS (443) і SSL-сертифікатом OpenSSL.

## Структура проєкту

```text
wordpress-salo/
├── docker-compose.yml       # Опис сервісів, томів та мережі
├── .env.example             # Шаблон змінних середовища (реальний .env у .gitignore)
├── .gitignore               # Виключення секретів із Git
├── .dockerignore            # Виключення службових файлів із контексту збірки
└── nginx-conf/
    ├── nginx.conf           # Конфігурація віртуальних хостів HTTP/HTTPS та FastCGI
    └── certs/               # Каталог для SSL-сертифікатів
```

## Швидкий запуск

1. Додайте локальний домен у файл `hosts`:
   ```text
   127.0.0.1 dmytro-salo.local
   ```
2. Створіть `.env` на базі `.env.example` зі своїми паролями.
3. Згенеруйте самопідписний SSL-сертифікат у каталозі `nginx-conf/`:
   ```bash
   openssl genrsa -out dmytro-salo.key 2048
   openssl req -new -x509 -key dmytro-salo.key -out dmytro-salo.crt -days 365
   ```
4. Запустіть стек контейнерів:
   ```bash
   docker compose up -d
   ```
5. Відкрийте у браузері `https://dmytro-salo.local`.
