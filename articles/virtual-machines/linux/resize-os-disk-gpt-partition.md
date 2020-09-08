---
title: Redimensionner un disque de système d’exploitation ayant une partition GPT | Microsoft Docs
description: Cet article fournit des instructions sur le redimensionnement d’un disque de système d’exploitation ayant une partition GPT.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 30a960c3ed76788158b15022947fec49a95ae299
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375208"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Redimensionner un disque de système d’exploitation ayant une partition GPT

> [!NOTE]
> Ce scénario s’applique uniquement aux disques de système d’exploitation ayant une partition GPT (table de partition GUID).

Cet article explique comment augmenter la taille d’un disque de système d’exploitation ayant une partition GPT dans Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Déterminer si le disque du système d’exploitation a une partition MBR ou GPT

Pour déterminer si la partition de disque a été créée à l’aide d’une partition d’enregistrement de démarrage principal (MBR) ou d’une partition GPT, utilisez la commande `parted`.

### <a name="mbr-partition"></a>Partition MBR

Dans la sortie suivante, **Partition Table** affiche une valeur de **msdos**. Cette valeur identifie une partition MBR.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>Partition GPT

Dans la sortie suivante, **Partition Table** affiche la valeur de **gpt**. Cette valeur identifie une partition GPT.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Si votre machine virtuelle a une partition GPT sur votre disque de système d’exploitation, augmentez la taille du disque du système d’exploitation.

## <a name="increase-the-size-of-the-os-disk"></a>Augmenter la taille du disque du système d’exploitation

Les instructions suivantes s’appliquent aux distributions approuvées par Linux.

> [!NOTE]
> Avant de continuer, effectuez une copie de sauvegarde de votre machine virtuelle, ou prenez un instantané de votre disque de système d’exploitation.

### <a name="ubuntu"></a>Ubuntu

Pour augmenter la taille du disque du système d’exploitation dans Ubuntu 16.x et 18.x :

1. Arrêtez la machine virtuelle.
1. Augmentez la taille du disque du système d’exploitation à partir du portail.
1. Redémarrez la machine virtuelle, puis connectez-vous à la machine virtuelle en tant qu'utilisateur **racine**.
1. Vérifiez que le disque du système d’exploitation affiche désormais une taille de système de fichiers accrue.

Comme indiqué dans l’exemple suivant, le disque du système d’exploitation a été redimensionné à 100 Go à partir du portail. Le système de fichiers **/dev/sda1** monté sur **/** affiche désormais 97 Go.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

Pour augmenter la taille du disque du système d’exploitation dans SUSE 12 SP4, SUSE SLES 12 pour SAP, SUSE SLES 15 et SUSE SLES 15 pour SAP :

1. Arrêtez la machine virtuelle.
1. Augmentez la taille du disque du système d’exploitation à partir du portail.
1. Redémarrez la machine virtuelle.

Une fois que la machine virtuelle a redémarré, procédez comme suit :

1. Accédez à votre machine virtuelle en tant qu’utilisateur **racine** à l’aide de la commande suivante :

   ```
   # sudo -i
   ```

1. Utilisez la commande suivante pour installer le package **growpart** qui sera utilisé pour redimensionner la partition :

   ```
   # zypper install growpart
   ```

1. Utilisez la commande `lsblk` pour rechercher la partition montée sur la racine du système de fichiers (« / »). Dans ce cas, nous voyons que la partition 4 du d’appareil est montée sur/ :

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Redimensionnez la partition requise à l’aide de la commande `growpart` en utilisant le numéro de partition trouvé à l’étape précédente.

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Exécutez de nouveau la commande `lsblk` pour vérifier si la partition a été augmentée.

   La sortie suivante montre que la partition **/dev/sda4** a été redimensionnée à 46.5 Go :
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Identifiez le type de système de fichiers sur le disque du système d’exploitation à l’aide de la commande `lsblk` avec l’indicateur `-f` :

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. Selon le type de système de fichiers, utilisez les commandes appropriées pour le redimensionner.
   
   Pour **xfs**, utilisez la commande suivante :
   
   ```
   #xfs_growfs /
   ```
   
   Exemple de sortie :
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Pour **ext4**, utilisez la commande suivante :
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Vérifiez la taille du système de fichiers augmentée pour **df -Th** à l’aide de la commande suivante :
   
   ```
   #df -Thl
   ```
   
   Exemple de sortie :
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   Dans l’exemple précédent, nous pouvons voir que la taille du système de fichiers pour le disque du système d’exploitation a été augmentée.

