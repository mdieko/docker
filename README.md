vmtestUser DockerHub y github
User diegomartinmunoz829
Pass 8vgeKaP1rj

***************************************************************************************************
# Configurar proxy en Docker

vi /etc/systemd/system/docker.service.d/http-proxy.conf
```
[Service]
Environment="HTTP_PROXY=10.210.56.35:3128" "NO_PROXY=localhost,127.0.0.1,docker-registry.example.com,.corp"
```
systemctl daemon-reload

systemctl restart docker

systemctl show --property=Environment docker
Environment=HTTP_PROXY=10.210.56.35:3128 NO_PROXY=localhost,127.0.0.1,docker-registry.example.com,.corp 

***************************************************************************************************
# Autocompletar en Docker 

yum -y install bash-completion

**************************************************************************************************
# Instalar Docker en UBUNTU

Ir a la Url "https://get.docker.com/"

curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh 

**************************************************************************************************
# Informacion del docker

docker info

**************************************************************************************************

# Version Docker

docker versión 

**************************************************************************************************
#Imagenes en Docker

https://hub.docker.com/

**************************************************************************************************
## Buscamos imágenes en hub.docker.com

docker search 

**************************************************************************************************
Bajar imágenes

 docker pull IMAGEN

Bajar container hello-world 
 docker run hello-world

**************************************************************************************************
Ver containers en ejecución

  docker ps

**************************************************************************************************
Ver Container creados y status

 docker ps -a

**************************************************************************************************
Borrar Containers 

 docker rm ID

**************************************************************************************************
Borrar todos los containers

 docker rm $(docker ps -a -q)

**************************************************************************************************
Listar dentro del container creado

 docker run ubuntu ls

**************************************************************************************************
ejecutar un bash de un linux

 docker run -i -t ubuntu bash
root@ee238cbe9a41:/#

Corremos un ~# docker run -it ubuntu
Y si salimos con "ctrl""p"+"q" se sigue ejecutando

**************************************************************************************************
Conectarnos al bash de un container en ejecución 

 docker attach "CONTAINER ID"

**************************************************************************************************
Poner nombre al contenedor 

 docker run --name ubuntu1 -it ubuntu

**************************************************************************************************
Crear nueva imagen a partir de un contenedor

 docker commit b74cc8ee49ed ubuntunuevo
sha256:057337500e4e439171a0e3f7110835e0354f4f383c750b0c919735319012bfdf

usamos la imagen nueva 
 docker run -it ubuntunuevo 

Hacemos la nueva forma de commit a partir de un archivo 

 docker commit --change='CMD ["apache2ctl", "-D FOREGROUND"]' -c "EXPOSE 85" a294c60441e4 apache2
sha256:8a7d8287aaaa6f3a3b996d7ff0ec70182984ea1deb7e2549617333441301d22b

CORREMOS IMAGEN creada
 docker run -d -p 5000:80 apache2 
D99df44d7a1f305089889ec577a8de7748dd4ec37a53d1c1f91e5e677d67d770

**************************************************************************************************

Eliminar imagen 

 docker rmi IMAGEN-ID

**************************************************************************************************
DOCKERFILE:

Creamos un archivo con el nombre Dockerfile
####################################################################
FROM ubuntu:18.04 #### NOMBRE IMAGEN 
MAINTAINER MartinMuñoz ### NOMBRE PROPIETARIO
RUN apt-get update  ### COMANDOS A EJECUTAR
RUN apt-get -y install apache2 
EXPOSE 81  ##PUERTO EXPUESTO
CMD /usr/sbin/apache2ctl -D FOREGROUND  ### COMANDO AL INICIAR
####################################################################

Corremos el Dockerfile 
 docker build -t miweb /root/miweb/

corremos la imagen creada 
 docker run -d -p 85:80 miweb

O correr en segundo plano 
 docker run -dti -p 85:80 miweb

**************************************************************************************************
Ejecutar comandos directamente sin ingresar a la instancia

 docker exec -ti cb29b8b082d0 uptime
 14:34:58 up 20:12,  0 users,  load average: 0.00, 0.01, 0.05

