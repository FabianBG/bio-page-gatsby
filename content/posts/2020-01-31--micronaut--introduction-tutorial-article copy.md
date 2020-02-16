---
title: Micro-servicios en JAVA ? - Micronaut una solución amigable para los devops
date: "2020-01-31T22:40:32.169Z"
template: "post"
draft: false
slug: "micronaut--introduction-tutorial-article"
category: "Spanish Dev Tutorial"
tags:
  - "Tutorial"
  - "Micronaut"
  - "Java"
  - "Microservices"
description: "Rapida introducción al desarrollo de microservicios en micronauta."
socialImage: "https://media-exp1.licdn.com/dms/image/C4E12AQG72Jbn1yF4mg/article-cover_image-shrink_600_2000/0?e=1587600000&v=beta&t=xb1hSQWVMWJ5Lf1_fJsX2l6WOpXkkDqAePHC-mCo_YI"
---
En la batalla por los mejores frameworks para micro-servicios existen una gran cantidad de contendientes y con variados lenguajes de programación que ofrecen la simplicidad y escalabilidad de un micro-servicio. En JAVA el principal framework que se viene a la mente es SPRING que ha sido y es uno de loas frameworks de JAVA mas robustos.

Pero porque la tendencia de desarrollo de micro-servicios esta siendo mas abarcada por otros lenguajes tales como NodeJS, GOLANG, entre otros; esto principalmente debido a que el generar imágenes docker de JAVA resulta pesado y tiende a tener un elevado consumo de recursos a pesar de generar micro-servicios simples, además que dentro de JAVA ya existen diferentes alternativas para reducir este consumo de recursos pero que requiere configuraciones extra, cosa que sus contrincantes lo hacen dentro de su configuración normal.

En este articulo no discutiremos cual es la mejor o peor tecnología para crear micro-servicios ya que eso depende en gran medida del alcance del proyecto y la arquitectura sobre la cual se desarrolla, el objetivo de este articulo es mostrar las nuevas tecnologías que rodean a JAVA orientadas al desarrollo de micro-servicios que buscan reducir al máximo el tamaño de la imagen de docker y consumo de recursos de una aplicación JAVA.

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQHb_swZ6u7PaQ/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=WocOGBB5ZUolu7QpJSWTzEfYdElowzE-W9YM8bOwlSY" alt="java-microservices-example">
	<figcaption>https://www.scnsoft.com/blog/java-microservices-example</figcaption>
</figure>



