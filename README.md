# Práctica de Particionado de Discos en Ubuntu
 Pablo Delgado Gamallo

---

## Índice
1. [Preparación del Entorno](#preparacion-del-entorno)
2. [Limpieza de los Discos](#limpieza-de-los-discos)
3. [Particionado MBR](#particionado-mbr)
4. [Montaje y Configuración en fstab](#montaje-y-configuracion-en-fstab)
5. [Particionado GPT](#particionado-gpt)
6. [Entrega](#entrega)

---

## Preparación del Entorno

Asegúrate de que los discos estén disponibles ejecutando:

```bash
lsblk
```

---

## Limpieza de los Discos

Eliminar todas las particiones previas en `/dev/sdb` y `/dev/sdc`:

```bash
sudo wipefs --all /dev/sdb
sudo wipefs --all /dev/sdc
```

---

## Particionado MBR

Usamos `fdisk` para crear las particiones en `/dev/sdb`:

```bash
sudo fdisk /dev/sdb
```

**Configuración de particiones:**
- `/dev/sdb1` → **20 GiB** (Primaria, ext4)
- `/dev/sdb2` → **10 GiB** (Primaria, ext4)
- `/dev/sdb3` → **15 GiB** (Primaria, ext4)
- `/dev/sdb4` → **Extendida**
  - `/dev/sdb5` → **10 GiB** (Lógica, ext4)
  - `/dev/sdb6` → **15 GiB** (Lógica, ext4)

Formateamos las particiones:

```bash
sudo mkfs.ext4 /dev/sdb1
sudo mkfs.ext4 /dev/sdb2
sudo mkfs.ext4 /dev/sdb3
sudo mkfs.ext4 /dev/sdb5
sudo mkfs.ext4 /dev/sdb6
```

---

## Montaje y Configuración en fstab

Creamos los puntos de montaje:

```bash
sudo mkdir -p /media/sdb1 /media/sdb2 /media/sdb3 /media/sdb5 /media/sdb6
```

Obtenemos los UUIDs:

```bash
sudo blkid
```

Editamos `/etc/fstab` y agregamos:

```plaintext
UUID=XXXXX /media/sdb1 ext4 defaults 0 2
UUID=XXXXX /media/sdb2 ext4 defaults 0 2
UUID=XXXXX /media/sdb3 ext4 defaults 0 2
UUID=XXXXX /media/sdb5 ext4 defaults 0 2
UUID=XXXXX /media/sdb6 ext4 defaults 0 2
```

Montamos todo:

```bash
sudo mount -a
```

Creamos archivos de prueba en cada partición:

```bash
echo "Soy la partición 1 MBR y tengo un tamaño de 20GiB" | sudo tee /media/sdb1/info.txt
echo "Soy la partición 2 MBR y tengo un tamaño de 10GiB" | sudo tee /media/sdb2/info.txt
echo "Soy la partición 3 MBR y tengo un tamaño de 15GiB" | sudo tee /media/sdb3/info.txt
echo "Soy la partición 5 MBR y tengo un tamaño de 10GiB" | sudo tee /media/sdb5/info.txt
echo "Soy la partición 6 MBR y tengo un tamaño de 15GiB" | sudo tee /media/sdb6/info.txt
```

---

## Particionado GPT

Creamos la tabla de particiones GPT en `/dev/sdc`:

```bash
sudo parted /dev/sdc mklabel gpt
sudo parted /dev/sdc mkpart primary ext4 1MiB 20GiB
sudo parted /dev/sdc mkpart primary ext4 20GiB 35GiB
```

Formateamos las particiones:

```bash
sudo mkfs.ext4 /dev/sdc1
sudo mkfs.ext4 /dev/sdc2
```

Montamos las particiones:

```bash
sudo mkdir -p /media/sdc1 /media/sdc2
sudo mount /dev/sdc1 /media/sdc1
sudo mount /dev/sdc2 /media/sdc2
```

Editamos `/etc/fstab`:

```plaintext
UUID=XXXXX /media/sdc1 ext4 defaults 0 2
UUID=XXXXX /media/sdc2 ext4 defaults 0 2
```

Creamos archivos de prueba en cada partición:

```bash
echo "Soy la partición 1 GPT y tengo un tamaño de 20GiB" | sudo tee /media/sdc1/info.txt
echo "Soy la partición 2 GPT y tengo un tamaño de 15GiB" | sudo tee /media/sdc2/info.txt
```

---



Se comprime la carpeta con el markdown y las imágenes:

```bash
zip -r practica.zip practica_particionado.md imagenes/
```



```bash
git init
git add .
git commit -m "Entrega de la práctica de particionado"
git branch -M main
git remote add origin https://github.com/tuusuario/practica-particionado.git
git push -u origin main
```

---


