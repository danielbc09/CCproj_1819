# Playbook de Ansible

Para realizar el Hito 3 he elegido Ansible como sistema de gestión de configuración. Se trata de una herramienta más sencilla de usar que Salt, y además está cubierta tanto por parte de los apuntes de la clase como por un [seminario](https://www.meetup.com/es-ES/Granada-Geek/events/255973562/?rv=ea1_v2&_xtd=gatlbWFpbF9jbGlja9oAJDViZWU1ZjJmLWViZDAtNGMwMi05ZTI3LTkxODY0M2YwZjYzNw) de la asignatura dedicado a la herramienta. Los Hitos 4 y 5 utilizaron también el playbook de Ansible creado en el Hito 3.

Los dos microservicios desarrollados, el de Información al Cliente y el de Gestión de Viajes, están basados en Java y usan una base de datos MongoDB. De esa manera, necesitarán no sólo de Git y de MongoDB, sino también de Java y Maven, este último para la gestión de las dependencias de la aplicación.

## Playbook

```
---
- hosts: azure
  become: yes
  tasks:
   - name: Install Java 8
     apt:
      name: default-jdk
      state: present

   - name: Install Git
     apt:
      name: git
      state: present

   - name: Install Maven
     apt:
      name: maven
      state: present

   - name: Add MongoDB public GPG key
     apt_key:
      keyserver: hkp://keyserver.ubuntu.com:80
      id: 9DA31620334BD75D9DCB49F368818C72E52529D4
      state: present

   - name: Add MongoDB repository
     lineinfile:
      line: "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/4.0 multiverse"
      dest: /etc/apt/sources.list.d/mongodb-org-4.0.list
      state: present
      create: yes

   - name: Install MongoDB
     apt:
      name: mongodb-org
      state: present
      update_cache: yes

   - name: Start MongoDB service
     service:
      name: mongod
      state: started

   - name: Clone Git project repository
     git:
      dest: Documents/CC1819
      repo: 'https://github.com/migueldgoncalves/CCproj_1819.git'
      version: HEAD
      
- hosts: azureInfo
  become: yes
  tasks:
   - name: Install application using Maven
     environment:
      PORT_INFO: 80
      PORT_VIAJES: 80
      URL_INFO: 'http://ccazureinfo.uksouth.cloudapp.azure.com'
      URL_VIAJES: 'http://ccazureviajes.uksouth.cloudapp.azure.com'
      SERVICIO: 1
     shell: mvn clean -Dtest=ClassInformacionTest,ServiceInformacionTest,LocalIntegracionTest install
     args:
      chdir: Documents/CC1819

   - name: Run application using Maven
     environment:
      PORT_INFO: 80
      PORT_VIAJES: 80
      URL_INFO: 'http://ccazureinfo.uksouth.cloudapp.azure.com'
      URL_VIAJES: 'http://ccazureviajes.uksouth.cloudapp.azure.com'
      SERVICIO: 1
     shell: mvn exec:java -Dexec.mainClass="CC1819.init.Main"
     args:
      chdir: Documents/CC1819
     async: 864000
     poll: 0
      
- hosts: azureViajes
  become: yes
  tasks:
   - name: Install application using Maven
     environment:
      PORT_INFO: 80
      PORT_VIAJES: 80
      URL_INFO: 'http://ccazureinfo.uksouth.cloudapp.azure.com'
      URL_VIAJES: 'http://ccazureviajes.uksouth.cloudapp.azure.com'
      SERVICIO: 2
     shell: mvn clean -Dtest=ClassViajesTest,ServiceViajesTest,LocalIntegracionTest,RemoteIntegracionTest install
     args:
      chdir: Documents/CC1819

   - name: Run application using Maven
     environment:
      PORT_INFO: 80
      PORT_VIAJES: 80
      URL_INFO: 'http://ccazureinfo.uksouth.cloudapp.azure.com'
      URL_VIAJES: 'http://ccazureviajes.uksouth.cloudapp.azure.com'
      SERVICIO: 2
     shell: mvn exec:java -Dexec.mainClass="CC1819.init.Main"
     args:
      chdir: Documents/CC1819
     async: 864000
     poll: 0
...
```
Ansible permite especificar las configuraciones y estados a obtener en las máquinas gestionadas utilizando recetas llamadas playbooks, con distintas tareas que indican a Ansible lo que hacer. Arriba se tiene el playbook `playbook.yml`, utilizado para provisionar dos máquinas virtuales Azure con el necesario para correr esta aplicación en su estado actual más su despliegue en dichas máquinas.

`---` y `...` delimitan el fichero .yml, el primero empiézalo y el segundo termínalo. Se sigue `- hosts: azure`, que indica que la receta se aplicará a los hosts del grupo `azure`, de acuerdo con el indicado en el fichero `hosts`. En este caso, el grupo `azure` contiene las direcciones DNS de las máquinas virtuales Azure que se crearán. Siendo la primera instrucción del playbook, hay que empezarla con un `-`. Después, `become: yes` indica a Azure que hay que tornarse root para ejecutar las tasks. Por fim, `tasks:` delimita la lista de tareas del playbook.

### Instalación del Java 8, Git y Maven

```
- name: Install Java 8
  apt:
   name: default-jdk
   state: present

- name: Install Git
  apt:
   name: git
   state: present

- name: Install Maven
  apt:
   name: maven
   state: present
```
Las primeras tareas del playbook instalan respectivamente el Java 8 (mientras no se actualice el paquete `default-jdk`), Git y Maven. Todas las tareas Ansible son empezadas con un `-`, seguidas de su nombre.
`apt` es un módulo Ansible para la gestión de paquetes `apt`, que en estas tareas recibe 2 parámetros: `name`, que corresponde al nombre del paquete; y `state`, dónde se indica el estado deseado del paquete. `present` es el estado por defecto, se asegura de que el paquete está instalado y una vez instalado no lo actualizará mismo que exista una versión más reciente.

### Instalación y arranque del MongoDB

```
- name: Add MongoDB public GPG key
  apt_key:
   keyserver: hkp://keyserver.ubuntu.com:80
   id: 9DA31620334BD75D9DCB49F368818C72E52529D4
   state: present

- name: Add MongoDB repository
  lineinfile:
   line: "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/4.0 multiverse"
   dest: /etc/apt/sources.list.d/mongodb-org-4.0.list
   state: present
   create: yes

- name: Install MongoDB
  apt:
   name: mongodb-org
   state: present
   update_cache: yes

- name: Start MongoDB service
  service:
   name: mongod
   state: started
```
Una vez que el paquete oficial de MongoDB, `mongodb-org`, no está disponible por defecto en los repositorios Ubuntu, hay que añadir el repositorio oficial antes de instalar MongoDB. Existe un paquete de MongoDB disponible por defecto para Ubuntu, `mongodb`, todavía no es mantenido de forma oficial por MongoDB.

La primera tarea utiliza el módulo `apt-key` para añadir a la máquina la llave pública del repositorio de MongoDB. Todos los repositorios Ubuntu deben estar firmados por una llave privada por razones de seguridad. El módulo recibe como parámetros `keyserver`, que indica al servidor donde obtener la llave; `id`, el id de la llave a obtener, y `state`, que en este caso es igual a `present` para garantizar que la llave está presente en la máquina.

Teniendo ya la llave, la segunda tarea incluí el repositorio en la lista de repositorios de la máquina, creando un fichero en el directorio de fuentes de la máquina y añadiéndole una línea de texto con el enlace del repositorio. `lineinfile` es un módulo Ansible que garantiza que una determinada línea de texto está presente en un fichero. `line` indica la línea a añadir; `dest` el fichero que recibirá la línea; `state: present` garantiza que la línea está presente en el fichero; y `create: yes` crea el fichero si no existe, lo que va a ocurrir en la primera vez que se ejecute el playbook.

La tercera tarea instala entonces el MongoDB en el sistema, utilizando ya el paquete oficial. La diferencia con las instalaciones de Java, Git y Maven es la presencia de `update_cache: yes`, que ejecuta el equivalente a `apt-get update` en el sistema de modo a descargar el paquete antes de la instalación.

La utilización de MongoDB requiere que su servicio sea iniciado previamente; la cuarta tarea se encarga de eso usando el módulo Ansible `service`. `name` indica el nombre del servicio a gestionar, siendo `mongod` el servicio que queremos iniciar; y `state: started` garantiza que el servicio está iniciado o que se inicie si no está.

### Despliegue de la aplicación

```
   - name: Clone Git project repository
     git:
      dest: Documents/CC1819
      repo: 'https://github.com/migueldgoncalves/CCproj_1819.git'
      version: HEAD
      
- hosts: azureInfo
  become: yes
  tasks:
   - name: Install application using Maven
     environment:
      PORT_INFO: 80
      PORT_VIAJES: 80
      URL_INFO: 'http://ccazureinfo.uksouth.cloudapp.azure.com'
      URL_VIAJES: 'http://ccazureviajes.uksouth.cloudapp.azure.com'
      SERVICIO: 1
     shell: mvn clean -Dtest=ClassInformacionTest,ServiceInformacionTest,LocalIntegracionTest install
     args:
      chdir: Documents/CC1819

   - name: Run application using Maven
     environment:
      PORT_INFO: 80
      PORT_VIAJES: 80
      URL_INFO: 'http://ccazureinfo.uksouth.cloudapp.azure.com'
      URL_VIAJES: 'http://ccazureviajes.uksouth.cloudapp.azure.com'
      SERVICIO: 1
     shell: mvn exec:java -Dexec.mainClass="CC1819.init.Main"
     args:
      chdir: Documents/CC1819
     async: 864000
     poll: 0
      
- hosts: azureViajes
  become: yes
  tasks:
   - name: Install application using Maven
     environment:
      PORT_INFO: 80
      PORT_VIAJES: 80
      URL_INFO: 'http://ccazureinfo.uksouth.cloudapp.azure.com'
      URL_VIAJES: 'http://ccazureviajes.uksouth.cloudapp.azure.com'
      SERVICIO: 2
     shell: mvn clean -Dtest=ClassViajesTest,ServiceViajesTest,LocalIntegracionTest,RemoteIntegracionTest install
     args:
      chdir: Documents/CC1819

   - name: Run application using Maven
     environment:
      PORT_INFO: 80
      PORT_VIAJES: 80
      URL_INFO: 'http://ccazureinfo.uksouth.cloudapp.azure.com'
      URL_VIAJES: 'http://ccazureviajes.uksouth.cloudapp.azure.com'
      SERVICIO: 2
     shell: mvn exec:java -Dexec.mainClass="CC1819.init.Main"
     args:
      chdir: Documents/CC1819
     async: 864000
     poll: 0
...
```
Teniendo todos los paquetes necesarios instalados, ya se puede desplegar la aplicación. En la primera tarea, el módulo `git` permite hacer clone del repositorio indicado en `repo` para el directorio indicado en `dest`. `version` es un parámetro opcional, y indica la versión del repositorio a descargar.

Las dos últimas tareas tienen que ser realizadas de forma individual en cada una de las máquinas, no solo para que la variable de entorno `SERVICIO` sea distinta en cada una sino también para que solo se ejecuten los tests adecuados a cada microservicio. El grupo `azureInfo` contiene el enlace al despliegue del microservicio de Información al Cliente cuando terminado, y el grupo `azureViajes` contiene el enlace al despliegue del microservicio de Gestión de Viajes cuando terminado.

Más información sobre las variables de entorno utilizadas [aquí](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/funcionamiento.md#variables-de-entorno).

La segunda y cuarta tareas instalan el microservicio respectivo utilizando la herramienta de gestión de dependencias Maven. Hay que asignar valores a las 5 variables de entorno que comandan la aplicación; esto se hace con la palabra-llave `environment`, seguida del nombre de la variable y de su valor. En seguida se instala la aplicación con el comando `mvn clean install`, que es pasado por una shell `/bin/sh` usando el parámetro `shell`. La flag `-Dtest` permite indicar que tests se quieren ejecutar. `chdir: Documents/CC1819` indica que hay que cambiar al directorio `Documents/CC1819`, donde hemos clonado el repositorio de la aplicación, antes de ejecutar el comando.

Aunque la ejecución de la aplicación solo se realice en la tarea siguiente, una vez que `environment` solo cambia variables de entorno dentro de una única tarea es necesario crear todas las variables de entorno también en la instalación de la aplicación para que los tests de la misma funcionen de forma correcta.

Por fin, la tercera y quinta tareas ejecutan la aplicación. De nuevo se crean las 5 variables de entorno, y de nuevo el comando indicado en `shell` se ejecuta en el directorio `Documents/CC1819`. `mvn exec:java` ejecuta la aplicación utilizando Java; `-Dexec.mainClass="CC1819.init.Main` indica que el método main() de la aplicación se encuentra en el fichero `CC1819.init.Main`.
