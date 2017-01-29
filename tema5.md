## Tema 5

### Ejercicio 1. Instalar los paquetes necesarios para usar KVM. Se pueden seguir estas instrucciones. Ya lo hicimos en el primer tema, pero volver a comprobar si nuestro sistema está preparado para ejecutarlo o hay que conformarse con la paravirtualización.


Puedes consultar la instalación del paquete y sus detalles en el [ejercicio 4 de la Relacion 1 de Ejercicios](https://github.com/Chentaco/EjerciciosIV/blob/master/tema1.md), donde ya hicimos dicha instalación.  

En este apartado voy a comprobar que sigue siendo posible instalarlo. Uso el comando:  

```kvm-ok```  

![img](capturadekvmok)  
  
Como vemos, está activo. Procedemos ahora a instalar los paquetes. Ubuntu nos ofrece [una guía detallada](https://help.ubuntu.com/community/KVM/Installation) con los pasos para llevar a cabo la instalación. En ella, podemos consultar lo anteriormente mecionado, y realizar la instalación de estos. Como mi versión de Ubuntu es superior a la **10.04**, utilizaré el siguiente comando:  
  
 ```sudo apt-get install qemu-kvm libvirt-bin ubuntu-vm-builder bridge-utils```  
 
 Completada la instalación, seguimos con lo que comenta la guía. Ahora nos pide que hay que añadir los usuarios a **libvirtd** para poder crear máquinas virtuales desde la linea de comandos con nuestro usuario. Para ello, antes de nada, necesito instalar le paquete, y tras eso, añadir los usuarios correspondientes. La secuencia de comandos seria la siguiente:  
 
 Instaldo el paquete.  
 ```sudo apt-get install virtinst```  
 
 Añado mi usuario, en este caso "chentaco":  
 ```sudo adduser chentaco kvm```  
 ```sudo adduser chentaco libvirtd```  
 
 **NOTA**: Es necesario recargar el sistema, o recargar el usuario en este para que los cambios tengan efecto.  
 
 Podemos verificar la instalación usando el comando ```virsh -c qemu://system list```:  
   
 ![img](capturadevirsh)  
 
 Ya estaría listo para usar. Si lo deseamos podemos utilizar una interfaz gráfica, instalando el paquete **virt-manager**:  
   
 ```sudo apt-get install virt-manager```  
 
 Podemos ejecutarlo desde nuestro propio sistema, en el apartado de **Aplicaciones** debería aparecer. Siempre podemos usar el buscador de aplicaciones de Ubuntu (buscar Virtual Machine Manager, o Gestor de Maquinas Virtuales, si lo tienes en español).
 
 ![img](imagendelvirtmanager)  
 
 
 **NOTA 2**: Al abrirlo, probablemente nos indique que faltan paquetes, pero gracias a dios puedes instalarlo desde el propio mensaje, no tienes que buscar en otro lado.  

### Ejercicio 2.1. Crear varias máquinas virtuales con algún sistema operativo libre tal como Linux o BSD. Si se quieren distribuciones que ocupen poco espacio con el objetivo principalmente de hacer pruebas se puede usar CoreOS (que sirve como soporte para Docker) GALPon Minino, hecha en Galicia para el mundo, Damn Small Linux, SliTaz (que cabe en 35 megas) y ttylinux (basado en línea de órdenes solo).
 
He usado **MiniNo** y **SliTaz**. Debes descargar sus imagenes desde sus páginas oficiales[[1](http://minino.galpon.org/gl)],[[2](http://www.slitaz.org/es/)]:  

* En primer lugar creo los correspondientes discos duros, cada uno con su qcow2 correspondiente:  
  
  ```qemu-img create -f qcow2 minino.qcow2 8G```  
  ```qemu-img create -f qcow2 slitaz.qcow2 8G```  

* Procedo a la instalación de las imagenes, creando asi las máquinas virtuales:  
  
```
qemu-system-x86_64 -machine accel=kvm -hda minino.qcow2 -cdrom /home/chentaco/Documentos/tema5/minino-artabros-2.1_minimal.iso -boot d
```  

```
qemu-system-x86_64 -machine accel=kvm -hda slitaz.qcow2 -cdrom /home/chentaco/Documentos/tema5/slitaz-4.0.iso -boot d
```  

-**MiniNo**:  
![img](imagendeMinino)

-**SliTaz**:  
![img](imagendesliztaz)  

Y continuando la instalación en ambas máquinas virtuales, siguiendo el instalador, hasta finalmente tenerlas funcionando.  

**NOTA 3**: El path es de mi pc, donde están las imagenes. Que cada uno cambie por el path donde están las imagenes que vaya a usar.

### Ejercicio 2.2. Hacer un ejercicio equivalente usando otro hipervisor como Xen, VirtualBox o Parallels.

Usaré la misma image de MiniNo anterior y la instalaré en VirtualBox:  

![img](instalacionVBMinino)  

### Ejercicio 3. Crear un benchmark de velocidad de entrada salida y comprobar la diferencia entre usar paravirtualización y arrancar la máquina virtual simplemente con *qemu-system-x86_64 -hda /media/Backup/Isos/discovirtual.img* 

La [documentación de Archilinux](https://wiki.archlinux.org/index.php/Benchmarking#dd) ofrece informaicón sobre como hacer usar herramienta.  
  
Arranco la máquina en primer lugar con el comando mostrado y uso el siguiente comando:  
  
```dd if=/dev/zero of=./tmp bs=1M count=1024 conv=fdatasync,notrunc```
  
 ![img](comando) 
  
 Ahora realizo lo mismo que en el paso anteior, pero esta vez, en lugar del comando para arrancarla automáticamente, uso paravirtualización:  
 
 ![img](paravirtualizacion)
 
 Podemos observar una diferencia destacable en lo que viene siendo los MB/s, donde es bastnate menor si **NO** usamos paravirtualizacion.
  
### Ejercicio 4. Crear una máquina virtual Linux con 512 megas de RAM y entorno gráfico LXDE a la que se pueda acceder mediante VNC y ssh.   

Necesitamos el entorno LXDE, y un sistema que nos proporciona esto, es **Lubuntu**. Realizamos los mismos pasos que en el ejercicio 2: crear el disco e instalar la máquina en él:  

![img](veoquetodook)  

Ahora, para acceder a ella, necesito un cliente VNC. En ubuntu disponemos del paquete **vinagre**. Procedos a su instalación:  

```sudo apt-get install vinagre```  

Buscamos la dirección de interfaz NAT de acceso a KVM, utilizo el comando ```ifconfig virb0```, donde **virb0* es dicha interfaz:  

![img](imagendondevirbo0)  

Usando esa dirección ip, realizo la conexión con vinagre:  

![img](imagenvinagre)  

Ahora trabajemos con el **SSH**. Recordando un poco lo mencionado en DAI, el puerto que SSH utiliza es el 22, y tenemos que hacer que se redirija a algún otro para poder utilizarlo desde la máquina anfitriona. ¿Por qué no 2211? Añado al comando de ejecución automática este parámetro de redirección:  

```qemu-system-x86_64 -boot order=c -drive file=~/qemu/hdd-lubuntu.img,if=virtio -m 512M -name lubuntu -redir tcp:2211::22
```

Solo quedaría conectarnos a dicho sistema con el comando siguiente:  

```ssh -p 2211 chentaco@localhost```  

Donde "chentaco" es mi usuario.

### Ejercicio 5. Crear una máquina virtual ubuntu e instalar en ella alguno de los servicios que estamos usando en el proyecto de la asignatura.

Como el proyecto final se desplegará sobre **Azure**, y dado que puedo trabajar con máquinas virtuales de dicho servicio, voy a aprovechar y realizar este ejercicio sobre una máquina virtual de Azure, y de paso mostrar como se trabaja con una.  

Lo que hacemos en primer lugar, para trabajar desde comandos en Ubuntu, es instalar el **CLI** de Azure, que a su vez necesita del paquete **npm** para instalarlo. Usamos los dos comandos siguientes:  

```sudo apt-get install npm```  
```sudo npm install -g azure-cli```  
  
Hecho esto, necesitamos ahora ingresar con nuestro credenciales, es decir, loguear desde la linea de comandos. Existe el comando ```azure login```, el cual nos abrirá en el navegador una ventana y nos dará una clave para ingresarla en dicha ventana. Después desde esta, ingresaremos nuestra cuenta de azure/outlook asociada, y estará listo para poderse usar.  

![img](capturadeazurelogin)  

Podemos empezar a crear nuestra máquina virtual. Para ver cuales están disponibles, podemos usar el comando ```azure vm image list``` y ver un listado de todas las disponibles. Yo escogí una de Ubuntu 14.04.  
  
 **NOTA 4**: Para utilizar comandos como "azure vm image list" y otros que veremos en la siguiente práctica, hay que indicar en el CLI el modo "asm" con ```azure config mode asm```.  
**NOTA 5**: La lista de imagenes es muy grande, es recomendable guardarlas en un archivo a parte para una mejor visualización.  

Una vez elegida, procedo a la instalación de mi máquina, utilizo el siguiente comando:  

```azure vm create <urlmaquina> <nombredelaimagen> <usuario> <contraseña> --location "Europe West" --ssh```  

Donde:  
- urlmaquina: la url que usaremos para verla desde azure y usada como nombre de la maquina virtual.
- nombredelaimagen: el nombre de la imagen que usamos obtenida desde la vm list.
- usuario: el nombre de mi usuario	
- contraseña: la contraseña. **OJO**: requiere una mayus, un caracter especial y un número.  

![img](semuestralainstalacion)  

Finalmente arranco la máquina con ```azure vm start <urlmaquina>```  

Ya tendriamos nuestra máquina funcionando. Ahora, sobre dicha máquina, voy a instalar el servicio **nginx**. Para ello, debo conect por conexion ssh. Uso ```ssh <urlmaquina>```  :

![img](enseñandocomosssh)  

Desde dentro, actualizo los repositorios de paquetese instalo nginx como hemos hecho siempre, con ```sudo apt-get install nginx```. También necesito abrir el puerto 80, que es el que usa nginx. Asi que, desde **fuera** ejecuto el siguiente comando:  
  
```azure vm endpoint create <urlmaquina> 80 80```  

Puedo ver que está abierto y que funciona desde el navegador:  

![img](listapuertos)  
![img](urlnginx)  
  
### Ejercicio 6. Instalar una máquina virtual con Linux Mint para el hipervisor que tengas instalado.

De nuevo, en **Virtualbox**, instalo la [imagen de Mint 17.3](https://www.linuxmint.com/edition.php?id=204):  

![img](mintinstalando)
![img](mintfuncionando)  