# Mounting-CHEATSHEET

# .E01 Encase files

###### 1. Maak een mount-directory aan voor de EnCase E01 image
mkdir -p /mnt/EWF_mount

###### 2. Mount de E01 image als een virtueel bestandssysteem
sudo ewfmount carimage.E01 /mnt/EWF_mount

###### 3. Controleer of het bestand correct gemount is
ls /mnt/EWF_mount

###### 4. Koppel de EWF image aan een loopback device en toon het toegewezen loopback apparaat
sudo losetup -Pf --show /mnt/EWF_mount/ewf1
lsblk

###### 5. Mount de eerste partitie van de image als read-only
mkdir -p /mnt/image_partition1
sudo mount -o ro /dev/loop0p1 /mnt/image_partition1


# Automatische mount, calculeert offset en sector, en mount daarna READ ONLY voor bijvoorbeeld .dd & .raw

###### 1. Koppel het .dd of .raw bestand aan een loopback device en toon het toegewezen apparaat
sudo losetup -Pf --show driverbox.dd
lsblk 

###### 2. In het geval van meerdere partities, mount de eerste en tweede partitie als read-only
sudo mount -o ro /dev/loop0p1 /mnt/driver_box
sudo mount -o ro /dev/loop0p2 /mnt/driver_box1

###### 3. Unmount de partities als je klaar bent
sudo umount /mnt/driver_box
sudo umount /mnt/driver_box1

###### 4. Verwijder het loopback device om resources vrij te maken
sudo losetup -d /dev/loop0


# RAID Mounting

###### 1. Maak directories aan voor de E01-mounts
mkdir -p /mnt/RAID_image1 /mnt/RAID_image2

###### 2. Mount de EnCase E01 images met ewfmount
sudo ewfmount carimage1.E01 /mnt/RAID_image1
sudo ewfmount carimage2.E01 /mnt/RAID_image2

###### 3. Koppel de EWF bestanden aan loopback devices
sudo losetup /dev/loop10 /mnt/RAID_image1/ewf1
sudo losetup /dev/loop11 /mnt/RAID_image2/ewf1

###### 4. Controleer of de loopback devices correct zijn toegewezen
lsblk | grep loop

###### 5. Assembleer de RAID-array in de ‘run’ modus
sudo mdadm --assemble --run /dev/md0 /dev/loop10 /dev/loop11

###### 6. Controleer de RAID-status en bekijk of de array correct is opgebouwd
cat /proc/mdstat

# RAID Unmounting

###### 1. Stop de RAID-array en maak deze inactief
sudo mdadm --stop /dev/md0

###### 2. Verwijder de loopback devices om resources vrij te maken
sudo losetup -d /dev/loop10
sudo losetup -d /dev/loop11

###### 3. Unmount de eerder gemaakte mount directories
sudo umount /mnt/RAID_image1
sudo umount /mnt/RAID_image2
