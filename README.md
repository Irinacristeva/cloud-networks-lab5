Лабораторная работа: Подключение EC2 к базе данных RDS MySQL

В этой лабораторной я разворачивала EC2-инстанс, настраивала ему сетевой доступ и подключала его к RDS MySQL. Все команды выполняла сама через консоль EC2.

Что было сделано

Сначала я создала EC2-инстанс в своей VPC и сразу поместила его в публичную подсеть. Public IP включила, чтобы можно было подключаться через браузер. Убедилась, что у публичной подсети маршрут по 0.0.0.0/0 идёт в Internet Gateway - иначе бы ничего не скачивалось.

После запуска EC2 я вошла через EC2 Instance Connect и проверила, есть ли интернет. Сначала интернета не было, но после корректировки security group и маршрутов начал проходить обычный ping google.com.

Дальше я обновила систему и установила MySQL client:

sudo dnf update -y
sudo dnf install mariadb105 -y


Когда клиент установился, в консоли RDS я взяла endpoint своей базы:

project-rds-mysql-prod.cbo0uwko686q.eu-central-1.rds.amazonaws.com


После этого подключилась к базе по логину admin:

mysql -h <endpoint> -u admin -p


Пароль вводила вручную (вставлять его нельзя - по SSH это нормально). Подключение прошло успешно, и я зашла в MySQL Shell.

Дальше создала новую базу:

CREATE DATABASE projectdb;


Переключилась в неё:

USE projectdb;


И создала таблицу:

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(50),
  email VARCHAR(100)
);


Добавила тестовые данные - одну строку со своим именем и одну тестовую:

INSERT INTO users (name, email)
VALUES ('Irina', 'irina@example.com'),
       ('TestUser', 'test@example.com');


Проверила, что данные появились:

SELECT * FROM users;


Обе записи отобразились - база на RDS работает, EC2 подключается, таблица создаётся, вставка/чтение выполняются.

Итог

Я успешно настроила EC2, обеспечила ему выход в интернет, подключила его к RDS MySQL и выполнила полный цикл работы с базой: создание, таблица, вставка и чтение данных. Всё функционирует корректно.

<img width="1699" height="786" alt="Снимок экрана 2025-12-08 234307" src="https://github.com/user-attachments/assets/37c6575d-8f6d-4515-a1c9-32b5942dd35c" />
<img width="1713" height="961" alt="Снимок экрана 2025-12-09 005101" src="https://github.com/user-attachments/assets/08a2368d-b394-4ac7-81a2-51218b26a58c" />
<img width="1706" height="1329" alt="Снимок экрана 2025-12-09 005822" src="https://github.com/user-attachments/assets/7c093251-3140-4db6-9dbf-e9c48c145e49" />
<img width="1084" height="558" alt="Снимок экрана 2025-12-09 012031" src="https://github.com/user-attachments/assets/b3fb44ab-a600-4f6c-a162-cfb3144f6196" />
<img width="861" height="989" alt="Снимок экрана 2025-12-09 184033" src="https://github.com/user-attachments/assets/43d5fadc-520e-4d64-bc11-d81e8e65c1fa" />
