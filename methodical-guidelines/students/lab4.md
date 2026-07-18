# Лабораторная работа №4: Коллекции: List, Set, Map

## Тема 

**Работа с основными коллекциями. Сортировка. Итерация.**

## Задачи

1. Создать List<Book> с 10 книгами. Выполнить: сортировку по году (Collections.sort + Comparator), сортировку по автору, фильтрацию (книги после 2000 года).
2. Создать Set<Book> — проверить, что дубликаты не добавляются (потребуется переопределить equals() и hashCode()).
3. Создать Map<String, List<Book>> — группировка книг по автору. Вывести автора и количество его книг.
4. Использовать Stream API: filter(), map(), collect(Collectors.groupingBy(...)), sorted().
5. Замерить время выполнения операции поиска в ArrayList и HashSet для 100 000 элементов.
6. Cгенерировать equals()/hashCode() для Book через Alt+Insert в IDEA (встроенный генератор) и  показать разницу с Copilot-генерацией, которая не всегда корректна.

## Стек технологий

- **Язык:** Java (версия 17+)
- **IDE:** JetBrains IntelliJ IDEA Community Edition
- **VCS:** Git
- **ИИ-помощник:** GitHub Copilot (или аналоги)

## Инструкция по выполнению

### 1. Подготовка

Используя код Лабораторной работы №3, создайте новую ветку:

```bash
git checkout -b feature/lab4-collections
```

### 2. Подготовка (Equals & HashCode)

В классе Book.java обязательно должны быть переопределены эти методы. Без них HashSet будет считать две разные книги с одинаковым ID как разные объекты.

IDEA Generator (Рекомендуемый способ): Установите курсор внутри класса, нажмите Alt+Insert  выберите equals() and hashCode()  выберите поле id.
Copilot: Напишите // generate equals and hashcode based on id. Проверьте, не добавила ли модель лишние поля (например, строки), которые могут замедлить работу или вызвать ошибки при сравнении незаполненных полей.

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (!(o instanceof Book)) return false;
    Book book = (Book) o;
    return id == book.id;
}

@Override
public int hashCode() {
    return Objects.hash(id);
}
```

### 3. Реализация Main.java

```java
package ru.yourname.lab4;

import java.time.LocalDate;
import java.util.*;
import java.util.stream.Collectors;

public class Main {

    public static void main(String[] args) {
        
        // --- 1. Создание данных ---
        List<Book> library = new ArrayList<>();
        for (int i = 1; i <= 10; i++) {
            library.add(new Book((long)i, "Title " + i, getRandomAuthor(), 1980 + i * 5));
        }
        
        System.out.println("=== РАБОТА С LIST ===");
        
        // --- 2. Сортировка List ---
        Collections.sort(library, Comparator.comparingInt(Book::getYear));
        System.out.println("\nСортировка по году:");
        library.forEach(System.out::println);

        library.sort(Comparator.comparing(Book::getAuthor));
        System.out.println("\nСортировка по автору:");
        library.forEach(System.out::println);

        // --- 3. Фильтрация ---
        List<Book> modernBooks = library.stream()
                .filter(b -> b.getYear() > 2000)
                .toList();
        System.out.println("\nКниги после 2000 года: " + modernBooks.size());

        // --- 4. Работа с SET (Дубликаты) ---
        Set<Book> uniqueBooks = new HashSet<>(library);
        // Добавим заведомо существующую книгу
        uniqueBooks.add(library.get(0)); 
        System.out.println("\nРазмер списка: " + library.size()); // 10
        System.out.println("Размер множества (дубликат отсечен): " + uniqueBooks.size()); // 10

        // --- 5. Группировка MAP ---
        Map<String, List<Book>> byAuthor = new HashMap<>();
        for (Book b : library) {
            byAuthor.computeIfAbsent(b.getAuthor(), k -> new ArrayList<>()).add(b);
        }
        
        System.out.println("\n=== ГРУППИРОВКА ПО АВТОРУ (MAP) ===");
        byAuthor.forEach((author, books) -> 
            System.out.println(author + ": " + books.size() + " шт."));

        // --- 6. STREAM API (аналог группировки) ---
        System.out.println("\n=== STREAM GROUPINGBY ===");
        Map<String, Long> countByAuthor = library.stream()
                .collect(Collectors.groupingBy(
                        Book::getAuthor,
                        Collectors.counting()));
        countByAuthor.forEach((a, c) -> System.out.println(a + " -> " + c));

        // --- 7. БЕНЧМАРК производительности ---
        performanceTest();
    }

