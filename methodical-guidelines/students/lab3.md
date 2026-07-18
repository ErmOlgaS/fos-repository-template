# Лабораторная работа №3: Наследование и полиморфизм

## Тема 

**Иерархия классов. Переопределение методов. Позднее связывание.**

## Задачи

1. Модифицировать класс Book, созданный в Лабораторной работе №2, превратив его в абстрактный базовый класс.
2. Создать два класса-наследника:

    - Ebook — наследник Book. Добавить специфичные поля: fileFormat (String) и isDrmProtected (boolean).
    - PrintedEdition — наследник Book. Добавить специфичные поля: bindingType (String) и pageCount (int).
3. Переопределение методов (@Override):

    - Реализовать абстрактный метод calculateDiscount() в классе Book.
        - В Ebook: скидка всегда фиксированная (например, 20%).
        - В PrintedEdition: скидка зависит от объема (например, > 500 страниц = 15%, иначе 5%).

    - Переопределить методы getDescription(boolean shortFormat) и toString() в каждом классе с учетом новых полей.
4. В методе main() создать массив базового типа Book[] library из 5 объектов (смесь Ebook и PrintedEdition).
5. В цикле вызвать System.out.println(book.getDescription(false)) и System.out.printf("Скидка: %.0f%%%n", book.calculateDiscount() * 100). Убедиться, что вызываются версии методов конкретных подклассов.
6. Добавить интерфейс Reportable с методом generateStockReport(). Реализовать этот интерфейс только в классе PrintedEdition (так как складской учет актуален для физических копий). Метод должен возвращать строку вида: "Склад [ID: X]: Печать 'Hardcover', Кол-во: 100 шт.".
7. Создать список List <Reportable> reporters. Пройти по массиву library, проверить объекты через instanceof Reportable и добавить подходящие элементы в список.Вывести результаты работы метода generateReport() единым циклом.
8. Сгенерировать шаблон иерархии с помощью ИИ-инструмента, затем вручную скорректировать бизнес-логику.

## Стек технологий

- **Язык:** Java (версия 17+)
- **IDE:** JetBrains IntelliJ IDEA Community Edition
- **VCS:** Git
- **ИИ-помощник:** GitHub Copilot (или аналоги)

## Инструкция по выполнению

### 1. Подготовка

Используя код Лабораторной работы №2, создайте новую ветку:

```bash
git checkout -b feature/lab3-inheritance
```

### 2. Код реализации

- **Базовый класс Book.java (Abstract)**
Обратите внимание: добавлен абстрактный метод скидки, поле id сделано protected для удобства доступа в наследниках:

```java
public abstract class Book {
    protected long id;
    private String title;
    private String author;
    private int year;

    // Конструкторы и статические счетчики остаются из Лабы 2...
    
    public abstract double calculateDiscount(); // Новый абстрактный метод

    @Override
    public String toString() {
        return "Book{id=" + id + ", title='" + title + '\'' + '}';
    }
}
```

- **Класс Ebook.java**


```java
public class Ebook extends Book {
    private String fileFormat;
    private boolean isDrmProtected;

    // Конструктор делегирует вызов родительскому
    public Ebook(long id, String title, String author, int year, 
                 String format, boolean drm) {
        super(id, title, author, year);
        this.fileFormat = format;
        this.isDrmProtected = drm;
    }

    @Override
    public double calculateDiscount() {
        return 0.20; // Фиксированные 20%
    }

    @Override
    public String getDescription(boolean shortFormat) {
        if (shortFormat) return author + " — \"" + title + "\"";
        return "[EBOOK] " + super.getDescription(false) + 
               " | Format: " + fileFormat;
    }
}
```

- **Класс PrintedEdition.java**

```java
public class PrintedEdition extends Book implements Reportable {
    private String bindingType;
    private int pageCount;

    public PrintedEdition(long id, String title, String author, int year,
                          String binding, int pages) {
        super(id, title, author, year);
        this.bindingType = binding;
        this.pageCount = pages;
    }

    @Override
    public double calculateDiscount() {
        if (pageCount > 800) return 0.30;
        if (pageCount > 500) return 0.15;
        return 0.05; 
    }

    @Override
    public String getDescription(boolean shortFormat) {
        if (shortFormat) return author + " — \"" + title + "\"";
        return "[PRINTED] " + super.getDescription(false) + 
               " | Pages: " + pageCount;
    }

    @Override
    public String generateStockReport() {
        return "Отчет склада [ID:" + id + "]: Тип переплета '" + 
               bindingType + "', Наличие: [ЗАГЛУШКА: 50 экз.]";
    }
}
```

