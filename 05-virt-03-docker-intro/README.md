1. https://hub.docker.com/repository/docker/aspire87/custom-nginx/general

2.  Запуск  контейнера с параметрами. Переименование контейнера без  его остановки. Проверка работы контейнера :

    ![Console output](img/TASK_2.png)

3. Присоединение к контейнеру c помощью  docker attach <container_id>
    ![Console output](img/TASk_3_docker_attach.png)

    При использовании  команды docker attach <container_id> мы связываем стандартный ввод, вывод или потоки ошибок контейнера с  оболочкой хоста. При использовании  комбинации  клавиш CTRL-c сеанс хоста  передает контейнеру сигнал  SIGINT , и завершает его основной процесс,  поэтому контейнер  и останавливается


    Присоединение в интерактивном режиме c docker exec -it <container_id> bash

    ![Concole output](img/TASK_3_docker_exec.png)


    Вывод  команд в контейнере и на хосте

    ![Console output](img/TASk_3_docker_nginx.png) 

    Проблема заключается в том,  что  изначально  мы пробрасывали 127.0.0.1:8080 порт  хоста на порт 80 nginx  в контейнере.  После смены  порта на 81 и мягкого перезапуска  nginx,  процесс веб-сервера в контейнере теперь ожидает подключения на 81  порту, вместо  80. Поэтому попытки соединения с хостовой  ВМ неуспешны. Для того, чтобы страница была доступна с хостовой ОС (извне) необходимо  изменить  биндинг  портов с 127.0.0.1:8080 -> 80 на 127.0.0.1:8080 -> 81

    Решение проблемы с биндингом портов хоста и контейнера без удаления контейнера и повторного изменения конфигуарции nginx
    
    ![Console output](img/TASk_3_docker_solution.png)


4. Работа с  docker  volume

    ![Console output](img/TASK_3_docker_volume.png) 


5. Т.к. docker ипсользовался на ВМ  VirtualBox вместо типа сети контейнера host,  пришлось  использовать  тип bridge (указан в  compose  файле, хотя  это не обязательно),  чтобы была возможность  попасть в  web-интерфейс portainer.

    ![Console output](img/TASK_5_compose%20files.png)


    ![Console output](img/TASK_5_compose_include.png)


    ![Console output](img/TASK_5_tag_and_push.png)


    ![Console output](img/TASK_5_portainer_admin.png)


    ![Console output](img/TASK_5_compose_nginx.png)


    ![Console output](img/TASK_5_nginx_inspect.png)


    ![Console output](img/TASK_5_compose_orphans_and_down.png)


Содеражние файла compose.yaml
```yaml

version: "3"
include:
  - docker-compose.yaml
services:
  portainer:
    image: portainer/portainer-ce:latest
    network_mode: bridge
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock

```
