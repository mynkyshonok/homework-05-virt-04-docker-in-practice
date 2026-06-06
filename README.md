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

Решение:

<img width="356" height="41" alt="image" src="https://github.com/user-attachments/assets/dff128d8-cefb-4628-bb68-bf300285f99e" />


---

## Задача 1
1. Сделайте в своем GitHub пространстве fork [репозитория](https://github.com/netology-code/shvirtd-example-python).

2. Создайте файл ```Dockerfile.python``` на основе существующего `Dockerfile`:
   - Используйте базовый образ ```python:3.12-slim```
   - Обязательно используйте конструкцию ```COPY . .``` в Dockerfile
   - Создайте `.dockerignore` файл для исключения ненужных файлов
   - Используйте ```CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]``` для запуска
   - Протестируйте корректность сборки

Решение:

<img width="443" height="164" alt="image" src="https://github.com/user-attachments/assets/91372a0d-55fc-4927-a24f-01d5874f3d48" />

<img width="558" height="168" alt="image" src="https://github.com/user-attachments/assets/5757ae45-a62b-4041-ae94-b91e913f1317" />

<img width="908" height="57" alt="image" src="https://github.com/user-attachments/assets/af307601-2605-4490-9bb2-5454d65f807d" />

2.1 Используйте multistage сборку вместо single stage.

<img width="569" height="276" alt="image" src="https://github.com/user-attachments/assets/e94b3e7a-c414-437b-b0f5-5d9c56ba6658" />


3. (Необязательная часть, *) Изучите инструкцию в проекте и запустите web-приложение без использования docker, с помощью venv. (Mysql БД можно запустить в docker run).
4. (Необязательная часть, *) Изучите код приложения и добавьте управление названием таблицы через ENV переменную.

---
### ВНИМАНИЕ!
!!! В процессе последующего выполнения ДЗ НЕ изменяйте содержимое файлов в fork-репозитории! Ваша задача ДОБАВИТЬ 5 файлов: ```Dockerfile.python```, ```compose.yaml```, ```.gitignore```, ```.dockerignore```,```bash-скрипт```. Если вам понадобилось внести иные изменения в проект - вы что-то делаете неверно!
---

## Задача 2 (*)
1. Создайте в yandex cloud container registry с именем "test" с помощью "yc tool" . [Инструкция](https://cloud.yandex.ru/ru/docs/container-registry/quickstart/?from=int-console-help)
2. Настройте аутентификацию вашего локального docker в yandex container registry.
3. Соберите и залейте в него образ с python приложением из задания №1.
4. Просканируйте образ на уязвимости.
5. В качестве ответа приложите отчет сканирования.

## Задача 3
1. Изучите файл "proxy.yaml"
2. Создайте в репозитории с проектом файл ```compose.yaml```. С помощью директивы "include" подключите к нему файл "proxy.yaml".
3. Опишите в файле ```compose.yaml``` следующие сервисы: 

- ```web```. Образ приложения должен ИЛИ собираться при запуске compose из файла ```Dockerfile.python``` ИЛИ скачиваться из yandex cloud container registry(из задание №2 со *). Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.5```. Сервис должен всегда перезапускаться в случае ошибок.
Передайте необходимые ENV-переменные для подключения к Mysql базе данных по сетевому имени сервиса ```web``` 

- ```db```. image=mysql:8. Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.10```. Явно перезапуск сервиса в случае ошибок. Передайте необходимые ENV-переменные для создания: пароля root пользователя, создания базы данных, пользователя и пароля для web-приложения.Обязательно используйте уже существующий .env file для назначения секретных ENV-переменных!

compose.yaml:

<img width="526" height="738" alt="image" src="https://github.com/user-attachments/assets/4c1e8b7d-70e8-4b12-922f-e70bfde0fefa" />


2. Запустите проект локально с помощью docker compose , добейтесь его стабильной работы: команда ```curl -L http://127.0.0.1:8090``` должна возвращать в качестве ответа время и локальный IP-адрес. Если сервисы не стартуют воспользуйтесь командами: ```docker ps -a ``` и ```docker logs <container_name>``` . Если вместо IP-адреса вы получаете информационную ошибку --убедитесь, что вы шлете запрос на порт ```8090```, а не 5000.

Решение:

<img width="1174" height="108" alt="image" src="https://github.com/user-attachments/assets/f24e4f95-080a-4803-8632-f1534bd30637" />

<img width="719" height="42" alt="image" src="https://github.com/user-attachments/assets/1e204180-c9d3-4d50-b85c-89bc047eadce" />


5. Подключитесь к БД mysql с помощью команды ```docker exec -ti <имя_контейнера> mysql -uroot -p<пароль root-пользователя>```(обратите внимание что между ключем -u и логином root нет пробела. это важно!!! тоже самое с паролем) . Введите последовательно команды (не забываем в конце символ ; ): ```show databases; use <имя вашей базы данных(по-умолчанию virtd, как это указано в .env)>; show tables; SELECT * from requests LIMIT 10;```. Примечание: таблица в БД создается после первого поступившего запроса к приложению.

6. Остановите проект. В качестве ответа приложите скриншот sql-запроса.

Решение:

<img width="482" height="618" alt="image" src="https://github.com/user-attachments/assets/b5c59ca6-a6a4-4952-9eeb-2fdecc181c67" />


## Задача 4
1. Запустите в Yandex Cloud ВМ (вам хватит 2 Гб Ram).
2. Подключитесь к Вм по ssh и установите docker.
3. Напишите bash-скрипт, который скачает ваш fork-репозиторий в каталог /opt и запустит проект целиком.

скрипт:

<img width="803" height="165" alt="image" src="https://github.com/user-attachments/assets/4c605f6d-450d-4af8-b6e7-36184481b0f2" />


5. Зайдите на сайт проверки http подключений, например(или аналогичный): ```https://check-host.net/check-http``` и запустите проверку вашего сервиса ```http://<внешний_IP-адрес_вашей_ВМ>:8090```. Таким образом трафик будет направлен в ingress-proxy. Трафик должен пройти через цепочки: Пользователь → Internet → Nginx → HAProxy → FastAPI(запись в БД) → HAProxy → Nginx → Internet → Пользователь

<img width="855" height="481" alt="image" src="https://github.com/user-attachments/assets/f848b70e-d475-4ae4-95d6-b4a63bda4543" />


6. (Необязательная часть) Дополнительно настройте remote ssh context к вашему серверу. Отобразите список контекстов и результат удаленного выполнения ```docker ps -a```
7. Повторите SQL-запрос на сервере и приложите скриншот и ссылку на fork.

Решение:

<img width="353" height="291" alt="image" src="https://github.com/user-attachments/assets/f4299127-afea-46e8-b6b2-8152bf8f8f05" />

[Ссылка на форк](https://github.com/mynkyshonok/shvirtd-example-python)


## Задача 5 (*)
1. Напишите и задеплойте на вашу облачную ВМ bash скрипт, который произведет резервное копирование БД mysql в директорию "/opt/backup" с помощью запуска в сети "backend" контейнера из образа ```schnitzler/mysqldump``` при помощи ```docker run ...``` команды. Подсказка: "документация образа."
2. Протестируйте ручной запуск
3. Настройте выполнение скрипта раз в 1 минуту через cron, crontab или systemctl timer. Придумайте способ не светить логин/пароль в git!!
4. Предоставьте скрипт, cron-task и скриншот с несколькими резервными копиями в "/opt/backup"

## Задача 6
Скачайте docker образ ```hashicorp/terraform:latest``` и скопируйте бинарный файл ```/bin/terraform``` на свою локальную машину, используя dive и docker save.
Предоставьте скриншоты  действий .

<img width="1225" height="535" alt="image" src="https://github.com/user-attachments/assets/15945cbf-229a-4cb1-9311-7eca927149cf" />

<img width="892" height="105" alt="image" src="https://github.com/user-attachments/assets/bb5d693e-ada9-4d03-9be1-913880bd2f0c" />

айди целевого архива найден в манифестах образа:

<img width="1191" height="55" alt="image" src="https://github.com/user-attachments/assets/dbbea8b7-c0f3-47c1-8bfd-6881916df4b0" />


## Задача 6.1
Добейтесь аналогичного результата, используя docker cp.  
Предоставьте скриншоты  действий .

<img width="752" height="76" alt="image" src="https://github.com/user-attachments/assets/cc76a6e1-1671-4ff8-b23c-eb2e5728fa95" />