### 3. Демонстрация в Main.java

```java
import java.util.ArrayList;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        System.out.println("=== ДЕМОНСТРАЦИЯ ПОЛИМОРФИЗМА ===\n");

        Book[] library = new Book[5];
        library[0] = new Ebook(1L, "Чистый код", "Мартин", 2008, "PDF", true);
        library[1] = new PrintedEdition(2L, "Дом в котором...", "Петросян", 2009, "Hardcover", 992);
        library[2] = new PrintedEdition(3L, "Мастер и Маргарита", "Булгаков", 1967, "Paperback", 512);
        library[3] = new Ebook(4L, "Атлант расправил плечи", "Рэнд", 1957, "EPUB", false);
        
        List<Reportable> reporters = new ArrayList<>();

        for (Book b : library) {
            System.out.println(b.getDescription(false));
            System.out.printf("Скидка: %.0f%%%n", b.calculateDiscount() * 100);
            
            // Проверка возможности генерации отчета
            if (b instanceof Reportable r) {
                reporters.add(r);
            }
            System.out.println("---------------------------------");
        }

        System.out.println("\n=== ОТЧЕТЫ ПО СКЛАДУ ===");
        for (Reportable rep : reporters) {
            System.out.println(rep.generateStockReport());
        }
    }
}

```

### 4. Генерация через ИИ (Copilot)

- Напишите комментарий над пустым файлом PrintedEdition:

```java
// Create a subclass of Book named PrintedEdition with fields bindingType and pageCount. Override discount logic based on page count. Implement Reportable interface.
```

Вручную скорректируйте бизнес-логику.

### 5. Работа с системой контроля версий

- Зафиксируйте изменения:

```bash
git add src/
git commit -m "feat(lab3): implemented inheritance hierarchy Book -> Ebook/Printed"

```

## Ожидаемый результат

Ссылка на репозиторий. Программа должна вывести 5 описаний книг с разными значениями скидок (доказательство вызова переопределенных методов), а затем сформировать отчет только для тех объектов, которые реализуют интерфейс Reportable (в данном случае — только для печатных изданий). Выполнено сравнение кода фабрики или конструкторов с результатом генерации GitHub Copilot.\.

## Контрольные вопросы

1. Почему мы не можем создать экземпляр new Book(...) напрямую?
2. Какую роль играет конструкция instanceof Reportable r в контексте интерфейсов?
3. Чем опасно использование переменной long для счетчика ID в многопоточном приложении?

## Критерии оценки

- Корректная иерархия (abstract, extends, implements).
- Цепочка конструкторов через this(...) работает без дублирования кода.
- Статический счетчик увеличивается при создании каждого объекта.
- Полиморфизм подтвержден выводом консоли.
Код закоммичен в отдельную ветку Git..

## Варианты 

