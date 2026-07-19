# Лабораторная работа №1: Настройка окружения. Первый класс

## Тема

**Установка JDK, IntelliJ IDEA (Community Edition), Git. Создание класса с инкапсуляцией.**

## Задачи

1. Установить среду выполнения JDK 17 или выше и интегрированную среду разработки IntelliJ IDEA Community Edition.
2. Инициализировать пустой Git-репозиторий для проекта, настроить .gitignore для игнорирования служебных файлов IDE и скомпилированного кода (*.class, /out/), выполнить первый коммит.
3. Создать проект в IntelliJ IDEA и реализовать класс Book, соблюдая принципы инкапсуляции:

    - все поля объявлены как private.
    - для каждого поля реализованы публичные методы доступа (getter) и изменения (setter).
    - реализовать метод getDescription(), формирующий строковое представление объекта по заданному шаблону.

4. Написать код в методе main() класса для инстанцирования трех различных объектов типа Book и вывода результата их метода getDescription() в консоль.
5. Протестировать работу программы, зафиксировать промежуточное состояние через коммиты в Git.
6. Сгенерировать шаблон класса Book с помощью ИИ-инструмента (GitHub Copilot) по текстовому комментарию, сравнить полученный результат с ручным написанием по критериям читаемости, полноты методов и соответствия стандартам оформления кода.

## Стек технологий

- **Язык:** Java (версия 17+)
- **Сборка:** Встроенная система сборки IntelliJ IDEA / Maven / Gradle (опционально)
- **IDE:** JetBrains IntelliJ IDEA Community Edition
- **VCS:** Git
- **ИИ-помощник:** GitHub Copilot (или аналоги)

***

## Инструкции

### 1. Подготовка окружения

- Скачайте и установите  Oracle JDK версии 17 и выше. Проверьте установку командой java -version и javac -version.
- Скачайте и установите IntelliJ IDEA Community Edition.
- Убедитесь, что установлен Git. Проверьте командой git --version. При необходимости выполните базовую настройку пользователя:

```bash

git config --global user.name "Ваше Имя"
git config --global user.email "your_email@example.com"

```

### 2. Создание репозитория

- Создайте новую папку для проекта.
- Откройте терминал в этой папке и выполните

```bash
git init
touch .gitignore
```

- Добавьте в .gitignore следующие строки:

```text
# IntelliJ files
.idea/
*.iml
out/

# Compiled class files
*.class

# OS generated files
.DS_Store
Thumbs.db

```

- Выполните первый коммит настроек:

```bash
git add .
git commit -m "chore: initial project setup and .gitignore"

```

### 3. Проект в IDEA

- Запустите IntelliJ IDEA -> New Project -> Java.
- Выберите установленный JDK 17+.
- Создайте новый пакет (например, ru.yourname.lab1) и внутри него — класс Book.java.

### 4. Ручная реализация класса Book

- Создайте файл src/ru/yourname/lab1/Book.java со следующей структурой:

```java
package ru.yourname.lab1;

public class Book {
    private long id;
    private String title;
    private String author;
    private int year;

    // Constructors
    public Book(long id, String title, String author, int year) {
        this.id = id;
        this.title = title;
        this.author = author;
        this.year = year;
    }

    public Book() {
        // Default constructor
    }

    // Getters and Setters
    public long getId() {
        return id;
    }

    public void setId(long id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public int getYear() {
        return year;
    }

    public void setYear(int year) {
        this.year = year;
    }

    /**
     * Возвращает описание книги в формате «"Название" — Автор (Год)».
     */
    public String getDescription() {
        return "\"" + title + "\" — " + author + " (" + year + ")";
    }
}
```

### 5. Класс Main для запуска

- Создайте класс Main.java (или Lab1.java) в том же пакете:

```java
package ru.yourname.lab1;

public class Main {
    public static void main(String[] args) {
        Book book1 = new Book(1L, "Война и мир", "Толстой Л.Н.", 1869);
        Book book2 = new Book(2L, "Преступление и наказание", "Достоевский Ф.М.", 1866);
        Book book3 = new Book(3L, "Мастер и Маргарита", "Булгаков М.А.", 1967);

        System.out.println(book1.getDescription());
        System.out.println(book2.getDescription());
        System.out.println(book3.getDescription());
    }
}
```

### 6. Генерация через ИИ (Copilot)

- Внутри тела класса Book напишите сигнатуру одного из полей (например, private long id;) или специальный комментарий-триггер над пустым классом:

```java
// Generate a Java class named Book with fields: long id, String title, String author, int year. Add private access modifiers, full getter/setter pairs for each field, a parameterized constructor, and a method getDescription().
```

Нажмите Tab (или Alt+\ для альтернативных предложений)

- проведите сравнение подходов, заполнив следующую таблицу:

| Критерий          | Ручное написание | Генерация через Copilot |
| :---------------- | :--------------- | :---------------------- |
| Скорость          |                  |                         |
| Контроль типов    |                  |                         |
| Стиль кода        |                  |                         |
| Переиспользование |                  |                         |

### 7. Работа с системой контроля версий

- После завершения реализации добавьте файлы в индекс и зафиксируйте прогресс:

```bash
git add src/
git commit -m "feat: implemented Book entity and Main runner"
```

- Для сдачи лабораторной подготовьте ссылку на репозиторий (GitHub / GitLab).

***

## Ожидаемый результат

Ссылка на репозиторий (GitHub / GitLab) с настроенным .gitignore и историей коммитов. Программа должна запускаться в IntelliJ IDEA без ошибок и выводить в консоль три строки строго заданного формата. Код класса Book должен демонстрировать инкапсуляцию через модификатор private для всех полей, а также содержать публичные геттеры, сеттеры и метод getDescription(). Дополнительно оценивается результат генерации кода через Copilot с кратким сравнением ручного и автоматического подходов.

## Контрольные вопросы

1. Что такое инкапсуляция и зачем нужны модификаторы доступа private?
2. Какова разница между полем и свойством (property)?
3. Почему в строке описания используются кавычки-ёлочки (") перед названием?
4. Какой тип сборщика рекомендуется использовать для управления зависимостями в реальном проекте?
5. Зачем нужен параметризированный конструктор при наличии сеттеров?
