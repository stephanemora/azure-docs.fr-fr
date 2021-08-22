---
title: Créer un benchmark de votre application sur Stockage sur disque Azure
description: En savoir plus sur le processus de benchmarking de votre application sur Azure.
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: a6e156350be0de13b6c6bfea3f8d220830c3480e
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360772"
---
# <a name="benchmark-a-disk"></a>Évaluer un disque

Le benchmarking consiste à simuler différentes charges de travail sur votre application et à mesurer les performances de l’application pour chaque charge de travail. En suivant la procédure décrite dans l'article [Conception pour de hautes performances](premium-storage-performance.md), vous avez pu recueillir les exigences de performances de l’application. Vous pouvez utiliser des outils de benchmarking sur les machines virtuelles qui hébergent l'application afin de déterminer les niveaux de performances que votre application peut atteindre avec des disques SSD premium. Cet article contient des exemples basés sur le benchmarking d'une machine virtuelle Standard_D8ds_v4 configurée avec des disques SSD premium Azure.

Nous avons utilisé les outils courants DiskSpd et FIO, respectivement pour Windows et Linux. Ces outils génèrent plusieurs threads qui simulent une charge de travail en production et mesurent les performances du système. L’utilisation de ces outils vous permet également de configurer des paramètres tels que la taille de bloc et la profondeur de file d’attente, que vous ne pouvez normalement pas modifier pour une application. Vous bénéficiez ainsi d'une plus grande souplesse pour optimiser les performances sur une machine virtuelle à grande échelle dotée de disques SSD premium pour différents types de charges de travail applicatives. Pour en savoir plus sur chaque outil de benchmarking, consultez [DiskSpd](https://github.com/Microsoft/diskspd/wiki/) et [FIO](http://freecode.com/projects/fio).

Pour suivre les exemples ci-dessous, créez une machine virtuelle Standard_D8ds_v4 et attachez-y quatre disques SSD premium. Sur ces quatre disques, configurez-en trois avec une mise en cache de l'hôte définie sur « Aucun » et entrelacez-les dans un volume appelé NoCacheWrites. Configurez la mise en cache de l’hôte en « Lecture seule » sur le disque restant et créez un volume appelé CacheReads avec ce disque. Cette configuration vous permet d'observer les performances maximales de lecture et d'écriture d'une machine virtuelle Standard_D8ds_v4. Pour obtenir des instructions détaillées sur la création d'une machine virtuelle Standard_D8ds_v4 dotée de disques SSD premium, consultez [Conception pour de hautes performances](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Étapes suivantes

Reportez-vous à notre article sur la [conception pour de hautes performances](premium-storage-performance.md).

Dans cet article, vous créez une check-list semblable à votre application prototype existante. À l’aide des outils de benchmarking, vous pouvez simuler les charges de travail et mesurer les performances de l’application prototype. En procédant ainsi, vous pouvez déterminer quelle offre de disque peut satisfaire ou dépasser les exigences de performances de votre application. Vous pouvez ensuite implémenter les mêmes règles pour votre application de production.
