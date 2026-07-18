# Лабораторная работа №5: Generics: универсальные структуры данных

## Тема 

**Параметризованные классы и методы.**

## Задачи

1. Реализовать собственный обобщённый класс Pair<A, B> с полями first и second, конструктором и getter-ами.
2. Реализовать обобщённый метод swap, меняющий местами два элемента в массиве любого типа.
3. Переписать Pair так, чтобы поля допускали только наследников Comparable. Реализовать метод min() и max().
4. Создать Repository<T> — минималистичное хранилище с методами save(T), findById(long), delete(long), findAll().
5. Реализовать конкретную версию для класса Book (из Лабораторной работы №3/4) — например, InMemoryBookRepository. Использовать Map<Long, Book> внутри для хранения.
6. Сгенерировать Repository<T> и проанализировать, где код требует доработки

## Стек технологий

- **Язык:** Java (версия 17+)
- **IDE:** JetBrains IntelliJ IDEA Community Edition
- **VCS:** Git
- **ИИ-помощник:** GitHub Copilot (или аналоги)

## Инструкция по выполнению

### 1. Подготовка

Используя код Лабораторной работы №4, создайте новую ветку:

```bash
git checkout -b feature/lab5-generics
```

### 2. Выполнение 

### 3. Работа с системой контроля версий

- Зафиксируйте изменения:

```bash
git add src/
git commit -m "feat(lab5): implemented generics, bounded types and Repository pattern"
```

## Ожидаемый результат

Ссылка на репозиторий. Программа демонстрирует работу Pair<String, Integer>, успешную перестановку элементов в массиве строк через swap, корректный поиск минимума/максимума в паре чисел и базовый функционал сохранения/поиска книг в репозитории без жесткой привязки к классу Book в самом интерфейсе репозитория.

## Контрольные вопросы

1. Почему в сигнатуре метода swap используется <T> перед void, а не после?
2. Что произойдет при попытке создать new  ComparablePair<Object>(obj1, obj2)? Почему?
3. В чем преимущество использования Function<T, Long> в конструкторе Repository вместо того, чтобы требовать от всех сущностей реализации общего интерфейса Identifiable?
4. Какие проблемы многопоточности (thread-safety) возникнут в вашем InMemoryRepository при одновременном вызове save из разных потоков?

## Критерии оценки

Оценка лабораторной работы производится по следующим критериям: корректность переопределения методов equals() и hashCode() строго по полю id, что подтверждается отсутствием дубликатов в HashSet; успешное выполнение сортировки списка (List) по году издания через Collections.sort и по автору через Comparator, а также фильтрация данных; правильное формирование структуры Map<String, List<Book>> для группировки книг с выводом количества изданий каждого автора; грамотное применение Stream API (цепочки filter(), map(), groupingBy()) для решения тех же задач функциональным стилем; результаты бенчмарка производительности поиска в ArrayList и HashSet на 100 000 элементах. Также оцениваются чистота кода (осмысленные имена переменных), наличие осмысленных коммитов в Git и умение аргументировать выбор штатного генератора IDEA вместо Copilot для создания методов сравнения.

## Варианты

