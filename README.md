# Docker 🚀️

* No se recomienda usar el Docker que viene por defecto con Ubuntu. Se debe instalar la mas actual.
* Al instalar Docker en windows, se instala una imagen de Linux por detrás, ya que Windows no provee soporte nativo para Docker.
* `docker images` => Ver la lista de imagenes
* `docker run hello-world` => Se fija si posee la imagen hello-world, si no la tiene, la descarga y la ejecuta
* Si tengo que usar `sudo` para cualquiera de los comandos, algo de la instalacion no esta bien.

### Arquitectura Microservicios

* Permiten tener ciertas features sin tocar codigo.
* **Rancher** => Orquestador de Clusters, se puede ver rapidamente el estado de la aplicacion.
* **Promitius** => Desarrollar metricas propias, o metricas out of the box para medir el estado de nuestros componentes
* **Istio** => Da mucha funcionalidad sin tener que tocar el codigo, es un agente que intercepta al trafico y agrega autenticacion a microservicios sin tener que desarrollar nada. Podemos visualizar el trafico y los request que se van moviendo entre los microservicios.
* **Opendistro** => Es un fork de Elasticsearch.
* **Docker** => Es la pieza fundamental para la creacion de Kubernetes, que es un orquestador de contenedores. Y Docker provee los contenedores.
* **Kubernetes** => Orquestador de contenedores, por dentro corre Docker.

#### Containers

* Existen desde 2007, implementado por Solaris Container, su idea era virtualizar un SO.
* Sus procesos son autocontenidos.
* Llega a Linux en 2008 LXC, es la implementacion de containers para Linux.
* **LXC (Linux container)** : Es exclusivo para Linux, en 2010 usan namespaces y usa como seguridad Selinux. Era muy dificil de configurar y mantener.
* **namespaces**: Los espacios de nombres donde se contienen los procesos.
* **cgroups**: Limita los recursos, prioriza los recursos, y controla los accesos. Tambien gestiona una seguridad de cuentas. Se usaba para el control de procesos.

## Qué es Docker? 🚀️

* Aparecio Hykes y se dio cuenta que LXC tenia mucho poder pero era muy complejo.
* Docker es un runtime que expone una API, que se consume, y orquesta contra el Kernel para crear el ambiente que cada proceso necesita.
* Docker es un unico proceso en ejecucion. No se puede correr mas de un proceso dentro de un proceso de Docker. La idea es que haya un unico proceso padre y que cuando este muera, el container tambien muera.
* La idea de orquestar containers es que, cuando una falle, se inicie automaticamente otro container que haga lo mismo que el anterior, para que no se caiga el sistema.
* Nos abstrae de la complejidad de la creacion de contenedores.

#### Beneficios Docker

* **Portabilidad** => Podemos desplegar en cualquier sistema y que funcione todo igual
* **Ligereza** => Las imagenes y los containers son cosas muy livianas.
* **Autosuficiente** => Un contenedor solo tiene lo necesario, no mas ni menos.

#### Docker vs Virtual Machine 👀️

* **VM** provee una capa que es un mini SO que construye ambientes virtuales de Hardware, y sobre estos, instala el SO y recien ahi, pone la aplicacion a correr. La capa de Hypervisor es liviana, pero el resto es pesado en recursos.
* **Docker** NO virtualiza hardware, virtualiza procesos. No tengo una VM corriendo, tengo un espacio de trabajo en el Kernel donde instancio las librerias/binarios que necesito para la aplicacion. Instalo las dependencias minimas que necesita la aplicacion para funcionar. Son procesos muy livianos.

#### Imagenes de Docker

* Una imagen es la plantilla de un contenedor. Como quiero que sea un contenedor.
* Las imagenes son inmutables, solo cambian si yo lo hago. Lo unico que puede tener cambios es el Container.
* Se pueden crear localmente.
* Existen Registry de imagenes. Son como repositorios donde se guardan las imagenes de Docker.
* Estan compuestas por muchas capas.

#### Qué son los layers de las imagenes?