Micronaut se levanta entre algunos competidores tales como QUARKUS(https://quarkus.io/) o HELIDON(https://helidon.io/) que tiene por detrás al mismo Oracle, en este caso se ha optado por MICRONAUT(https://micronaut.io/) basándose netamente en el tamaño de la comunidad para tener mayor acceso a información y soporte sobre algunos problemas que se encuentren el camino. Se puede revisar la comparación de los tres frameworks antes mencionadas en la siguiente dirección (https://itaffinity.wordpress.com/2019/05/16/graal-native-images-quarkus-micronaut-helidon/)

En este articulo se pretende realizar un proyecto base de un micro-servicio que tenga una conexión a una base de datos(Postgresql) y exponga un CRUD de una tabla, todo esto para al final generar una imagen de docker y verificar el tamaño final de nuestro proyecto ya dockerizado.

### Requisitos:
* Se necesita tener una instalación solida de JAVA por lo que se recomienda instalar SDKMAN (https://sdkman.io/install) una herramienta útil a la hora de controlar las versiones de JAVA
* Cliente de Micronaut instalado (https://micronaut.io/download.html)
* Docker funcional y corriendo (https://docs.docker.com/install/)
* Postgresql > 9
### Alcance:
El proyecto se basa en una gestión de tareas por hacer, en donde se crea una tarea y esta puede estar en tres estados pendiente, en proceso y finalizada.

Para esto se creara una sola tabla que contenga la descripción de la tarea, la fecha de creación y su estado.

### Proyecto:
En un terminal procedemos a ejecutar el siguiente comando:

``` mn create-app com.up.base --features=graal-native-image ```

Este comando permite generar un proyecto Micronaut que usa el paquete principal bajo el nombre de com.up.base y con la variante de imágenes nativas mediante la siguiente bandera --features=graal-native-image, GraalVM esn una tecnología que no se discutirá en este articulo pero es interesante como mediante este proyecto se busca realizar una ejecución nativa de JAVA para ms información en su pagina oficial (https://www.graalvm.org/). Se genera una carpeta con el nombre de nuestro proyecto el cual tiene la siguiente estructura:

Para verificar si esta todo correcto en un terminal accedemos a la carpeta en donde se encuentra el gradlew del proyecto ejecutamos:

```./gradlew run```

Resultado de la ejecución
Ahora se procede con la configuración de Postgres, JPA y Lombok librerías necesarias para la conexión a la base de datos y desarrollo mas acelerado. Para esto se configura el archivo build.gradle


<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQEGIcte0IVMBA/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=8bz0qfo1Qmf25YJd6bVcl4_53b7IsnFaB54P1jh_fs4" alt="microservice_micronaut_base/blob/master/base/build.gradle">
	<figcaption>Fuente: https://github.com/FabianBG/microservice_micronaut_base/blob/master/base/build.gradle</figcaption>
</figure>


Ahora se configura las dependencias mediante el archivo de configuración del proyecto **application.yml:**

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQEXLOv-kKrUaQ/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=QMgMpGPF_E_WG3tXDwjTd32Dw2XjuEo62X7EEl_Nt-M" alt="microservice_micronaut_base/blob/master/base/src/main/resources/application.yml">
	<figcaption>Fuente: https://github.com/FabianBG/microservice_micronaut_base/blob/master/base/src/main/resources/application.yml</figcaption>
</figure>

**Nota: Tener en cuenta la cadena de conexión a la base de datos y que la misma este creada dentro de postgresql.**

Configuradas las dependencias se procede a crear el dominio de la aplicación que consta de servicios de dominio y modelos, además de los repositorios de acceso a la base de datos y controladores http.

1. Modelo de tarea - [ver archivo](https://github.com/FabianBG/microservice_micronaut_base/blob/master/base/src/main/java/com/up/domain/model/Task.java)
2. Repositorio de tarea - [ver archivo](https://github.com/FabianBG/microservice_micronaut_base/blob/master/base/src/main/java/com/up/infraestructure/repository/ITaskRepository.java)
3. Servicio de dominio de tarea - [ver archivo](https://github.com/FabianBG/microservice_micronaut_base/blob/master/base/src/main/java/com/up/domain/service/TaskService.java)
4. Controlador http de tarea - [ver archivo](https://github.com/FabianBG/microservice_micronaut_base/blob/master/base/src/main/java/com/up/application/controller/http/TaskController.java)

Generado el código correspondiente se ejecuta el proyecto y podemos probar los servicios que se ejecuten correctamente mediante cualquier cliente REST.


<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQG4Zofhsmhn0A/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=RBn-OZqcZvJK5w9E2IqokteqLbzJKQwl55wi2bPrjd4">
</figure>

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQGnoLBzEbhjUg/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=hbepVtaA3bhIJ7G9H0BRHg2h4ATCSChj5KphoXwhpGw">
</figure>

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQEnFq66Gf6eDA/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=k82dBBMvq-ul-Y8nxB_UJ9-2WBnrZmm9HffHyW5U7KY">
</figure>

Ahora para finalizar nuestro pequeño experimento vamos se generará una imagen nativa de docker del proyecto, para esto se procede a ejecutar el siguiente comando:

Nota: dependiendo de la base de datos usada hay que configurar ciertos argumentos para la generación correcta de la imagen, mas información en el siguiente link (https://github.com/micronaut-projects/micronaut-data/blob/master/src/main/docs/guide/graal/graalJDBC.adoc)

```
./gradlew assemble


./docker-build.sh
```
<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C5612AQGmDzGXjYTcUQ/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=1CBYG--ly9d5gzbzvMm8fufOy-bC2JvMfgp_kHD-_yc">
</figure>

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C5612AQGSPtwKNxg9xw/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=aw0oF8NWBzbbeGZOLfGZ33th_MOBtEf2QqxEMrziTJM">
</figure>

### Conclusiones:
En conclusión Micronaut es un framework de desarrollo de micro-servicios similar a Spring que toma lo mejor del mismo para un desarrollo rápido y mejora la compatibilidad con docker rápidamente sin configuraciones externas, adicionalmente de tener soporte para generar imágenes nativas que reducen aun mas el tamaño de imagen y optimizan la ejecución del código mejorando el uso de recursos.