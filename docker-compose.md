# Docker-Compose

#### Paso 1: Instalar Docker Compose

> Primero, confirmamos la versión más reciente disponible en su página de versiones
```ssh
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose 
```
> A continuación, estableceremos los permisos correctos para que el comando docker-compose sea ejecutable:
```ssh
sudo chmod +x /usr/local/bin/docker-compose
```
> Para verificar que la instalación se realizó correctamente, puede ejecutar:
```ssh
docker-compose --version
```
Visualizará un resultado similar a esto:
```ssh
	Output
	docker-compose version 1.26.0, build 8a1c60f6
```

#### Paso 2: Configurar un archivo docker-compose.yml

> Comience creando un nuevo directorio en su carpeta de inicio, y luego muévalo a él:
```ssh
mkdir ~/compose-demo
cd ~/compose-demo
```
> En este directorio, configure una carpeta de aplicaciones que servirá como la raíz del documento para su entorno Nginx:
```ssh
mkdir app
```
> Usando su editor de texto preferido, cree un nuevo archivo index.html en la carpeta app:
```ssh
nano app/index.html
```
> Coloque el siguiente contenido en este archivo:
```ssh
~/compose-demo/app/index.html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Docker Compose Demo</title>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/kognise/water.css@latest/dist/dark.min.css">
</head>
<body>
<h1>This is a Docker Compose Demo Page.</h1>
<p>This content is being served by an Nginx container.</p>
</body>
</html>
```
Guarde y cierre el archivo cuando termine. Si utiliza nano, puede hacerlo pulsando CTRL+X, Y e INTRO para confirmar.

> A continuación, cree el archivo docker-compose.yml:
```ssh
nano docker-compose.yml
```	

> Inserte el siguiente contenido en su archivo docker-compose.yml:
```ssh
version: '3.7'
services:
  web:
    image: nginx:alpine
    ports:
      - "8000:80"
    volumes:
      - ./app:/usr/share/nginx/html
```
Guarde y cierre el archivo.

#### Paso 3: Ejecutar Docker Compose

> Con el archivo docker-compose.yml implementado, ahora podemos ejecutar Docker Compose para mostrar nuestro entorno. El siguiente comando descargará las imágenes Docker necesarias, creará un contenedor para el servicio web y ejecutará el entorno en contenedor en modo segundo plano:
```ssh
docker-compose up -d
```	
> Docker Compose primero buscará la imagen definida en su sistema local, y si no puede encontrar la imagen, descargará la imagen desde Docker Hub. 
Verá un resultado como este:
```ssh
Output
	Creating network "compose-demo_default" with the default driver
	Pulling web (nginx:alpine)...
	alpine: Pulling from library/nginx
	cbdbe7a5bc2a: Pull complete
	10c113fb0c77: Pull complete	
	9ba64393807b: Pull complete
	c829a9c40ab2: Pull complete
	61d685417b2f: Pull complete
	Digest: sha256:57254039c6313fe8c53f1acbf15657ec9616a813397b74b063e32443427c5502
	Status: Downloaded newer image for nginx:alpine
	Creating compose-demo_web_1 ... done
```
> Su entorno ahora está funcionando en segundo plano. Para verificar que el contenedor está activo, puede ejecutar:
```ssh
docker-compose ps
```
> Este comando le mostrará información sobre los contenedores en ejecución y su estado, además de cualquier redireccionamiento de puertos en vigor actualmente:
```ssh
Output
	       Name                     Command               State          Ports        
	----------------------------------------------------------------------------------
	compose-demo_web_1   /docker-entrypoint.sh ngin ...   Up      0.0.0.0:8000->80/tcp
```
Ahora puede acceder a la aplicación demo apuntando su servidor a 
```ssh
localhost:8000
```
si está ejecutando esta demo en su equipo local, o:
```ssh
your_server_domain_or_IP:8000 
```
si está ejecutando esta demo en un servidor remoto.

Verá una página como la siguiente:
```ssh
Página demo de Docker Compose
	Para realizar algún cambio al archivo 
	index.html, serán recogidos automáicamente por el contenedor y se reflejarán en su navegador cuando vuelva a cargar la página.
```

#### Paso 4: Familiarizarse con los comandos de Docker Compose

Para verificar los registros producidos por su contenedor Nginx, puede usar el comando logs:
```ssh
docker-compose logs
```
Visualizará un resultado similar a esto:
```ssh
	Output
	Attaching to compose-demo_web_1
	web_1  | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
	web_1  | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
	web_1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
	web_1  | 10-listen-on-ipv6-by-default.sh: Getting the checksum of /etc/nginx/conf.d/default.conf
	web_1  | 10-listen-on-ipv6-by-default.sh: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
	web_1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
	web_1  | /docker-entrypoint.sh: Configuration complete; ready for start up
	web_1  | 172.22.0.1 - - [02/Jun/2020:10:47:13 +0000] "GET / HTTP/1.1" 200 353 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) 		Chrome/83.0.4103.61 Safari/537.36" "-"
```

> Si desea pausar la ejecución del entorno sin cambiar el estado actual de sus contenedores, puede usar:
```ssh
docker-compose pause
```
```ssh
	Output
	Pausing compose-demo_web_1 ... done
```

> Para reanudar la ejecución tras emitir una pausa:
```ssh
docker-compose unpause
```
```ssh
	Output
	Unpausing compose-demo_web_1 ... done
```

> El comando stop finalizará la ejecución del contenedor, pero no destruirá ningún dato asociado con sus contenedores:
```ssh
docker-compose stop
```
```ssh
	Output
	Stopping compose-demo_web_1 ... done
```

> Si desea eliminar los contenedores, redes y volúmenes asociados con este entorno en contenedor, utilice el comando down:
```ssh
docker-compose down
```
```ssh
	Output
	Removing compose-demo_web_1 ... done
	Removing network compose-demo_default
	Observe que esto no eliminará la imagen base usada por Docker Compose para hacer girar su entorno (en nuestro caso, nginx:alpine). De esta forma, siempre que 	vuelva a abrir su entorno con un docker-compose up, el proceso será mucho más rápido ya que la imagen ya está en su sistema.
```
> En el caso de que también desee eliminar la imagen base de su sistema, puede usar:
```ssh
docker image rm nginx:alpine
```
```ssh
	Output
	Untagged: nginx:alpine
	Untagged: nginx@sha256:b89a6ccbda39576ad23fd079978c967cecc6b170db6e7ff8a769bf2259a71912
	Deleted: sha256:7d0cdcc60a96a5124763fddf5d534d058ad7d0d8d4c3b8be2aefedf4267d0270
	Deleted: sha256:05a0eaca15d731e0029a7604ef54f0dda3b736d4e987e6ac87b91ac7aac03ab1
	Deleted: sha256:c6bbc4bdac396583641cb44cd35126b2c195be8fe1ac5e6c577c14752bbe9157
	Deleted: sha256:35789b1e1a362b0da8392ca7d5759ef08b9a6b7141cc1521570f984dc7905eb6
	Deleted: sha256:a3efaa65ec344c882fe5d543a392a54c4ceacd1efd91662d06964211b1be4c08
	Deleted: sha256:3e207b409db364b595ba862cdc12be96dcdad8e36c59a03b7b3b61c946a5741a
	Nota: Consulte nuestra guía sobre Cómo instalar y usar Docker para obtener información más detallada sobre los comandos de Docker.
```

###### Referencia
> https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04-es
