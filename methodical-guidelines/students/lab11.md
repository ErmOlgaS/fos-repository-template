# Лабораторная работа №11: JDBC: подключение к БД и CRUD

## Тема

**ПJDBC: подключение к БД и CRUD**

## Задачи

1. Установить PostgreSQL локально или через Docker (docker run --name pg-java -e POSTGRES_PASSWORD=pass -p 5432:5432 -d postgres:16).
2. Создать таблицу books (id SERIAL PRIMARY KEY, title, author, year, isbn UNIQUE, publisher).
3. Подключиться через JDBC. Реализовать операции: вставка одной книги, вставка пакетная (batch insert), выборка всех книг, выборка по автору, обновление года, удаление по ISBN.
4. ВСЕ ресурсы (Connection, Statement, ResultSet) закрывать в try-with-resources.
5. Реализовать BookDao — класс, инкапсулирующий все операции с таблицей books (паттерн DAO).
6. генерировать CRUD-методы для BookDao, проверить — корректно ли ИИ закрывает ресурсы и обрабатывает SQLException.


## Стек технологий

- **Язык:** Java (версия 17+)
- **IDE:** JetBrains IntelliJ IDEA Community Edition
- **VCS:** Git
- **ИИ-помощник:** GitHub Copilot (или аналоги)

## Инструкция по выполнению

### 1. Подготовка

Cоздайте новую ветку:

```bash
git checkout -b feature/lab11-jdbc
```

### 2. Настройка БД (PostgreSQL)

- РЗапустить PostgreSQL локально или через Docker

```bash
docker run --name pg-java -e POSTGRES_PASSWORD=pass -p 5432:5432 -d postgres:16
```

- Подключиться (psql или DBeaver) и создать базу и таблицу

``` sql
dCREATE TABLE books (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    author VARCHAR(255) NOT NULL,
    year INT NOT NULL CHECK (year > 0),
    isbn VARCHAR(20) UNIQUE NOT NULL,
    publisher VARCHAR(255)
);
```

### 3. Подключение (JDBC Driver)

- Добавить зависимость в pom.xml: org.postgresql:postgresql:42.7.2.
- Реализовать класс получения соединения, вынеся URL, логин и пароль в константы.

### 4.Реализация операций (CRUD)

- Create (Single): Метод insertBook(Book book) через PreparedStatement.executeUpdate(). Вернуть сгенерированный ID (RETURNING id).
- Create (Batch): Метод batchInsert(List<Book> books). Использовать addBatch() / executeBatch().
Read (All): Метод findAll()→ List<Book>.
- Read (By Author): Метод findByAuthor(String author) → List<Book>.
- Update: Метод updateYearByIsbn(String isbn, int newYear).
- Delete: Метод deleteByIsbn(String isbn).

### 5. Управление ресурсами

- ВСЕ объекты (Connection, PreparedStatement, ResultSet) должны быть обернуты в try-with-resources..

### 6. Паттерн DAO

- Создать интерфейс BookDao с описанием всех методов выше.
- Создать реализацию JdbcBookDao implements BookDao.
- В классе main использовать только интерфейс BookDao, не обращаясь к SQL напрямую из бизнес-логики.

### 8. Генерация через ИИ (Copilot)

- Дать Copilot промпт: «Создай Java-класс с именем JdbcBookDao, который реализует интерфейс BookDao. Используй чистый JDBC (plain JDBC) с конструкцией try-with-resources для автоматического закрытия объектов Connection, PreparedStatement и ResultSet.
Реализуй все CRUD-методы:
save(Book book): возвращает Optional<Long> со сгенерированным базой ID.
batchSave(List<Book> books): использует методы addBatch() и executeBatch().
findAll() и findByAuthor(String author).
updateYear(String isbn, int year).
delete(String isbn).
Оборачивай любое исключение SQLException в непроверяемое исключение RuntimeException».».

### 8. Работа с системой контроля версий

- Зафиксируйте изменения:

```bash
git status
git add .
git commit -m "feat(lab11): implemented JDBC BookDao with CRUD operations"
```

## Ожидаемый результат

Ссылка на репозиторий. Программа выполняет последовательность действий:

- Удаляет старые тестовые данные.
- Пакетно вставляет 100 книг.
- Находит книги автора «Толстой».
- Обновляет год издания одной из них.
- Печатает итоговый список всех книг в консоль.
- Код разделен на слои: сущность, DAO-интерфейс, DAO-реализация.

## Контрольные вопросы

1. Почему для передачи строковых данных (ISBN) обязательно нужно использовать setString() у PreparedStatement, а не конкатенацию строки "WHERE isbn = '" + isbn + "'"?
2. В чем преимущество использования try-with-resources перед блоком finally { if (conn != null) conn.close(); } при работе с сетевыми соединениями?
3. Какую проблему решает паттерн DAO? Что произойдет с кодом контроллера, если мы решим заменить PostgreSQL на MongoDB?

## Критерии оценки

