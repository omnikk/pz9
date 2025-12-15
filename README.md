     PZ9 Технологии индустриального программирования.  
     Студент: Выборнов О.А.  
     Группа: ЭФМО-02-25  
    
Цели работы: 
        
        -Научиться безопасно хранить пароли (bcrypt), валидировать вход и обрабатывать ошибки.
        -Реализовать эндпоинты POST /auth/register и POST /auth/login.
        -Закрепить работу с БД (PostgreSQL + GORM или database/sql) и валидацией ввода.
        -Подготовить основу для JWT-аутентификации в следующем ПЗ (No 10).


## Как запустить

1) Клонировать репозиторий:

        git clone -b main --single-branch https://github.com/omnikk/PZ9.git
        cd PZ9/pz9-auth

2)Инициализация проекта и установка зависимостей:

    go mod init example.com/pz9-auth
    go get github.com/go-chi/chi/v5
    go get gorm.io/gorm gorm.io/driver/postgres
    go get golang.org/x/crypto/bcrypt

3)установить переменные окружения:

    setx DB_DSN "postgres://user:pass@localhost:5432/pz9?sslmode=disable"
    setx BCRYPT_COST 12
    setx APP_ADDR ":8080"

4)Запуск сервера:

    go run cmd/api/main.go

Ошибки:
- 400 Bad Request - невалидный JSON или отсутствуют параметры
- 401 Unauthorized - неверный email или пароль (унифицированное сообщение)
- 500 Internal Server Error - внутренние ошибки сервера

Примеры запросов и результат их выполнения

Отчет работы:

установка зависимостей:

<img width="958" height="218" alt="image" src="https://github.com/user-attachments/assets/8cdd7924-f104-4152-9f11-17b224c427a5" />


регистрация успешная:

<img width="838" height="558" alt="image" src="https://github.com/user-attachments/assets/f74dd334-f3b2-46ef-9289-1d4733a6181a" />

авторизация успешная:

<img width="627" height="562" alt="image" src="https://github.com/user-attachments/assets/ec8056af-f945-4d17-89a2-79fb4cff83c0" />

повторная регистрация на тот же самый email, не успешная:

<img width="697" height="510" alt="image" src="https://github.com/user-attachments/assets/8e1db58d-0929-4190-8401-ac7557b128fb" />

авторизация с неверным паролем:

<img width="645" height="499" alt="image" src="https://github.com/user-attachments/assets/28a12070-0f39-4e60-b441-cee6fb6bc7c4" />

авторизация с неверным email:

<img width="683" height="501" alt="image" src="https://github.com/user-attachments/assets/eafaa761-a0c5-4502-95bc-b9ba378c29e8" />

регистрация c паролем меньше 8-ми символов, не успешная:

<img width="736" height="481" alt="image" src="https://github.com/user-attachments/assets/8483fec9-3d3f-4737-a3a1-a085effcba91" />






Структура проекта

```
app
├── cmd
│   └── api
│       └── main.go
├── go.mod
├── go.sum
└── internal
    ├── core
    │   └── user.go
    ├── http
    │   └── handlers
    │       └── auth.go
    ├── platform
    │   └── config
    │       └── config.go
    └── repo
        ├── postgres.go
        └── user_repo.go
```



Описание структуры:

    go.mod / go.sum – файл модуля Go и контрольные суммы зависимостей.
    main.go – точка входа, создание HTTP-сервера и маршрутов /auth/register и /auth/login.
    auth.go – HTTP-обработчики для регистрации и входа пользователей, включая валидацию и bcrypt-хэширование паролей.
    user.go – модель пользователя для GORM.
    user_repo.go – репозиторий для работы с пользователями (создание, поиск по email, миграции).
    postgres.go – подключение к PostgreSQL через GORM.
    config.go – загрузка конфигурации из переменных окружения (DSN, BCRYPT_COST, APP_ADDR).

Выводы:

  Пароли нельзя хранить в открытом виде – утечка базы приведёт к компрометации всех аккаунтов.
  Используем bcrypt, так как он безопасен: встроенная соль, регулируемая сложность, устойчивая к brute-force атакам.
  Снижение cost ускоряет хэширование, но снижает безопасность; увеличение – наоборот, но может замедлить регистрацию/логин.
  Приложение предоставляет 2 основных эндпоинта для регистрации и входа пользователей:
  
      http://localhost:8080/auth/register - регистрация новых пользователей с хэшированием паролей
      http://localhost:8080/auth/login - аутентификация существующих пользователей
