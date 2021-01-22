# Práctica 15: Instalación de Wordpress usando contenedores Docker y Docker Compose

Esta práctica consiste en instalar Wordpress desde un contenedor Docker. Una vez tengamos nuestra máquina creada con los puertos correspondientes abiertos (80 de apache y el 8080 de phpmyadmin) comenzaremos a crear nuestro archivo .yml para docker. 

YAML es un formato para guardar objetos de datos con estructura de árbol. Sus siglas significan YAML Ain't Markup Language. 

Nuestro archivo estará compuesto por 3 contenedores: 

- Wordpress 
- phpmyadmin
- MySQL

Además estará compuesta por una capa backend y frontend.

## Configuración del docker compose:

Lo primero que escribiremos en nuestro archivo será la versión de docker que usaremos y seguidamente abajo pondremos los servicios que vayamos a utilizar.

### phpmyadmin
Usaremos la imagen oficial de phpmyadmin, añadiremos unas variables para especificar la base de datos con la que entrará por defecto y asignaremos el puerto 8080.
Además señalar que usará las dos redes (back y front) y que también depende de que MySQL esté antes.
Por último señalaremos que reinicie cada vez que se detenga de forma inesperada. 
```
phpmyadmin:
        image: phpmyadmin
        environment:
            - PMA_HOST=mysql
        ports:
            - 8080:80
        networks:
            - frontend-network
            - backend-network
        depends_on: 
            - mysql
        restart: always
```

### MySQL 
Utilizaremos la imagen mysql, que usará la última que haya aunque podemos especificar que versión queremos. Añadiremos el puesto para MySQL Seguidamente crearemos las variables para la contraseña de root, base de datos y usuario y contraseña. Se creará un volumen para guardar esos datos y para mysql solo usará la red de backend. 
Por último señalaremos que reinicie cada vez que se detenga de forma inesperada. 

```
mysql:
        image: mysql
        command: --default-authentication-plugin=mysql_native_password
        ports:
            - 3306:3306
        environment:
            - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
            - MYSQL_DATABASE=${MYSQL_DATABASE}
            - MYSQL_USER=${MYSQL_USER}
            - MYSQL_PASSWORD=${MYSQL_PASSWORD}
        volumes:
            - mysql_data:/var/lib/mysql
        networks:
            - backend-network
        restart: always
```

### Wordpress
Usaremos la imagen Wordpress y necesitaremos las variables para el archivo wp-config.php. Crearemos un volumen para la base de datos de la web que se guardará dentro de /var/www/html. Usará las redes del front y phpmyadmin:
        image: phpmyadmin
        environment:
            - PMA_HOST=mysql
        ports:
            - 8080:80
        networks:
            - frontend-network
            - backend-network
        depends_on: 
            - mysql
        restart: alwaysel back además de añadir el 8080:80 y por último señalaremos que reinicie cada vez que se detenga de forma inesperada. 
```
wordpress:
        image: wordpress
        ports:
            - 80:80
        environment:
            - WORDPRESS_DB_NAME=${WORDPRESS_DB_NAME}
            - WORDPRESS_DB_USER=${WORDPRESS_DB_USER}
            - WORDPRESS_DB_PASSWORD=${WORDPRESS_DB_PASSWORD}
        volumes:
            - wordpress_data:/var/www/html
        networks:
            - frontend-network
            - backend-network
        restart: always
```

Las variables se guardaran en un archivo oculto llamado .env para que las coja directamente.