**************************************************************************************************
COMANDOS ÚTILES

 docker ps -l **Muestra el ultimo contenedor ejecutado**
 docker ps -n 4 **Muestra los últimos 4 contenedores ejecutados**
 docker ps -a -q **Muestra id de los contenedores**
 docker ps -a -n 3 -s **Tamaño de los contenedores**
 docker ps -a -f "name=gifted_ardinghelli" **Filtro**
 docker start -i e85 **Arranca un contenedor creado**
 docker  run -it ubuntu **crea un contenedor con la imagen ubuntu**
 docker  rmi IMAGEN-ID **Borra imagen**
 docker  rm container-ID **Borra container**
 docker  rm $(docker ps -a -q) **borra todos los containers**
 docker  run -it --name mi_ubuntu ubuntu ** asignamos un nombre a mi container**
 docker  exec mi_ubuntu echo hola **Envió el comando al ubuntu **
 docker  pull python ** Bajamos la imagen sin arrancarla**
 docker image ** TOdos los comandos con imágenes**
 docker  container **Todos los comandos con containers**
 docker  run -d ubuntu sh -c "while true; do date; done"
 docker logs "id" **Muestra lo que esta en pantalla**
 docker logs "id" --tail 10 **Ultimas 10 líneas**
 docker logs "id" -f **Continuo "tail -f**
 docker kill "id" **Mata contenedor"
Cuando lo matamos el container queda Exited (137) y no 0 como queda cuando cerramos bien el container.
 docker top "id" **Cual proceso esta consumiendo mas en el contenedor**
 docker stats "id" **Información Online del contenedor**
 docker inspect "id" > container1.txt **Información completa del contenedor/imagen**
 docker inspect "id" | grep IPAddress **Podemos hacer grep para buscar IP, versiones, memoria, Etc"
 docker inspect --format='{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mongo1 **IP**
 docker inspect --format='{{.LogPath}}' mongo1 **Donde deja el fichero log**
 docker inspect --format='{{.Config.Image}}' mongo1 **En que imagen esta basada**
 docker inspect --format='{{.Config.ExposedPorts}}' mongo **En que puerto escucha la imagen 

**************************************************************************************************
PUERTOS

 docker  run -d -P nginx ** Ya expone los puertos con puertos aleatorios**
 docker  run -d --name nginx2 -p 8080:80 nginx ** Especifica el puerto**
 docker  port mongo2 **Vemos en que puerto escucha el Container**

**************************************************************************************************
REDES 
 docker network ls *Lista las redes**
bridge : Redes privadas que se pueden conectar desde la red física del exterior
host: Todos los contenedores que están en esta red solo se pueden ver con el host original. o sea los contenedores no se ven entre sí
null: Contenedor sin red.

 docker network inspect bridge **Inspeccionar las redes**

CREAR REDES

 docker network create red1 **Crea una red con driver bridge por defecto****
 nmcli con **Comando que dice que conexiones hay en el SO -- EN CENTOS **
 docker inspect red1 
 docker network create --subnet=192.168.0.0/16 red2 **Crea Red cuyo rango es 192.168.0.0/16**

 docker run -it --name ubuntus --network red1 ubuntu **asociamos contenedor a red **
 docker inspect ubuntus | grep IPAdd **Verificamos la ip asignada **
 apt install iputils-ping ** Instalamos ping en ubuntu**
 docker network connect red2 ubuntua ** conectamos contenedor a otra red sin desconectar la otra **
 docker network disconnect red2 ubuntua **Desconectamos de la red el contenedor**

 docker network create net2 --subnet=172.30.0.0/16 --ip-range=172.30.10.0/24 **Creamos una Red con subred 172.30.0.0/16 y los contenedores q se conecten usen la 172.30.10.0/24**

**************************************************************************************************
ENLAZAR CONTENEDORES (EL PROBLEMA ES Q ES UNIDIRECCIONAL) 

https://docs.docker.com/network/links/

buscamos busybox para probar el entorno 
 docker run -it --rm --name b1 busybox **Buscamos el container busibox y con la opción rm se borra después de usarlo**
 docker run -it --rm --name b3 --link b1:maquina1 busybox **Creamos un contenedor enlazado con otro, te agrega en /etc/host los nombres de los enlazados**
	piing b1 o maquina1 llega

**************************************************************************************************
ENLAZAR CONTENEDORES EN REDES PERSONALIZADAS (Se crea automaticamente el dns al ingresar un contenedor a la red personalizada, sin necesiada de poner --link)

 docker run -d --name mysql_server --rm --network red1 -e MYSQL_ROOT_PASSWORD=secret mysql **con “e” pasamos la variable de entornos

 docker run -it --name mysql_client --rm --network red1 mysql bash **Ingresamos al Client mysql en modo bash
