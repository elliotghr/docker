# DOCKER

## ¿Qué es un contenedor?

Es una manera de empaquetar nuestras aplicaciones con todas las dependencias y archivos de configuración

Los contenedores almacen nuestro HTML, NodeJS, .env, etc.
Al estar todo empaquetado esto hace que nuestros contenedores sean Portables, faciles de compartir entre desarrolladores y operaciones

En general hace el despliegue y desarrollo más facil

## ¿Dónde se almacenan?

En un respositorio de contenedores (parecido a github)
Existen 2 tipos de repositorios

- Publico: Docker Hub ->Aqui encontramos aplicaciones como
  - NodeJS
  - Python
  - Postgres
  - MySQL
  - Distntias distribuciones de Linux
- Privado

## Antes de los contenedores

Cada desarrollador tiene una versión de sus aplicaciones, por ejemplo, NodeJS, un dev puede tener la v16 y otro la v17, esto genera una incompatibilidad. Por otro lado están los SO, las instalaciones no son las mismas entre SO
Esto hará que la instalación de las dependencias sean diferentes. Esto puede generar errores entre dependencias, DB, caché, etc.

## Con contenedores

Descargamos una imagen basada en Linux el cual con 1 comando tendremos nuestra app corriendo en nuestra maquina, incluso podemos tener 2 versiones corriendo simultaneamente sin ningún conflicto (esto en caso de desarrollo)

## Despliegues sin contenedores

Se necesita obtener el código, actualizar las dependencias, archivos de configuración

- Aqui se debe tener cuidado con las distintas versiones de dependencias
- Puede existir problemas de comunicación

Esto generará problemas y se tendrá que recurrir a un RollBack

## Despliegues con contenedores

Aqui se genera una imagen Linux, la unica dependencia que necesita la imagen para ejecutarse es el runtime de Docker, todo este proceso pude ser automatizado gracias a los pipeline que tienen los proveedores de servicio como github, bitbucket, etc que hace esto de maner automatica

## Imagen

Una imagen es el empaquetado que contiene:

- Dependencias
- Código
- Lo que se comparte

Y se ejecuta con un comando y ya contiene todo listo, las dependencias, las variables de entorno, etc

## ¿Qué es un container?

Son capas tras capas de imagenes, donde la capa de abajo será una distribución de Linux, la más utilizada es Alpine Linux
Desde esa base de Alpine (por ejemplo) se apilan capas hasta llegar a la última capa que contiene la aplicación como php o python o mysql
Los contenedores pesan poco a comparación de las maquinas virtuales

## Virtualización

Docker es una forma de virtualización
![Docker VS MV](./img/Docker-vs-MV.png)

Una MV trae la Imagen del Kernel y las aplicaciones, lo cual consume muchos GB
Docker solo utiliza el Kernel de donde está siendo ejecutado y trae las aplicaciones

Los contenedores de Docker tiene un gran rendimiento

## Docker Desktop

Es una Maquina Virtual, coore linux y

- ejecuta containers
- Permite acceder al sistema de archivos
- Accede a la red interna y externa

Docker Desktop contiene varias herramientas como:

- Docker Compose
- CLI (Linea de comandos)

Docker puede correr de maner nativa en Windows con WSL2

## Comando de imagenes

Una vez abierto Docker tecleamos el siguiente comando

```CLI
docker images
```

Esto nos devuelve un listado completo de todas las imagenes que hayamos descargado en nuestra máquina

```CLI
docker pull node
```

Este comando descargará una imagen
Aqui podemos optar por especificar la versión que queremos u omitir la versión y traerá la versión más reciente

Este proceso descarga cada una de las capas de la imagen

Tras ejecutar la descarga y checar las imagenes de nuevo veremos un header el cual contiene:
REPOSITORY, el cual hace referencia al nombre de la Imagen que nosotros descargamos
En TAG veremos la versión de la imagen
IMAGE ID, contiene el identificador de la imagen
CREATED, cuando fue creada la imagen
SIZE, cuánto espacio en disco duro ocupa

![Docker VS MV](./img/docker-images.png)

Ahora especificaremos la versión 18, esto lo haremos con el comando

```CI
docker pull node:18
```