    private static String getRandomAuthor() {
        String[] authors = {"Толстой", "Булгаков", "Стругацкие", "Ремарк", "Оруэлл"};
        return authors[new Random().nextInt(authors.length)];
    }

    private static void performanceTest() {
        final int ELEMENTS = 100_000;
        final int SEARCHES = 10_000;
        
        List<Book> bigList = new ArrayList<>();
        Set<Book> bigSet = new HashSet<>();
        
        // Заполнение данными
        for (int i = 0; i < ELEMENTS; i++) {
            Book b = new Book((long) i, "Item" + i, "AuthorX", 2020);
            bigList.add(b);
            bigSet.add(b);
        }
        
        // Поиск существующего элемента
        Book searchTarget = bigList.get(ELEMENTS / 2); 
        
        long start = System.nanoTime();
        for (int i = 0; i < SEARCHES; i++) {
            bigList.contains(searchTarget);
        }
        long listTime = System.nanoTime() - start;
        
        start = System.nanoTime();
        for (int i = 0; i < SEARCHES; i++) {
            bigSet.contains(searchTarget);
        }
        long setTime = System.nanoTime() - start;

        System.out.println("\n=== ПРОИЗВОДИТЕЛЬНОСТЬ (поиск 10k раз) ===");
        System.out.printf("ArrayList (O(n)): %.2f мс%n", listTime / 1_000_000.0);
        System.out.printf("HashSet (O(1)): %.2f мс%n", setTime / 1_000_000.0);
    }
}
```

### 4. Работа с системой контроля версий

- Зафиксируйте изменения:

```bash
git add src/
git commit -m "feat(lab4): implemented collections, streams and performance benchmark"

