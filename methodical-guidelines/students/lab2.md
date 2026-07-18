# Лабораторная работа №2: Конструкторы, перегрузка, static

## Тема 

**Перегрузка конструкторов и методов. Статические члены класса.**

## Задачи

1. Доработать класс Book: добавить конструктор без параметров (id=0, title="Без названия", author="Неизвестен", year=0), конструктор со всеми параметрами, конструктор без id (где полю id присваивается значение 0).
2. Организовать цепочку вызовов конструкторов с использованием ключевого слова this(...).
3. Добавить статическое поле-счетчик counter и статический метод getCounter(), возвращающий общее количество созданных объектов.
4. Реализовать статический фабричный метод createBook(String title, String author, int year), который автоматически инкрементирует счетчик ID и возвращает новый объект Book.
5. Реализовать перегруженный метод getDescription(boolean shortFormat). Если флаг равен true, возвращается краткий формат (например, только «Автор — Название»), если false — полный формат из первой лабораторной.
6. Протестировать все варианты создания объектов в методе main() и зафиксировать код через коммиты Git.
7. Сгенерировать логику фабричного метода по текстовому описанию на русском языке с помощью ИИ-инструмента.

## Стек технологий

- **Язык:** Java (версия 17+)
- **IDE:** JetBrains IntelliJ IDEA Community Edition
- **VCS:** Git
- **ИИ-помощник:** GitHub Copilot (или аналоги)

## Инструкция по выполнению

### 1. Подготовка

Убедитесь, что репозиторий инициализирован. Создайте новую ветку для чистоты истории:

```bash
git checkout -b feature/lab2-overloading-statics
```

### Модификация класса Book.java

- Конструкторы и this(...)
- Реализуйте три конструктора, используя цепочку вызовов для исключения дублирования кода:

```java
package ru.yourname.lab2;

public class Book {
    private long id;
    private String title;
    private String author;
    private int year;
    
    // Статические поля будут добавлены ниже

    // 1. Конструктор без параметров
    public Book() {
        this(0, "Без названия", "Неизвестен", 0);
    }

    // 2. Конструктор без id (делегируем полному конструктору, id будет присвоен внутри или останется 0)
    public Book(String title, String author, int year) {
        this(0, title, author, year);
    }

    // 3. Полный конструктор
    public Book(long id, String title, String author, int year) {
        this.id = id;
        this.title = title;
        this.author = author;
        this.year = year;
    }

    // ... геттеры и сеттеры остаются прежними ...
}
```

- Статические члены (счетчик и фабрика)
Добавьте управление общим состоянием класса:


```java
private static long counter = 0; // Общее число созданных книг
private static long nextId = 1;   // Для генерации уникальных автоинкрементных ID

// Возвращает количество созданных экземпляров
public static long getCounter() {
    return counter;
}

// Фабричный метод: создает книгу с уникальным ID
public static Book createBook(String title, String author, int year) {
    counter++; // Увеличиваем счетчик живых/созданных объектов
    return new Book(nextId++, title, author, year); 
}

@Override
protected void finalize() throws Throwable {
    try {
        counter--; // Декремент при сборке мусора (упрощенно, для учебных целей)
    } finally {
        super.finalize();
    }
}
```
Примечание: В реальных проектах использование finalize() не рекомендуется. Для точного подсчета живых объектов лучше использовать фабрику-обертку.

- Перегрузка getDescription()
Метод должен менять поведение в зависимости от входного параметра:

```java
/**
 * Возвращает описание книги. Формат зависит от флага shortFormat.
 */
public String getDescription(boolean shortFormat) {
    if (shortFormat) {
        return author + " — \"" + title + "\"";
    } else {
        return "\"" + title + "\" — " + author + " (" + year + ")";
    }
}
```

### 3. Класс Main.java для тестирования

- Запустите IntelliJ IDEA -> New Project -> Java.
- Выберите установленный JDK 17+.
- Создайте новый пакет (например, ru.yourname.lab1) и внутри него — класс Book.java.

### 4. Ручная реализация класса Book

```java
package ru.yourname.lab2;

public class Main {
    public static void main(String[] args) {
        System.out.println("Всего книг создано до начала: " + Book.getCounter());

        // Тест конструкторов
        Book b1 = new Book(); // По умолчанию
        Book b2 = new Book("Война и мир", "Толстой Л.Н.", 1869); // Без явного id
        Book b3 = new Book(99L, "Мастер и Маргарита", "Булгаков М.А.", 1967); // С полным id

        // Тест фабрики
        Book b4 = Book.createBook("Преступление и наказание", "Достоевский Ф.М.", 1866);
        
        System.out.println("\n--- Описания (полные) ---");
        System.out.println(b1.getDescription(false));
        System.out.println(b2.getDescription(false));
        System.out.println(b3.getDescription(false));
        System.out.println(b4.getDescription(false));

        System.out.println("\n--- Описания (краткие) ---");
        System.out.println(b1.getDescription(true));
        System.out.println(b2.getDescription(true));
        System.out.println(b3.getDescription(true));
        System.out.println(b4.getDescription(true));

        System.out.println("\nИтоговое количество созданных книг: " + Book.getCounter());
    }
}
```

