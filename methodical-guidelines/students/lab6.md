# Лабораторная работа №6: Исключения и логирование

## Тема 

**Обработка ошибок. Создание собственных исключений. Логирование.**

## Задачи

1. Создать checked-исключение BookNotFoundException и unchecked InvalidBookDataException.
2. В Repository<Book> выбрасывать BookNotFoundException при отсутствии книги в findById.
3. В сеттерах Book валидировать входные данные (title не null/пустой, year > 0 и year <= текущий) — при нарушении бросать InvalidBookDataException.
4. Подключить библиотеку SLF4J + Logback. Расставить логирование:
   - INFO: успешное сохранение/удаление книги.
   - WARN: попытка получить несуществующую книгу.
   - ERROR: некорректные данные при создании книги.
5. Написать try-catch-finally и try-with-resources для работы с файлами (запись лога в файл отдельно от консоли).
6. Дать ИИ описание BookNotFoundException и попросить сгенерировать класс — оценить, добавил ли он конструктор с сообщением и цепочкой причин.


## Стек технологий

- **Язык:** Java (версия 17+)
- **IDE:** JetBrains IntelliJ IDEA Community Edition
- **VCS:** Git
- **ИИ-помощник:** GitHub Copilot (или аналоги)

## Инструкция по выполнению

### 1. Подготовка

Используя код Лабораторной работы №5, создайте новую ветку:

```bash
git checkout -b feature/lab6-exceptions-logging
```

### 2. Создание исключений

- BookNotFoundException — наследник Exception (checked)
- InvalidBookDataException — наследник RuntimeException (unchecked).

### 3. Валидация в сущности (Book)

- В сеттерах полей title, author, year добавить проверку данных.
- При невалидных данных бросать new InvalidBookDataException("Описание ошибки").

### 4. Логирование (SLF4J + Logback)

- Подключить зависимости Maven/Gradle для slf4j-api и logback-classic.
- Настроить файл src/main/resources/logback.xml: вывод INFO+ в консоль, WARN+ в файл app.log.
- Добавить приватное статическое поле logger в классы Repository и Main.
- Расставить уровни:
INFO: Успешные операции save(), delete().
WARN: Вызов findById() вернул пустой результат (книга не найдена).
ERROR: Поймано исключение InvalidBookDataException при создании объекта.

### 5. Обработка в Repository

- Метод findById(long id) должен выбрасывать BookNotFoundException, если книга отсутствует.

### 6. Работа с потоками (I/O):

- Написать метод сохранения списка книг в текстовый файл через try-with-resources (используя BufferedWriter или PrintWriter).
- Написать метод чтения из файла через классический блок try-catch-finally (для демонстрации закрытия ресурса вручную).

### 7. Генерация через ИИ (Copilot)

Дать Copilot комментарий: // Create a checked exception BookNotFoundException extending Exception with constructors for message and cause.
Оценить: добавил ли ИИ конструктор super(message, cause)?

### 8. Работа с системой контроля версий

- Зафиксируйте изменения:

```bash
git add src/
git commit -m "feat(lab6): implemented checked/unchecked exceptions and SLF4J logging"
```

## Ожидаемый результат

Ссылка на репозиторий. Программа корректно валидирует данные книги, перехватывает ошибки и пишет сообщения разного уровня важности в разные назначения (консоль и файл). Код работы с файлами защищен от утечек ресурсов.

## Контрольные вопросы

1. Почему InvalidBookDataException сделан unchecked? Какое правило проектирования это диктует?
2. Что произойдет, если убрать вызов setTitle из конструктора Book, а передать пустую строку через конструктор напрямую в поле?
3. Зачем в BookNotFoundException нужен конструктор (String message, Throwable cause)?
4. Какой уровень логирования выбрать для вывода SQL-запросов в базу данных: DEBUG или TRACE?

## Критерии оценки