Критерии оценки: корректная настройка Docker или локального PostgreSQL и создание таблицы books с нужными ограничениями, реализация класса DAO (JdbcBookDao) с полным набором CRUD-операций, где все ресурсы JDBC (Connection, PreparedStatement, ResultSet) закрыты через try-with-resources, а SQLException обернуты в непроверяемые исключения без протечки абстракций; пакетная вставка реализована через addBatch()/executeBatch(), при сохранении книги возвращается сгенерированный ключ через RETURNING id или getGeneratedKeys(), логика SQL скрыта за интерфейсом BookDao (паттерн DAO), предоставлен скрипт инициализации БД, а изменения зафиксированы коммитом в Git с осмысленным сообщением.

## Варианты

Общая задача: Реализовать слой доступа к данным (DAO), где каждый объект предметной области хранится в отдельной таблице PostgreSQL.

1. Банковское дело (BankAccount)
    - Таблицы: accounts, transactions.
    - Задача: Транзакция перевода между счетами должна быть атомарной (использовать JDBC Transaction / setAutoCommit(false)). При ошибке — Rollback.
2. Транспорт (Car)
    - Таблица: cars (VIN как PRIMARY KEY).
    - Задача: Реализовать метод findDuplicatesByChassisNumber() через SQL GROUP BY ... HAVING count(*) > 1.

3. E-commerce (Product)
    - Таблицы: products, order_items.
    - Задача: Реализовать проверку остатков на складе внутри одной транзакции заказа, чтобы избежать состояния -1 (Optimistic Locking или SELECT FOR UPDATE)
4. Соцсети (UserProfile)
    - Таблица: users (логин UNIQUE).
    - Задача: Обработать пакетную регистрацию из CSV. Если логин занят, строка должна упасть с ошибкой, не прерывая весь батч (Savepoints)

5. Геолокация (GeoPoint)
    - Таблица: geo_points.
    - Задача: Написать SQL-запрос поиска всех точек в радиусе NNN км от заданных координат (используя формулу Haversine в WHERE).

6. Университет (Student)
    - Таблицы: students, grades.
    - Задача: Вычисление среднего балла (GPA) прямо на стороне СУБД через AVG() и JOIN, а не маппингом в Java.
7. Видеоигры (VideoGame)
    - Таблица: games (поле install_size_mb BIGINT).
    - Задача: Перед установкой проверять свободное место в «облачном хранилище» пользователя (отдельная таблица storage), используя транзакцию.

8. Медицина (PatientCard)
    - Таблица: patients (SNILS UNIQUE).
    - Задача: Реализовать частичное обновление записи (UPDATE ... SET field=? WHERE id=?) только для тех полей, которые не null в объекте.
9. Библиотека (LibraryItem)
    - Таблицы: books, borrow_log.
    - Задача: Агрегатный SQL-запрос: вывести топ-5 авторов по количеству выдач за последний месяц.
10. Космонавтика (Spacecraft)
    - Таблица: systems (энергопотребление INT).
    - Задача: Написать DAO-метод getTotalPowerConsumption(), использующий SQL SUM().
11. Умный дом (SmartDevice)
    - Таблица: devices (ip_address UNIQUE).
    - Задача: Реализовать поиск подсети (WHERE ip LIKE '192.168.1.%') и массовое обновление статуса онлайн/оффлайн.
12. Ресторан (MenuItem)
    - Таблицы: menu, allergens, menu_allergen_map (Many-to-Many).
    - Задача: Найти все блюда, НЕ содержащие конкретный аллерген, через LEFT JOIN + NULL check.
13.	Музыка (MusicTrack)
    - Таблица: tracks (duration_sec).
    - Задача: Реализовать постраничный вывод (Pagination) библиотеки треков с помощью LIMIT и OFFSET.
14.	Управление проектами (Task)
    - Таблица: tasks (status VARCHAR, parent_id FK).
    - Задача: Рекурсивный SQL-запрос (CTE) для построения дерева зависимостей задач («блокеров»).
15. Зоопарк (Animal)
    - Таблица: feeding_log.
    - Задача: Вставка записи о кормлении должна провалидировать тип корма относительно диеты животного (Enum в Java →\rightarrow→ CHECK constraint в SQL).
16. Фитнес (Exercise)
    - Таблица: workouts.
    - Задача: Вычислить суммарный тоннаж (sets * reps * weight) одним SQL-запросом для конкретного атлета.
17. Кибербезопасность (NetworkCredential)
    - Таблица: auth_logs.
    - Задача: Запрос поиска IP-адресов с количеством неудачных попыток >5> 5>5 за последние 5 минут (Window Functions или GROUP BY + TIME_BUCKET).
18. Агро (CropField)
    - Таблица: harvest_reports.
    - Задача: Группировка урожая по неделям года (EXTRACT(WEEK FROM date)) и типу культуры.
19. Event-менеджмент (ConcertTicket)
    - Таблицы: tickets, sectors.
    - Задача: Вывести схему зала (схема рассадки) с указанием забронированных мест через булев флаг is_booked.
20. Экология (RecyclableWaste)
    - Таблица: waste_batches.
    - Задача: Рассчитать средний эко-рейтинг по категориям материалов, исключая выбросы (значения <5< 5<5-го перцентиля) через оконные функции.