* Cada imagen tiene uno o mas layers
* Permite reutilizar varios layers en muchas imagenes
* Se identificas con hashes
* Si dos layers tienen el mismo hash, solo se almacena uno de ambos.
* Cada layer representa un statement/comando del dockerfile.

#### Dockerfile

* Es el plano de la imagen, es la plantilla para crear la imagen.
* Son los comandos a ejecutar.
* Soporta variables y parametros.
* Se puede versionar. Es un archivo de texto plano con codigo.
* El orden de los comandos pueden optimizar a las imagenes
* Solo podemos modificar a una imagen mediante su dockerfile.

#### Registrys

* Son los repositorios de Docker
* Almacenan las imagenes
* Las versiones de una misma imagen se llaman tag. latest es un tag especial, representa a la ultima imagen.
* Pueden ser privadas o publicas. DockerHub es la mas conocida y publica (que tambien brinda servicios privados).
* Siempre deben tener un certificado asociado seguro. Si estoy experimentando de forma local, podemos correr sin certificado, pero en produccion no se puede.
* Docker NO conecta con registrys inseguras.
* Las registrys privadas necesitan un login previo, una autenticacion.

# Ejecutando Containers

#### Imagenes base

Estan creadas desde scratch, y basadas en Linux usualmente (como debian, etc).
OpenJDK, PHP, Ruby, y otros tambien entregan imagenes base. Estan albergadas en **dockerhub.io**

#### Imagenes Parents

Son las mas usadas, se crean desde una imagen base, y son de uso mas especifico, como Wordpress, Python con alguna herramienta especifica, etc..
Tambien estan en dockerhub, pueden pesar mucho y tener muchas vulnerabilidades, ya que son mantenidas por la comunidad

## Comandos 👀️ 

Correr el container de apache:

`docker run httpd:2.4`

* Si no se tiene un container localmente, se va automaticamente a buscarla a dockerhub
* Al hacerlo en consola, perdemos la consola, podemos ejecutar `docker run -td httpd:2.4` para correrlo a segundo plano y no perder la consola. Esto nos devolvera un hash solo.
* `docker ps` => Listar los container en ejecucion
* `docker stop (idContainer)` => Stoppear el container
* `docker start (idContainer)` => Reiniciar container
* `docker rm (idContainer)` => Borrar container, si esta corriendo no puedo borrarlo, a menos que corra `docker rm --force (idContainer)` para borrarlo a la fuerza
* Docker genera los nombres de los container de manera random, los podemos nombrar nosotros mismos y referenciarnos a los mismos por eso y no por su ID `docker run -td --name (nombre) httpd:2.4`

#### Acceder al container por localhost

`docker run -td --name webserver -p8089:80 httpd:2.4`

Y podemos acceder mediante `localhost://8089 `

#### Comandos dentro del container en ejecucion

Quiero ejecutar de forma interactiva en el container un comando (en este caso, ps -aux)

`docker exec -ti webserver ps -aux`

Nos va a dar error, entonces, instalamos lo necesario, updateamos e instalamos propcs

`docker exec -ti webserver apt-get update; docker exec -ti webserver apt-get install -y procps`

Ahora si podremos correr el primer comando y ver los procesos dentro del container.

Si quiero ver cuanto esta usando el container de mis recursos, RAM, etc..

`docker stats (nombreContainer)`

Sin el nombre del container, va a traer de todos los containers en ejecucion.

Puedo limitar la cantidad de cores que puede usar mi container

`docker run -td --cpus 2 8089:80 --name webserver httpd:2.4`

Existen comandos para consumir mucha CPU nuestra, hasta el punto de asesinar a nuestro propio equipo, o que el container muera ya que el CPU ya no puede responder como debe.

Limitar memoria a 400 megas

`docker run -td --cpus 2 --memory 400m -p 8089:80 --name webserver httpd:2.4`

El container seguira viendo la misma cantidad de memoria RAM que antes, la total disponible, pero solo estará limitado a 400m

Si quiero **ver los logs de mi container**

`docker logs webserver`

