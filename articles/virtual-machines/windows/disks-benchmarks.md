---
title: Benchmarking de votre application sur Stockage sur disque Azure
description: En savoir plus sur le processus de benchmarking de votre application sur Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: e73999925c68ca8f68ba386808e443c9e2779498
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460173"
---
# <a name="benchmarking-a-disk"></a>Benchmarking d’un disque

Le benchmarking consiste à simuler différentes charges de travail sur votre application et à mesurer les performances de l’application pour chaque charge de travail. En suivant la procédure décrite dans l'article [Conception pour de hautes performances](premium-storage-performance.md), vous avez pu recueillir les exigences de performances de l’application. À présent, vous pouvez utiliser des outils de benchmarking sur les machines virtuelles qui hébergent l’application afin de déterminer les niveaux de performance que votre application peut atteindre avec Premium Storage. Dans cet article, nous allons vous fournir des exemples d’un benchmarking effectué sur une machine virtuelle DS14 Standard configurée avec des disques Azure Stockage Premium.

Nous avons utilisé les outils courants Iometer et FIO, pour Windows et Linux respectivement. Ces outils génèrent plusieurs threads qui simulent une charge de travail en production et mesurent les performances du système. L’utilisation de ces outils vous permet également de configurer des paramètres tels que la taille de bloc et la profondeur de file d’attente, que vous ne pouvez normalement pas modifier pour une application. Vous bénéficiez ainsi d’une plus grande souplesse pour optimiser les performances sur une machine virtuelle à grande échelle dotée de disques premium pour différents types de charges de travail applicatives. Pour en savoir plus sur chaque outil de benchmarking, visitez [Iometer](http://www.iometer.org/) et [FIO](http://freecode.com/projects/fio).

Pour suivre les exemples ci-dessous, créez une machine virtuelle DS14 Standard et attachez-y 11 disques Premium Storage. Sur ces 11 disques, configurez 10 disques avec une mise en cache de l’hôte définie sur « Aucun » et entrelacez-les dans un volume appelé NoCacheWrites. Configurez la mise en cache de l’hôte en « Lecture seule » sur le disque restant et créez un volume appelé CacheReads avec ce disque. Avec cette configuration, vous êtes en mesure d’observer les performances de lecture et d’écriture maximale à partir d’une machine virtuelle DS14 Standard. Pour obtenir des instructions détaillées sur la création d’une machine virtuelle DS14 avec des disques SSD Premium, accédez à [Conception pour de hautes performances](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Étapes suivantes

Parcourez notre article de conception pour de hautes performances. Dans celui-ci, vous créez une check-list semblable à votre application prototype existante. À l’aide des outils de benchmarking, vous pouvez simuler les charges de travail et mesurer les performances de l’application prototype. En procédant ainsi, vous pouvez déterminer quelle offre de disque peut satisfaire ou dépasser les exigences de performances de votre application. Vous pouvez ensuite implémenter les mêmes règles pour votre application de production.

> [!div class="nextstepaction"]
> Consultez l’article sur le début de la [conception pour de hautes performances](premium-storage-performance.md).