### <a name="rhel"></a>RHEL

Pour augmenter la taille du disque du système d’exploitation dans RHEL 7.x avec LVM :

1. Arrêtez la machine virtuelle.
1. Augmentez la taille du disque du système d’exploitation à partir du portail.
1. Démarrez la machine virtuelle.

Une fois que la machine virtuelle a redémarré, procédez comme suit :

1. Accédez à votre machine virtuelle en tant qu’utilisateur **racine** à l’aide de la commande suivante :
 
   ```
   #sudo su
   ```

1. Installez le package **gptfdisk** requis pour augmenter la taille du disque du système d’exploitation.

   ```
   #yum install gdisk -y
   ```

1. Pour voir le secteur le plus volumineux disponible sur le disque, exécutez la commande suivante :

   ```
   #sgdisk -e /dev/sda
   ```

1. Redimensionnez la partition sans la supprimer à l’aide de la commande suivante. La commande **parted** offre une option nommée **resizepart** permettant de redimensionner la partition sans la supprimer. Le nombre 4 après **resizepart** indique le redimensionnement de la quatrième partition.

   ```
   #parted -s /dev/sda "resizepart 4 -1" quit
   ```
    
1. Exécutez la commande suivante pour vérifier que la partition a été augmentée :

   ```
   #lsblk
   ```

   La sortie suivante montre que la partition **/dev/sda4** a été redimensionnée à 99 Go.

   ```
   [user@myvm ~]# lsblk
   NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   fd0                 2:0    1    4K  0 disk
   sda                 8:0    0  100G  0 disk
   ├─sda1              8:1    0  500M  0 part /boot/efi
   ├─sda2              8:2    0  500M  0 part /boot
   ├─sda3              8:3    0    2M  0 part
   └─sda4              8:4    0   99G  0 part
   ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
   ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
   ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
   ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
   ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
   └─rootvg-rootlv   253:5    0    2G  0 lvm  /
   sdb                 8:16   0   50G  0 disk
   └─sdb1              8:17   0   50G  0 part /mnt/resource
   ```

1. Utilisez la commande suivante pour redimensionner le volume physique (PV) :

   ```
   #pvresize /dev/sda4
   ```

   La sortie suivante montre que le volume physique a été redimensionné à 99,02 Go.

   ```
   [user@myvm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized

   [user@myvm ~]# pvs
   PV         VG     Fmt  Attr PSize   PFree
   /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
   ```

1. Dans l’exemple suivant, **/dev/mapper/rootvg-rootlv** est redimensionné de 2 Go à 12 Go (soit une augmentation de 10 Go) à l’aide de la commande suivante. Cette commande redimensionne également le système de fichiers.

   ```
   #lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Exemple de sortie :

   ```
   [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```
         
1. Vérifiez si **/dev/mapper/rootvg-rootlv** a une taille de système de fichiers augmentée à l’aide de la commande suivante :

   ```
   #df -Th /
   ```

   Exemple de sortie :

   ```
   [user@myvm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [user@myvm ~]#
   ```

> [!NOTE]
> Afin d’utiliser la même procédure pour redimensionner tout autre volume logique, modifiez le nom **lv** à l’étape 7.

## <a name="next-steps"></a>Étapes suivantes

- [Redimensionner le disque](expand-disks.md)
