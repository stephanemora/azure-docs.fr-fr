---
title: Meilleures pratiques Linux en matière de lecture anticipée NFS – Emplacements de session et entrées de table d’emplacements | Microsoft Docs
description: Décrit les pratiques recommandées pour le cache du système de fichiers et la lecture anticipée NFS Linux pour Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: f87d2742ab34cdf5b6509e88b403240d388fa373
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233464"
---
# <a name="linux-nfs-read-ahead-best-practices-for-azure-netapp-files"></a>Meilleures pratiques en matière de lecture anticipée NFS pour Azure NetApp Files

Cet article aide à comprendre les meilleures pratiques en matière de cache de système de fichiers concernant Azure NetApp Files.  

La lecture anticipée NFS demande de manière prédictive les blocs d’un fichier à l’avance des demandes d’E/S par l’application. Elle est conçue pour améliorer le débit de lecture séquentielle du client.  Jusqu’à récemment, toutes les distributions Linux modernes définissent la valeur de lecture anticipée de façon à ce qu’elle soit équivalente à 15 fois les systèmes de fichiers montés `rsize`.  

Le tableau suivant indique les valeurs de lecture anticipée par défaut pour chaque `rsize` option de montage donnée.

| Système de fichiers monté `rsize` | Lecture anticipée des blocs |
|-|-|
| 64 Kio | 960 Kio |
| 256 Kio | 3 840 Kio |
| 1 024 Kio | 15 360 Kio |

RHEL 8.3 et Ubuntu 18.04 ont introduit des modifications qui peuvent avoir un impact négatif sur les performances de lecture séquentielle du client.  Contrairement aux versions antérieures, ces distributions définissent la lecture anticipée sur une valeur par défaut de 128 Kio, quelle que soit l'`rsize` option de montage utilisée. La mise à niveau à partir de versions avec la plus grande valeur de lecture anticipée vers celles ayant la valeur par défaut 128-Kio a connu des baisses de performances dans la lecture séquentielle. Toutefois, les valeurs de lecture anticipée peuvent être paramétrées vers le haut à la fois dynamiquement et de façon permanente.  Par exemple, le test avec SAS GRID a trouvé la valeur de lecture 15 360 Kio optimale par rapport à 3 840 Kio, 960 Kio et 128 Kio.  Il n’y a pas eu assez de tests exécutés au-delà de 15 360 Kio pour déterminer l’impact positif ou négatif.

Le tableau suivant indique les valeurs de lecture anticipée par défaut pour chaque distribution disponible.

|     Distribution    |     Libérer    |     Lecture anticipée des blocs    |
|-|-|-|
|     RHEL    |     8.3    |     128 Kio    |
|     RHEL    |     7.X/8.0, 8.1, 8.2    |     15 X `rsize`    |
|     SLES    |     12. X – au moins 15SP2    |     15 X `rsize`    |
|     Ubuntu    |     18.04 – au moins 20.04    |     128 Kio    |
|     Ubuntu    |     16.04    |     15 X `rsize`    |
|     Debian    |     Jusqu’à au moins 10    |     15 x `rsize`    |


## <a name="how-to-work-with-per-nfs-filesystem-read-ahead"></a>Utilisation de la lecture anticipée de système de fichiers NFS   

La lecture anticipée NFS est définie au point de montage d’un système de fichiers NFS. Le paramètre par défaut peut être affiché et défini à la fois dynamiquement et de façon permanente.  Pour plus de commodité, le script bash suivant écrit par Red Hat a été fourni pour afficher ou définir dynamiquement la lecture anticipée pour le système de fichiers NFS.

La lecture anticipée peut être définie de façon dynamique par montage NFS à l’aide du script suivant ou à l’aide de `udev` règles de manière permanente, comme indiqué dans cette section.  Pour afficher ou définir une lecture anticipée pour un système de fichiers NFS monté, vous pouvez enregistrer le script suivant en tant que fichier bash, modifier les autorisations du fichier pour en faire un exécutable (`chmod 544 readahead.sh`) et exécuter comme indiqué. 

## <a name="how-to-show-or-set-read-ahead-values"></a>Comment afficher ou définir des valeurs de lecture anticipée   

Pour afficher la valeur actuelle de lecture anticipée (la valeur retournée est en Kio), exécutez la commande suivante :  

`$ ./readahead.sh  show <mount-point>`   

Pour définir une nouvelle valeur de lecture anticipée, exécutez la commande suivante :   

`$ ./readahead.sh  show <mount-point> [read-ahead-kb]`
 
### <a name="example"></a>Exemple   

```
#!/bin/bash
# set | show readahead for a specific mount point
# Useful for things like NFS and if you do not know / care about the backing device
#
# To the extent possible under law, Red Hat, Inc. has dedicated all copyright
# to this software to the public domain worldwide, pursuant to the
# CC0 Public Domain Dedication. This software is distributed without any warranty.
# See <http://creativecommons.org/publicdomain/zero/1.0/>.
#

E_BADARGS=22
function myusage() {
echo "Usage: `basename $0` set|show <mount-point> [read-ahead-kb]"
}

if [ $# -gt 3 -o $# -lt 2 ]; then
   myusage
   exit $E_BADARGS
fi

MNT=${2%/}
BDEV=$(grep $MNT /proc/self/mountinfo | awk '{ print $3 }')

if [ $# -eq 3 -a $1 == "set" ]; then
   echo $3 > /sys/class/bdi/$BDEV/read_ahead_kb
elif [ $# -eq 2 -a $1 == "show" ]; then
   echo "$MNT $BDEV /sys/class/bdi/$BDEV/read_ahead_kb = "$(cat /sys/class/bdi/$BDEV/read_ahead_kb)
else
   myusage
   exit $E_BADARGS
fi
```

## <a name="how-to-persistently-set-read-ahead-for-nfs-mounts"></a>Comment définir de manière permanente la lecture anticipée pour les montages NFS

Pour définir de façon permanente les montages NFS, les `udev` règles peuvent être écrites comme suit :    

1. Créer et tester `/etc/udev/rules.d/99-nfs.rules` :

    `SUBSYSTEM=="bdi", ACTION=="add", PROGRAM="/bin/awk -v bdi=$kernel 'BEGIN{ret=1} {if ($4 == bdi) {ret=0}} END{exit ret}' /proc/fs/nfsfs/volumes", ATTR{read_ahead_kb}="15380"`

2. Appliquer la règle `udev` :   

    `$udevadm control --reload`

## <a name="next-steps"></a>Étapes suivantes  

* [Bonnes pratiques d’E/S directes Linux pour Azure NetApp Files](performance-linux-direct-io.md)
* [Meilleures pratiques en matière de cache de système de fichiers Linux pour Azure NetApp Files](performance-linux-filesystem-cache.md)
* [Meilleures pratiques Linux concernant les options de montage NFS pour Azure NetApp Files](performance-linux-mount-options.md)
* [Bonnes pratiques pour la concurrence Linux](performance-linux-concurrency-session-slots.md)
* [Bonnes pratiques pour les références SKU de machine virtuelle Azure](performance-virtual-machine-sku.md) 
* [Test d’évaluation des performances pour Linux](performance-benchmarks-linux.md) 
