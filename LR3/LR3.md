# **Лабораторная работа №3**
*Выполняли работу студенты РИ-571227*

*Иванов Роман*

*Поветина Виолетта*

*Худякова Елена*


Цель работы: Поиск и устранение XSS уязвимостей.
## **Задание**
В папке lab3 находится nodejs уязвимое приложение. Необходимо развернуть его, найти источники XSS и исправить. Модифицированное приложение загрузить в свой репозиторий GitHub.

Для выполнения лабораторной потребуется проделать следующие шаги (если вы их проделали для лабораторной 2, то повторять не нужно):

### 1. Установить PostgreSQL сервер любой версии 

### 2. Создать БД lib

### 3. Применить к ней скрипты из папки db (либо создать объекты и вставить данные в таблицы руками). Скрипты выполнять в порядке указанном в имени файла. 

3.1 Восстановить данные из файла data.sql 

### 4. Установить nodejs версии 14. 

Данные пункты выполнялись в лабораторной работе №2.

### 5. Перейти в папку lab3 и выполнить в ней команду npm install. 

### 6. Запустить сайт через Visual Studio Code или через команду npm start.

Потребовалось установить модуль «cookie-parser» для последующей работы с файлами Cookie.

![Aspose Words 9caaed46-bbb4-4406-80f1-5db4046c8311 001](https://user-images.githubusercontent.com/87654857/147643373-088fcf71-34fe-469a-bd33-64cf65bb916d.png)

---

### 7. Войти на сайт и увидеть список книг и авторов
### 8. На странице со списком книг найти 

8.1 Reflected XSS в поиске книг


*** <img src=1 href=1 onerror='javascript:alert(1)'> ***


![Aspose Words 9caaed46-bbb4-4406-80f1-5db4046c8311 002](https://user-images.githubusercontent.com/87654857/147643403-9eff70fb-02ed-4911-a3cd-d671d41bc4a4.png)


Исправление уязвимости:

Для строки поиска книг можно воспользоваться prepared statement, как в ЛР2



![Aspose Words 9caaed46-bbb4-4406-80f1-5db4046c8311 003](https://user-images.githubusercontent.com/87654857/147643430-2c1bf953-1975-4436-8704-a2026498ee9d.png)

![Aspose Words 9caaed46-bbb4-4406-80f1-5db4046c8311 004](https://user-images.githubusercontent.com/87654857/147643442-890a0974-1ff0-48a7-8a25-fa0274aef0b0.png)

---
8.2 Persisted (Stored) XSS при создании книги и отображении списка книг

***<html onclick=“alert(1)”>test</html>***

Теперь при любом клике на сайте будет выскакивать окно:


![Aspose Words 9caaed46-bbb4-4406-80f1-5db4046c8311 005](https://user-images.githubusercontent.com/87654857/147643465-795ac8fb-7f67-470b-bb5c-63b8eeba7399.png)


Команда в исходном коде:


![Aspose Words 9caaed46-bbb4-4406-80f1-5db4046c8311 006](https://user-images.githubusercontent.com/87654857/147643488-21384e19-9aa3-4a69-8fbe-a61fc55be902.png)


Исправление уязвимости:

При добавлении книг самый простой способ – просто заменять символы < и >, например:

***let bname = req.body.bookname.replace(‘<’, ‘(’).replace(‘>’, ‘)’)***

![Aspose Words 9caaed46-bbb4-4406-80f1-5db4046c8311 007](https://user-images.githubusercontent.com/87654857/147643505-7fd47b50-267e-450e-85ae-829ff0c04a12.png)

---

8.3 Потенциальную уязвимость через Cookie Injection

***<img src=1 onerror=‘javascript:alert(document.cookie)’/>***

Добавляем в cookie:

![Aspose Words 9caaed46-bbb4-4406-80f1-5db4046c8311 008](https://user-images.githubusercontent.com/87654857/147643520-f6e3dac6-dda7-484c-a7ef-04d8f79063ff.png)

Результат при обновлении страницы:

![Aspose Words 9caaed46-bbb4-4406-80f1-5db4046c8311 009](https://user-images.githubusercontent.com/87654857/147643544-9f0f7250-82fb-449f-bbd9-00cb5feb0d8c.png)

---

8.4 Некорректное создание сессионной cookie, которое приводит к захвату сессии (Session hijacking)

![Aspose Words 9caaed46-bbb4-4406-80f1-5db4046c8311 010](https://user-images.githubusercontent.com/87654857/147643555-63a98d78-3125-4ef9-987c-741a906cd75e.png)


---
9. Исправление уязвимостей:

Для уязвимостей Cookie установим флаг http-only, который запрещает любой доступ к куки из JavaScript, а также изменим значение, выводимое браузером в Value с помощью алгоритма хеширования Sha256:

![ddrFt4eSQIs](https://user-images.githubusercontent.com/87654857/149473425-084be45c-ce3b-4659-9ef7-acd39bf7f1de.jpg)

![Aspose Words 9caaed46-bbb4-4406-80f1-5db4046c8311 012](https://user-images.githubusercontent.com/87654857/147643711-3a52dee2-bb37-4ac1-87d3-20a24ebaf2c6.png)

