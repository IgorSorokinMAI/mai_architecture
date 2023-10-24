# Компонентная архитектура
<!-- Состав и взаимосвязи компонентов системы между собой и внешними системами с указанием протоколов, ключевые технологии, используемые для реализации компонентов.
Диаграмма контейнеров C4 и текстовое описание. 
-->
## Компонентная диаграмма

```plantuml
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

AddElementTag("microService", $shape=EightSidedShape(), $bgColor="CornflowerBlue", $fontColor="white", $legendText="microservice")
AddElementTag("storage", $shape=RoundedBoxShape(), $bgColor="lightSkyBlue", $fontColor="white")

Person(admin, "Администратор")
Person(moderator, "Модератор")
Person(user, "Пользователь")

System_Boundary(conference_site, "Сайт блогов") {
   Container(service_front, "Лицевая сторона сервиса", "C++/HTML", "Отображаемое пользователю лицо сервиса доставок, взаимодествует с микросервисами", $tags = "microService")
   Container(client_service, "Сервис авторизации", "C++", "Сервис управления пользователями", $tags = "microService")    
   Container(package_service, "Сервис посылок", "C++", "Сервис управления посылками", $tags = "microService") 
   Container(delivery_service, "Сервис доставок", "C++", "Сервис управления доставками", $tags = "microService")

   ContainerDb(db, "База данных", "MySQL", "Хранение данных о посылках, доставках и пользователях", $tags = "storage")
   
}

Rel(user, service_front, "Регистрация, просмотр информации о посылках и доставках их получение и создание")

Rel(service_front, client_service, "Работа с пользователями", "localhost/person")
Rel(client_service, db, "INSERT/SELECT/UPDATE", "SQL")

Rel(service_front, package_service, "Работа с посылками", "localhost/pres")
Rel(package_service, db, "INSERT/SELECT/UPDATE", "SQL")

Rel(service_front, delivery_service, "Работа с доставками", "localhost/conf")
Rel(delivery_service, db, "INSERT/SELECT/UPDATE", "SQL")

@enduml
```
## Список компонентов  

### Сервис авторизации
**API**:
-	Создание нового пользователя
      - входные параметры: login, пароль, имя, фамилия, email, пол
      - выходные параметры: отсутствуют
-	Поиск пользователя по логину
     - входные параметры:  login
     - выходные параметры: имя, фамилия, email, пол
-	Поиск пользователя по маске имени и фамилии
     - входные параметры: маска фамилии, маска имени
     - выходные параметры: login, имя, фамилия, email, пол

### Сервис посылок
**API**:
- Создание посылки
  - Входные параметры: название посылки, категория, владелец и дата создания
  - Выходыне параметры: id посылки
- Получение списка всех посылок
  - Входные параметры: отсутствуют
  - Выходные параметры: массив с посылками, где для каждой указаны её id, название, категория, владелец и дата создания

### Сервис доставок
**API**:
- Создание доставки
  - Входные параметры: владелец, категория, дата отправления
  - Выходные параметры: id доставки
- Получение списка посылок в доставке
  - Входные параметры: id доставки
  - Выходные параметры: массив с доставками (id, категория, владелец, дата создания)
- Изменение доставки
  - Входные параметры: id доставки, категория, владелец, дата создания
  - Выходные параметры: отсутствуют