1. Банковское дело (BankAccount)
Класс: Repository<BankAccount> с использованием Map<Long, BankAccount>.
Stream: Подсчет общей суммы процентов: .mapToDouble(BankAccount::calculateInterest).sum().
2. Автомобильный транспорт (Car)
Класс: Pair<String, Double> (Модель и расход топлива).
Set: HashSet<Car> для хранения уникальных VIN-номеров.
Stream: Фильтрация внедорожников: .filter(Car::isOffroadCapable).
3. Электронная коммерция (Product)
Класс: Обобщенный Box<T extends Product> для корзины.
Set: Удаление дубликатов товаров из List<Product> через new HashSet<>(list).
Stream: Расчет цены: .peek(p -> p.applyPromoCode()).map(Product::getPrice).sum().
4. Социальные сети (UserProfile)
Класс: ComparablePair<UserProfile> для сравнения пользователей по лимиту загрузки.
Set: Set<String> логинов для проверки уникальности при регистрации.
Stream: Профили с рекламой: .filter(UserProfile::isAdvertisable).
5. Геолокация (GeoPoint)
Класс: ArrayList<GeoPoint> как путь.
Set: Set<GeoPoint> для исключения повторных координат.
Stream: Поиск максимума: .max(Comparator.comparingDouble(GeoPoint::getAltitude)).
6. Университет (Student)
Класс: Универсальный статический метод <T extends Comparable<T>> T findMax(List<T> list).
Set: Set<Integer> номеров зачеток.
Stream: Студенты в общежитии: .filter(s -> s instanceof DormitoryResident).
7. Видеоигры (VideoGame)
Класс: Pair<VideoGame, SystemRequirements>.
Set: Проверка наличия игры перед покупкой через library.contains(game).
Stream: Игры с мультиплеером: .filter(g -> g instanceof Multiplayer).
8. Медицина (PatientCard)
Класс: Repository<PatientCard>.
Set: Set<Long> СНИЛС/ID пациентов.
Stream: Пациенты со страховкой: .filter(Insured.class::isInstance).
9. Библиотечное дело (LibraryItem)
Класс: ComparablePair<LocalDate> для поиска самой ранней даты возврата.
Set: Set<Reservable> забронированных книг.
Stream: Просроченные издания: .filter(i -> i.getReturnDueDate().isBefore(LocalDate.now())).
10. Космонавтика (Spacecraft)
Класс: Pair<Spacecraft, Double> (Корабль и топливо).
Set: Реестр ID запущенных аппаратов.
Stream: Вызов метода у зондов: .filter(d -> d instanceof DeepSpaceProbe).forEach(DeepSpaceProbe::transmitSignal).
11. Умный дом (SmartDevice)
Класс: Pair<String, SmartDevice> (Комната и устройство).
Set: Set<String> IP-адресов для обнаружения конфликтов.
Stream: Устройства на батарейках: .filter(BatteryPowered.class::isInstance).
12. Ресторанный бизнес (MenuItem)
Класс: Box<MenuItem> заказа.
Set: Set<String> ингредиентов блюда.
Stream: Острые блюда: .filter(Spicy::isGlutenFree) (опечатка в примере выше, должно быть Spicy или проверка остроты).
13. Музыка (MusicTrack)
Класс: Repository<MusicTrack>.
Set: Плейлист без дублей (LinkedHashSet для сохранения порядка).
Stream: Скрытие треков: .filter(t -> !t.hasParentalWarning()).
14. Управление проектами (Task)
Класс: Pair<Task, Employee> (Задача и исполнитель).
Set: Список заблокированных задач (Blocker).
Stream: Оценка времени спринта: .mapToInt(Task::estimateHours).sum().
15. Зоопарк (Animal)
Класс: Class<? extends Animal> как параметр фабрики.
Set: Учет чипированных животных.
Stream: Кормление млекопитающих: .filter(Mammal.class::isInstance).forEach(a -> a.feed(food)).
16. Фитнес (Exercise)
Класс: Pair<Exercise, Integer> (Упражнение и подходы).
Set: Доступные упражнения дня.
Stream: Суммарный вес: .mapToDouble(e -> e.caloriesBurnedPerHour() * reps).sum().
17. Кибербезопасность (NetworkCredential)
Класс: Repository<NetworkCredential>.
Set: Отозванные токены.
Stream: Очистка: .filter(NetworkCredential::isExpired).collect(Collectors.toList()).
18. Сельское хозяйство (CropField)
Класс: Pair<CropField, Double> (Поле и урожайность).
Set: Карта засеянных участков.
Stream: Потребность в воде: .mapToDouble(Irrigated::waterVolumeNeeded).sum().
19. Event-менеджмент (ConcertTicket)
Класс: ComparablePair<Double> (Цена билета).
Set: Возвращенные билеты (Refundable).
Stream: VIP-проход: .filter(VIPTicket.class::isInstance).
20. Экология (RecyclableWaste)
Класс: Box<RecyclableWaste> мусоровоза.
Set: Типы материалов в баке.
Stream: Баллы: .mapToInt(RecyclableWaste::getEcoPoints).sum().