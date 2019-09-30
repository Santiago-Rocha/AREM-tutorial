# Tutorial AWS EC2-RDS-S3
En el sigueinte tutorial se explica como creaer una aplicacion web que se conecte una base de datos AWS y como desplegar dicha aplicacion en EC2. Adicionalmente se explica como crear recursos estáticos usando S3

## Creación de RDS
Antes de crear la base de datos debemos crear un security group con los puertos de mysql

1. Nos dirigimos a la pestaña de Security Groups y damos click en create Seucrity Group 
![image](https://user-images.githubusercontent.com/13685178/65841277-37eac900-e2e6-11e9-9d0c-54eca54dd4b4.png)

2. Agregamos una recla al security group que creamos, esta regla va a ser de tipo Mysql y en la seccion de source ponemos anywhere 
![image](https://user-images.githubusercontent.com/13685178/65841318-88622680-e2e6-11e9-9c5a-87b337b6e574.png)

Ahora si procedemos a crear la base de datos la cual vamos a enlazar con el seucrity group que creamos

1. Nos dirigimos a la pestaña de RDS y damos click en crear database
![image](https://user-images.githubusercontent.com/13685178/65841066-1c32f300-e2e5-11e9-91f0-9f6dac15810e.png) 

2. Seleccionamos la configuración estandar y Mysql como motor de base daatos
![image](https://user-images.githubusercontent.com/13685178/65841164-4edceb80-e2e5-11e9-8c56-6a958b178c4f.png)

3. Agregamos un usuario y una clave para la base de datos, para este caso usaremos admin y 12345678
![image](https://user-images.githubusercontent.com/13685178/65841216-b98e2700-e2e5-11e9-83aa-e3a3761f8902.png)

4. En la seccion de configuraciónes adicioanales de conexión ponemos publico el acceso y le agregamos el security group que habiamos creado anteriormente
![image](https://user-images.githubusercontent.com/13685178/65841369-2c4bd200-e2e7-11e9-972a-9d67b6af2044.png)

5. En la configuración adicional agregamos el nombre de la base de datos
![image](https://user-images.githubusercontent.com/13685178/65841581-703fd680-e2e9-11e9-87f1-92ea143af0fb.png)


6. Una vez que la base de datos este disponible vamos a probar la conectividad usando DBeaver. 
\
Ubicamos entonces el host de la DB en la configuracion y nos conectamos con esa información
![image](https://user-images.githubusercontent.com/13685178/65841656-64084900-e2ea-11e9-915b-dbe03cae365d.png)
\
\
![image](https://user-images.githubusercontent.com/13685178/65841698-d416cf00-e2ea-11e9-92ea-cf52fd90d84a.png)


7. Cuando estemos conectados correctamente creamos una tabla y le insertamos algunos datos
![image](https://user-images.githubusercontent.com/13685178/65841709-f1e43400-e2ea-11e9-9efb-6c2ef69be5c7.png)



## Creación Aplicación web

1. Clonamos este [repositorio](https://github.com/Santiago-Rocha/SpringAWS) el cual cuenta con unaa de spring para conectarse a una base de datos 

2. Una vez clonado el repositorio debemos modificar la infromación del archivo aplication.properties para que coincida con nuestra DB
![image](https://user-images.githubusercontent.com/13685178/65841804-a4b49200-e2eb-11e9-8373-dc96b1f6d844.png)

3. Luego debemos modificar la entidad que va a ser mapeada de la base de datos (no olvide agregar los getters y setters de cada atributo y dos consutrctores, uno vacio y otro con todos los parametros)
![image](https://user-images.githubusercontent.com/13685178/65841830-0543cf00-e2ec-11e9-9815-91dee3019ba3.png)

4. Posteriormente debemos crear la consulta que se va a realizar a la base de datos, al igual que construir el objeto a partir de esa consulta
![image](https://user-images.githubusercontent.com/13685178/65842562-ffe98300-e2f1-11e9-9a90-f6ffd72efd8c.png)


5. al ejecutar la aplicación con el comando
``` mvn spring-boot:run ```
\
nos dirijimos a [localhost:8080/test](http://localhost:8080/test) y observamos que esta realizando la consulta correctamente
![image](https://user-images.githubusercontent.com/13685178/65841971-7d5ec480-e2ed-11e9-8755-bd9d5e4f49ea.png)

ahora vamos a crear un formulario simple que permita buscar los usuarios con un nombre asociado 

1. Creamos una nueva consulta 
![image](https://user-images.githubusercontent.com/13685178/65841849-420fc600-e2ec-11e9-9eb1-0ffb277ef0cc.png)
2. Creamos otros dos controladores para manejar el get con el formulario y el post con la rspuesta
![image](https://user-images.githubusercontent.com/13685178/65842590-3e7f3d80-e2f2-11e9-9856-afb23ee0feb5.png)

3. Ejecutamos para probar
\
![image](https://user-images.githubusercontent.com/13685178/65842606-59ea4880-e2f2-11e9-8361-400559c2ec66.png)
\
![image](https://user-images.githubusercontent.com/13685178/65842615-62db1a00-e2f2-11e9-94a8-840e39fdb6a9.png)


### Despliegue en EC2
1. Lo siguiente que debemos hacer es crear una instancia de EC2, pero en la configuracion le agregamos una regla para que habilite el puerto 8080
![image](https://user-images.githubusercontent.com/13685178/65842334-68376500-e2f0-11e9-8e29-d3c9f19d0e5c.png)


2. Una vez creada la instancia nos conectamos por ssh y cambiamos la version de java ejecutando los siguientes comandos
```
sudo su
yum remove java-1.7.0-openjdk -y
yum install java-1.8.0

```

3. cuando hayamos cambiado la version de java, usanddo sftp transeferimos el .jar de nuestra aplicación, una vaz transferida la ejecutamos con el comando``` java -jar aplication.jar```
![image](https://user-images.githubusercontent.com/13685178/65842264-e2b3b500-e2ef-11e9-9794-64018cc5a4d5.png)

4. Cuando se haya ejecutado correctamente vamos a la direccion publica de la maquina y observamos si esta funcionando
![image](https://user-images.githubusercontent.com/13685178/65842312-3f16d480-e2f0-11e9-887b-2cb89bf8a1b8.png)


## Creación de recurso estático
1. Nos dirijimos a la seccion de S3 y creamos un nuevo Bucket
![image](https://user-images.githubusercontent.com/13685178/65842389-c2382a80-e2f0-11e9-8b23-72d757e347df.png)

2. En la seccion de permisos desmarcamos la opcion de bloquer el accesso publico
![image](https://user-images.githubusercontent.com/13685178/65842411-ef84d880-e2f0-11e9-94e1-5c08aeca2bdb.png)

3. una vez creado el bucket vamos a cargar un archivo cualquiera, para ello entramos al bucket y presionamos cargar
![image](https://user-images.githubusercontent.com/13685178/65842433-1e9b4a00-e2f1-11e9-9465-b3dc0477f194.png)

4. Cuando el objeto este cargado lo presionamos y vamos al tab de permisos donde agregamos la configuracion publica de solo lectura
![image](https://user-images.githubusercontent.com/13685178/65842448-4e4a5200-e2f1-11e9-971c-5606423a10d2.png)

5. Finalmente accedemos al recurso a traves de la url que encontramos en información general
![image](https://user-images.githubusercontent.com/13685178/65842469-6e7a1100-e2f1-11e9-95aa-41dc864a0c05.png)

