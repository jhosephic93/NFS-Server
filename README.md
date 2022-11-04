# NFS SERVER

1. Actualizar OS Linux

```console
$ sudo apt-get update -y
```

## Setting up Network File System (NFS)

```console
$ sudo apt search nfs-kernel-server
```

1. Install NFS-KERNEL-SERVER

```console
$ sudo apt-get install nfs-kernel-server -y
```

2. Verificar Status de servicio de NFS-KERNEL-SERVER.

```console
$ sudo systemctl status nfs-server
```

3. Crear Carpeta a compartir.

```console
$ sudo mkdir /mnt/nfs-share
```

4. Permisos de usuario y grupo para la carpeta **nfs-share**.

```console
$ sudo chown -R ubuntu:ubuntu /mnt/nfs-share/
$ sudo chown -R nobody:nogroup /mnt/nfs-share/ #Opcional
```

### Opcional

-  La primera linea busca todos los directorios de nuestra carpeta **/mnt/nfs-share** y corre el comando chmod para dar a cada direcotrio "755", lo mismo con la segunda linea solo que archivos.

```console
$ sudo find /mnt/nfs-share/ -type d -exec chmod 755 {} \;
$ sudo find /mnt/nfs-share/ -type f -exec chmod 644 {} \;
```

5. Permisos a quien puede acceder a la carpeta.

```console
$ id
$ sudo nano /etc/exports #Agregar la siguiente linea. | Asegurarse de cambiar el valor de anounid con el valor recuperado de uid y remplazar anongid con el valor recuperado de gid.
/mnt/nfs-share *(rw,all_squash,insecure,async,no_subtree_check,anonuid=1000,anongid=1000)
```

- El asterisco define que todas las direcciones de IP deben poder acceder a esta carpeta compartida. Puede cambiar esto para permitir IP especificas o un rango de IP cambiando el "*" a la IP.

- Todas las opciones contenidas entre paréntesis se aplican solo para esa dirección IP, puede definir varias IP diferentes cada una con su propio conjunto de opciones.
    - **rw** – Esta opción permite tanto solicitudes de lectura como de escritura en el volumen NFS.
    - **all_squash** – Esta opción asignará todos los uids y gids al usuario anónimo.
    - **insecure** – Esta opción permite a los clientes con una implementación NFS que no utiliza un puerto NFS reservado.
    - **async** – Esta opción permite que el servidor NFS rompa el protocolo NFS para mejorar el rendimiento a costa de que los datos se corrompan potencialmente si el servidor falla.
    - **no_subtree_check** – Esto deshabilita la verificación de subárboles, mientras que tiene un costo de seguridad, puede mejorar la confiabilidad del servidor NFS. Puede leer más sobre esto en la página de documentación de exportaciones que vinculamos anteriormente.
    - **anonuid** – Este es el UID que debe utilizar para un usuario que se conecta de forma anónima.
    - **anongid** – Este es el GID que se utilizará para un usuario que se conecta de forma anónima.

6. Para que nuestra carpeta recien agregada sea accesible a traves del protocolo NFS, debemos ejecutar.

```console
$ sudo exportfs -ra
```

7. Recuperar direccion de IP para conectarnos desde el cliente al Server NFS.

```console
$ hostname -I
```

## Cliente

1. Installar, montar y desmontar la carpeta en el cliente.

```console
$ sudo apt install nfs-common
$ sudo mount nfs-server-ip:/mnt/nfs-share /mnt
$ sudo umount /mnt
```

### Mas Info -> https://pimylifeup.com/raspberry-pi-nfs/