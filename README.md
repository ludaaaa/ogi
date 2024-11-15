# Установить Linux Oracle на VirtualBox:


Установить образ Linux

![image](https://github.com/user-attachments/assets/1d698457-fe0e-4e1f-9f55-6aad6c269ca4)

Выделить 4 ядра

Выделить 4096 МБ оперативной памяти

![image](https://github.com/user-attachments/assets/6dcc2669-aefc-470c-a133-163215089fbf)


# Установка docker

Устанавливает утилиту wget на систему

     sudo yum install wget

![image](https://github.com/user-attachments/assets/cbbfb087-1846-4172-a621-7b14bd39c363)


Скачиваем файл репозитория

     sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo

![image](https://github.com/user-attachments/assets/f329b74f-10dc-49b8-9b1c-26091a27e1cf)

Устанавливаем docker

     sudo yum install docker-ce docker-ce-cli containerd.io

![image](https://github.com/user-attachments/assets/57d6f0d6-7a5a-4050-9570-f87852cdefaa)

Запускаем его и разрешаем автозапуск

     sudo systemctl enable docker --now

![image](https://github.com/user-attachments/assets/f3ad9197-bfcb-4117-a77a-eaf1d22dbf35)


# Установка compose

Для начала нужно убедиться в наличии пакета curl

     sudo yum install curl

![image](https://github.com/user-attachments/assets/d819eeeb-44a9-4a4a-8e97-c5666de0d3be)


Объявление переменной COMVER, полученной в результате curl запроса, хранящей в себе номер последней
версии Docker Compose

     COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)

Теперь скачиваем скрипт docker-compose последней версии, используя объявленную ранее переменную и помещаем его в каталог /usr/bin

     sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose

![image](https://github.com/user-attachments/assets/45d3058b-6975-497c-9da6-47606a2275e1)


Предоставление прав на выполнение файла docker-compose

     sudo chmod +x /usr/bin/docker-compose

Проверка установленной версии Docker Compose

     sudo docker-compose --version

![image](https://github.com/user-attachments/assets/e07a4840-7860-4552-8e8d-5901c4a93548)

# Делаем grafana

Установка git

     sudo yum install git

![image](https://github.com/user-attachments/assets/1fafc932-0906-47cf-8f95-8d2a6c736b1e)


Этот код скачивает содержимое репозитория skl256/grafana_stack_for_docker

     sudo git clone https://github.com/skl256/grafana_stack_for_docker.git

![image](https://github.com/user-attachments/assets/26c8955b-d03a-46c2-9eec-8b5d0e82f253)


Заходит в папку - cd

     cd grafana_stack_for_docker

cd .. - возвращает в папку выше

![image](https://github.com/user-attachments/assets/5ed09383-32b1-4a8f-b940-44a9ff1cd5e4)


(После этого можно вставлять готовый docker-compose)

Cоздаем папки двумя разными способами

     sudo mkdir -p /mnt/common_volume/swarm/grafana/config

     sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data}

Выдаем права

     sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}

Создаем файл

     sudo touch /mnt/common_volume/grafana/grafana-config/grafana.ini

Копирование файлов

     sudo cp config/* /mnt/common_volume/swarm/grafana/config/

Переименовывание файла

     sudo mv grafana.yaml docker-compose.yaml

![image](https://github.com/user-attachments/assets/7f01e5ce-8ce3-439d-9013-332e20f6dd41)


Собрать докер (нужно запускать из папки где docker-compose.yaml)

     sudo docker compose up -d

Опустить докер - sudo docker compose stop

![image](https://github.com/user-attachments/assets/ba2421bc-44cb-4811-a1c8-817d87bff209)



# Начинаем чистку файлов

Команда открывает файл docker-compose.yaml в текстовом редакторе vi с правами суперпользователя

     sudo vi docker-compose.yaml

Что-бы что-то изменить в тесковом редакторе нужно нажать insert на клавиатуре

Затем в docker-compose нужно вставить node-exporter и удалить ненужные файлы (но у нас уже вставлен готовый докер)

![image](https://github.com/user-attachments/assets/44d39597-94bb-4632-8e6c-f2f5ba51f9c0)

выйти не сохраняясь из vim - esc -> :q!

выйти сохраняясь из vim - esc -> :wq!

Заходим в другую папку 

     sudo cd /mnt/common_volume/swarm/grafana/config

Открываем файл prometheus.yaml в текстовом редакторе vi с правами суперпользователя

     sudo vi prometheus.yaml

![image](https://github.com/user-attachments/assets/9ed6287d-a3a6-47dd-a5ac-c5bb1af1cf40)


Далее нужно исправить targets: на exporter:9100

![image](https://github.com/user-attachments/assets/6fa47729-6b23-4deb-ab7f-e79109ed7e58)


# Делаем grafana на сайте

   •	переходим на сайт localhost:3000

                User & Password GRAFANA: admin

                Код графаны: 3000

                Код прометеуса: http://prometheus:9090

   •	в меню выбираем вкладку Dashboards и создаем Dashboard

                ждем кнопку +Add visualization, а после "Configure a new data source"

                выбираем Prometheus

                Connection

                http://prometheus:9090

   •	Authentication

                Basic authentication

                User: admin

                Password: admin

                Нажимаем на Save & test и должно показывать зелёную галочку

   •	в меню выбираем вкладку Dashboards и создаем Dashboard

                ждем кнопку "Import dashboard"

                Find and import dashboards for common applications at grafana.com/dashboards: 1860 //ждем кнопку Load

                Select Prometheus ждем кнопку "Import"



![image](https://github.com/user-attachments/assets/1baa7e02-29d9-48d1-bcb1-7e9b3d81fcac)



![image](https://github.com/user-attachments/assets/ac3ec5fc-f576-415a-a90a-ce116c401c42)





# Делаем VictoriaMetrics

Для начала зайдем в нужную папку

     cd grafana_stack_for_docker

Открываем docker-compose

     sudo vi docker-compose.yaml

После prometheus вставляем vmagent (но у нас уже вставлен готовый докер)

![image](https://github.com/user-attachments/assets/6c101f26-b2e3-4070-8a93-f15c00d49ecb)


Захом в connection там где мы писали http://prometheus:9090 пишем http://victoriametrics:8428 И заменяем имя из "Prometheus-2" в "Vika" нажимаем на dashboards add visualition выбираем "Vika" снизу меняем на "code" Переходим в терминал и пишем

     echo -e "# TYPE OILCOINT_metric1 gauge\nOILCOINT_metric1 0" | curl --data-binary @- http://localhost:8428/api/v1/import/prometheus

• команда отправляет бинарные данные (например, метрики в формате Prometheus) на локальный сервер, который слушает на порту 8428.

     curl -G 'http://localhost:8428/api/v1/query' --data-urlencode 'query=OILCOINT_metric1'

• команда делает запрос к API для получения данных по метрике OILCOINT_metric1

• команда выводит текст, который может быть использован для определения метрики в формате, совместимом с Prometheus

• команда выводит информацию о типе и значении этой метрики в формате, который может быть использован системой мониторинга Prometheus.

![image](https://github.com/user-attachments/assets/89d885aa-95ec-41c0-b214-48594f8830ac)

Значение 0 меняем на любое другое

Копируем переменную OILCOINT_metric1 и вставляем в query

Нажимаем run

![image](https://github.com/user-attachments/assets/c9a52b4c-1b65-4def-ac8b-b78a188faacb)

![image](https://github.com/user-attachments/assets/7f1e473c-64e6-41ac-a44e-5ef4203a3153)

Копируем переменную OILCOINT_metric1 и вставляем в code

![image](https://github.com/user-attachments/assets/014556de-9a3c-48d6-9d9b-6122f7aae2dd)