Оценка лабораторной работы производится по следующим критериям: корректность иерархии исключений, где BookNotFoundException является проверяемым (checked), а InvalidBookDataException — непроверяемым (unchecked), при этом оба класса должны содержать конструкторы с сообщением и цепочкой причин; наличие валидации данных внутри сеттерах сущности Book, выбрасывающей исключение при некорректных значениях (пустое название, невалидный год); реализация метода findById в репозитории, который сигнализирует об отсутствии объекта через выброс BookNotFoundException вместо возврата null; правильная настройка логирования SLF4J + Logback с использованием файла конфигурации для разделения вывода INFO+ в консоль и WARN+ в файл, а также корректная расстановка уровней сообщений (INFO для успешных операций, WARN для поиска несуществующих ID, ERROR для технических сбоев со стектрейсом); демонстрация безопасной работы с файлами через конструкции try-with-resources и классический try-catch-finally; чистота коммита в Git с осмысленным сообщением согласно Conventional Commits, а также аналитический разбор кода, сгенерированного ИИ-инструментом на предмет наличия всех необходимых конструкторов.

## Варианты

1. Банковское дело (BankAccount)
Exception: InsufficientFundsException при попытке списания суммы больше баланса.
Logging: INFO — успешный перевод; ERROR — недостаточно средств.
2. Автомобильный транспорт (Car)
Exception: DuplicateVINException при добавлении авто с существующим VIN в реестр.
Logging: WARN — попытка регистрации дубля; INFO — регистрация пройдена.
3. Электронная коммерция (Product)
Exception: OutOfStockException при заказе товара, которого нет на складе.
Logging: INFO — заказ оформлен; WARN — товар закончился во время оформления.
4. Социальные сети (UserProfile)
Exception: UsernameTakenException при занятом логине.
Logging: ERROR — неудачная попытка подбора/регистрации; INFO — вход выполнен.
5. Геолокация (GeoPoint)
Exception: InvalidCoordinatesException при выходе за пределы широты/долготы.
Logging: WARN — некорректные GPS-данные от устройства.
6. Университет (Student)
Exception: GradeFormatException при передаче неверного формата оценки (например, > 100).
Logging: ERROR — ошибка импорта ведомости из файла.
7. Видеоигры (VideoGame)
Exception: UnsupportedPlatformException при запуске игры на несовместимой ОС.
Logging: INFO — игра запущена; DEBUG — проверка системных требований.
8. Медицина (PatientCard)
Exception: MedicalDataAccessException при ошибке доступа к защищенным данным пациента.
Logging: ERROR — несанкционированный доступ к базе СНИЛС.
9. Библиотечное дело (LibraryItem)
Exception: LoanLimitExceededException при превышении лимита книг у читателя.
Logging: INFO — книга выдана; WARN — читатель имеет задолженность.
10. Космонавтика (Spacecraft)
Exception: FuelDepletionException при недостатке топлива для маневра.
Logging: FATAL — критическая потеря связи или топлива.
11. Умный дом (SmartDevice)
Exception: ConnectionTimeoutException при потере связи с устройством.
Logging: WARN — устройство оффлайн; INFO — переподключение успешно.
12. Ресторанный бизнес (MenuItem)
Exception: AllergenNotDeclaredException если в блюде есть аллерген без маркировки.
Logging: ERROR — жалоба клиента на состав блюда.
13. Музыка (MusicTrack)

Exception: LicenseExpiredException при попытке воспроизведения трека по истекшей подписке.
Logging: INFO — трек проигрывается; WARN — регион заблокирован.
14. Управление проектами (Task)
Exception: CircularDependencyException при создании циклической зависимости задач.
Logging: ERROR — нарушение структуры спринта.
15. Зоопарк (Animal)
Exception: DietMismatchException при попытке кормления хищника травой.
Logging: WARN — животное отказывается от еды.
16. Фитнес (Exercise)
Exception: EquipmentInUseException при попытке занять занятый тренажер.
Logging: INFO — начало подхода; DEBUG — расчет пульса.
17. Кибербезопасность (NetworkCredential)
Exception: BruteForceDetectedException при множественных неудачных попытках входа.
Logging: SECURITY — блокировка IP-адреса.
18. Сельское хозяйство (CropField)
Exception: SoilContaminationException при обнаружении вредителей выше нормы.
Logging: WARN — требуется обработка пестицидами.
19. Event-менеджмент (ConcertTicket)
Exception: SeatAlreadyOccupiedException при двойном бронировании места.
Logging: INFO — скан билета прошел; WARN — попытка повторного прохода.
20. Экология (RecyclableWaste)
Exception: HazardousMaterialException при попадании запрещенного мусора в бак.
Logging: ERROR — остановка конвейера сортировочной ленты.