root@41c91969fa31:/# mysql -h mysql_server -u root -p
Enter password: 


 docker volume rm $(docker volume ls -f dangling=true -q) **(Borramos el espacio utilizado en las imagenes de Docker

**************************************************************************************************
VOLÚMENES

 docker run -it -v /datos --name ubuntu1 ubuntu bash **(Crea un volumen /datos alojando su contenido en "/var/lib/docker/volumes"
 docker volume ls **Lista los volumenes 
 docker volume prune **Borra los volúmenes que están sin uso

**************************************************************************************************
COMPARTIR DIRECTORIO CON HOST LOCAL

 mkdir /root/dir1
 docker run -it -v /root/dir1:/dir1 --name ubuntu2 ubuntu **Compartimos dir1

**************************************************************************************************
COMPARTIR VOLÚMENES

 docker run -it -v /datos --name ubuntu4 ubuntu bash **Creamos un Container con un volumen llamado /datos
 docker run -it --name ubuntu5 --volumes-from ubuntu4 ubuntu bash **Usamos los volúmenes de Ubuntu4

**************************************************************************************************
CREAR VOLUMEN MANUALMENTE

 docker volume create vol1 **Creamos un volumen que se va a alojar en /var/lib/docker/volumes/
 docker run -it --name ubuntu7 -v vol1:/dir1 ubuntu bash ** Asociamos ese volumen a una instancia 
 docker run -it --name ubuntu8 -v vol1:/datos:ro bash **Compartimos ese volumen con otra instancia en modo lectura

**************************************************************************************************
MODIFICAR CONTENEDOR

 docker diff ubuntu1 **Te muestra los cambios que tuvo el contenedor A=fichero nuevo / C=Cambio / D=Delete

 docker commit ubuntu1 mi_ubuntu **Creamos una imagen a partir del contenedor

DOCKERFILE

Creamos un directorio
 mkdir /root/image_python
 vi /root/image_python/Dockerfile
############################################################################
FROM ubuntu ##Imagen inicial 
RUN apt-get update ##Comando a ejecutar
RUN apt-get install -y python
############################################################################

 docker build -t imagen_python /root/imagen_python/ **Ejecutamos el Dockerfile, y crea la imagen 

 docker image history imagen_python ** Todos los cambios que tuvo la imagen 

CMD **Comando por defecto en el Dockerfile
CMD ["echo","Welcome to this container"] **Si hacemos con corchetes el comando CMD no depende del bash

ENTRYPOINT
ejecutar algo cuando arrancamos el contenedor, Ejecuta el comando siempre, mientras que con CMD no 
Sirve para pasar argumentos al comando, añade argumentos al comando.

############################################################################
WORKDIR
FROM ubuntu
RUN apt-get update
RUN apt-get install -y python
RUN mkdir /datos
WORKDIR /datos ## Directorio de trabajo, nos permite determinar un directorio de trabajo 
RUN touch f1.txt
RUN mkdir /datos1
WORKDIR /datos1 ##Puede haber multiples directorios de trabajo
RUN touch f2.txt
ENTRYPOINT ["/bin/bash"]
############################################################################

COPY / ADD 
Copiar el contenido de la maquina host a algun directorio del contenedor

############################################################################
FROM ubuntu
RUN apt-get update
RUN apt-get install -y python
RUN mkdir /datos
WORKDIR /datos
RUN touch f1.txt
RUN mkdir /datos1
WORKDIR /datos1
RUN touch f2.txt
##COPY##
COPY index.html . ##Lo copia directo del directorio de Dockerfile
COPY app.log /datos 
ENTRYPOINT ["/bin/bash"]
############################################################################
ADD
############################################################################
FROM ubuntu
RUN apt-get update
RUN apt-get install -y python
RUN mkdir /datos
WORKDIR /datos
RUN touch f1.txt
RUN mkdir /datos1
WORKDIR /datos1
RUN touch f2.txt
##COPY##
COPY index.html .
COPY app.log /datos
##ADD##
ADD docs docs ##Crea el directorio copiado en el ultimo workdir "/datos1"
ADD f* /datos/ ##METACARACTERES Copia todo lo que empieza con f al /datos
ADD f.tar . ##Desempaqueta el .tar al workdir 
ENTRYPOINT ["/bin/bash"]
############################################################################





**************************************************************************************************



Subir imagenes a Hub.docker 

LOGUEARSE 
 docker login
User diegomartinmunoz829
Pass 

El nombre de la imagen tiene que coincidir con el nombre de usuario
 docker image tag mmunoz/nginx:v1 diegomartinmunoz829/nginx:v1

Subimos la imagen
 docker push diegomartinmunoz829/nginx:v1

**************************************************************************************************
DOCKER COMPOSE

Bajarse la ultima version 
  curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

Podemos ver la ultima release en https://github.com/docker/compose/releases

Damos permisos
 chmod +x /usr/local/bin/docker-compose 
############################################################################
Por error “/usr/local/bin/docker-compose-bin: error while loading shared libraries: libz.so.1: failed to map segment from shared object: Operation not permitted”

# mv /usr/local/bin/docker-compose /usr/local/bin/docker-compose-bin

# cat /usr/local/bin/docker-compose
#!/bin/bash

export TMPDIR=/opt/compose-tmp
/usr/local/bin/docker-compose-bin "$@"

# chmod 755 /usr/local/bin/docker-compose

# mkdir -p /opt/compose-tmp

# docker-compose --version
docker-compose version 1.24.1, build 4667896b
############################################################################

Creamos el Directorio de Trabajo que luego Docker-compose lo va a utilizar como nombre de proyecto
 mkdir rp_nginx
creamos el fichero docker-compose.yml
############################################################################
version: '3'
services:
  nginx:
    image: nginx
    ports:
    - "80:80"
############################################################################

 docker-compose up **Lanzamos el proceso en modo interactivo

Creating network "pr_nginx_default" with the default driver
Pulling nginx (nginx:)...
latest: Pulling from library/nginx
b8f262c62ec6: Pull complete
e9218e8f93b1: Pull complete
7acba7289aa3: Pull complete
Digest: sha256:aeded0f2a861747f43a01cf1018cf9efe2bdd02afd57d2b11fcc7fcadc16ccd1
Status: Downloaded newer image for nginx:latest
Creating pr_nginx_nginx_1 ... done
Attaching to pr_nginx_nginx_1

 docker-compose ps **
     Name               Command          State         Ports       
--------------------------------------------------------------------
pr_nginx_nginx_1   nginx -g daemon off;   Up      0.0.0.0:80->80/tcp

**************************************************************************************************
Enlazar contenedores:

creamos el archivo docker-compose.yml
############################################################################
version: '3'
services:
  wordpress:
     image: wordpress
     environment:  #### Pasamos las variables de entorno
      WORDPRESS_DB_HOST: dbserver:3306
      WORDPRESS_DB_PASSWORD: mysqlpw
     ports:
      - 80:80
     depends_on:
      - dbserver #### Dice q server va a encender primero
  dbserver:
     image: mysql:5.7
     environment:
      MYSQL_ROOT_PASSWORD: mysqlpw
     ports:
      - 3306:3306
############################################################################

Luego corremos 
 docker-compose up

 docker-compose ps
         Name                         Command               State                 Ports              
------------------------------------------------------------------------------------------------------
compose_nginx_dbserver_1    docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp, 33060/tcp
compose_nginx_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp      

 docker-compose  images
        Container           Repository    Tag       Image Id      Size 
-----------------------------------------------------------------------
compose_nginx_dbserver_1    mysql        5.7      383867b75fd2   356 MB
compose_nginx_wordpress_1   wordpress    latest   c8643a2acf55   517 MB

 docker network ls
NETWORK ID          NAME                    DRIVER              SCOPE
bcdbd539a497        bridge                  bridge              local
addfdbeb455b        compose_nginx_default   bridge              local
eb9404e04340        host                    host                local
549189a1de5b        none                    null                local

 docker-compose stop

**************************************************************************************************
COMANDOS DE DOCKER COMPOSE

 docker-compose config **Comprueba si el archivo esta bien hecho
 docker-compose config --services **Te dice los servicios que tiene el archivo
 docker-compose start **arrancamos los servicios
 docker-compose logs "service" ** Los logs del servicio
 docker-compose top "service" ** Los procesos más pesados del contenedores
 docker-compose pause **Parar temporalmente los servidores
 docker-compose unpause **Volvemos a la normalidad
 docker-compose restart ** Reiniciamos los contenedores
 docker-compose stop ** Bajo los contenedores
 docker-compose rm ** Borrar los contenedores
 docker-compose up ** Se recrean los contenedores

**************************************************************************************************
VOLÚMENES

 docker-compose exec web sh ** Para conectarse al contenedor
 docker-compose down ** Para los servicios, borra los contenedores y redes.
 docker volume prune ** Luego eso para recuperar el espacio de los volúmenes.

 docker-compose -f "nombre fichero" -p "nombre proyescto" up -d ** Se puede cambiar el nombre del fichero y el proyecto.
 docker-compose -p pr1 ps **Ahora para chequear los containers hay que agregar el nombre del proyecto 

**************************************************************************************************
DOCKER REGISTRY

 docker pull registry ** Nos bajamos la imagen 

 docker run -d -p 5000:5000 --name registro1 registry **Creamos el Container 

 docker run -d -p 5001:5001 --name registro2 registry **Creamos el Container Registro2 Para subir imagen en otro registro

Subimos la imagen:

Antes de subir la imagen al repositorio hay que etiquetarla.
 docker tag ubuntu "server":5000/mi-ubuntu

 docker images "server":5000/mi-ubuntu
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
localhost:5000/mi-ubuntu   latest              2ca708c1c9cc        2 weeks ago         64.2MB

 docker push "server":5000/mi-ubuntu ** Subir la imagen al repositorio 

Descargamos la imagen:

 docker images localhost:5000/* **Vemos las imágenes locales que tenemos en la maquina

 docker rmi localhost:5000/mi-ubuntu ** Borrar a nivel local la imagen No la que esta en el Registro

 docker pull localhost:5000/mi-ubuntu:latest
latest: Pulling from mi-ubuntu
5667fdb72017: Pull complete 
d83811f270d5: Pull complete 
ee671aafb583: Pull complete 
7fc152dfb3a6: Pull complete 
Digest: sha256:1bbdea4846231d91cce6c7ff3907d26fca444fd6b7e3c282b90c7fe4251f9f86
Status: Downloaded newer image for localhost:5000/mi-ubuntu:latest
localhost:5000/mi-ubuntu:latest


ALMACENAMIENTO DEL DOCKER REGISTRY

 docker run -d --name repo1 -p 5000:5000 -v /root/reg_docker:/var/lib/registry registry 

**************************************************************************************************
DOCKER SWARM

Cluster en Docker 

Para inicializar el cluster usamos el siguiente comando 

 docker swarm init

Este comando inicializa un nuevo cluster, identifica a esta maquina como manager y darle un token (codigo para conectarse a otros nodos del cluster)

Si es que en el máster tenemos mas de una ip tenemos que poner:

 docker swarm init --advertise-addr IP


  docker swarm init
Swarm initialized: current node (ru6baeorinzlkoct35nm485u6) is now a manager.

To add a worker to this swarm, run the following command:

 docker swarm join --token SWMTKN-1-5cge6ez7hx1nxejgt6ed6l5npn0lpfqoxef8jwoa81mjegii4s-amh2bw1m9ci5e7k9wydfdxo35 10.210.56.150:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.


GUARDAMOS ESTA LINEA "docker swarm join --token SWMTKN-1-5cge6ez7hx1nxejgt6ed6l5npn0lpfqoxef8jwoa81mjegii4s-amh2bw1m9ci5e7k9wydfdxo35 10.210.56.150:2377"

 docker swarm join-token worker **Por si perdemos el tocken


 docker node ls ** Vemos los nodos que tenemos

 docker node inspect --pretty docker-nodo1 ** Vemos la info de los nodos

Si queremos tener un nuevo manager 

Con un node ls comprobamos que "docker-manager" es el lider

 docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
ifbeo2a7xul7vuh3sk6xzseod *   docker-manager      Ready               Active              Leader              19.03.2
ydlpm4pqnznvw659zzxv1348d     docker-nodo1        Ready               Active                                  19.03.2
le32cofmuccy8nnzfrmmcl4g5     docker-nodo2        Ready               Active              	              19.03.2

Si queremos que el manager pase al "docker-nodo2" hacemos.

 docker node promote docker-nodo2 
Node docker-nodo2 promoted to a manager in the swarm.

Comprobamos que se pasó al "docker-nodo2" pero quedo en modo Reachable. Quiere decir que está a la espera que falle el nodo principal

 docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
ifbeo2a7xul7vuh3sk6xzseod *   docker-manager      Ready               Active              Leader              19.03.2
ydlpm4pqnznvw659zzxv1348d     docker-nodo1        Ready               Active                                  19.03.2
le32cofmuccy8nnzfrmmcl4g5     docker-nodo2        Ready               Active              Reachable           19.03.2

Con demote promovemos al nodo "docker-nodo2" a q sea leader

 docker node demote docker-manager 
Manager docker-manager demoted in the swarm.



Comprobamos que "docker-nodo2" que el mismo paso a ser leader.

 docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
ifbeo2a7xul7vuh3sk6xzseod     docker-manager      Ready               Active                                  19.03.2
ydlpm4pqnznvw659zzxv1348d     docker-nodo1        Ready               Active                                  19.03.2
le32cofmuccy8nnzfrmmcl4g5 *   docker-nodo2        Ready               Active              Leader              19.03.2

Para quitar al nodo1 del cluster

 docker swarm leave 
Node left the swarm.

 docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
ifbeo2a7xul7vuh3sk6xzseod     docker-manager      Down                Active                                  19.03.2
ydlpm4pqnznvw659zzxv1348d     docker-nodo1        Ready               Active                                  19.03.2
le32cofmuccy8nnzfrmmcl4g5 *   docker-nodo2        Ready               Active              Leader              19.03.2


**************************************************************************************************
SERVICIOS

Creamos un servicio con una réplica con el Nombre Servicio1 y una mini imagen haciendo un ping a docker.com

 docker service create --replicas 1 --name servicio1 alpine ping docker.com 
us0wsd804j4lorq9b0wxxv5n9
overall progress: 1 out of 1 tasks 
1/1: running   [==================================================>] 
verify: Service converged





 docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
us0wsd804j4l        servicio1           replicated          1/1                 alpine:latest       

Nos muestra en donde esta replicado el servicio

 docker service ps servicio1 
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE           ERROR                       PORTS
y6bs1u7rlk56        servicio1.1         alpine:latest       docker-nodo1        Ready               Ready 1 second ago                                  
j55zrzicirkp         \_ servicio1.1     alpine:latest       docker-nodo2        Shutdown            Failed 1 second ago     "task: non-zero exit (1)"   
20vasvpiau4q         \_ servicio1.1     alpine:latest       docker-nodo1        Shutdown            Failed 13 seconds ago   "task: non-zero exit (1)"   
pm7znbkcnnuv         \_ servicio1.1     alpine:latest       docker-nodo2        Shutdown            Failed 24 seconds ago   "task: non-zero exit (1)"   
j3vlxkjrb88b         \_ servicio1.1     alpine:latest       docker-nodo2        Shutdown            Failed 35 seconds ago   "task: non-zero exit (1)"   

 docker service logs servicio1 
servicio1.1.90hqh59zu3x0@docker-nodo2    | ping: bad address 'docker.com'
servicio1.1.mnxjggv664oa@docker-nodo2    | ping: bad address 'docker.com'
servicio1.1.ic6r5b509ahx@docker-manager    | ping: bad address 'docker.com'
servicio1.1.bqcuvf95u5m6@docker-nodo1    | ping: bad address 'docker.com'

 docker service inspect --pretty servicio1 

ID:		us0wsd804j4lorq9b0wxxv5n9
Name:		servicio1
Service Mode:	Replicated
 Replicas:	1
Placement:
UpdateConfig:
 Parallelism:	1
 On failure:	pause
 Monitoring Period: 5s
 Max failure ratio: 0
 Update order:      stop-first
RollbackConfig:
 Parallelism:	1
 On failure:	pause
 Monitoring Period: 5s
 Max failure ratio: 0
 Rollback order:    stop-first
ContainerSpec:
 Image:		alpine:latest@sha256:72c42ed48c3a2db31b7dafe17d275b634664a708d901ec9fd57b1529280f01fb
 Args:		ping docker.com 
 Init:		false
Resources:
Endpoint Mode:	vip

**************************************************************************************************
ESCALAR UN SERVICIO

 docker service scale servicio1=4

 docker service ps servicio1 
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                      ERROR                       PORTS
tsh361sdkewd        servicio1.1         alpine:latest       docker-nodo1        Running             Running 2 seconds ago                                          
5goc7gi0fopr         \_ servicio1.1     alpine:latest       docker-manager      Shutdown            Failed 8 seconds ago               "task: non-zero exit (1)"   
63a8gf17j91s         \_ servicio1.1     alpine:latest       docker-manager      Shutdown            Failed 19 seconds ago              "task: non-zero exit (1)"   
t606tcvp8dmm         \_ servicio1.1     alpine:latest       docker-manager      Shutdown            Failed 30 seconds ago              "task: non-zero exit (1)"   
bh70dn3ctlsc         \_ servicio1.1     alpine:latest       docker-manager      Shutdown            Failed 41 seconds ago              "task: non-zero exit (1)"   
xvglvtj6y830        servicio1.2         alpine:latest       docker-nodo2        Ready               Preparing less than a second ago                               
zydgi1r72e29         \_ servicio1.2     alpine:latest       docker-manager      Shutdown            Failed less than a second ago      "task: non-zero exit (1)"   
edj7wumgeus9         \_ servicio1.2     alpine:latest       docker-nodo2        Shutdown            Failed 11 seconds ago              "task: non-zero exit (1)"   
yu6xi7qgzcz7         \_ servicio1.2     alpine:latest       docker-nodo2        Shutdown            Failed 22 seconds ago              "task: non-zero exit (1)"   
17ndcgociriv         \_ servicio1.2     alpine:latest       docker-nodo2        Shutdown            Failed 33 seconds ago              "task: non-zero exit (1)"   
2b93qrdexfi6        servicio1.3         alpine:latest       docker-manager      Running             Running 5 seconds ago                                          
zjhgvbd9yyoj         \_ servicio1.3     alpine:latest       docker-manager      Shutdown            Failed 11 seconds ago              "task: non-zero exit (1)"   
k5gp5cdep72c         \_ servicio1.3     alpine:latest       docker-nodo2        Shutdown            Failed 21 seconds ago              "task: non-zero exit (1)"   
uy65xtx2keru         \_ servicio1.3     alpine:latest       docker-nodo1        Shutdown            Failed 33 seconds ago              "task: non-zero exit (1)"   
xmpgdgluvzj5         \_ servicio1.3     alpine:latest       docker-nodo1        Shutdown            Failed 44 seconds ago              "task: non-zero exit (1)"   
xhzydk1u3d1o        servicio1.4         alpine:latest       docker-nodo2        Running             Running 4 seconds ago                                          
kc03jghf3sdr         \_ servicio1.4     alpine:latest       docker-manager      Shutdown            Failed 11 seconds ago              "task: non-zero exit (1)"   
jom0w8a33t6u         \_ servicio1.4     alpine:latest       docker-manager      Shutdown            Failed 22 seconds ago              "task: non-zero exit (1)"   
7pkg0kr3iny5         \_ servicio1.4     alpine:latest       docker-nodo1        Shutdown            Failed 33 seconds ago              "task: non-zero exit (1)"   
2fbvomy44ls4         \_ servicio1.4     alpine:latest       docker-nodo1        Shutdown            Failed 44 seconds ago              "task: non-zero exit (1)"   

**************************************************************************************************
KUBERNETES

Instalar kubctl
sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl

compobamos que tebemos vmx habilitado 
grep -E --color 'vmx|svm' /proc/cpuinfo

Tiene que estar instalado virtualbox

Bajamos la ultima version de minikube
https://github.com/kubernetes/minikube/releases
wget https://github.com/kubernetes/minikube/releases/download/v1.4.0/minikube-linux-amd64

Renombramos el archivo 
mv minikube-linux-amd64 minikube

Damos permisos de ejecucion 
chmod  +x minikube

Lo movemos a /bin
cp minikube /usr/local/bin/.

arrancamos minikube
minikube start

Comprobamos que funciona
mmunoz@mmunoz-dell:~$ minikube status
host: Running
kubelet: Running
apiserver: Running
kubectl: Correctly Configured: pointing to minikube-vm at 192.168.99.100



lanzamos el Dashboard de minikube
minikube dashboard


Vamos a desplegar un nginx
kubectl run nginx1 --image=nginx

Ver estado del deploy
kubectl get deployments
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
nginx1   0/1     1            0           74s

Se crea automaticamente un pod
kubectl get pods
NAME                     READY   STATUS         RESTARTS   AGE
nginx1-675bf6c9f-xxdjc   0/1     ErrImagePull   0          108s

Arrancamos kubectl proxy
kubectl proxy

si ingresamos a 127.0.0.1:8001 desde el navegador nos da
todas las apis que tenemos 
"""""""""""""""""""""""""""""2
{
  "paths": [
    "/api",
    "/api/v1",
    "/apis",
    "/apis/",
    "/apis/admissionregistration.k8s.io",
    "/apis/admissionregistration.k8s.io/v1",
    "/apis/admissionregistration.k8s.io/v1beta1",
    "/apis/apiextensions.k8s.io",
    "/apis/apiextensions.k8s.io/v1",
    "/apis/apiextensions.k8s.io/v1beta1",
    "/apis/apiregistration.k8s.io",
    "/apis/apiregistration.k8s.io/v1",
    "/apis/apiregistration.k8s.io/v1beta1",
    "/apis/apps",
    "/apis/apps/v1",
    "/apis/authentication.k8s.io",
    "/apis/authentication.k8s.io/v1",
    "/apis/authentication.k8s.io/v1beta1",
    "/apis/authorization.k8s.io",
    "/apis/authorization.k8s.io/v1",
    "/apis/authorization.k8s.io/v1beta1",
    "/apis/autoscaling",
    "/apis/autoscaling/v1",
    "/apis/autoscaling/v2beta1",
    "/apis/autoscaling/v2beta2",
    "/apis/batch",
    "/apis/batch/v1",
    "/apis/batch/v1beta1",
---
--
-
"""""""""""""""""""""""""""""

http://127.0.0.1:8001/version

Version del servidor 

mmunoz@mmunoz-dell:~$ kubectl get pods
NAME                     READY   STATUS             RESTARTS   AGE
nginx1-675bf6c9f-xxdjc   0/1     ImagePullBackOff   0          11m


http://127.0.0.1:8001/api/v1/namespaces/default/pods/nginx1-675bf6c9f-xxdjc

nos sale la informacion del pods

http://127.0.0.1:8001/api/v1/namespaces/default/pods/nginx1-675bf6c9f-xxdjc/proxy


*******************************************************************************************


kubectl describe pods *** Describe los pods que tenemos

kubectl logs nginx1-675bf6c9f-xxdjc ** Que se esta ejecutando dentro del pod

kubectl exec nginx1-675bf6c9f-xxdjc ls **Ejecutamos comando del pod

kubectl exec -it nginx1-675bf6c9f-xxdjc bash *** Ingresamos al bash

CREAR UN SERVICIO
kubectl expose deployments/nginx1 --type=NodePort --port 80 ***Creamos un servicio de tipo nodeport y por le puerto 80
service/nginx1 exposed

kubectl get services
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        46m
nginx1       NodePort    10.111.36.193   <none>        80:30943/TCP   49s

kubectl describe service/nginx1
Name:                     nginx1
Namespace:                default
Labels:                   run=nginx1
Annotations:              <none>
Selector:                 run=nginx1
Type:                     NodePort
IP:                       10.111.36.193
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30943/TCP
Endpoints:                
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

Escalar servicios

kubectl scale deployments/nginx1 --replicas=3 

mmunoz@mmunoz-dell:~$ kubectl get deployments
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
nginx1   0/3     3            0           32m
mmunoz@mmunoz-dell:~$ kubectl get pods
NAME                     READY   STATUS              RESTARTS   AGE
nginx1-675bf6c9f-dpdm5   0/1     ContainerCreating   0          26s
nginx1-675bf6c9f-xxdjc   0/1     ImagePullBackOff    0          32m
nginx1-675bf6c9f-zv9fv   0/1     ErrImagePull        0          26s

mmunoz@mmunoz-dell:~$ kubectl get pods -o wide
NAME                     READY   STATUS             RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
nginx1-675bf6c9f-dpdm5   0/1     ImagePullBackOff   0          50s   172.17.0.8   minikube   <none>           <none>
nginx1-675bf6c9f-xxdjc   0/1     ImagePullBackOff   0          33m   172.17.0.6   minikube   <none>           <none>
nginx1-675bf6c9f-zv9fv   0/1     ImagePullBackOff   0          50s   172.17.0.7   minikube   <none>           <none>







COMANDOS 
############################################################################
# docker system prune ** Te limpia todo lo que esta sin uso del docker
# docker inspect “ID” | jq ‘.[].Mounts’ ** te trae los volumenes montados
# docker cp hola “ID CONTENEDOR”:/tmp ** Copiar archivos al contenedor que esta corriendo
# docker logs --tail=10 -t “ID” ** Con “-t” te da el dato del tiempo que se realizó el error en el log
