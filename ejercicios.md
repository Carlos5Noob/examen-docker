# Ejercicio 1

- Descargar la aplicacion asteroides (fichero [asteroids-game-master.zip](https://drive.google.com/drive/folders/1dqBa5lMtpGaw3JcA-dtQbTwFAkzXs7Hj) de la Unidad Compartida. Descompimirla en directorio.
- Lanzar un contenedor con un servidor web y montar como volumen la aplicación anterior.
- Compruebe que funciona la aplicación al acceder a http://localhost.
- Modificar el fichero index.html de la aplicación para que su nombre y apellidos aparezcan en <ooter y en el title.
- Compruebe que funciona la aplicación al acceder a http://localhost y muestra su nombre y apellidos.

Solución:

1. Nos descargamos la carpeta y en la terminal nos movemos a dicha ubicación
2. Para lanzar el contenedor en un servidor web y montar un volumen:

```bash
    docker run -dp 80:80 --name nombrecontenedor -v ~/nombrecontenedor:/usr/share/nginx/html nginx
```
Cabe destacar que el directorio del volumen sería diferente si usamos apache por ejemplo. (esa dirección la encontramos en la información de la imagen en dockerhub).

Por otro lado, para ver que el contenedor se ha lanzado podemos hacer 'docker ps'

```bash
    docker ps
```

3. Hacemos un 'firefox localhost:80' para verificar que el contenedor funciona.

```bash
    firefox localhost:80
```

4. Modificamos el archivo HTML
5. Volvemos a acceder a localhost:80 y comprobamos que se ha modificado la página. Si esto es así, de puta madre.

# Ejercicio 2

- Crear fichero Dockerfile para generar una imagen que ya incluya la aplicación modificada de apartado anterior.
- Crear la imagen y comprobar que se ejecuta correctamente al acceder a http://localhost.

Solución:

1. Creamos un fichero Dockerfile en la raiz de la carpeta, y le damos el siguiente contenido:

```Dockerfile
    # Usa una imagen base con un servidor web
    FROM nginx:latest

    # Copia los archivos de tu aplicación al directorio donde Nginx sirve contenido
    COPY . /usr/share/nginx/html

    # Asegura que los archivos tengan los permisos correctos
    RUN chmod -R 755 /usr/share/nginx/html

    # Exponer el puerto 80 (opcional)
    EXPOSE 80

```

2. Construimos la imagen:

```bash
    docker build -t nombreimagen .
```

3. Verificamos que la imagen se ha creado correctamente:

```bash
    docker images
```

4. Creamos un contenedor con dicha imagen:

```bash
    docker run --name nombrecontenedor -dp 80:80 nombreimagen
```

5. Verifiamos que funciona correctamente:

```bash
    firefox localhost:80
```

# Ejercicio 3

- Subir la imagen anterior a DockerHub.
- Borrarla de su equipo.
- Probar a descargarla y comprobar que se ejecuta correctamente al acceder a http://localhost.

Solución:

1. Iniciamos sesión en dockerhub:

```bash
    docker login
```

2. Etiquetamos la imagen con tu nombre de usuario de DockerHub, ya que esta necesita que la imagen incluya tu nombre de usuario:

```bash
    docker tag nombreimagen <dockerhub-username>/nombreimagen:latest
```

3. Verificamos que la imagen aparece etiquetada correctamente:

```bash
    docker images
```

4. Subimos la imagen a DockerHub:

```bash
    docker push <dockerhub-username>/nombreimagen:latest
```

5. Si la imagen se ha subido correctamente deberíamos verla en nuestra cuenta de DockerHub.

6. Borramos la imagen del equipo local:

```bash
    docker rmi <dockerhub-username>/nombreimagen:latest
    docker rmi nombreimagen
```

7. Verificamos que se ha borrado correctamente:

```bash
    docker images
```

8. Nos descargamos la imagen desde DockerHub:

```bash
    docker pull <dockerhub-username>/nombreimagen:latest
```

9. Verificamos que se ha descargado correctamente:

```bash
    docker images
```

10. Creamos un contenedor con dicha imagen:

```bash
    docker run --name nombrecontenedor -d -p 80:80 <dockerhub-username>/nombreimagen:latest
```

11. Lo abrimos en el navegador para ver que funciona correctamente:

```bash
    firefox localhost:80
```

# Ejercicio 4

- Desplegar la aplicación Joomla usando docker compose. Puede usar este renlace de ayuda: https://www.avonture.be/blog/docker-joomla/
- Probar cómo inicia la aplicación y el acceso inicial a http://localhost

Solución:

1. Cremos un archivo 'docker-compose.yaml' en otro directorio nuevo y le damos el siguiente contenido (cuidado con las tabulaciones):

```docker-compose
services:

  joomla:
    image: joomla
    restart: always
    ports:
      - 8080:80
    environment:
      JOOMLA_DB_HOST: db
      JOOMLA_DB_USER: joomla
      JOOMLA_DB_PASSWORD: examplepass
      JOOMLA_DB_NAME: joomla_db
      JOOMLA_SITE_NAME: Joomla
      JOOMLA_ADMIN_USER: Joomla Hero
      JOOMLA_ADMIN_USERNAME: joomla
      JOOMLA_ADMIN_PASSWORD: joomla@secured
      JOOMLA_ADMIN_EMAIL: joomla@example.com
    volumes:
      - joomla_data:/var/www/html
    networks:
      - joomla_network

  db:
    image: mysql:8.0
    restart: always
    environment:
      MYSQL_DATABASE: joomla_db
      MYSQL_USER: joomla
      MYSQL_PASSWORD: examplepass
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - joomla_network

volumes:
  joomla_data:
  db_data:

networks:
  joomla_network:
```

*He de destacar que el contenido anterior lo he copiado directamente de la documentación oficial de la imagen joomla en DockerHub.*

2. Despegamos la aplicación con docker-compose:

```bash
    docker compose up -d
```

3. Verificamos que se ha creado el contenedor:

```bash
    docker ps
```

4. Abrimos el navegador y visitamos 'http:localhost'.
5. Aparecerá el instalador de Joomla. Completamos la instalación (el usuario y contraseña están definidas en el docker-compose.yaml).
6. Tras esto, verificamos que la instalación de joomla ha sido un éxito. 
7. Detenemos el contenedor:

```bash
    docker compose down
```

# Ejercicio 5

- Desplegar servicio traefik en local con: - Servicio del apartado 03 usando su imagen en DockerHub. A este servicio se accede en http://web.suapellido.internal - Aplicación Joomla del apartado 04. A este servicio se accede en http://joomla.suapellido.internal

- Debe modificar el fichero local /etc/hosts para asignar la IP local a los siguientes nombres (sustituyendo suapellido por su primer apellido): - joomla.suapellido.internal - web.suapellido.internal

- Mostrar fichero docker-compose.yml y iniciar los servicios.
- Probar el acceso a http://web.suapellido.internal y http://joomla.suapellido.internal

Solución:

1. Creamos un docker-compose.yaml en una nueva carpeta y le damos el siguiente contenido:

```docker-compose
services:
  # Servicio web (del apartado 03)
  web:
    image: <dockerhub-username>/nombreimagen:latest
    container_name: web-app
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.web.rule=Host(`web.suapellido.internal`)"
      - "traefik.http.services.web.loadbalancer.server.port=80"
    networks:
      - web_net
    restart: always

  # Servicio Joomla (del apartado 04)
  joomla:
    image: joomla
    container_name: joomla-app
    environment:
      JOOMLA_DB_HOST: db
      JOOMLA_DB_USER: joomla
      JOOMLA_DB_PASSWORD: examplepass
      JOOMLA_DB_NAME: joomla_db
      JOOMLA_SITE_NAME: Joomla
      JOOMLA_ADMIN_USER: Joomla Hero
      JOOMLA_ADMIN_USERNAME: joomla
      JOOMLA_ADMIN_PASSWORD: joomla@secured
      JOOMLA_ADMIN_EMAIL: joomla@example.com
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.joomla.rule=Host(`joomla.suapellido.internal`)"
      - "traefik.http.services.joomla.loadbalancer.server.port=80"
    networks:
      - web_net
    restart: always

  # Servicio de Traefik (Proxy inverso)
  traefik:
    image: traefik:v2.10
    container_name: traefik
    command:
      - "--api.insecure=true"  # Habilita la API de Traefik (para monitoreo en localhost:8080)
      - "--providers.docker=true"  # Habilita el proveedor de Docker
      - "--entryPoints.web.address=:80"  # Define el punto de entrada para HTTP
    ports:
      - "80:80"   # HTTP
      - "8080:8080" # Panel de control de Traefik (opcional, para monitorear)
    networks:
      - web_net
    restart: always

  # Servicio de base de datos para Joomla
  db:
    image: mysql:8.0
    container_name: joomla-db
    environment:
      MYSQL_DATABASE: joomla_db
      MYSQL_USER: joomla
      MYSQL_PASSWORD: examplepass
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - web_net
    restart: always

volumes:
  db_data:

networks:
  web_net:
    driver: bridge
```

2. Modificamos el fichero /etc/hosts y añadimos las IP locales:

```bash
    127.0.0.1  joomla.suapellido.internal
    127.0.0.1  web.suapellido.internal
```

3. Iniciamos los servicios (asegúrate de estar en el directorio correcto): 

```bash
    docker compose up -d
```

4. Comprobamos que los contenedores están funcionando:

```bash
    docker ps
```

5. Accedemos a las aplicaciones en el navegador:

-  http://web.suapellido.internal 
-  http://joomla.suapellido.internal

Si nos aparece las aplicaciones significa que lo hemos hecho todo correcto y Ango nos pondrá un 10 :)

# Ejercicio 6

- Crear instancia EC2 en AWS con docker instalado. Desplegar el servicio traefik del apartado anterior. Modifique el fichero /etc/hosts para asignar la IP de la instancia EC2 a joomla.suapellido.internal y web.suapellido.internal
- Probar el acceso a http://web.suapellido.internal y a http://joomla.suapellido.internal

1. Creamos una nueva instancia EC2 en el laboratorio AWS con Debian (lo hemos hecho en clase varias veces).
2. Obtenemos la IP pública de la instancia (botón CONECTAR arriba de la instancia)
3. Accedemos a la instancia usando SSH:

```bash
    ssh -i "tu-clave.pem" ec2-user@<ip-publica-de-tu-instancia>
```

A partir de aquí no tengo ni puta idea. 