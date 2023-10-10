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

System_Ext(web_site, "Приложение", "HTML, CSS, JavaScript, React", "Веб-интерфейс")

System_Boundary(conference_site, "Сервис поиска попутчиков") {
   'Container(web_site, "Клиентский веб-сайт", ")
   Container(client_service, "Сервис авторизации", "C++", "Сервис управления пользователями", $tags = "microService")    
   Container(post_service, "Сервис маршрутов", "C++", "Сервис управления маршрутами", $tags = "microService") 
   Container(blog_service, "Сервис поездок", "C++", "Сервис управления поездками", $tags = "microService")   
   ContainerDb(db, "База данных", "MySQL", "Хранение данных о блогах, постах и пользователях", $tags = "storage")
   
}

Rel(admin, web_site, "Просмотр, добавление и редактирование информации о пользователях и их маршрутах")
Rel(moderator, web_site, "Модерация контента и пользователей")
Rel(user, web_site, "Регистрация, просмотр и создание маршрутов, подключение к существующим маршрутам")

Rel(web_site, client_service, "Работа с пользователями", "localhost/person")
Rel(client_service, db, "INSERT/SELECT/UPDATE", "SQL")

Rel(web_site, post_service, "Работа с маршрутами", "localhost/pres")
Rel(post_service, db, "INSERT/SELECT/UPDATE", "SQL")

Rel(web_site, blog_service, "Работа с поездками", "localhost/conf")
Rel(blog_service, db, "INSERT/SELECT/UPDATE", "SQL")

@enduml
```
## Список компонентов  

### Сервис авторизации
**API**:
-	Создание нового пользователя
      - входные параметры: login, пароль, имя, фамилия, email, обращение (г-н/г-жа)
      - выходные параметры: отсутствуют
-	Поиск пользователя по логину
     - входные параметры:  login
     - выходные параметры: имя, фамилия, email, обращение (г-н/г-жа)
-	Поиск пользователя по маске имени и фамилии
     - входные параметры: маска фамилии, маска имени
     - выходные параметры: login, имя, фамилия, email, обращение (г-н/г-жа)

### Сервис маршрутов
**API**:
- Создание маршрута
  - Входные параметры: название маршрута, начальная точка, конечная точка, аннотация, автор и дата создания
  - Выходыне параметры: идентификатор маршрута
- Получение списка маршрутов пользователя
  - Входные параметры: маска фамилии, маска имени
  - Выходные параметры: массив с маршрутами, где для каждого указаны его идентификатор, название, аннотация, начальная и конечная точки, автор и дата создания

### Сервис поездок
**API**:
- Создание поездки
  - Входные параметры: идентификатор маршрута, автор, дата поездки, количество свободных мест, аннотация, дата создания
  - Выходные параметры: идентификатор поездки
- Подключение пользователей к поездке 
  - Входные параметры: идентификатор поездки, имя, фамилия
  - Выходные параметры: отсутствуют
- Получение информации о поездке
  - Входнае параметры: идентификатор поездки
  - Выходные парамтеры: автор, начальная точка, конечная точка, дата поездки, количество свободных мест, аннотация, дата создания



### Модель данных
```puml
@startuml

class route {
  id
  name
  starting_point
  end_point
  description
  date
  author_id
}

class User {
  id
  login
  first_name
  last_name
  email
  title
}

class trip {
  id
  author_id
  route_id
  departure_date
  available_seats
  description
  date_of_creation
  
}

class passengers {
 trip_id
 user_id

}


User <- route
trip <- route
passengers -> User
passengers -> trip

@enduml
```