```

## Ожидаемый результат

Программа должна вывести отсортированные списки, показать, что Set игнорирует дубликаты благодаря equals/hashCode, отобразить карту группировок и продемонстрировать колоссальную разницу в скорости поиска между ArrayList (линейный поиск) и HashSet (поиск по хэшу).

## Контрольные вопросы

1. Что произойдет, если добавить объект класса Book (без переопределенных методов) в HashSet, а затем попытаться найти его там через другой экземпляр с тем же полем id?
2. Почему для бенчмарка на 100 000 элементов время выполнения ArrayList растет линейно, а у HashSet остается практически неизменным?
3. Как поведет себя сортировка по полю author типа String, если использовать Collections.sort(list) без передачи Comparator? Почему это может быть не тем результатом, который мы ожидаем от бизнес-логики?
4. Какой класс из пакета java.util.concurrent стоит использовать вместо HashMap, если один поток будет записывать данные, а другой — читать их одновременно без внешней синхронизации?
5. Чем отличается интерфейс Comparable от функционального интерфейса Comparator?

## Критерии оценки

Оценка лабораторной работы производится по следующим критериям: корректность переопределения методов equals() и hashCode() строго по полю id, что подтверждается отсутствием дубликатов в HashSet; успешное выполнение сортировки списка (List) по году издания через Collections.sort и по автору через Comparator, а также фильтрация данных; правильное формирование структуры Map<String, List<Book>> для группировки книг с выводом количества изданий каждого автора; грамотное применение Stream API (цепочки filter(), map(), groupingBy()) для решения тех же задач функциональным стилем; результаты бенчмарка производительности поиска в ArrayList и HashSet на 100 000 элементах. Также оцениваются чистота кода (осмысленные имена переменных), наличие осмысленных коммитов в Git и умение аргументировать выбор штатного генератора IDEA вместо Copilot для создания методов сравнения.

## Варианты

1. Банковское дело (BankAccount)
List: ArrayList счетов, сортировка по балансу.
Set: Поиск дубликатов номеров счетов.
Map: Группировка счетов по типу (Credit, Savings).
Stream: Подсчет общей суммы процентов по всем счетам.
2. Автомобильный транспорт (Car)
List: Список авто, сортировка по расходу топлива.
Set: Уникальные VIN-номера автомобилей.
Map: Группировка машин по бренду производителя.
Stream: Фильтрация внедорожников (OffroadCapable == true).
3. Электронная коммерция (Product)
List: Корзина покупок, сортировка по цене.
Set: Удаление одинаковых товаров из корзины.
Map: Группировка товаров по категории доставки.
Stream: Расчет итоговой цены с учетом промокодов (applyPromoCode).
4. Социальные сети (UserProfile)
List: Лента пользователей, сортировка по лимиту загрузки.
Set: Проверка уникальности логинов при регистрации.
Map: Группировка профилей по статусу (Verified, Business).
Stream: Вывод только тех профилей, где включена реклама.
5. Геолокация (GeoPoint)
List: Точки маршрута, сортировка по широте.
Set: Исключение повторных посещений одних и тех же координат.
Map: Группировка точек по часовому поясу.
Stream: Поиск самой высокой точки (getAltitude()) в списке.
6. Университет (Student)
List: Рейтинг студентов, сортировка по размеру стипендии.
Set: Учет уникальных номеров зачеток.
Map: Группировка по уровню обучения (Undergraduate, Graduate).
Stream: Формирование списка студентов, проживающих в общежитии.
7. Видеоигры (VideoGame)
List: Библиотека игр, сортировка по требованиям к ОЗУ.
Set: Проверка наличия игры в библиотеке перед покупкой.
Map: Группировка игр по жанру/издателю.
Stream: Выборка игр, поддерживающих мультиплеер.
8. Медицина (PatientCard)
List: Очередь на прием, сортировка по стоимости лечения.
Set: Проверка пациента на наличие дублей в базе по СНИЛС/ID.
Map: Разделение пациентов на стационаре и амбулаторных.
Stream: Поиск пациентов с определенной страховкой.
9. Библиотечное дело (LibraryItem)
List: Выданные книги, сортировка по дате возврата.
Set: Реестр забронированных книг (Reservable).
Map: Группировка фонда по типу носителя (BookCopy, MagazineIssue).
Stream: Поиск всех просроченных изданий.
10. Космонавтика (Spacecraft)
List: Корабли на орбите, сортировка по запасу топлива.
Set: Реестр запущенных аппаратов (без дублей ID).
Map: Группировка по миссии (OrbitalStation, Lander).
Stream: Отправка сигнала (transmitSignal()) всем зондам дальнего космоса.
11. Умный дом (SmartDevice)
List: Устройства в доме, сортировка по MAC-адресу.
Set: Обнаружение конфликтов IP-адресов.
Map: Группировка устройств по комнате.
Stream: Вывод списка девайсов, требующих замены батарейки.
12. Ресторанный бизнес (MenuItem)
List: Меню дня, сортировка по калорийности.
Set: Составление набора ингредиентов без повторений.
Map: Группировка блюд по отделу кухни (Beverage, MainCourse).
Stream: Печать меню только для острых блюд (Spicy).
13. Музыка (MusicTrack)
List: Плейлист, сортировка по размеру файла.
Set: Создание плейлиста «Избранное» без повторов треков.
Map: Группировка по типу записи (LiveRecording, StudioMix).
Stream: Скрытие треков с возрастным ограничением.
14. Управление проектами (Task)
List: Backlog задач, сортировка по часам (estimateHours).
Set: Список заблокированных задач (Blocker).
Map: Группировка спринта по типу задачи (BugFix, FeatureDevelopment).
Stream: Оценка общего времени на выполнение текущего спринта.
15. Зоопарк (Animal)
List: Животные вольера, сортировка по названию вида.
Set: Учет чипированных животных.
Map: Кормление: группировка по рациону (Mammal, Reptile).
Stream: Вызов звука (makeSound()) у всех млекопитающих.
16. Фитнес (Exercise)
List: Программа тренировки, сортировка по сложности.
Set: Набор доступных упражнений без дублей названий.
Map: Группировка по группе мышц (Cardio, StrengthTraining).
Stream: Подсчет суммарного объема поднятого веса.
17. Кибербезопасность (NetworkCredential)
List: Список доступов сервера.
Set: Ревокация (удаление) отозванных токенов.
Map: Группировка ключей по владельцу.
Stream: Автоматическая очистка просроченных паролей (isExpired()).
18. Сельское хозяйство (CropField)
List: Поля фермы, сортировка по площади.
Set: Карта засеянных участков (уникальные координаты).
Map: Группировка урожая по культуре (WheatField, CornField).
Stream: Расчет потребности в воде для орошения (Irrigated).
19. Event-менеджмент (ConcertTicket)
List: Проданные билеты, сортировка по сектору.
Set: Реестр возвращенных билетов (Refundable).
Map: Группировка очереди на вход по воротам (EntryGate).
Stream: Поиск VIP-билетов для прохода через отдельный вход.
20. Экология (RecyclableWaste)
List: Мусоровоз (партия отходов), сортировка по весу.
Set: Типы материалов в текущем баке.
Map: Сортировочная лента: группировка по материалу (PlasticBottle, GlassJar).
Stream: Расчет экологических баллов за партию мусора.
