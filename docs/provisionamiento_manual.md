# Configuración manual y provisionamiento de dos MVs, y despliegue en ellas de la aplicación

Este tutorial fue realizado en una máquina física con el sistema operativo Ubuntu 16.04.5 LTS (Xenial Xerus).

Se destina a enseñar paso a paso a configurar manualmente dos máquinas virtuales en Microsoft Azure, a provisionarlas con el necesario para el despliegue de la aplicación de este repositorio, y a desplegarla en esas máquinas virtuales.

## Instalación y configuración de Ansible

El provisionamiento de la máquina virtual se hará usando Ansible. Para instalarlo en nuestra máquina local (no es necesario instalarlo en las máquinas virtuales) hay que tener la utilidad de instalación de módulos `pip`. Una vez teniendo `pip` instalado, Ansible se instalará utilizando el comando

`pip install paramiko PyYAML jinja2 httplib2 ansible`

Se puede comprobar la correcta instalación de Ansible con el comando `ansible --version`, que deberá retornar un output similar a este:

```
ansible 2.7.4
config file = /etc/ansible/ansible.cfg
configured module search path = [u'/home/miguel/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
ansible python module location = /home/miguel/.local/lib/python2.7/site-packages/ansible
executable location = /usr/bin/ansible
python version = 2.7.12 (default, Nov 12 2018, 14:36:49) [GCC 5.4.0 20160609]
```

La configuración de Ansible se encuentra por defecto en el directorio `/etc/ansible`, donde se encontrará el fichero `hosts`. Este fichero tendrá una grande cantidad de líneas comentadas que sirven de ejemplo a nuevos usuarios de Ansible; sin embargo se pueden borrar sin problemas. En seguida, hay que añadir al fichero `hosts` las siguientes líneas:

```
[local]
127.0.0.1   ansible_connection=local

[azure]
ccazureinfo.uksouth.cloudapp.azure.com
ccazureviajes.uksouth.cloudapp.azure.com

[azureInfo]
ccazureinfo.uksouth.cloudapp.azure.com

[azureViajes]
ccazureviajes.uksouth.cloudapp.azure.com
```

Con estas líneas, estamos indicando a Ansible cuales los hosts donde se ejecutará el provisionamiento. Ansible permite agrupar los hosts en grupos; en este caso el grupo `local` contiene la dirección IP del localhost y los grupos `azure`, `azureInfo` y `azureViajes` contiene las direcciones DNS de las máquinas virtuales que se crearán en Azure. Ansible por defecto se comunica con los hosts via SSH, `ansible_connection=local` indica a Ansible que la conexión al host `127.0.0.1` es local y no via SSH.

Se asume que las máquinas virtuales a crear se llamarán `ccazureinfo` y `ccazureviajes`, que recibirán respectivamente el microservicio de Información al Cliente y de Gestión de Viajes. Cambie el nombre en las direcciones DNS arriba si desea poner otros nombres a las máquinas virtuales.

En seguida, hay que crear también en el directorio `/etc/ansible` un fichero .yml. El nombre no tiene importancia desde que su nombre termine con `.yml`. Se asumirá que el fichero se llama `playbook.yml`, sin embargo `playbook` puede ser sustituido por cualquier otro nombre.

