---
title: Attacher un disque de données à une machine virtuelle Linux
description: Utilisez le portail pour attacher un disque de données nouveau ou existant à une machine virtuelle Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 9b0602f526991be37b7a9cce1d621dc2138dec48
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279136"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Utiliser le portail pour attacher un disque de données à une machine virtuelle Linux 
Cet article vous explique comment attacher des disques nouveaux et existants à une machine virtuelle Linux par le biais du portail Azure. Vous pouvez également [attacher un disque de données à une machine virtuelle Windows dans le Portail Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Avant d’attacher des disques à votre machine virtuelle, lisez les conseils suivants :

* La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Les disques attachés aux machines virtuelles sont en réalité des fichiers .vhd stockés dans Azure. Pour plus d’informations, consultez [Introduction aux disques managés](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Après avoir attaché le disque, vous devez vous [connecter à la machine virtuelle Linux pour monter le nouveau disque](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Recherchez la machine virtuelle.
1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, cliquez sur **Machines virtuelles**.
3. Sélectionnez la machine virtuelle dans la liste.
4. Dans la page Machines virtuelles, dans **Essentials**, cliquez sur **Disques**.
   
    ![Ouverture des paramètres d’un disque](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Attacher un nouveau disque

1. Dans le panneau **Disques**, cliquez sur **+ Ajouter un disque de données**.
2. Cliquez sur le menu déroulant **Nom** et sélectionnez **Create disk** (Créer un disque) :

    ![Créer un disque géré Azure](./media/attach-disk-portal/create-new-md.png)

3. Entrez un nom pour votre disque géré. Vérifiez les paramètres par défaut, mettez-les à jour le cas échéant, puis cliquez sur **Créer**.
   
   ![Examen des paramètres d’un disque](./media/attach-disk-portal/create-new-md-settings.png)

4. Cliquez sur **Enregistrer** pour créer le disque géré et mettre à jour la configuration de la machine virtuelle :

   ![Enregistrer le nouveau disque géré Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Après qu’Azure a créé le disque et l’a attaché à la machine virtuelle, le nouveau disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**. Étant donné que les disques managés sont des ressources de niveau supérieur, le disque s’affiche à la racine du groupe de ressources :

   ![Disque géré Azure dans le groupe de ressources](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Association d'un disque existant
1. Dans le panneau **Disques**, cliquez sur **+ Ajouter un disque de données**.
2. Cliquez sur le menu déroulant **Nom** pour afficher une liste des disques managés disponibles dans votre abonnement Azure. Sélectionnez le disque géré à attacher :

   ![Attacher un disque géré Azure existant](./media/attach-disk-portal/select-existing-md.png)

3. Cliquez sur **Enregistrer** pour attacher le disque géré existant et mettre à jour la configuration de la machine virtuelle :
   
   ![Enregistrer les mises à jour Azure Managed Disk](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Après qu’Azure a attaché le disque à la machine virtuelle, le disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Se connecter à la machine virtuelle Linux afin de monter le nouveau disque
Vous devez exécuter SSH dans votre machine virtuelle Azure afin de partitionner, de formater et de monter votre nouveau disque pour que votre machine virtuelle Linux puisse l’utiliser. Pour plus d’informations, consultez l’article [Utilisation de SSH avec Linux sur Azure](mac-create-ssh-keys.md). L’exemple suivant permet de se connecter à une machine virtuelle avec l’entrée DNS publique de *mypublicdns.westus.cloudapp.azure.com* avec le nom d’utilisateur *azureuser* : 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Une fois connecté à votre machine virtuelle, vous êtes prêt à attacher un disque. Recherchez tout d’abord le disque à l’aide de `dmesg` (la méthode que vous utilisez pour découvrir votre nouveau disque peut varier). L’exemple suivant utilise dmesg pour filtrer les disques *SCSI* :

```bash
dmesg | grep SCSI
```

Le résultat ressemble à l’exemple suivant :

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Ici, *sdc* est le disque que nous recherchons. 

### <a name="partition-a-new-disk"></a>Partitionnement d’un nouveau disque
Si vous utilisez un disque existant contenant des données, passez au montage du disque. Si vous attachez un nouveau disque, vous devez partitionner ce disque.

> [!NOTE]
> Il est recommandé d’utiliser les dernières versions de fdisk ou séparées disponibles pour votre distribution.

Partitionnez le disque avec `fdisk`. Si la taille du disque est supérieure ou égale à 2 tébioctets (Tio), vous devez utiliser le partitionnement GPT. Pour procéder au partitionnement GPT, vous pouvez utiliser `parted`. Si la taille du disque est inférieure à 2 Tio, vous pouvez utiliser le partitionnement MBR ou GPT. Faites-en le disque principal sur la partition 1 et acceptez les autres valeurs par défaut. L’exemple suivant démarre le processus `fdisk` sur */dev/sdc* :

```bash
sudo fdisk /dev/sdc
```

Utilisez la commande `n` pour ajouter une nouvelle partition. Dans cet exemple, nous avons également choisi `p` comme partition principale et accepté le reste des valeurs par défaut. Vous devez obtenir un résultat semblable à l’exemple qui suit :

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Imprimez la table de partition en tapant `p`, puis utilisez `w` pour écrire la table sur le disque et quitter. Vous devez obtenir un résultat semblable à l’exemple qui suit :

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

À présent, écrivez un système de fichiers dans la partition avec la commande `mkfs`. Spécifiez le type de votre système de fichiers et le nom de l’appareil. L’exemple suivant crée un système de fichiers *ext4* sur la partition */dev/sdc1* créée dans les étapes précédentes :

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Le résultat ressemble à l’exemple suivant :

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

#### <a name="alternate-method-using-parted"></a>Autre méthode utilisant parted
L’utilitaire fdisk a besoin d’une entrée interactive et n’est donc pas idéal pour une utilisation dans les scripts d’automatisation. Toutefois, l’utilitaire [parted](https://www.gnu.org/software/parted/) peut être scripté et se prête donc mieux aux scénarios d’automatisation. L’utilitaire parted peut être utilisé pour partitionner et formater un disque de données. Pour la procédure pas à pas ci-dessous, nous utilisons un nouveau disque de données /dev/sdc et le formatons à l’aide du système de fichiers [XFS](https://xfs.wiki.kernel.org/).
```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
partprobe /dev/sdc1
```
Comme indiqué ci-dessus, nous utilisons l’utilitaire [partprobe](https://linux.die.net/man/8/partprobe) pour vérifier que le noyau est immédiatement conscient de la nouvelle partition et du nouveau système de fichiers. Si vous ne parvenez pas à utiliser partprobe, les commandes blkid ou lslbk ne retournent pas immédiatement l’UUID du nouveau système de fichiers.

### <a name="mount-the-disk"></a>Monter le disque
Créez un répertoire afin de monter le système de fichiers à l’aide de `mkdir`. L’exemple suivant crée un répertoire sous */datadrive* :

```bash
sudo mkdir /datadrive
```

Utilisez `mount` pour monter le système de fichiers. L’exemple suivant monte la partition */dev/sdc1* pour le point de montage */datadrive* :

```bash
sudo mount /dev/sdc1 /datadrive
```

Pour vous assurer que le lecteur est remonté automatiquement après un redémarrage, vous devez l’ajouter au fichier */etc/fstab*. Il est aussi vivement recommandé d’utiliser l’UUID (identificateur global unique) dans */etc/fstab* comme référence au lecteur, plutôt que le nom d’appareil uniquement (par exemple, */dev/sdc1*). Si le système d’exploitation détecte une erreur disque pendant le démarrage, l’utilisation de l’UUID évite que le disque incorrect ne soit monté sur un emplacement donné. Les disques de données restants reçoivent alors les mêmes ID d’appareil. Pour rechercher l’UUID du nouveau lecteur, utilisez l’utilitaire `blkid` :

```bash
sudo -i blkid
```

Le résultat ressemble à l’exemple qui suit :

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> si vous ne modifiez pas correctement le fichier **/etc/fstab** , il se peut que le système ne puisse plus démarrer. En cas de doute, reportez-vous à la documentation de la distribution pour obtenir des informations sur la modification adéquate de ce fichier. Il est par ailleurs vivement recommandé de créer une sauvegarde du fichier /etc/fstab avant de le modifier.

Ensuite, ouvrez le fichier */etc/fstab* dans un éditeur de texte, comme suit :

```bash
sudo vi /etc/fstab
```

Dans cet exemple, utilisez la valeur UUID pour l’appareil */dev/sdc1* créé lors des étapes précédentes et le point de montage */datadrive*. Ajoutez la ligne suivante à la fin du fichier */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```
Une fois terminé, enregistrez le fichier */etc/fstab* et redémarrez le système.
> [!NOTE]
> La suppression ultérieure d’un disque de données sans modifier fstab pourrait entraîner l’échec du démarrage de la machine virtuelle. La plupart des distributions fournissent les options fstab *nofail* et/ou *nobootwait*. Ces options permettent à un système de démarrer même si le disque n’est pas monté au moment du démarrage. Pour plus d’informations sur ces paramètres, consultez la documentation de votre distribution.
> 
> L’option *nofail* garantit que la machine virtuelle démarre même si le système de fichiers est endommagé ou si le disque n’existe pas au moment du démarrage. Sans cette option, vous pouvez être confronté au comportement décrit dans [Cannot SSH to Linux VM due to FSTAB errors](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) (Connexion SSH vers machine virtuelle Linux impossible en raison d’erreurs FSTAB)

### <a name="trimunmap-support-for-linux-in-azure"></a>Prise en charge de TRIM/UNMAP pour Linux dans Azure
Certains noyaux Linux prennent en charge les opérations TRIM/UNMAP pour ignorer les blocs inutilisés sur le disque. Cette fonctionnalité est particulièrement utile dans le stockage standard pour informer Azure que des pages supprimées ne sont plus valides et peuvent être ignorées. Elle peut vous permettre d’économiser de l’argent si vous créez des fichiers volumineux, puis que vous les supprimez.

Il existe deux façons d’activer la prise en charge de TRIM sur votre machine virtuelle Linux. Comme d’habitude, consultez votre distribution pour connaître l’approche recommandée :

* Utilisez l’option de montage `discard` dans */etc/fstab*, par exemple :

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Dans certains cas, l’option `discard` peut avoir un impact sur la performance. Vous pouvez également exécuter la commande `fstrim` manuellement à partir de la ligne de commande ou l’ajouter à votre crontab pour l’exécuter régulièrement :
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez également [attacher un disque de données](add-disk.md) à l’aide d’Azure CLI.
