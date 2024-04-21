# Docker_lab2
● Run a container using nginx image, and mount a directory from your host into the 
Docker container. example: /home/samy/nginx:/home/nginx (bind mount)

```` bash
mkdir nginx_bind_mount

cd nginx_bind_mount

pwd

docker run -d --name nginx_bind_mount -v /root/nginx_bind_mount:/usr/share/nginx/html nginx

docker exec -it nginx_bind_mount bash

cd /usr/share/nginx/html

vim index.html

exit

docker inspect -f '{{.NetworkSettings.IPAddress}}' nginx_bind_mount

curl 172.17.0.2

````
Part 2
● Create 2 docker network (net-1 & net-2)
`````bash
docker network create network_1
docker network create network_2
`````
● Run 2 new containers using nginx:alpine image, and attach the net-1 to them.

`````bash
docker run -d --name nginx_net1 --net network_1 nginx:alpine
docker run -d --name nginx_net2 --net network_1 nginx:alpine
```````
● Run another 1 new containers using nginx:alpine image, and attach the net-2 to 
them.
````bash
docker run -d --name nginx_net3 --net network_2 nginx:alpine
``````
● Inspect the 3 containers to know their IPs and write them aside.
`````bash
docker inspect -f '{{.NetworkSettings.Networks.network_1.IPAddress}}' nginx_net1
172.18.0.2

docker inspect -f '{{.NetworkSettings.Networks.network_1.IPAddress}}' nginx_net2
172.18.0.3

docker inspect -f '{{.NetworkSettings.Networks.network_2.IPAddress}}' nginx_net3
172.20.0.2
`````
● Enter a container in the net-1 network and try to ping a container in the net-2 
network (What do you notice?)
````bash
docker exec -it nginx_net1 sh 
ping 172.20.0.2
 ping fails. This is because containers in different Docker networks are isolated from each other by default.
`````

● Enter a container in the net-1 network and try to ping the other container in the 
same network (What do you notice?)
`````bash
docker exec -it nginx_net1 sh 
curl 172.18.0.2
ping 172.18.0.3
 successful ping response, Docker containers within the same network can communicate with each other directly using their IP addresses or container names.
``````
● Explain the difference between Docker volumes and Bind Mount.
````bash
Docker volumes are managed by Docker and provide a higher level of abstraction for data storage and management, while bind mounts offer more control and transparency by directly mapping host files or directories into containers
Docker volumes are stored within the Docker environment, isolated from the host filesystem,  bind mounts directly access directories or files on the host filesystem.
Docker volumes persist independently of the container's lifecycle,  bind mounts depend on the existence of the host directory or file.
`````
