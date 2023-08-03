mkdir project_folder
cd project_folder
mkdir lab
cd lab
nano  lab_service.dk
version: '3.1'

services:
  lab-service:
    image: nginx
    ports:
      - "8000:80"
    environment:
      - ENVIRONMENT=lab
    deploy:
      placement:
        constraints:
          - node.labels.env == lab
    networks:
      - lab-network

networks:
  lab-network:
    driver: overlay
cd ..
mkdir dev
cd dev
nano dev_service.dk
version: '3.1'

services:
  dev-service:
    image: nginx
    ports:
      - "8080:80"
    environment:
      - ENVIRONMENT=dev
    deploy:
      placement:
        constraints:
          - node.labels.env == dev
    networks:
      - dev-network

networks:
  dev-network:
    driver: overlay
cd ..
docker swarm init
docker swarm join --token SWMTKN-1-57imvmknminndlfq95lsj9xg4c4i7cd7wwkbp4wdcrsciwgbit-9d3dj9kztaxj6e4fx2andzgkg 192.168.1.185:2377
docker node ls
docker node update --label-add env=lab yoyt5ur30pk72a1je2f17qxqh
docker node update --label-add env=dev h6vkb5pweb5b8mr4zf8kjflz9
docker service create --name lab-service-port80 --replicas 3 --constraint 'node.labels.env == lab' -p 80:80 nginx
docker service create --name dev-service-port90 --replicas 3 --constraint 'node.labels.env == dev' -p 90:90 nginx
docker service ls
docker service ps lab-service-port80
docker service ps dev-service-port90

![Результат выполнения](/lab-dev-new.PNG)
