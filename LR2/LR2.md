# Лабораторная работа №2
*Выполняли студенты группы РИ-571227*

*Иванов Роман*

*Поветина Виолетта*

*Худякова Елена*


Цель работы: Поиск и устранение SQL Injection.

**Задание**

В папке lab2 находится nodejs уязвимое приложение. Необходимо развернуть его, найти SQL инъекцию и исправить. Модифицированное приложение загрузить в свой репозиторий GitHub.
Для выполнения лабораторной потребуется проделать следующие шаги:

### 1. Установить PostgreSQL сервер любой версии
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 001](https://user-images.githubusercontent.com/87654857/147637198-76e4fe75-43a4-41c5-a9b9-16e66ead1e1d.png)

---
### 2. Создать БД lib
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 002](https://user-images.githubusercontent.com/87654857/147637232-c7a80b6c-6369-45a4-bf34-f420dc7b5047.png)
---

### 3. Применить к ней скрипты из папки db (либо создать объекты и вставить данные в таблицы руками). Скрипты выполнять в порядке указанном в имени файла.
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 003](https://user-images.githubusercontent.com/87654857/147637250-e2038a53-13cd-4602-8dff-c367d9d53a03.png)
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 004](https://user-images.githubusercontent.com/87654857/147637258-3fa39e0b-7b75-433b-b264-4575becc5041.png)
---

3.1 Восстановить данные из файла data.sql

При применении скрипта data.sql из-за присутствия кириллицы названия сбились, исправили их через pgAdmin:
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 005](https://user-images.githubusercontent.com/87654857/147637279-1eb741eb-4013-4cc9-ae3c-ab53e3f808ab.png)
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 006](https://user-images.githubusercontent.com/87654857/147637285-8556bd26-432e-4d47-91b6-1047de75577b.png)


### 4. Установить nodejs версии 14.
### 5. Перейти в папку lab2 и выполнить в ней команду npm install.
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 007](https://user-images.githubusercontent.com/87654857/147637317-3d6be078-0b50-4e4c-8c34-a3f1aca6cc47.png)
---

### 6. Запустить сайт через Visual Studio Code или через команду npm start.
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 008](https://user-images.githubusercontent.com/87654857/147637335-5c248fd8-db7c-46ca-a103-00ba88681a64.png)
---

### 7. Войти на сайт и увидеть список книг и авторов
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 009](https://user-images.githubusercontent.com/87654857/147637349-0a67ef45-0b27-4089-b0d5-0759d5521a66.png)
---
### 8. Обнаружить sql инъекцию
Введем конструкцию, которая заведомо вызовет ошибку. Например, символ «b». Получаем следующую ошибку:
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 010](https://user-images.githubusercontent.com/87654857/147637375-ed83b928-f38f-43f6-9d9a-d8c03470ecca.png)

---
### 9. Написать отчёт с описанием найденной уязвимости и примерами её эксплуатации

9.1. Обход установленного фильтра 

9.2. Получение данных из другой таблицы 

9.3. Похищение пароля пользователя

Для начала опробуем классическую конструкцию: OR 1=1 -- . Подставим в URL: 3000’ OR 1 = 1 – 
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 011](https://user-images.githubusercontent.com/87654857/147637413-15d58ef7-6a8b-48c3-bb8c-ab205e9c7c43.png)


Далее с помощью UNION - запроса получим названия таблиц и колонок базы данных. Составим запрос такого вида: 

UNION SELECT 0, table\_name, column\_name FROM information\_schema.columns – 
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 012](https://user-images.githubusercontent.com/87654857/147637426-02f1dba4-7f93-4228-8330-9838d21a31f0.png)


Теперь получим данные из таблицы book запросом:

` `UNION SELECT id, name, null FROM book -- 
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 013](https://user-images.githubusercontent.com/87654857/147637440-77cbfebf-fb7e-4338-aeaa-9ec7b32d0da8.png)



Чтобы похитить пароль, для начала посмотрим, из чего состоит таблица users аналогичным запросом:

UNION SELECT 0, column\_name, data\_type FROM information\_schema.columns WHERE table\_name = ‘users’ – 
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 014](https://user-images.githubusercontent.com/87654857/147637470-d7ed76e8-4a30-4642-baad-e3eac53ea4e1.png)



Видим, что таблица имеет две колонки с текстовыми данными – имя и пароль.

Чтобы найти пароль, используем запрос:

UNION SELECT 0, name, pass FROM public.users – 
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 015](https://user-images.githubusercontent.com/87654857/147637486-59c938f1-4fcc-43e7-830a-e76cc86d363c.png)
---

### 10. Исправить уязвимость. 10.1 В отчёте привести пример того, что уязвимости больше не эксплуатируются

Добавим корректную валидацию для вводимых данных:
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 016](https://user-images.githubusercontent.com/87654857/147637492-93c1efe4-a86c-450f-b720-4bc7e041fded.png)



Теперь при попытке эксплуатации SQL Injection наблюдаем следующую ошибку:
![Aspose Words 89a7bdb0-d0e2-4305-a48a-63cdff6a1364 017](https://user-images.githubusercontent.com/87654857/147637499-073911bd-29ac-4ef5-b1a2-3ef45f941872.png)








