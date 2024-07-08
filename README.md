# FINAL-INFRAESTRUCTURA

**Profesor:** Sergio Pernas

**Alumno:** Alejandro Areco

**Fecha:** 10/07/2024

## Agregar Volumen desde VirtualBox

1. Seleccionar la VM a utilizar
2. Ir a Configuracion -> Almacenamiento
3. Seleccionar "Controlador SATA"
4. Agregar volumen a gusto (Para este TP, se agrego un volumen de 500 MB)

## Formatear volumen

1. Identificar el volumen agregado con el comando *** lsblk *** 

```
alejandro@ubuntu:~$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0    7:0    0 39,1M  1 loop /snap/snapd/21184
loop1    7:1    0 63,9M  1 loop /snap/core20/2318
loop2    7:2    0 63,3M  1 loop /snap/core20/1822
loop3    7:3    0   87M  1 loop /snap/lxd/27428
loop4    7:4    0 38,8M  1 loop /snap/snapd/21759
loop5    7:5    0   87M  1 loop /snap/lxd/28373
sda      8:0    0   10G  0 disk
├─sda1   8:1    0    1M  0 part
└─sda2   8:2    0   10G  0 part /
**sdb      8:16   0  500M  0 disk**
sr0     11:0    1 1024M  0 rom

```

2. Una vez identificado el volumen, proceder a realizar el fomateo

```
alejandro@ubuntu:~$ sudo mkfs.ext4 /dev/sdb
[sudo] password for alejandro:
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 128000 4k blocks and 128000 inodes
Filesystem UUID: 67cca7a3-2cf2-4b0f-b7ff-127211f7e345
Superblock backups stored on blocks:
	32768, 98304

Allocating group tables: done
Writing inode tables: done
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done

```

3. Crear punto de montaje

``` 
alejandro@ubuntu:~$ sudo mkdir /mnt/tp-final
```

4. Montar Volumen

```
alejandro@ubuntu:~$ sudo mount /dev/sdb /mnt/tp-final/
```

5. Comprobar correcto montaje de volumen
```
alejandro@ubuntu:~$ ls /mnt/
tp-final
```

6. Agregar el volumen creado y montado a **fstab** para que monte automaticamente al iniciar el sistema, donde:
- **defaults:** Utiliza las opciones predetermindas de montaje
- **0:** No realizar respaldos con 'dump'
- **2:** 'fsck' verificara este sistema de archivos luego del sistema de archivos raiz.

```
alejandro@ubuntu:~$ sudo vim /etc/fstab

# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/c8548773-5349-435b-999b-7891ee6219ee / ext4 defaults 0 1
/swap.img	none	swap	sw	0	0
/dev/sdb  /mnt/tp-final/  ext4  defaults  0  2
```

## Clonar repositorio y crear rama "develop"

1. Utilizar 'git clone' para clonar repositorio

```
alejandro@ubuntu:~$ git clone git@gitlab.com:alejandro.areco/final-infraestructura.git
```

2. Verificar que nos encontremos en la rama 'main'

```
alejandro@ubuntu:~/final-infraestructura$ git checkout main
Already on 'main'
Your branch is up to date with 'origin/main'.
```

3. Crear rama "develop"

```
alejandro@ubuntu:~/final-infraestructura$ git checkout -b develop
Switched to a new branch 'develop'
```

4. Pushear nueva rama al repositorio remoto
```
alejandro@ubuntu:~/final-infraestructura$ git push origin develop
Enter passphrase for key '/home/alejandro/.ssh/id_ed25519':
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
remote:
remote: To create a merge request for develop, visit:
remote:   https://gitlab.com/alejandro.areco/final-infraestructura/-/merge_requests/new?merge_request%5Bsource_branch%5D=develop
remote:
To gitlab.com:alejandro.areco/final-infraestructura.git
 * [new branch]      develop -> develop
```

5. Verificar correcta creacion de rama
```
alejandro@ubuntu:~/final-infraestructura$ git branch
* develop
  main
```

6. Pasar de 'develop' a 'main'
```
alejandro@ubuntu:~/final-infraestructura$ git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
```
*La rama 'develop' contiene archivos genericos "index.html" y "proxy-reverso.conf". Las versiones finales de los mismos se encuentran en la rama 'main'*