En el fichero `playbook.yml` (o cualquier que sea su nombre) hay en seguida que copiar y pegar el contenido de este [fichero .yml](https://github.com/migueldgoncalves/CCproj_1819/blob/master/provision/playbook.yml).

Ansible estará así configurado y listo para provisionar máquinas.

## Creación y configuración de las máquinas virtuales

Este tópico tendrá que hacerse 2 veces, una por cada máquina virtual. Las diferencias en el proceso de creación de cada máquina son mínimas, como se verá enseguida.

Para empezar, hay que acceder al [panel de Azure](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/01.png). En él seleccionar "Máquinas virtuales" en el menú de la izquierda y [pulsar](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/02.png) en "Crear máquina virtual".

Aparecerá una pantalla de [Datos Básicos](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/03.png), una de las más importantes. Hay que seleccionar la suscripción Azure que se quiere usar para crear la máquina virtual. Después, elegir un grupo de recursos o crear un nuevo. Las dos máquinas pueden estar en el mismo grupo o en distintos. El nombre de la máquina virtual puede ser cualquier uno, aunque en este tutorial se asume que es respectivamente `ccazureinfo` y `ccazureviajes`. Elegir como región "Sur de Reino Unido"; elegir otras regiones cambiará la dirección DNS de la máquina virtual. Es importante elegir una región en la Unión Europea de modo a que se apliquen las leyes europeas. No será necesaria redundancia de la infraestructura.

Como imágen utilizar Ubuntu Server 16.04 LTS, con la cual se ha probado este tutorial. Una máquina virtual B1s estándar con 1 virtual CPU y 1 GB de RAM es suficiente para correr la aplicación; la série B de máquinas virtuales de Azure se destina a ofrecer una forma económica de hacer despliegues que no necesiten de desempeño total del CPU de forma permanente.

La autenticación se hará con Clave pública SSH; para eso es recomendable crear un par de llaves SSH que por defecto se guardan en el directorio `~/.ssh`. Hay que copiar el contenido del fichero <nombre_llave>.pub, donde <nombre_llave> es el nombre atribuido a la llave, al campo Clave pública SSH. Dejar el campo de Azure Active Directory desactivado.

Como puertos de entrada, hay que elegir el HTTP (puerto 80) y el SSH (puerto 22), y apenas esos.

Pasar a la pantalla siguiente, [Discos](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/04.png). El disco del sistema operativo puede ser HDD estándar para ahorrar dinero, no es necesario más para correr la aplicación. Crear y adjuntar un nuevo disco de datos de 1 GB, vacío y de tipo también HDD estándar, con un nombre cualquier. Cada máquina virtual tendrá el suyo. Mantener seleccionada la opción por defecto de Sí en "Usar discos administrados".

No hay necesidad de cambiar las opciones por defecto en las pantallas de [Redes](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/05.png), [Administración](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/06.png), [Configuración de invitado](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/07.png) y [Etiquetas](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/08.png). Si todo está de acuerdo con el esperado en la pantalla de [Revisar y crear](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/09.png), seleccionar Crear. La [implementación de los diferentes recursos](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/10.png) deberá tardar 3-4 minutos.

Acceder al recurso de la máquina virtual creada pulsando en su enlace, y en su [panel](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/11.png) pulsar en Configurar abajo de Nombre DNS. Irá aparecer una dirección IP en el panel, pero por defecto es dinámica y cambiará cada vez que se detenga y inicie la máquina virtual. En la [pantalla de configuración](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/12.png) añadir como Etiqueta de nombre DNS `ccazureinfo` o `ccazureviajes` consoante la máquina virtual (o cualquier que sean los nombres de la máquinas virtuales), y pulsar en Guardar. Los cambios se harán efectivos en segundos, y entonces ya se podrá proceder al provisionamiento de las máquinas virtuales y al despliegue de la aplicación.

## Provisionamiento de las máquinas virtuales y despliegue remoto de la aplicación

El fichero `playbook.yml`, que estará en el directorio `/etc/ansible`, consiste en un playbook de Ansible que no solo provisionará las máquinas virtuales sino también desplegará la aplicación para que escuche en el puerto 80. Para ejecutarlo hay que entrar con la consola en dicho directorio y ejecutar `ansible-playbook playbook.yml`. Cambiar el nombre del fichero si necesario. Hay que decir que no sería necesario entrar en dicho directorio, sino escribir la ruta completa del fichero en el comando.

El provisionamiento y después el despliegue tardarán unos minutos; después de ese tiempo la [consola](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/13.png) tendrá un aspecto similar a esto (tener en cuenta que esta imagen fue obtenida cuando el playbook provisionaba una sola máquina virtual), y la aplicación está lista a recibir pedidos HTTP.

Bastará entonces escribir en un browser `ccazureinfo.uksouth.cloudapp.azure.com` o `ccazureviajes.uksouth.cloudapp.azure.com` (asumiendo que las máquinas se llamam `ccazureinfo` y `ccazureviajes` y tienen esas direcciones DNS) para se acceder a la [ruta raiz](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/16.png) de la aplicación, y desde ahí a todas las otras. Ver [aquí](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/17.png) y [aquí](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/18.png) capturas de otras rutas del microservicio de Información al Cliente.

Para detener la ejecución de las máquinas hay que detenerlas en el panel de Azure. Ejecutar el playbook `playbook.yml` de nuevo ya producirá un [output diferente](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/Provisionamiento/14.png), similar a esto, una vez que las tasks ya fueron ejecutadas antes. La excepción es la task "Install application using Maven", que siempre retornará `changed` y no `ok` una vez que en esa task se ejecuta un comando directamente en la shell.
