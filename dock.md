## Правильно настроен Maven-проект, тесты проходят
  
  Репозиторий должен быть папкой вашего мавен-проекта. Обратите внимание, что репозиторием не должна быть папка в которой лежит папка мавен-проекта, он сам должен быть папкой проекта. В нём должны быть соответствующие файлы и папки - `pom.xml`, `src` и др.
  
  Не забудьте создать .gitignore-файл в корне проекта и добавить туда в игнорирование автогенерируемую папку `target`.
  
  Общая схема вашего `pom.xml`-файла:
  
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>ru.netology</groupId>
    <artifactId>НАЗВАНИЕ-ВАШЕГО-ПРОЕКТА-БЕЗ-ПРОБЕЛОВ</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>


    <dependencies>
        <dependency>
            ...
        </dependency>
        ...
    </dependencies>


    <build>
        <plugins>
            <plugin>
              ...
            </plugin>

            <plugin>
              ...
              <executions>
                <execution>
                  ...
                </execution>
                ...
              </executions>
            </plugin>
            ...
        </plugins>
    </build>

</project>
  ```
  
  #### JUnit
  Обратите внимание что у артефакта нет `-api` на конце. Если у вас автоматически добавилась зависимость вида `<artifactId>junit-jupiter-api</artifactId>`, то лучше поменять артефакт на тот что ниже, иначе будут сюрпризы в работе.

  ```xml
          <dependency>
              <groupId>org.junit.jupiter</groupId>
              <artifactId>junit-jupiter</artifactId>
              <version>5.7.0</version>
              <scope>test</scope>
          </dependency>
  ```

  #### Surefire
  Без этого плагина тесты могут мавеном не запускаться, хоть в идее через кнопки они и будут проходить. Чтобы лишний раз убедиться, что всё работает, нажмите `Ctrl+Ctrl` и затем `mvn clean test`.
  
  ```xml
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-surefire-plugin</artifactId>
                  <version>2.22.2</version>
                  <configuration>
                      <failIfNoTests>true</failIfNoTests>
                  </configuration>
              </plugin>
  ```
  
  ---
  


## Отформатирован код
  
  Кроме правил, нарушение которых приводит к ошибкам компиляции, есть ещё и [правила форматирования кода](https://google.github.io/styleguide/javaguide.html), соблюдение которых обязательно при напиании программ.
  
  С большинством проблем может справиться автоформатирование в идее. Для этого выберите `Code -> Reformat code` в меню или используйте горячие сочетания клавиш (в меню будет показано актуальное сочетание для вашей операционной системы). Так, идея поправит неправильные отступы, пробелы и некоторые другие ошибки. Следите, чтобы у `if-else`, `for`, `while` всегда были `{}`.
  
  Проблемы с именованием сущностей нужно решать самим. Так, все ячейки кроме `final`-констант и методы должны писаться с маленькой буквы [камелкейсом](https://ru.wikipedia.org/wiki/CamelCase) с **маленькой** буквы, а классы и интерфейсы - камелкейсом с **большой** буквы.
  
  Мы вам настоятельно советуем всегда держать код в отформатированном виде во время разработки, со временем глаз привыкнет и вы почувствуете насколько это облегчает поиск ошибок в коде и его анализ. В любом случае, перед отправкой кода на проверку его обязательно нужно отформатировать, иначе он может быть отправлен на доработку без более глубокой проверки на этой итерации.


## Настроен Github CI с verify-сборкой Maven и JaCoCo в режиме генерации отчётов с покрытием на 100% по бранчам [:warning:: в этом задании тестов нет, CI также можно не настраивать]</summary>
  
  #### CI
  После связывания локального репозитория с удалённым и первого пуша в заготовки проекта, время настроить CI на основе Github Actions. Шаблон вашего maven.yml должен выглядеть вот так, убедитесь что всё совпадает с вашим шаблоном (например, что вы указали фазу `verify`, а не `package`):
  ```yml
  name: Java CI with Maven

  on: [push, pull_request]

  jobs:
    build:

      runs-on: ubuntu-latest

      steps:
      - uses: actions/checkout@v2
      - name: Set up JDK 11
        uses: actions/setup-java@v2
        with:
          java-version: '11'
          distribution: 'adopt'
      - name: Build with Maven
        run: mvn -B -e verify
  ```
  
  #### JaCoCo

  ```xml
              <plugin>
                  <groupId>org.jacoco</groupId>
                  <artifactId>jacoco-maven-plugin</artifactId>
                  <version>0.8.5</version>
                  ...
  ```

  Инициализация:
  ```xml
                      <execution>
                          <id>prepare-agent</id>
                          <goals>
                              <goal>prepare-agent</goal>
                          </goals>
                      </execution>
  ```

  В режиме генерации отчётов:
  ```xml
                      <execution>
                          <id>report</id>
                          <phase>verify</phase>
                          <goals>
                              <goal>report</goal>
                          </goals>
                      </execution>
  ```

  В режиме проверки и обрушения сборки по уровню покрытия:
  ```xml
                      <execution>
                          <id>check</id>
                          <goals>
                              <goal>check</goal>
                          </goals>
                          <configuration>
                              <rules>
                                  <rule>
                                      <limits>
                                          <limit>
                                              <counter>LINE</counter>
                                              <value>COVEREDRATIO</value>
                                              <minimum>100%</minimum>
                                          </limit>
                                      </limits>
                                  </rule>
                              </rules>
                          </configuration>
                      </execution>
  ```

[Инструкции по установке Postman, JMeter и BlazeMeter и запуску Terminal](https://github.com/netology-code/iqa-homeworks/blob/iqa-12/Instruction.md)
Есть специальный инструмент, при помощи которого можно проверить нагрузку, — [JMeter](https://jmeter.apache.org/).
Обратите внимание, что Java мы ставим строго по инструкции [отсюда](https://github.com/netology-code/javaqa-homeworks/blob/master/intro/openjdk11-manual.md) для того, чтобы далее в обучении не было конфликтов версий.



## Задание 1. API

Вспомните [условие задачи](https://github.com/netology-code/iqa-homeworks/tree/iqa-12/2.4#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-2) с курса по ручному тестированию, 
где вы общались с сервером. На сервер при отправке [формы](http://zayavka-na-kartu-3.sdew.ru/) отправляется запрос в виде JSON - текстового формата описания 
объекта в JavaScript-стиле, выглядит он так:

```json
{
  "birthday": "13.06.1999",
  "name": "Иван",
  "passport": "4444 № 44444444",
  "patronymic": "Иванович",
  "phone": "+7 (999)-999-99-99",
  "surname": "Иванов"
}
```

Усложним и добавим в неё ещё информацию, согласен ли пользователь получать информацию о скидках в виде sms: `"subscription": true`. Также предлагается чтобы дата рождения была не полем-строкой, а тоже была объектом: `"birthday": {"day" : 13, "month": 6, "year": 1999}`. В итоге объект в запросе будет выглядеть, например, так:

```json
{
  "birthday": {
    "day": 13,
    "month": 6,
    "year": 1999
  },
  "name": "Иван",
  "passport": "4444 № 44444444",
  "patronymic": "Иванович",
  "phone": "+7 (999)-999-99-99",
  "surname": "Иванов",
  "subscription": true
}
```

Вашей задачей будет разработать классы, описывающие объекты запроса анкеты на сервер и продемонстрировать их работу в `Main`.

Создайте класс `Post`, описывающий объект запроса на сервер и заполните его полями (сперва можно без `birthday`, тк для него придётся создавать вспомогательный класс), выбрав подходящие типы:
```java
public class Post {
  public String name; // поле для имени
  // добавьте другие поля
}
```

Создадим класс `Main`, в `main` которого создадим и заполним наш объект:
```java
public class Main {
    public static void main(String[] args) {
        Post post = new Post();
        post.name = "Иван";
        // заполните другие поля
    }
}
```

Поставим точку останова на последней строчке, можно прямо на закрывающей фигурной скобке `main`, и запустим отладчик, чтобы посмотреть что у нас будет лежать в переменной с объектом:
![My First Board - Frame 14](https://user-images.githubusercontent.com/53707586/151747800-bbd79552-0f62-4105-90fa-b0d7df7dbce6.jpg)


Если вы сделали всё правильно, то в отладчике будет виден созданный и заполненный объект типа `Post` по схеме данных (полей), указанных вами в классе `Post`.

Осталось добавить поле `birthday`. Значение в этом поле должно быть объектом, для которого также нужно будет создать свой класс. Создайте класс `FormDate` для описания схемы объекта в поле `birthday`:
```java
public class FormDate {
  // добавьте три необходимых поля
}
```

Теперь в классе `Post` добавьте поле `birthday` с типом `FormDate`. После чего добавьте заполнение этого поля в `main`:
```java
  post.birthday = new FormDate();
  post.birthday.day = 13;
  // заполните другие поля даты рождения
```

Запустите отладчик снова и убедитесь, что всё заполнено как надо и соответствует JSON-описанию объекта выше.

**Итого:** отправьте на проверку ссылку гитхаб-репозиторий с вашим проектом. 