### 5. Работа с системой контроля версий

- Зафиксируйте изменения:

```bash
git add src/
git commit -m "feat: constructor overloading, static counter and factory method"

```

### 6. Генерация через ИИ (Copilot)

- Удалите тело метода createBook, оставив только сигнатуру. Напишите над ней комментарий :

```java
// Создай статический фабричный метод createBook, принимающий заголовок, автора и год. 
// Метод должен увеличивать общий счетчик и возвращать новый экземпляр Book с автоматическим инкрементом ID.
```

Сравните предложенный ИИ вариант с вашим ручным кодом (обратите внимание, как модель обработала инкремент nextId и counter).

## Ожидаемый результат

Ожидаемый результат

Ссылка на репозиторий (GitHub / GitLab) с настроенным .gitignore и историей коммитов. Программа должна запускаться в IntelliJ IDEA без ошибок и выводить в консоль описания всех созданных объектов как в полном, так и в кратком формате. Код класса Book должен демонстрировать инкапсуляцию через модификатор private для всех полей, содержать публичные геттеры, сеттеры, три перегруженных конструктора, вызывающих друг друга через this(...), статическое поле-счетчик с методом доступа, а также фабричный метод createBook() и перегруженный метод getDescription(boolean shortFormat). Дополнительно оценивается корректность работы автоинкремента ID и результат генерации кода фабрики через Copilot по текстовому описанию на русском языке с анализом различий между ручным написанием и ИИ-предложением.

## Контрольные вопросы

1. Почему нельзя вызвать один конструктор из другого напрямую без this(...)?
2. Чем отличается статический метод от обычного (нестатического) с точки зрения доступа к полям?
3. Какое практическое преимущество дает перегрузка метода getDescription вместо написания двух разных методов вроде getShortInfo()?
4. Какие риски несет использование переменной типа long для счетчика ID в высоконагруженном многопоточном приложении?
5. Почему фабричные методы часто делают приватными или размещают в отдельном классе-фабрике?

## Критерии оценки

- Все пять конструкторов/модификаторов работают корректно, вызовы делегируются через this(...).
- Статический счетчик увеличивается при создании каждого нового объекта (через любой конструктор или фабрику).
- Метод getDescription(boolean) возвращает разные строки в зависимости от значения аргумента.
- Программа компилируется, вывод консоли соответствует ожидаемому формату.
- Код закоммичен в отдельную ветку Git, присутствует сравнение подходов с ИИ.

Ниже представлены доработанные варианты заданий. В каждом из них сохранена структура исходного упражнения: цепочка конструкторов через this(...), статический счетчик/состояние, фабричный метод и перегрузка бизнес-метода.

## Варианты 

