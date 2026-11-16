1. ¿Por qué el frontend no necesita conectarse a PostgreSQL?
Porque el frontend solo es la parte que ve el usuario (la página web). No guarda ni lee datos directamente, ya que le pide las cosas al backend, y es el backend el que habla con la base de datos. Así cada pieza hace una sola cosa.

2. ¿Por qué el backend pertenece a dos redes?
Porque hace de puente. Por un lado tiene que hablar con el frontend (red frontend) y por otro con la base de datos (red datos). Es el único que necesita ver a los dos.

3. ¿Qué servicio necesita publicar un puerto en el anfitrión?
El frontend, porque es el que usa la persona desde el navegador. En el compose se ve con 127.0.0.1:${PUERTO_WEB}:8080, que significa que se entra desde nuestro propio equipo y no desde fuera.

4. ¿Por qué publicar PostgreSQL hacia fuera no es la solución adecuada para un fallo de comunicación interno?
Porque el fallo está dentro de Docker, por ejemplo, que el backend y la base de datos no estén en la misma red y abrir el puerto al exterior no lo arregla. Además es peligroso: cualquiera que llegue a ese puerto podría intentar entrar en la base de datos. Lo correcto es arreglar las redes.

5. ¿Qué ventaja ofrece Docker Compose respecto a ejecutar los tres contenedores manualmente?
Que con un solo archivo y un solo comando (docker compose up -d) se crean las redes, el volumen y los tres contenedores, en el orden correcto y siempre igual. A mano habría que escribir tres comandos docker run largos, crear las redes y acordarse de todo cada vez, con más riesgo de equivocarse.

6. ¿Por qué hemos dividido la arquitectura en dos redes?
Para separar y dar seguridad. El frontend solo está en la red frontend, así que no puede llegar a la base de datos. La base de datos solo está en la red datos, así que solo el backend puede hablar con ella. Si alguien entrara por el frontend, no tendría acceso directo a los datos.

7. ¿Por qué el backend utiliza db como dirección de PostgreSQL?
Porque db es el nombre del servicio en el compose, y Docker tiene un DNS interno que traduce ese nombre a la IP del contenedor. Así no hay que apuntar IPs, que pueden cambiar cada vez que se recrea el contenedor. Esto solo funciona si los dos están en la misma red.

8. ¿Qué diferencia existe entre detener un proyecto y eliminar sus volúmenes?
Con docker compose down se paran y se borran los contenedores y las redes, pero los datos se quedan en el volumen. Con docker compose down -v también se borran los volúmenes, y entonces los datos desaparecen. Es como apagar el ordenador frente a formatear el disco duro.

9. ¿Qué consecuencias tendría para la empresa perder el volumen de PostgreSQL?
Se perderían todos los datos de la aplicación, en este caso, todas las incidencias registradas. La empresa tendría que empezar de cero, perdería el historial y no podría hacer seguimiento de los problemas. Por eso hay que hacer copias de seguridad del volumen y tener cuidado con el -v.