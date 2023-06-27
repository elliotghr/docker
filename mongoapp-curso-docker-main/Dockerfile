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