Si ejecutamos docker images de nuevo veremos nuestras dos imagenes
![Docker VS MV](./img/docker-images-2.png)

Para conocer que imagenes queremos descargar debemos acceder a [docker hub](https://hub.docker.com/)

Para eliminar alguna imagen haremos uso del comando

```CLI
docker image rm node:18
```

## Comandos de contenedores

Primero descargamos la imagen, EJ. MongoDB

```CLI
docker pull mongo
```

para crear el contenedor escribimos

```CLI
docker create mongo
```

El cual se hace con el comando create y el nombre de la imagen que se utilizará como baseEsta operación nos devolverá el ID del contenedor
![Docker VS MV](./img/docker-container.png)

Para ejecutar nuestro contenedor haremos uso del comando

```CLI
docker start 5ab1076f1da4ddb6beb90130463879e326931201c1c0acbedefd3825dbd07896
```

El cual iniciará el container y podremos comprobarlo con el comando

```CLI
docker ps
```

Este comando nos devolverá una tabla, el encabezado tiene
CONTAINER ID, identificador del contenedor
IMAGE, la imagen base del contenedor
COMMAND el comando que necesita para ejecutarse
CREATED, hace cuanto fue creado el contenedor
STATUS, el estado del contenedor
PORTS, el puerto donde se está ejecutando
NAMES, el nombre que tiene nuestro contenedor

Para detener el contenedor usamos el comando

```CLI
docker stop 5ab1076f1da4
```

Para ver los contenedores que no están siendo ejecutados usaremos el comando

```CLI
docker ps -a
```

Para eliminar un container usaremos su nombre o ID

```CLI
docker rm nombre
```

Para asignar un nombre al container usaremos el comando

```CLI
docker create --name monguito mongo
```

Este comando usa la bandera --name seguido del nombre asignado y por último escribimos la imagen
Con esto podremos hacer referencia a el con el nombre

Aunque nuestro contenedor esté corriendo y muestre el puerto donde se está ejecutando no podemos acceder a la imagen, para esto necesitamos especificarle a nuestra computadora ese puero

## Port mapping

Debemos configurar nuestros puertos externos a los puertos que están en docker, con esto cada solicitud de x puerto irá a nuestra aplicación especificada
![Docker VS MV](./img/port-mapping.png)

Haremos uso del comando

```CLI
docker create -p3000:3000 --name monguito mongo
```

donde -p3000 es el puerto fisico de nuestra computadora y :3000 es la referencia al puerto de la imagen en docker

```CLI
docker logs monguito
```

Este comando nos muestra si todo se está ejecutando correctamente, si nos saca inmediatamente significa que todo está OK

```CLI
docker run mongo
```

El comando docker run verifica si existe la imagen, si no existe la descarga, posteriormente creará un contenedor, después iniciará el contenedor
Este comando mostrará los logs como si usaramos el comando "docker log --follow" y al salir de ahi con ctr + c detendrá el proceso

Para solucionar este comportamiento haremos uso del comando

```CLI
docker run -d mongo
```

Aqui no se mostrarán los logs y nos devolverá a la linea de comandos con el contenedor inicializado

## Conectándose a los contenedores

Para los contenedores existen diferentes tipos de configuraciones y cada uno tiene su propia manera de configurarse, esto se realiza con las variables de entorno, podemos verificar esto en docker hub

```CLI
docker create -p27017:27017 --name monguito -e MONGO_INITDB_ROOT_USERNAME=nico -e MONGO_INITDB_ROOT_PASSWORD=password mongo
```

En este comando de ejemplo estamos creando un contenedor, haciendo un port mapping, nombrando el container y asignando las variables de entorno (-e) de username y password a nuestra imagen de mongo
Por último asignamos el nombre de la imagen.

Inciamos el container y ejectuamos nuestro código que se aloja en el puerto 3000 y accede a la DB mongo en el puerto 27017

Arhivo index.js

```js
import express from "express";
import mongoose from "mongoose";

const Animal = mongoose.model(
  "Animal",
  new mongoose.Schema({
    tipo: String,
    estado: String,
  })
);

const app = express();

mongoose.connect(
  "mongodb://nico:password@localhost:27017/miapp?authSource=admin"
);

app.get("/", async (_req, res) => {
  console.log("listando... chanchitos...");
  const animales = await Animal.find();
  return res.send(animales);
});
app.get("/crear", async (_req, res) => {
  console.log("creando...");
  await Animal.create({ tipo: "Chanchito", estado: "Feliz" });
  return res.send("ok");
});

app.listen(3000, () => console.log("listening..."));
```

![Mongo image](./img/mongo.png)

Gracias a esto estamos utilizando mongo de manera exitosa, nos saltamos el proceso de instalación y realizamos peticiones a esta DB

## Aplicación dentro de un container

En nuestra carpeta de la aplicación creamos un archivo llamado "Dockerfile"
En este archivo se escribirá las instrucciones para crear nuestra imagen

```docker
# FROM -> palabra reservada para indicar que queremos que se base en una imagen
FROM node:18

# Con RUN crearemos una carpeta donde estará el código fuente de nuestra aplicación
RUN mkdir -p /home/app
# Esta ruta es una ruta dentro del contenedor

# Con Copy indicaremos la ruta de nuestra pc donde están los archivos y después la ruta en donde se alojarán en el container
COPY . /home/app

# Expondremos un puerto para que otros contenedores o nosotros mismos nos podamos conectar al container
EXPOSE 3000

# Por último escribiremos el comando que necesitamos ejecutar para que nuestra aplicación se inicie
CMD ["node", "/home/app/index.js"]
```

Con esto configuraremos la imagen de nuestra aplicación, sin embargo hay que crear una red interna para comunicar nuestras imagenes entre sí

![red](./img/red.png)

Para verificar nuestras redes haremos uso del comando

```CLI
docker network -ls
```

Para poder crear una red escibiremos

```CLI
docker network create mired
```

Para eliminar una red usaremos

```CLI
docker network rm mired
```

Para generar la conexión a ese contenedor tenemos que acceder a él por su nombre de contenedor

![red](./img/nombre-contenedor.png)

```js
// En nuestro archivo ya no haremos referencia al localhost
mongoose.connect(
  "mongodb://nico:password@localhost:27017/miapp?authSource=admin"
);
// Ahora haremos referencia al nombre del container
mongoose.connect(
  "mongodb://nico:password@monguito:27017/miapp?authSource=admin"
);
```

Para crear la imagen de la aplicación creada a partir del Dockerfile haremos uso del comando

```CLI
docker build -t miapp:1.0.0 .
```

Este comando recibe dos parametros, el nombre de la imagen el cual necesitamos usar la bandera -t seguido del nombre de imagen, seguido de dos puntos (:) y un número de etiqueta (puede ser un número entero, o latest, o 1.0.0), como segundo paremtro pondremos la ruta donde se encuentra nuestra aplicación (. porque lo estamos creando desde esa ruta en la terminal)
Una vez hecho esto tendremos nuestra imagen creada

![red](./img/imagen-app.png)

Ahora crearemos un contenedor que va a almacenar la imagen de mongo y otro contendor que tendrá la imagen de "miapp", tenemos que indicarle a este container que estas imagenes pertenecen a una red en especifico

![red](./img/containers-app.png)

Una vez creado los contendores con una red en común inicializamos cada uno

![red](./img/containers-start.png)

## Docker Compose

Para reducir el proceso de

- Descargar una imagen
- Crear una red
- Crear un container
  - Asignar puertos
  - Asignar nombre
  - Asignar red
  - Asignrar nombre de imagen

Haremos uso de Docker Composer que hace todo esto mucho más facil

Primero creamos un archivo llamado "docker-compose.yml"
Yml es un lenguaje de configuraciones

```yml
# Especificacmos la versión
version: "3.9"
# Especificamos los contenedores
services:
  chanchito:
    # Especificamos la ruta del build
    build: .
    # Especificamos el port mapping
    ports:
      - "3000:3000"
    # Indicamos el nombre del contenedor a mapear
    links:
      - monguito
  monguito:
    # Especificamos la imagen
    image: mongo
    # Especificamos el port mapping
    ports:
      - "27017:27017"
    # Especificamos las variables de entorno
    environment:
      - MONGO_INITDB_ROOT_USERNAME=nico
      - MONGO_INITDB_ROOT_PASSWORD=password
```

Y usaremos los comandos siguientes para crear los contenedores vinculados

```CLI
docker compose up
```

Una vez hecho esto se crea una nueva imagen que hace referencia al comando que creó codocker compose, también creó 2 nuevos contenedores y se ejecutan los contenedores para poder manipularlos

![red](./img/compose-up.png)

Para eliminar los contenedores y la red en grupo haremos uso del comando

```CLI
docker compose down
```

![red](./img/compose-down.png)

## Volumes

Parte del sistema de archivos del contenedor se montará dentro del sistema de archivos del anfitrión y estos datos no serán eliminados cada que se elimine el contenedor, esto es muy util con bases de datos y cuando estemos desarrollando
Existen 3 tipos de volumenes:

1. Anónimo: solo indicas la ruta, lo malo es que no podemos utilizarlo con otro contenedor
2. De anfitrión o Host, aqui decidimos que carpeta montar y donde montarla
3. Nombrado, es como el anónimo pero si podemos referenciar este volumen nombrad con otro contenedor

```yml
# Especificacmos la versión
version: "3.9"
# Especificamos los contenedores
services:
  chanchito:
    # Especificamos la ruta del build
    build: .
    # Especificamos el port mapping
    ports:
      - "3000:3000"
    # Indicamos el nombre del contenedor a mapear
    links:
      - monguito
  monguito:
    # Especificamos la imagen
    image: mongo
    # Especificamos el port mapping
    ports:
      - "27017:27017"
    # Especificamos las variables de entorno
    environment:
      - MONGO_INITDB_ROOT_USERNAME=nico
      - MONGO_INITDB_ROOT_PASSWORD=password
    # Agregamos la propiedad volumes
    # Primero definimos volumes de manera general y posteriormente lo hacemos por container
    volumes:
      # Agreamos el nombre del volume y la ruta dentro del contenedor donde estará el volumen de mongo-data
      - mongo-data:/data/db
      # mysql -> /var/lib/mysql
      # postgres -> /var/lib/postgresql/data
# Definimos volumes que hace referencia a todos nuestros contenedores
volumes:
  # Escribimos el nombre
  mongo-data:
```

## Ambientes y hot reload

Para crear un ambiente de desarrollo (y utilizar el anterior como producción) tendremos que crear un nuevo Dockerfile pero con extensión .dev

```docker
FROM node:18

#Agregamos nodemon que detectará los cambios
RUN npm i -g nodemon
RUN mkdir -p /home/app

# Agregamos la ruta con WORKDIR
WORKDIR /home/app

# No necesitamos la linea de COPY
EXPOSE 3000

# Gracias workdir ya solo especificamos el nombre del archivo
CMD ["nodemon", "index.js"]
```

Así mismo crearemos otro archivo yml para el ambiente de desarrollo, este se llamará docker-compose-dev.yml

```yml
version: "3.9"
services:
  chanchito:
    build:
      # Agregamos la propiedad de context y pasamos la ruta donde se encuentra la aplicación
      context: .
      #  Le especificamos que la imagen la debe construir pero con el archivo de Dockerfile.dev
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    links:
      - monguito
    # Configuramos un volume anónimo para esta imagen
    volumes:
      - .:/home/app
  monguito:
    image: mongo
    ports:
      - "27017:27017"
    environment:
      - MONGO_INITDB_ROOT_USERNAME=nico
      - MONGO_INITDB_ROOT_PASSWORD=password
    volumes:
      - mongo-data:/data/db
      # mysql -> /var/lib/mysql
      # postgres -> /var/lib/postgresql/data

volumes:
  mongo-data:
```

Una vez hecho esto ejecutamos el siguiente comando para inicializarlo

```CLI
docker compose -f docker-compose-dev.yml up
```

La diferencia es que la bandera -f nos permite especificar un archivo .yml custom que no sea el docker-compose.yml que es el archivo base

Esto hará un hot reload, cuando actualicemos nuestros archivos o DB eso se reflejará en los logs y en los datos que cambian

Cuando alguien nuevo se integre al equipo solo tendrá que hacer uso del comando docker compose up y ya tendrá todo listo