1. Банковское дело (BankAccount)
Иерархия: BankAccount  CreditAccount, SavingsAccount.
Абстракция: Метод calculateInterest().
Полиморфизм: Массив BankAccount[] с вызовом расчета процентов.
Интерфейс: TransactionLoggable (метод getHistory()).
2. Автомобильный транспорт (Car)
Иерархия: Car  Truck, SportCar.
Абстракция: Метод fuelConsumptionPer100km().
Полиморфизм: Массив Car[] для вывода расхода топлива разных типов авто.
Интерфейс: OffroadCapable (метод engage4WD()).
3. Электронная коммерция (Product)
Иерархия: Product  DigitalGood, PhysicalGood.
Абстракция: Метод calculateShippingCost(). Для цифровых товаров — 0.
Полиморфизм: Список корзины, подсчет итоговой стоимости доставки.
Интерфейс: Discountable (метод applyPromoCode()).
4. Социальные сети (UserProfile)
Иерархия: UserProfile  VerifiedUser, BusinessUser.
Абстракция: Метод getMaxUploadSize().
Полиморфизм: Массив профилей для отображения лимитов загрузки.
Интерфейс: Advertisable (метод showAdsPanel()).
5. Геолокация (GeoPoint)
Иерархия: GeoPoint  CityLocation, MountainPeak.
Абстракция: Метод getAltitude().
Полиморфизм: Расчет расстояния между массивом точек относительно базовой.
Интерфейс: TimeZoneAware (метод getLocalTime()).
6. Университет (Student)
Иерархия: Student  Undergraduate, Graduate.
Абстракция: Метод getScholarshipAmount().
Полиморфизм: Ведомость выплат стипендий из массива студентов.
Интерфейс: DormitoryResident (метод getRoomNumber()).
7. Видеоигры (VideoGame)
Иерархия: VideoGame  IndieGame, AAA_Title.
Абстракция: Метод getSystemRequirements().
Полиморфизм: Библиотека игр, проверка запуска на ПК.
Интерфейс: Multiplayer (метод connectToServer()).
8. Медицина (PatientCard)
Иерархия: PatientCard  Inpatient, Outpatient.
Абстракция: Метод calculateTreatmentCost().
Полиморфизм: Реестр пациентов стационара и амбулатории.
Интерфейс: Insured (метод getPolicyNumber())
9. Библиотечное дело (LibraryItem)
Иерархия: LibraryItem  BookCopy, MagazineIssue.
Абстракция: Метод getReturnDueDate().
Полиморфизм: Выдача читателю любого типа издания.
Интерфейс: Reservable (метод placeHold()).
10. Космонавтика (Spacecraft)
Иерархия: Spacecraft  OrbitalStation, Lander.
Абстракция: Метод currentFuelLevel().
Полиморфизм: Стартовый стол со списком кораблей.
Интерфейс: DeepSpaceProbe (метод transmitSignal()).
11. Умный дом (SmartDevice)
Иерархия: SmartDevice  Thermostat, SecurityCamera.
Абстракция: Метод updateFirmware().
Полиморфизм: Управление всеми устройствами дома через единый список.
Интерфейс: BatteryPowered (метод checkBatteryStatus()).
12. Ресторанный бизнес (MenuItem)
Иерархия: MenuItem  Beverage, MainCourse.
Абстракция: Метод isGlutenFree().
Полиморфизм: Печать меню с разделением по категориям.
Интерфейс: Spicy (метод getScovilleIndex()).
13. Музыка (MusicTrack)
Иерархия: MusicTrack  LiveRecording, StudioMix.
Абстракция: Метод getFileSizeMB().
Полиморфизм: Плейлист с расчетом общего веса файлов.
Интерфейс: ExplicitContent (метод hasParentalWarning()).
14. Управление проектами (Task)
Иерархия: Task  BugFix, FeatureDevelopment.
Абстракция: Метод estimateHours().
Полиморфизм: Спринт-борд с задачами разного типа.
Интерфейс: Blocker (метод blockProgress()).
15. Зоопарк (Animal)
Иерархия: Animal  Mammal, Reptile.
Абстракция: Метод makeSound().
Полиморфизм: «Пробуждение зоопарка» — вызов звуков у всех животных в массиве.
Интерфейс: Feedable (метод feed(Food food)).
16. Фитнес (Exercise)
Иерархия: Exercise  Cardio, StrengthTraining.
Абстракция: Метод caloriesBurnedPerHour().
Полиморфизм: Тренировочный план с подсчетом сожженных калорий.
Интерфейс: EquipmentRequired (метод getGearList()).
17. Кибербезопасность (NetworkCredential)
Иерархия: NetworkCredential  SSHKey, PasswordToken.
Абстракция: Метод isExpired().
Полиморфизм: Проверка валидности списка доступов сервера.
Интерфейс: Revocable (метод revokeAccess()).
18. Сельское хозяйство (CropField)
Иерархия: CropField  WheatField, CornField.
Абстракция: Метод yieldPerHectare().
Полиморфизм: Учет урожая со всего фермерского хозяйства.
Интерфейс: Irrigated (метод waterVolumeNeeded()).
19. Event-менеджмент (ConcertTicket)
Иерархия: ConcertTicket  VIPTicket, StandardTicket.
Абстракция: Метод getEntryGate().
Полиморфизм: Очередь на вход: определение ворот для каждого билета.
Интерфейс: Refundable (метод requestRefund()).
20. Экология (RecyclableWaste)
Иерархия: RecyclableWaste  PlasticBottle, GlassJar.
Абстракция: Метод getProcessingFee().
Полиморфизм: Сортировочная линия: расчет стоимости переработки кучи мусора.
Интерфейс: BioDegradable (метод decompositionDays()).