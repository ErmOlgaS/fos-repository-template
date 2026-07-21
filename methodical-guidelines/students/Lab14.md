# Лабораторная 14. ETL-пайплайн: Load + мониторинг (ETL завершён)

## Тема

**Этап Load. Идемпотентность. Логирование и метрики ETL-процесса.**

## Задачи

1. Load: реализовать запись очищенных данных в PostgreSQL через BookDao, созданный ранее. Использовать UPSERT для обеспечения идемпотентности.
2. Собрать весь пайплайн в единый класс BookEtlPipeline:
java
public EtlResult run(String sourcePath, String sourceFormat) { ... }
где EtlResult содержит метрики: extracted, filtered, enriched, loaded, errors.
3. Реализовать EtlResult как иммутабельный объект (record или final-класс).
4. Добавить логирование каждого этапа: начало, завершение, количество обработанных записей, ошибки.
5. Реализовать сохранение записей с ошибками в отдельный файл etl_errors.csv (bad records) — dead-letter-queue.
6. Запустить пайплайн на тестовых данных (500+ записей, включая заведомо некорректные). Проверить идемпотентность повторным запуском.
7. Проанализировать с помощью ИИ код пайплайна и предложить улучшения по обработке ошибок и читаемости.

## Стек технологий

- **Язык:** Java (версия 17+)
- **IDE:** JetBrains IntelliJ IDEA Community Edition
- **VCS:** Git
- **ИИ-помощник:** GitHub Copilot (или аналоги)

## Инструкция по выполнению

### 1. Подготовка

Cоздайте новую ветку:

```bash
git checkout -b feature/lab14-etl-load
```

### 2. Load (Загрузка в БД)

- Использовать существующий BookDao из Лабораторной работы №12 или №13.
- Реализовать идемпотентную запись через SQL INSERT ... ON CONFLICT(isbn) DO UPDATE. Это гарантирует, что повторный запуск пайплайна не создаст дубликатов.
- Обернуть вызов DAO в блок try-catch. При ошибке записи конкретной книги — не прерывать весь процесс.

### 3. Cборка пайплайна (BookEtlPipeline)

- Создать класс BookEtlPipeline, объединяющий Extract (из Лабы 13), Transform (из Лабы 13) и новый Load.
- Метод должен выглядеть так:

### 4.Результат

- Метод runPipeline() должен вернуть готовый List<Book>.

```java
public EtlResult run(String sourcePath, String sourceFormat) { ... }
```

### 5. Метрики (EtlResult)

Создать иммутабельный объект для результата. Рекомендуется использовать Java Record:

```java
public record EtlResult(int extracted, int filtered, int enriched, int loaded, int errors) {}
```

### 6. Логирование (SLF4J / Logback)

- Добавить логгер в BookEtlPipeline.
- Логировать начало/конец этапа, количество записей на входе/выходе каждого шага (Extracted → Filtered→ Enriched → Loaded).

### 7. Dead Letter Queue (DLQ)

- Если книга не прошла очистку (Transform) или упала при записи в БД (Exception), она должна быть сохранена в файл etl_errors.csv.
- В файле DLQ обязательно сохранить исходные данные строки и колонку с текстом ошибки (error_message).

### 8. Тестирование

- Подготовить тестовый CSV/JSON с 500+ записями, где часть ISBN пустые, авторы написаны криво, а у части книг уже есть дубликаты в БД.
- Запустить пайплайн дважды. Убедиться, что при втором запуске счетчик loaded равен 0 (так как все валидные данные уже загружены благодаря UPSERT).

### 9. Генерация через ИИ (Copilot)

Скопировать код класса BookEtlPipeline в Copilot и сформировать запрос: «Analyze this code for error handling and suggest improvements using CompletableFuture or better logging practices».

### 10. Работа с системой контроля версий

- Зафиксируйте изменения:

```bash
git status
git add .
git commit -m "feat(lab14): completed ETL pipeline with Load stage and metrics"
```

## Ожидаемый результат

Ссылка на репозиторий. Программа выводит 

```text
INFO : [EXTRACT] Read 512 items from books.csv
INFO : [TRANSFORM] Filtered out 5 items (no ISBN). Enriched 480 authors.
INFO : [LOAD] Successfully upserted 475 items into DB.
WARN : [DLQ] Saved 7 bad records to etl_errors.csv
INFO : [PIPELINE] Finished. Result: EtlResult[extracted=512, loaded=475, errors=7]
```

## Контрольные вопросы

1. Почему использование save(book) внутри Stream API считается плохой практикой и чем это лучше заменить?
2. Что такое идемпотентность? Как именно конструкция ON CONFLICT её обеспечивает на уровне СУБД?
3. Зачем нужен файл etl_errors.csv (Dead Letter Queue)? Почему нельзя просто залогировать ошибку упавшей строки и забыть про неё?

## Критерии оценки

Корректная реализация иммутабельного объекта метрик (через record или final-класс), полная интеграция этапов Extract и Transform с этапом Load через универсальный DAO, обеспечивающий идемпотентность данных за счет SQL-конструкции ON CONFLICT DO UPDATE, наличие централизованного класса BookEtlPipeline с методом run(), возвращающим объект метрик, качественное логирование каждого этапа процесса через SLF4J без использования System.out.println, а также реализованный механизм Dead Letter Queue для сохранения некорректных записей в файл etl_errors.csv внутри стрима; итоговая проверка пайплайна на наборе из 500+ записей должна подтвердить отсутствие дубликатов при повторном запуске (идемпотентность) и соответствие фактических значений в объекте EtlResult записям в базе данных и файле ошибок.
