# ashfauihf_microservices
Дз по logging-1
Выполнить сборку образов при помощи скриптов docker_build.sh 
$ export USER_NAME='ваш логин на Docker Hub'
$ cd ./src/ui && bash docker_build.sh && docker push $USER_NAME/ui
$ cd ../post-py && bash docker_build.sh && docker push $USER_NAME/post
$ cd ../comment && bash docker_build.sh && docker push $USER_NAME/comment
Kibana - инструмент для визуализации и анализа логов от компании Elastic.
Перезапустить ui:
docker-compose stop ui
docker-compose rm ui
docker-compose up -d






Дз по monitoring-1

ссылка: https://hub.docker.com/repository/docker/asz1assassins/post

Создадим инстанс на YC
yc compute instance create \
--name docker-host \
--zone ru-central1-a \
--network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4 \
--create-boot-disk image-folder-id=standard-images,image-family=ubuntu-1804-lts,size=15 \
--ssh-key ~/.ssh/appuser.pub


Запустим docker-machine
docker-machine create \
--driver generic \
--generic-ip-address=51.250.88.131 \
--generic-ssh-user yc-user \
--generic-ssh-key ~/.ssh/appuser \
docker-host

переключимся на работу с docker-machine
eval $(docker-machine env docker-host)

Уточнить ip docker-machine
docker-machine ip docker-host

Запустить prometheus в ВМ
docker run --rm -p 9090:9090 -d --name prometheus prom/prometheus

Соберем образ своего prometheus
export USER_NAME=docker_login
docker build -t $USER_NAME/prometheus .

Создадим образы через docker_build.sh

for i in ui post-py comment; do cd src/$i; bash docker_build.sh; cd -; done

Создадим описание нашего docker-compose и запустим его
docker-compose up -d

Проверим как отзываются метрики если потушим один из контейнеров
docker-compose stop post
docker-compose start post

Добавим node_exporter, проверим состояние CPU, дадим нагрузку
docker-machine ssh docker-host
yes > /dev/null

Залогинимся и запушим свои образы на DockerHub
docker login
docker push $USER_NAME/ui
docker push $USER_NAME/comment
docker push $USER_NAME/post
docker push $USER_NAME/prometheus


Дз по gitlab-ci-1

Команды по Docker:
Запустите контейнер - docker-compose up -d
Написание Yandex.Cloud CLI для создание ВМ
Установка Docker с помошью docker-machine

ДЗ по докер-3

Команды Docker:
Остановить старые копии контейнеров:
docker kill $(docker ps -q)
Создание сети:
docker network create back_net --subnet=
Создадние bridge-сеть в docker
docker network create reddit --driver bridge
Избавляем бизнес от ИТ-зависимости
Host network driver
Запуск контейнера в сетевом пространстве docker-хоста
docker run -ti --rm --network host joffotron/docker-net-tools -c ifconfig

ДЗ по докер-2
Доккер команды:

Собираем наши образы

docker build -t <your-dockerhub-login>/post:1.0 ./post-py
docker build -t <your-dockerhub-login>/comment:1.0 ./comment
docker build -t <your-dockerhub-login>/ui:1.0 ./ui

Создаем сеть для контейнеров

docker network create reddit

Запускаем

docker run -d --network=reddit \
    --network-alias=post_db --network-alias=comment_db mongo:latest
docker run -d --network=reddit \
    --network-alias=post <your-dockerhub-login>/post:1.0
docker run -d --network=reddit \
    --network-alias=comment <your-dockerhub-login>/comment:1.0
docker run -d --network=reddit \
    -p 9292:9292 <your-dockerhub-login>/ui:1.0

Выполнение дз по докеру-1.
Команды docker

docker version
docker info
docker run hello-world
docker ps
docker ps -a
docker images
docker run -it ubuntu:18.04 /bin/bash
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.CreatedAt}}\t{{.Names}}"
docker start <u_container_id>
docker attach <u_container_id>
docker run -dt nginx:latest
docker exec -it a5ca8a74eaa8 bash
docker commit a5ca8a74eaa8 adavidenko/nginx-tmp-file
docker images
docker inspect 7425d3a7c478
docker ps -q
docker kill $(sudo docker ps -q)
docker system df
docker rm $(sudo docker ps -a -q) удалит все незапущенные контейнеры
docker rmi $(sudo docker images -q)
Создаем Docker в Yandex Cloud
yc compute instance create \
 --name docker-host \
 --zone ru-central1-a \
 --network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4 \
 --create-boot-disk image-folder-id=standard-images,image-family=ubuntu-1804-lts,size=15 \
 --ssh-key ~/.ssh/appuser.pub

Смотрим какой ip-address
one_to_one_nat:
address: 51.250.86.25

При Установка docker-machine 
curl -L https://github.com/docker/machine/releases/download/v0.16.2/docker-machine-`appuser -s`-`appuser -m` >/tmp/docker-machine && chmod +x /tmp/docker-machine && sudo cp /tmp/docker-machine /usr/local/bin/docker-machine

При создание столкнулся с ошибкой(Error creating machine: Error in driver during machine creation: unable to copy ssh key: open /home/aleksey/.ssh/id_rsa: permission denied)
Решилось неоднократной переустановкой ВМ в Яндекс Клауде
docker-machine create \
 --driver generic \
 --generic-ip-address=51.250.90.254 \
 --generic-ssh-user yc-user \
 --generic-ssh-key ~/.ssh/appuser \
 docker-host

Команды docker-machine
docker-machine help 
docker-machine ls (для просмотра список машин)
Команда создания - docker-machine create <имя> (для создания)
Переключение между ними через eval $(docker-machine env <имя>)
Переключение на локальный докер - eval $(docker-machine env --unset)
Удаление - docker-machine rm <имя>
sudo docker run --rm -ti tehbilly/htop
sudo docker run --rm --pid host -ti tehbilly/htop

Сборка и запуск образа
docker build -t reddit:latest . - Собрать образ
docker images -a
docker run --name reddit -d --network=host reddit:latest
docker logs your_container_id

Работа с Docker.Hub
sudo docker login
docker tag reddit:latest adavidenko/otus-reddit:1.0
docker push adavidenko/otus-reddit:1.0
docker run --name reddit -d -p 9292:9292 adavidenko/otus-reddit:1.0

Зачистка
docker-machine rm docker-host
yc compute instance delete docker-host