```
alejandro@ubuntu:~/final-infraestructura$ git checkout develop
Switched to branch 'develop'
alejandro@ubuntu:~/final-infraestructura$ ls
index.html  proxy-reverso.conf  README.md
```


## Lanzar contenedor Apache con volumen creado y montado ##

1. Lanzar contenedor

```
alejandro@ubuntu:~/final-infraestructura$ sudo docker run -d --name apache -v /mnt/tp-final:/var/www/html ubuntu/apache2
[sudo] password for alejandro:
Unable to find image 'ubuntu/apache2:latest' locally
latest: Pulling from ubuntu/apache2
a84d279d584e: Pull complete
97ce21b9a892: Pull complete
901e02a5c8e5: Pull complete
Digest: sha256:42285b690decc8f0777a0c8f3f4a8a4cefb8011a6b3adfcdb79accab32298d67
Status: Downloaded newer image for ubuntu/apache2:latest
24a63218f2317cff94634f4c8f3978542c49eac8f99fc807406e7f8d9927fb50
```

2. Copiar archivo index.html al volumen montado
```
alejandro@ubuntu:~/final-infraestructura$ sudo cp index.html /mnt/tp-final/
alejandro@ubuntu:~/final-infraestructura$ ls /mnt/tp-final/
index.html  lost+found
```

3. Editar archivo index.html a gusto
```
alejandro@ubuntu:~/final-infraestructura$ cat index.html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Final Infraestructura de Servidores</title>
</head>
<body>
    <h1>Final Infraestructura de Servidores</h1>
    <h2>Profesor: Sergio Pernas<h2>
    <h2>Alumno: Alejandro Areco<h2>
    <h3>Fecha: 10/07/2024<h3>

</body>
</html>
```

## Lanzar Imagen Docker ##

1. Verificar que Docker este instalado y funcionando correctamente

```
sudo systemctl status docker
```
En caso de no estar instalado, realizarlo
```
sudo apt install docker.io
```

## Lanzar imagen docker con volumen asociado ##
```
alejandro@ubuntu:~$ sudo docker run -d -p 8081:80 -v /mnt/tp-final:/var/www/html ubuntu/apache2
a114a67af134e5577d7c523abfef04fcee7dff8fc9acd64ccdbf20329c792cf
```

Acceder al contenido a traves de la ip **192.168.0.240:8081**

## NGINX Proxy reverso ##

1. Verificar que nginx este instalado

```
sudo systemctl status nginx
```

En caso de no estarlo, realizarlo

```
sudo apt install nginx
```

2. Dentro de /etc/nginx/sites-available crear un archivo de configuración llamado “final-infraestructura”

```
alejandro@ubuntu:/etc/nginx/sites-available$ sudo vim final-infraestructura
```

3. Cargar el archivo creado con los siguientes parametros
```
server {
    listen 80;
    server_name final-infraestructura.com;

    location / {
        proxy_pass http://192.168.0.240:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

4. Dentro de “sites-enabled”, crear un archivo simbolico al archivo de configuración previamente creado
```
sudo ln -s /etc/nginx/sites-available/final-infraestructura /etc/nginx/sites-enabled/
```

5. En caso de que localmente el navegador no pueda resolver el nombre de dominio, editar el archivo “/etc/hosts” locamente

```
vim nano /etc/hosts
```
```
alejandroareco@Alejandros-MacBook-Pro ~ % sudo vim /etc/hosts

alejandroareco@Alejandros-MacBook-Pro ~ % cat /etc/hosts
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
255.255.255.255	broadcasthost
::1             localhost
#192.168.0.13	infra.miercoles
#54.165.128.153	laboratorio.miercoles
192.168.0.240	final-infraestructura.com
```

6. Verificar correcta configuracion de Nginx
```
alejandro@ubuntu:/etc/nginx/sites-available$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

7. Superado el checkeo de configuracion de Nginx, reiniciar el servicio
```
sudo systemctl restart nginx
```

8. Finalmente, probar acceso local en navegador web
```
http://final-infraestructura.com
```
