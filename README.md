# kubernetes
  

1. Docker  
Para ver la utilidad de Docker vamos a crear un contenedor y realizar las siguientes acciones:  
1.1. crear una aplicacion de nombre "my-app"  
1.2. crear un Dockerfile que contenga la aplicacion "my-app"  
1.3. crear la imagen  
1.4. crear el contenedor  
1.5. subir la imagen en el repositorio local o remoto  

1.1. Creamos una aplicacion de tipo node, creamos una carpeta del proyecto:  
```
mkdir myapp
cd myapp

nano package.json
```

contenido del fichero package.json:
```
{
  "name": "docker_web_app",
  "version": "1.0.0",
  "description": "Node.js on Docker",
  "author": "First Last <mguado@gmail.com>",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.16.1"
  }
}

```
  
Creamos la aplicacion "server.js"
```
'use strict';

const express = require('express');

// Constants
const PORT = 8080;
const HOST = '0.0.0.0';

// App
const app = express();
app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(PORT, HOST);
console.log(`Running on http://${HOST}:${PORT}`);

```
  
Ejecutamos la aplicacion para validar que funciona:
```
npm start
```
  
Invocamos desde un navegador o consola a la url http://localhost:8080  
  
Listo! tenemos la aplicacion ejecutandose entonces vamos a crear una imagen para poder ejecutar esta aplicacion desde un contenedor - podemos detener Ctrl-C la aplicacion para despues no entrar en conflicto con el puerto cuando se ejecute desde el contenedor.  
  
1.2. Creamos el fichero Dockerfile con el contenido siguiente:
```
FROM node:10

# Creamos la carpeta de trabajo
WORKDIR /usr/src/app

# copiamos las dependenccias
COPY package*.json ./

RUN npm install
# Instalamos las dependencias
# RUN npm ci --only=production

# copiamos el codigo del proyecto
COPY . .

EXPOSE 8080
CMD [ "node", "server.js" ]
# este comando inicializa la aplicacion por defecto en el puerto 8080

```
  
1.3. Creamos la imagen:  
```
Docker build . -t my-app:v1
```
  
Podemos ejecutar algunos comandos para ver si la imagen se ha creado "docker images"
  
1.4. Creamos el contenedor
```
docker run -v ${PWD}:/myapp -p 8080:8080 --name=myapp my-app:v1
```
  
Ejecutamos "docker ps -a" para visualizar el contenedor ejecutandose y lo probamos invocando la url http://localhost:8080
  
  
1.5. Con la imagen creada "my-app:v1" podemos crear N contenedores y para distribuirlo lo podemos subir a nuestro repositorio de empresa o proyecto o subirlo a la nube, haremos una subido remota al repositorio "github.docker.com" requisito para ejecutar es necesario tener una cuenta previa creada.
  
desde la consola ejecutamos:
```
docker login
# nos validamos

#creamos una imagen que cumpla con la siguiente regla
docker tag my-app:v1 magzupao/my-app:v1  

#visualizamos las imagenes
vagrant@debian-10:~$ docker images
REPOSITORY        TAG       IMAGE ID       CREATED        SIZE
my-app            v1        c896312d3f4d   2 hours ago    914MB
magzupao/my-app   v1        c896312d3f4d   2 hours ago    914MB
node              10        28dca6642db8   6 months ago   910MB

# subimos la imagen al repositorio remoto github.docker.com
docker push magzupao/my-app:v1

```
  
  
Importante debe tener una cuenta activa en IBM Cloud para efectos practicos crear una cuenta nueva.

1. creamos una cuenta nueva:
https://cloud.ibm.com/

2. nos logeamos y accedemos a esta url:
https://cloud.ibm.com/registry/start

3. seguimos las instrucciones:
```
Instalar, configurar e iniciar sesión
1. Instale la CLI de IBM Cloud. - https://cloud.ibm.com/docs/containers?topic=containers-cs_cli_install

2. Instale la CLI de Docker. - https://docs.docker.com/engine/install/

3. Instale el plug-in de Container Registry.
ibmcloud plugin install container-registry -r 'IBM Cloud'

4. Inicie sesión en la cuenta de IBM Cloud.
ibmcloud login -a https://cloud.ibm.com

5. Asegúrese de que el destino sea la región correcta de IBM Cloud Container Registry.  
Al momento de logearme defini la region con lo cual este paso se puede saltar sino lo ha definido en el paso anterior:  
ibmcloud cr region-set global

6. Elija un nombre para el primer espacio de nombres y cree ese espacio de nombres. Utilice este espacio de nombres para el resto del Inicio rápido.
ibmcloud cr namespace-add magztest

7. Creo mi imagen:
docker tag my-app:v1 us.icr.io/magztest/my-app:v1  

8. Me logeo a IBM Cloud
ibmcloud cr login

9. Subo la imagen al repositorio:
docker push us.icr.io/magztest/my-app:v1

10. Verifico la subida de la imagen:
ibmcloud cr image-list

```

Tambien podemos subir las imagenes a github de es esta forma, primero crear un token que permita realizar el push, despues de logearnos a github vamos a esta url:
https://github.com/settings/tokens  
  
Agregamos permisos marcando repo, user, write:packages y delete:packages

generamos nuestro token y lo guardamos en un archivo PAT.txt en nuestra raiz vagrant@debian-10:~

Ejecutamos:
cat PAT.txt | docker login https://docker.pkg.github.com -u magzupao --password-stdin

-- nos visualiza un mensaje OK

Creamos la imagen a subir siguiendo esta regla: docker tag IMAGE_NAME docker.pkg.github.com/OWNER/REPOSITORY/IMAGE_NAME:VERSION

docker tag my-app:v1 docker.pkg.github.com/magzupao/cursoprueba/my-app:v1

Ejecutamos el push:
docker push docker.pkg.github.com/magzupao/cursoprueba/my-app:v1

Al finalizar nos da un mensaje Ok y podemos ver en la web de github en el repositorio creado en la opcion packages la imagen subida.