1. Банковское дело (BankAccount)
Конструкторы: По умолчанию (баланс 0), с номером счета, со всеми данными.
Статика: Статическая переменная nextAccountNumber для автогенерации номеров счетов в фабричном методе.
Фабрика: createSavingsAccount(String owner) — создает счет с типом «Сберегательный» и уникальным номером.
Перегрузка: getStatement(boolean includeTransactions). Если false — только итоговый баланс, если true — полная выписка.
2. Автомобильный транспорт (Car)
Конструкторы: Дефолтный («Безымянный», год 2000), полный, конструктор копирования (Car anotherCar).
Статика: Поле totalCarsCreated.
Фабрика: fromJson(String jsonString) — парсит строку вида "brand":"Lada","model":"Vesta" и возвращает объект.
Перегрузка: drive(double kilometers) и drive(double distance, String unit), где unit может быть "km" или "miles".
3. Электронная коммерция (Product)
Конструкторы: Без ID, с ID, со скидкой по умолчанию.
Статика: Счетчик activeProducts (увеличивается в конструкторе, уменьшается при вызове метода archive()).
Фабрика: createBundle(Product p1, Product p2, double discount) — собирает два товара в один бандл.
Перегрузка: getPrice(), возвращающий double, и getPrice(Locale locale), форматирующий цену под валюту региона.
4. Социальные сети (UserProfile)
Конструкторы: Приватный основной, публичный без параметров (заглушка «Гость»).
Статика: Set<String> existingLogins для проверки уникальности на этапе создания.
Фабрика: registerViaOAuth(String provider, String externalId).
Перегрузка: updateEmail(String newEmail) и updateEmail(String newEmail, String confirmationCode).
5. Геолокация (GeoPoint)
Конструкторы: Градусы/минуты/секунды, десятичные градусы, пустой (0,0).
Статика: Константа EARTH_RADIUS_KM.
Фабрика: randomPointInRadius(GeoPoint center, double radiusKm).
Перегрузка: distanceTo(GeoPoint other) (в км) и distanceTo(GeoPoint other, DistanceUnit unit).
6. Университет (Student)
Конструкторы: Только ФИО, ФИО + номер зачетки, все данные.
Статика: Map<Integer, Student> registry для поиска студента по номеру зачетки.
Фабрика: expelledStudent(String name, int id) — помечает статус как «Отчислен».
Перегрузка: addGrade(int grade) и addGrades(List<Integer> grades).
7. Видеоигры (VideoGame)
Конструкторы: Название/жанр, полное издание (Collector's Edition), клон существующей игры.
Статика: Список List<String> bannedGenres.
Фабрика: createIndieGame(String title, String developer).
Перегрузка: isCompatible(OS os) и isCompatible(OS os, boolean checkDrivers).
8. Медицина (PatientCard)
Конструкторы: Анонимный пациент, с паспортными данными.
Статика: Пороговые значения температуры и давления (CRITICAL_TEMP).
Фабрика: emergencyCase(String symptoms) — заполняет карту экстренного пациента.
Перегрузка: takeMedication(String drugName) и takeMedication(String drugName, double dosageMg).
9. Библиотечное дело (LibraryItem)
Конструкторы: Базовый класс; подклассы вызывают super(...).
Статика: Общее количество экземпляров во всей библиотеке.
Фабрика: createFromBarcode(String barcodeData).
Перегрузка: renewLoan(User user) и renewLoan(User user, int additionalDays).
10. Космонавтика (Spacecraft)
Конструкторы: Прототип, готовая к запуску ракета.
Статика: Очередь запуска Queue<Spacecraft> launchPad.
Фабрика: cargoMission(String destination, double payloadKg).
Перегрузка: statusReport() (текстом) и statusReport(OutputStream stream) (запись в лог-файл).
11. Умный дом (SmartDevice)
Конструкторы: Только MAC-адрес, адрес + тип устройства.
Статика: Эмуляция задержки сети PROCESSING_DELAY_MS.
Фабрика: discoverOnNetwork(String ipRange).
Перегрузка: setPower(boolean onOff) и setPower(PowerLevel level) (где уровень — enum LOW/MEDIUM/HIGH).
12. Ресторанный бизнес (MenuItem)
Конструкторы: Простое блюдо, комбо-обед.
Статика: Налоговая ставка SALES_TAX_RATE.
Фабрика: dailySpecial(LocalDate date).
Перегрузка: calculateCost() и calculateCost(int guestCount).
13. Музыка (MusicTrack)
Конструкторы: Инструментальный трек (без слов), трек с вокалом.
Статика: Максимальная длительность трека в сервисе (например, 10 часов).
Фабрика: generateSilence(long durationMs).
Перегрузка: play() и play(double speedFactor) (ускоренное воспроизведение).
14. Управление проектами (Task)
Конструкторы: Задача без срока, задача с высоким приоритетом.
Статистика: Счетчик просроченных задач overdueCounter.
Фабрика: subtaskOf(Task parent).
Перегрузка: markComplete() и markComplete(String completionNotes).
15. Зоопарк (Animal)
Конструкторы: Новорожденное животное (возраст 0), взрослое.
Статика: Общее число калорий еды в зоопарке на сегодня.
Фабрика: rescuedAnimal(String species, LocalDate rescueDate).
Перегрузка: feed(Food food) и feed(List<Food> dailyRation).
16. Фитнес (Exercise)
Конструкторы: Упражнение с собственным весом, со штангой.
Статика: Словарь коэффициентов сложности упражнений.
Фабрика: warmupRoutine().
Перегрузка: logSet(int reps) и logSet(int reps, double weightKg).
17. Кибербезопасность (NetworkCredential)
Конструкторы: Создание нового пользователя, восстановление доступа.
Статика: История хэшей паролей List<String> passwordHistory для запрета старых паролей.
Фабрика: serviceAccount(String botName).
Перегрузка: rotatePassword() (автогенерация) и rotatePassword(String newPlainText).
18. Сельское хозяйство (CropField)
Конструкторы: Прямоугольное поле (длина, ширина), квадратное (сторона).
Статика: Средняя урожайность по региону.
Фабрика: irrigatedZone(double area).
Перегрузка: harvest() (убирает всё) и harvest(double hectares).
19. Event-менеджмент (ConcertTicket)
Конструкторы: Обычное место, VIP-ложа.
Статика: AtomicInteger nextSeatId (потокобезопасная нумерация).
Фабрика: buyBackReturnedTicket(int originalId).
Перегрузка интерфейса: compareTo(Ticket o) сортирует сначала по сектору, затем по ряду, затем по месту.
20. Экология (RecyclableWaste)
Конструкторы: Неизвестный тип мусора, отсортированный мусор.
Статика: Тарифная сетка баллов Map<MaterialType, Integer> ecoRates.
Фабрика: fromHouseholdWeight(double totalWeight) (автоклассификация).
Перегрузка: processBatch(double kg) и processBatch(double kg, boolean isCleanSort).
