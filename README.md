![image](https://github.com/user-attachments/assets/cb61e2a7-69f2-434a-bc47-7df7cbdad03d)# Docker 6 --> Prestashop

Si queremos instalar este servicio en un contenedor de manera manual podemos hacerlo siguiendo la siguiente [guía](https://hub.docker.com/r/prestashop/prestashop/).   
En este ReadMe, se pondrá la estructura de un docker compose y se explicará por partes cada bloque de código.  
Yml completo: 
```   
version: '3.3'   
services:   
  db:   
    image: mariadb:latest   
    restart: 'no'   
    environment:   
      MYSQL_ROOT_PASSWORD: 1234   
      MYSQL_DATABASE: prestashop   
      MYSQL_USER: pts_usr   
      MYSQL_PASSWORD: 1234   
  prestashop:   
    depends_on:   
      - db    
    image: prestashop/prestashop:latest   
    restart: 'no'   
    ports:   
      - '9080:80'   
    environment:   
      DB_SERVER: db   
      DB_USER: pts_usr   
      DB_NAME: prestashop   
      DB_PASSWD: 1234   
      DB_PREFIX: ps_   
      PS_DOMAIN: "localhost:8080"   
   ```

### Primer bloque *(Base del documento)*:  
```
version: '3.3'
services:
```
La primera línea indica la versión de docker-compose que se va a utilizar mientras que la segunda es donde empieza la ***declaración/configuración*** de los servicios.   

### Segundo bloque *(Base de datos)*:
```
db:       
    image: mariadb:latest     
    restart: 'no'   
    environment:     
      MYSQL_ROOT_PASSWORD: 1234     
      MYSQL_DATABASE: prestashop     
      MYSQL_USER: pts_usr     
      MYSQL_PASSWORD: 1234    
```

En este bloque se define el nombre que se va a utilizar para el servicio ***(que no tiene que ser igual al tipo de servicio que vas a usar, por ej., puedes llamar manolo pero usar una imagen de ubuntu)***, la imagen, a la cual se le puede añadir una etiqueta para descargar una versión específica de la imagen, y el entorno, donde se definen ciertos parámetros los cuales hablaremos más adelante.  
La etiqueta *restart* la utilizamos para indicarle al contenedor que no reinicie el servicio, sino no pararía **(en este caso).**    
Dentro de la etiqueta *environment* podemos ver:

```
MYSQL_ROOT_PASSWORD: 1234     
```
Indica la contraseña del *root* de MariaDB.   
```
MYSQL_DATABASE: prestashop     
```
Indica el nombre de la base de datos.   
```
MYSQL_USER: pts_usr     
```
Indica el nombre del usuario que se va a crear.   
```
MYSQL_PASSWORD: 1234
```
Indica la contraseña del usuario anterior.  
    
### Tercer bloque *(Prestashop)*:    
```
prestashop:   
    depends_on:   
      - db    
    image: prestashop/prestashop:latest   
    restart: 'no'   
    ports:   
      - '9080:80'   
    environment:   
      DB_SERVER: db   
      DB_USER: pts_usr   
      DB_NAME: prestashop   
      DB_PASSWD: 1234   
      DB_PREFIX: ps_   
      PS_DOMAIN: "localhost:8080"   
   ```

Para esto desglosaremos cada parte para explicarla y que quede clara:    
```
prestashop:
```
Nombre que se utiliza para el servicio *(explicado arriba)*.    
```
    depends_on:   
      - db    
```
Este bloque indica las dependencias necesarias para que adherirlas al servicio que estas definiendo actualmente. En este caso, Prestashop requiere *obligatoriamente* una base de datos.   
```
image: prestashop/prestashop:latest   
```
Indica la imagen que se utiliza *(explicado arriba)*.   
```
restart: 'no'
```
Igual que el anterior explicado.   
```
ports:   
      - '9080:80'  
```
Puerto por el que se va a escuchar este servicio.   
```
 environment:   
      DB_SERVER: db   
      DB_USER: pts_usr   
      DB_NAME: prestashop   
      DB_PASSWD: 1234   
      DB_PREFIX: ps_   
      PS_DOMAIN: "localhost:9080"   
   ```

Entorno que define ciertos parámetros:  
```
      DB_SERVER: db
```
Servidor que va a usar Prestashop, necesario definir este nombre del servidor para más adelante.   
```
      DB_USER: pts_usr
```
Usuario que va a usar el servicio.  
```
      DB_PASSWD: 1234   
```
Contraseña del usuario de arriba.   
```
      DB_PREFIX: ps_   
```
Prefijo que van a tener las tablas del servicio. Puede ser útil para identificar las tablas de cada servicio.   
```
      PS_DOMAIN: "localhost:9080"   
```
Dominio del servicio.   
Con todo esto se ha abarcado todo el documento para tener claro qué hace cada cosa, ahora solo faltaría lanzar los contenedores con el siguiente comando:
```
sudo docker-compose up -d
```
Aquí podrían surgir *2 problemas*:
 1. Nombre mal puesto
    Docker compose solo acepta archivos que tengan el nombre de docker-compose con la extensión .yaml o .yml.   
 
 2. Formato incorrecto
    Los desniveles se tienen que formar con 2 espacio **y no valen las tabulaciones**.

Con todo esto en cuenta, cuando ejecutes el comando debería de salirte algo así:   
[imagenContainer](https://github.com/YoelGarciaLago/dockerEJ6/blob/master/imagenes/Captura%20de%20pantalla%20de%202024-10-29%2020-39-03.png?raw=true)  
Luego tendrás que ejecutar este comando y buscarás la ip del contenedor de PrestaShop.   
```
sudo docker ps -a #Para ver el id/nombre del contenedor   
```
```
sudo docker inspect <id/nombre_del_contenedor> #Aquí saldrá un montón de información. Solo es necesario que bucar  IPAddress    
```

En tu navegador buscarás:
```
http://<ip_de_PrestaShop>
```
Y *(si todo sale bien)* te saldrá esto:  
[imgInicio](https://github.com/YoelGarciaLago/dockerEJ6/blob/master/imagenes/Captura%20de%20pantalla%20de%202024-10-29%2018-55-58.png?raw=true)    

Después de escoger el idioma tendrás que aceptar las condiciones de la siguiente captura:  
[imgCond](https://github.com/YoelGarciaLago/dockerEJ6/blob/master/imagenes/Captura%20de%20pantalla%20de%202024-10-29%2018-56-19.png?raw=true)

Tras aceptar las condiciones, el servicio analizará si tu máquina tiene las dependencias necesarias y, en caso de que no las tuviera, se descargarían *(con esto no quiere decir que se tengan que definir con el compose, solo que mirará si tu máquina es capaz de tener PHP o cosas así).   

Con esto superado, tendrás que rellenar los datos de tu tienda, como el nombre, el usuario que la va a usar, su contraseña, etc... *(no tiene nada que ver con la BD)*

Con todo ello, te saldrá la siguiente pestaña:   
[imgdep](https://github.com/YoelGarciaLago/dockerEJ6/blob/master/imagenes/Captura%20de%20pantalla%20de%202024-10-29%2018-57-37.png?raw=true)   
Esto es básicamente si quieres instalar los módulos y las demostraciones de PrestaShop.   

Luego te saldrá la siguiente pestaña:   
[imgDB](https://github.com/YoelGarciaLago/dockerEJ6/blob/master/imagenes/380812119-a0cf58d3-eccf-4bff-a943-92c13b01ca77.png?raw=true)   
Aquí tendrás que rellenar los datos que te piden con los datos que metiste en la etiqueta *environment* del .yml.  
A continuación, empezará a configurar todo el sistema. Te aparecerá como en la siguiente imagen:  
[imgConfg](https://github.com/YoelGarciaLago/dockerEJ6/blob/master/imagenes/Captura%20de%20pantalla%20de%202024-10-29%2019-37-41.png?raw=true)   
Tras esto, aparecerá la siguiente ventana conforme se ha instalado correctamente:  
[imgInst](https://github.com/YoelGarciaLago/dockerEJ6/blob/master/imagenes/Captura%20de%20pantalla%20de%202024-10-29%2019-43-59.png?raw=true)   
Viendo esto pensarás que has acabado, pero ahora mismo solo podrás ver la página de inicio que puede ver todo el mundo.  
[imgFront](https://github.com/YoelGarciaLago/dockerEJ6/blob/master/imagenes/Captura%20de%20pantalla%20de%202024-10-29%2021-07-01.png?raw=true)   
Al intentar acceder a la administración de la tienda te saldrá lo siguiente:   
[imgErr](https://github.com/YoelGarciaLago/dockerEJ6/blob/master/imagenes/Captura%20de%20pantalla%20de%202024-10-29%2019-46-52.png?raw=true)
Como te indica el cuadro, tienes que eliminar la carpeta **/install** y modificar la **/admin** con los siguientes comandos:
```
docker exec -it <nombre_o_id_del_contenedor_prestashop> rm -rf /var/www/html/install
docker exec -it <nombre_o_id_del_contenedor_prestashop> mv /var/www/html/admin /var/www/html/admin896sjdin1230jdndi
```
Con esto tendrás que ir al siguiente enlace:   
```
http://<IP_del_contenedor>/admin896sjdin1230jdndi
```
Y te saldrá esto:   
[imgIS](https://github.com/YoelGarciaLago/dockerEJ6/blob/master/imagenes/Captura%20de%20pantalla%20de%202024-10-29%2019-49-04.png?raw=true)   
Tras poner el nombre y contraseña del usuario que **TÚ** mismo has hecho, te saldrá la siguiente pantalla.   
[imgBack](https://github.com/YoelGarciaLago/dockerEJ6/blob/master/imagenes/Captura%20de%20pantalla%20de%202024-10-29%2019-49-12.png?raw=true)   

Y *(ahora sí)* se ha terminado la instalación de PrestaShop.  
