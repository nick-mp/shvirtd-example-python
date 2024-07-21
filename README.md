# Домашнее задание к занятию 5. «Практическое применение Docker»

## Задача 0
1. Убедитесь что у вас НЕ(!) установлен ```docker-compose```, для этого получите следующую ошибку от команды ```docker-compose --version```
```
Command 'docker-compose' not found, but can be installed with:

sudo snap install docker          # version 24.0.5, or
sudo apt  install docker-compose  # version 1.25.0-1

See 'snap info docker' for additional versions.
```
В случае наличия установленного в системе ```docker-compose``` - удалите его.  
2. Убедитесь что у вас УСТАНОВЛЕН ```docker compose```(без тире) версии не менее v2.24.X, для это выполните команду ```docker compose version```  

###  Ответ 1:
![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/0-1.png)

---

## Задача 1
1. Сделайте в своем github пространстве fork репозитория ```https://github.com/netology-code/shvirtd-example-python/blob/main/README.md```.   
2. Создайте файл с именем ```Dockerfile.python``` для сборки данного проекта(для 3 задания изучите https://docs.docker.com/compose/compose-file/build/ ). Используйте базовый образ ```python:3.9-slim```. Протестируйте корректность сборки. Не забудьте dockerignore. 

###  Ответ 2: 
  [Dockerfile.python](https://github.com/nick-mp/shvirtd-example-python/blob/main/Dockerfile.python)
  [.gockerignore](https://github.com/nick-mp/shvirtd-example-python/blob/main/.dockerignore)


## Задача 2 (*)
1. Создайте в yandex cloud container registry с именем "test" с помощью "yc tool" . [Инструкция](https://cloud.yandex.ru/ru/docs/container-registry/quickstart/?from=int-console-help)
2. Настройте аутентификацию вашего локального docker в yandex container registry.
3. Соберите и залейте в него образ с python приложением из задания №1.
4. Просканируйте образ на уязвимости.
5. В качестве ответа приложите отчет сканирования.

###  Ответ 2*:
  [Отчет_сканирования_образа](https://github.com/nick-mp/shvirtd-example-python/blob/main/vulnerabilities.csv)


## Задача 3
1. Изучите файл "proxy.yaml"
2. Создайте в репозитории с проектом файл ```compose.yaml```. С помощью директивы "include" подключите к нему файл "proxy.yaml".
3. Опишите в файле ```compose.yaml``` следующие сервисы: 

- ```web```. Образ приложения должен ИЛИ собираться при запуске compose из файла ```Dockerfile.python``` ИЛИ скачиваться из yandex cloud container registry(из задание №2 со *). Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.5```. Сервис должен всегда перезапускаться в случае ошибок.
Передайте необходимые ENV-переменные для подключения к Mysql базе данных по сетевому имени сервиса ```web``` 

- ```db```. image=mysql:8. Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.10```. Явно перезапуск сервиса в случае ошибок. Передайте необходимые ENV-переменные для создания: пароля root пользователя, создания базы данных, пользователя и пароля для web-приложения.Обязательно используйте уже существующий .env file для назначения секретных ENV-переменных!

4. Запустите проект локально с помощью docker compose , добейтесь его стабильной работы: команда ```curl -L http://127.0.0.1:8090``` должна возвращать в качестве ответа время и локальный IP-адрес. Если сервисы не стартуют воспользуйтесь командами: ```docker ps -a ``` и ```docker logs <container_name>``` . Если вместо IP-адреса вы получаете ```None``` --убедитесь, что вы шлете запрос на порт ```8090```, а не 5000.


    ![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/3-1.png)
    ![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/3-1-1.png)


5. Подключитесь к БД mysql с помощью команды ```docker exec <имя_контейнера> mysql -uroot -p<пароль root-пользователя>```(обратите внимание что между ключем -u и логином root нет пробела. это важно!!! тоже самое с паролем) . Введите последовательно команды (не забываем в конце символ ; ): ```show databases; use <имя вашей базы данных(по-умолчанию example)>; show tables; SELECT * from requests LIMIT 10;```.


    ![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/3-2-1.png)
    ![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/3-2-2.png)


6. Остановите проект. В качестве ответа приложите скриншот sql-запроса.

## Задача 4
1. Запустите в Yandex Cloud ВМ (вам хватит 2 Гб Ram).
2. Подключитесь к Вм по ssh и установите docker.
3. Напишите bash-скрипт, который скачает ваш fork-репозиторий в каталог /opt и запустит проект целиком.
4. Зайдите на сайт проверки http подключений, например(или аналогичный): ```https://check-host.net/check-http``` и запустите проверку вашего сервиса ```http://<внешний_IP-адрес_вашей_ВМ>:8090```. Таким образом трафик будет направлен в ingress-proxy.


    ![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/4-1.png)
    ![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/4-2.png)    


5. (Необязательная часть) Дополнительно настройте remote ssh context к вашему серверу. Отобразите список контекстов и результат удаленного выполнения ```docker ps -a```
### тут как не пытался не получилось

![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/4-5_fail.png)


6. В качестве ответа повторите  sql-запрос и приложите скриншот с данного сервера, bash-скрипт и ссылку на fork-репозиторий.

### ответы приведены на скрине в п.4. ссылку на репозиторий видно в скрипте перед выполнением img/4-1.png.

[репозиторий](https://github.com/nick-mp/shvirtd-example-python.git)


## Задача 5 (*)
1. Напишите и задеплойте на вашу облачную ВМ bash скрипт, который произведет резервное копирование БД mysql в директорию "/opt/backup" с помощью запуска в сети "backend" контейнера из образа ```schnitzler/mysqldump``` при помощи ```docker run ...``` команды. Подсказка: "документация образа."
2. Протестируйте ручной запуск
3. Настройте выполнение скрипта раз в 1 минуту через cron, crontab или systemctl timer. Придумайте способ не светить логин/пароль в git!!
4. Предоставьте скрипт, cron-task и скриншот с несколькими резервными копиями в "/opt/backup"

### нашел как просто через cron делать backup mysqldubp, но через образ schnitzler/mysqldump и команду run не получилось. что-то не так с передаваемыми параметрами после очистки entrypoint.

## Задача 6 (*)
Скачайте docker образ ```hashicorp/terraform:latest``` и скопируйте бинарный файл ```/bin/terraform``` на свою локальную машину, используя dive и docker save.
Предоставьте скриншоты  действий .

![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/6-2-1.png)
![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/6-3-1.png)
![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/6-3-2.png)


    ### распаковываем архив, который мы видели на 1м скрине 6-2-1.png
![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/6-3-3.png)
![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/6-3-3.png)
![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/6-3-4.png)

## Задача 6.1
Добейтесь аналогичного результата, используя docker cp.  
Предоставьте скриншоты  действий .

![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/6-4-1.png)
![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/6-4-2.png)
![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/6-4-3.png)

### заменять не стал, показал, что файл terraform в папке bin аналогичен полученному в этой задаче


## Задача 6.2 (**)
Предложите способ извлечь файл из контейнера, используя только команду docker build и любой Dockerfile.  
Предоставьте скриншоты  действий .

### Ответ: при формировании файла можно указать volume, который будет соответствовать каталогу на хосте, тем самым мы получим доступ к нему в любое время. Показал на пример с БД mysql

![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/6.2.2_compose.png)
![Task](https://github.com/nick-mp/shvirtd-example-python/blob/main/img/6.2.2_files.png)