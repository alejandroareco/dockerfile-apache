# FINAL-INFRAESTRUCTURA

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


