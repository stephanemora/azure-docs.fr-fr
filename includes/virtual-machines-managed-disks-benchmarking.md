---
title: Fichier include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094626"
---
## <a name="warm-up-the-cache"></a>Préchauffer le cache

Le disque dont la mise en cache de l’hôte est définie en lecture seule peut générer un taux d’E/S par seconde supérieur à sa propre limite. Pour obtenir ces performances de lecture maximales à partir du cache de l’hôte, vous devez tout d’abord préchauffer le cache du disque. Ainsi, les E/S en lecture que l’outil de benchmark génèrera sur le volume CacheReads atteindront le cache plutôt que le disque directement. Le fait d’intervenir au niveau du cache permet de générer des E/S par seconde supplémentaires à partir du seul disque ayant une mise en cache.

> [!IMPORTANT]
> Vous devez préchauffer le cache avant d’exécuter l’outil de benchmarking à chaque redémarrage de la machine virtuelle.

## <a name="diskspd"></a>DISKSPD

[Téléchargez l’outil DISKSP](https://github.com/Microsoft/diskspd) sur la machine virtuelle. DISKSPD est un outil que vous pouvez personnaliser pour créer vos propres charges de travail synthétiques. Nous utiliserons la même configuration que celle décrite ci-dessus pour exécuter des tests d’évaluation. Vous pouvez modifier les spécifications pour tester différentes charges de travail.

Dans cet exemple, nous utilisons les paramètres de base suivants :

- -c200G : crée (ou recrée) l’exemple de fichier utilisé dans le test. Celle-ci peut être définie en octets, en Kio, en Mio, en Gio ou en blocs. Dans ce cas, un fichier cible volumineux de 200 Gio est utilisé pour réduire au minimum la mise en cache de la mémoire.
- -w100 : spécifie le pourcentage d'opérations correspondant à des demandes d'écriture (-w0 équivaut à 100 % de lectures).
- -b4K : indique la taille de bloc en octets, Kio, Mio ou Gio. Dans cet exemple, la taille de bloc 4K est utilisée pour simuler un test d’E/S aléatoire.
- -F4 : définit un total de quatre threads.
- -r : indique le test d’E/S aléatoire (remplace le paramètre -s).
- -o128 : indique le nombre de demandes d’E/S en attente par cible et par thread. Il s’agit également de la profondeur de file d’attente. Dans ce cas, 128 est utilisé pour contraindre l’UC.
- -W7200 : spécifie la durée du délai de préchauffage avant le début des mesures.
- -d30 : spécifie la durée du test, sans compter le temps de préchauffage.
- -Sh : désactive la mise en cache en écriture des logiciels et du matériel (équivalent de -Suw).

Pour obtenir la liste complète des paramètres, reportez-vous au [dépôt GitHub](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters).

### <a name="maximum-write-iops"></a>Taux d’E/S maximal en écriture
Nous utilisons une profondeur de file d’attente élevée de 128, une petite taille de bloc de 8 Ko et 4 threads de travail pour la conduite des opérations d’écriture. Les Workers d’écriture orientent le trafic sur le volume « NoCacheWrites », qui comporte 3 disques avec une mise en cache définie sur « Aucun ».

Exécutez la commande suivante pendant 30 secondes de préchauffage et 30 secondes de mesure :

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

Les résultats indiquent que la machine virtuelle Standard_D8ds_v4 fournit sa limite d’E/S par seconde d’écriture maximale de 12 800.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="Pour 3208642560 octets au total, nombre total d’E/S de 391680, avec un total de 101,97 Mio/s et un total de 13052,65 E/S par seconde.":::

### <a name="maximum-read-iops"></a>Taux d’E/S maximal en lecture

Nous utilisons une profondeur de file d’attente élevée de 128, une petite taille de bloc de 4 Ko et 4 threads de travail pour la conduite des opérations de lecture. Les Workers de lecture orientent le trafic sur le volume « CacheReads », qui comporte un disque avec une mise en cache définie sur « Lecture seule ».

Exécutez la commande suivante pendant 2 heures de préchauffage et 30 secondes de mesure :

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

Les résultats indiquent que la machine virtuelle Standard_D8ds_v4 fournit sa limite d’E/S par seconde de lecture maximale de 77 000.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="Pour 9652785152 octets au total, il existait 2356637 E/S au total, à 306,72 Mio/s au total et un total de 78521,23 E/S par seconde.":::

### <a name="maximum-throughput"></a>Débit maximal

Pour obtenir le débit maximal de lecture et d’écriture, vous pouvez passer à une taille de bloc supérieure de 64 Ko.
## <a name="fio"></a>FIO

FIO est un outil communément utilisé pour tester le stockage sur des machines virtuelles Linux. Cet outil offre la possibilité de sélectionner différentes tailles d’E/S, avec des lectures et des écritures séquentielles ou aléatoires. Il génère des threads de travail ou des processus pour exécuter les opérations d’E/S spécifiées. Vous pouvez spécifier le type d’opérations d’E/S que chaque thread de travail doit exécuter à l’aide de fichiers de travail. Nous avons créé un fichier de travail par scénario, comme illustré dans les exemples ci-dessous. Vous pouvez modifier les spécifications de ces fichiers de travail pour tester différentes charges de travail exécutées sur Premium Storage. Dans ces exemples, nous utilisons une machine virtuelle Standard_D8ds_v4 exécutée sous **Ubuntu**. Utilisez la configuration décrite au début de la section Benchmarking et préchauffez le cache avant d’exécuter les tests de benchmarking.

Avant de commencer, [téléchargez FIO](https://github.com/axboe/fio) et installez-le sur votre machine virtuelle.

Exécutez la commande ci-dessous pour Ubuntu.

```
apt-get install fio
```

Nous allons utiliser quatre threads de travail pour générer les opérations d’écriture et quatre threads de travail pour générer les opérations de lecture sur les disques. Les Workers d’écriture orientent le trafic sur le volume « nocache », qui comporte 3 disques avec une mise en cache définie sur « Aucun ». Les Workers de lecture orientent le trafic sur le volume « readcache », qui comporte 1 disque avec une mise en cache définie sur « Lecture seule ».

### <a name="maximum-write-iops"></a>Taux d’E/S maximal en écriture

Créez le fichier de travail avec les spécifications suivantes pour obtenir le taux maximal d’E/S par seconde en écriture. Nommez ce fichier « fiowrite.ini ».

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Tenez compte des remarques suivantes qui sont en phase avec les instructions de conception présentées dans les sections précédentes. Ces spécifications sont essentielles pour générer le taux d’E/S par seconde maximal.  

* Une profondeur de file d’attente élevée de 256.  
* Une petite taille de bloc de 4 Ko.  
* Plusieurs threads effectuant des écritures aléatoires.

Exécutez la commande suivante pour lancer le test FIO pendant 30 secondes.  

```
sudo fio --runtime 30 fiowrite.ini
```

Pendant l’exécution du test, le nombre d’E/S par seconde en écriture générées par la machine virtuelle et les disques Premium s’affiche. Comme indiqué dans l’exemple ci-dessous, la machine virtuelle Standard_D8ds_v4 produit un taux d’E/S par seconde maximal en écriture limité à 12 800 IOPS.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="Le nombre d’E/S par seconde d’écriture que la machine virtuelle et les disques SSD Premium fournissent indique que les écritures sont de 13 000 E/S par seconde.":::

### <a name="maximum-read-iops"></a>Taux d’E/S maximal en lecture

Créez le fichier de travail avec les spécifications suivantes pour obtenir le taux maximal d’E/S par seconde en lecture. Nommez ce fichier « fioread.ini ».

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Tenez compte des remarques suivantes qui sont en phase avec les instructions de conception présentées dans les sections précédentes. Ces spécifications sont essentielles pour générer le taux d’E/S par seconde maximal.

* Une profondeur de file d’attente élevée de 256.  
* Une petite taille de bloc de 4 Ko.  
* Plusieurs threads effectuant des écritures aléatoires.

Exécutez la commande suivante pour lancer le test FIO pendant 30 secondes.

```
sudo fio --runtime 30 fioread.ini
```

Pendant l’exécution du test, le nombre d’E/S par seconde en lecture générées par la machine virtuelle et les disques Premium s’affiche. Comme indiqué dans l’exemple ci-dessous, la machine virtuelle Standard_D8ds_v4 génère plus de 77 000 E/S par seconde en lecture. Cette valeur représente les performances combinées du disque et du cache.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="Capture d’écran Le nombre d’E/S par seconde d’écriture que la machine virtuelle et les disques SSD Premium fournissent indique que les écritures sont de 78 600 E/S par seconde en lecture.":::

### <a name="maximum-read-and-write-iops"></a>Taux d’E/S maximal en lecture et en écriture

 Créez le fichier de travail avec les spécifications suivantes pour obtenir le taux maximal combiné d’E/S par seconde en lecture et en écriture. Nommez ce fichier « fioreadwrite.ini ».

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=3200
```

Tenez compte des remarques suivantes qui sont en phase avec les instructions de conception présentées dans les sections précédentes. Ces spécifications sont essentielles pour générer le taux d’E/S par seconde maximal.

* Une profondeur de file d’attente élevée de 128.  
* Une petite taille de bloc de 4 Ko.  
* Plusieurs threads effectuant des opérations d’écriture et de lecture aléatoires.

Exécutez la commande suivante pour lancer le test FIO pendant 30 secondes.

```
sudo fio --runtime 30 fioreadwrite.ini
```

Pendant l’exécution du test, le nombre d’E/S par seconde combinées en lecture et en écriture générées par la machine virtuelle et les disques Premium s’affiche. Comme indiqué dans l’exemple ci-dessous, la machine virtuelle Standard_D8ds_v4 génère plus de 90 000 E/S par seconde combinées en lecture et en écriture. Cette valeur représente les performances combinées du disque et du cache.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="Les E/S par seconde en lecture et en écriture combinées montrent que les lectures sont de 78 300 E/S par seconde et que les écritures sont de 12 600 E/S par seconde.":::

### <a name="maximum-combined-throughput"></a>Débit maximal combiné

 Pour obtenir le débit maximal combiné en lecture et en écriture, utilisez une plus grande taille de bloc et une grande profondeur de file d’attente avec plusieurs threads effectuant des opérations de lecture et d’écriture. Vous pouvez utiliser une taille de bloc de 64 Ko et une profondeur de file d’attente de 128.