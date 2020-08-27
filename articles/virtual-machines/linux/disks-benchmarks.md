---
title: Benchmarking de votre application sur Stockage sur disque Azure
description: En savoir plus sur le processus de benchmarking de votre application sur Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 293164413c4c8ecec0295152cb6db3d73718e00d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690780"
---
# <a name="benchmark-your-application-on-azure-disk-storage"></a>Créer un benchmark de votre application sur Stockage sur disque Azure

Le benchmarking consiste à simuler différentes charges de travail sur votre application et à mesurer les performances de l’application pour chaque charge de travail. À l’aide de la procédure décrite dans l’article [Conception pour de hautes performances](../premium-storage-performance.md). À présent, vous pouvez utiliser des outils de benchmarking sur les machines virtuelles qui hébergent l’application afin de déterminer les niveaux de performance que votre application peut atteindre avec Premium Storage. Dans cet article, nous allons vous fournir des exemples d’un benchmarking effectué sur une machine virtuelle DS14 Standard configurée avec des disques Azure Stockage Premium.

Nous avons utilisé les outils courants Iometer et FIO, pour Windows et Linux respectivement. Ces outils génèrent plusieurs threads qui simulent une charge de travail en production et mesurent les performances du système. L’utilisation de ces outils vous permet également de configurer des paramètres tels que la taille de bloc et la profondeur de file d’attente, que vous ne pouvez normalement pas modifier pour une application. Vous bénéficiez ainsi d’une plus grande souplesse pour optimiser les performances sur une machine virtuelle à grande échelle dotée de disques premium pour différents types de charges de travail applicatives. Pour en savoir plus sur chaque outil de benchmarking, visitez [Iometer](http://www.iometer.org/) et [FIO](http://freecode.com/projects/fio).

Pour suivre les exemples ci-dessous, créez une machine virtuelle DS14 Standard et attachez-y 11 disques Premium Storage. Sur ces 11 disques, configurez 10 disques avec une mise en cache de l’hôte définie sur « Aucun » et entrelacez-les dans un volume appelé NoCacheWrites. Configurez la mise en cache de l’hôte en « Lecture seule » sur le disque restant et créez un volume appelé CacheReads avec ce disque. Avec cette configuration, vous êtes en mesure d’observer les performances de lecture et d’écriture maximale à partir d’une machine virtuelle DS14 Standard. Pour obtenir des instructions détaillées sur la création d’une machine virtuelle DS14 avec des disques premium, accédez à [Conception pour de hautes performances](../premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Étapes suivantes

Reportez-vous à notre article sur la [conception pour de hautes performances](../premium-storage-performance.md).

Dans cet article, vous créez une check-list semblable à votre application prototype existante. À l’aide des outils de benchmarking, vous pouvez simuler les charges de travail et mesurer les performances de l’application prototype. En procédant ainsi, vous pouvez déterminer quelle offre de disque peut satisfaire ou dépasser les exigences de performances de votre application. Vous pouvez ensuite implémenter les mêmes règles pour votre application de production.