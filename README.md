# sonar-docker-compose-POC-ESP

![image info](images/image1.png)


En este documento vamos a detallar como montar a nivel local via docker-compose un artefacto de SONAR para así poder validar nuestros microservicios de spring boot en dicho sonar.

## Creación del fichero docker-compose.yml

En primer lugar, como requisíto indispensable, tendremos que tener instalado docker-compose en nuestra máquina local. 

Crearemos un archivo docker-compose que tendrá que ser como el que se muestra a continuación:

<pre><code>
docker-compose.yml
version: "3"
 
services:
 
  sonarqube:
    image: sonarqube:9.9.0-community
    ports:
        # Si quisiéramos cambiar el puerto en el que se va a levantar sonar bastaría con, por ejemplo, poner "9001:9000"
      - "9000:9000"
    environment:
        # Si cambiamos el puerto del artefacto de postgres, también deberíamos indicarlo en la variable abajo de este comentario
      - SONARQUBE_JDBC_URL=jdbc:postgresql://sonarqubedb:5440/sonar
      - SONARQUBE_JDBC_USERNAME=sonar
      - SONARQUBE_JDBC_PASSWORD=sonar
    networks:
      - sonarqube-net
    volumes:
      - sonarqube_conf:/opt/sonarqube/conf
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
      - sonarqube_bundled-plugins:/opt/sonarqube/lib/bundled-plugins
 
  sonarqubedb:
    image: postgres
    container_name: sonarqubedb
    networks:
      - sonarqube-net
    environment:
      - POSTGRES_USER=sonar
      - POSTGRES_PASSWORD=sonar
    ports:
           # Si quisiéramos cambiar el puerto en el que se va a levantar POSTGRES bastaría con, por ejemplo, poner "5432:5432"
      - '5440:5432'
    volumes:
      - sonarqube_postgresql:/var/lib/postgresql
      - sonarqube_postgresql_data:/var/lib/postgresql/data
 
networks: 
  sonarqube-net:
 
volumes: 
  sonarqube_conf:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_logs:
  sonarqube_bundled-plugins:
  sonarqube_postgresql:
  sonarqube_postgresql_data:
</pre></code>

Como podemos observar en el fichero anteriormente mostrado, además del propio artefacto de sonar, se tendrá que crear paralelamente un artefacto de postgres asociado al propio sonar, el cuál será el encargado de alojar los datos de nuestros análisis.

En mi caso en concreto, dicho artefacto de postgres lo estoy dando de alta por el puerto local 5440 ya que el puerto por defecto 5432 lo tengo ocupado por otra instacia de postgres. En el caso que se necesite cambiar a otro puerto, se podría sin problema siempre y cuando se cambie también el puerto en el campo SONARQUBE_JDBC_URL del artefacto de sonar. 
