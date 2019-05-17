---
title: Niveaux de service pour Azure NetApp Files | Microsoft Docs
description: Décrit les performances de débit des niveaux de service d'Azure NetApp Files.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 1f9c427045c9d42f6a11cc4bcc798cfc47a4428c
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523102"
---
# <a name="service-levels-for-azure-netapp-files"></a>Niveaux de service pour Azure NetApp Files
Niveaux de service sont un attribut d’un pool de capacité. Niveaux de service sont définis et différenciées par le débit maximal autorisé pour un volume dans le pool de capacité basé sur le quota est attribué au volume.

## <a name="supported-service-levels"></a>Niveaux de service pris en charge

Les fichiers NetApp Azure prend en charge trois niveaux de service : *Ultra*, *Premium*, et *Standard*. 

* <a name="Ultra"></a>Stockage ultra

    Le niveau de stockage Ultra fournit jusqu'à 128 Mio/s de débit par 1 TIO de quota de volume attribué. 

* <a name="Premium"></a>Stockage Premium

    Le niveau de stockage Premium offre jusqu'à 64 Mio/s de débit par 1 TIO de quota de volume attribué. 

* <a name="Standard"></a>Stockage Standard

    Le niveau de stockage Standard fournit jusqu'à 16 Mio/s de débit par 1 TIO de quota de volume attribué.

## <a name="throughput-limits"></a>Limites de débit

La limite de débit pour un volume est déterminée par la combinaison des facteurs suivants :
* Le niveau de service du pool de capacité à laquelle appartient le volume
* Le quota attribué au volume  

Ce concept est illustré dans le diagramme ci-dessous :

![Illustration de niveau de service](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

Dans l’exemple 1 ci-dessus, un volume à partir d’un pool de capacité avec le niveau de stockage Premium est affecté de 2 TIO de quota recevront une limite de débit de 128 Mio/s (2 TIO * 64 Mio/s). Ce scénario s’applique quel que soit la taille de pool de capacité ou de la consommation réelle de volume.

Dans l’exemple 2 ci-dessus, un volume à partir d’un pool de capacité avec le niveau de stockage Premium est affecté à 100 Go de quota recevront une limite de débit de 6,25 Mio/s (TIO 0.09765625 * 64 Mio/s). Ce scénario s’applique quel que soit la taille de pool de capacité ou de la consommation réelle de volume.

## <a name="next-steps"></a>Étapes suivantes

- Consultez la [page des tarifs Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) pour connaître le prix des différents niveaux de service
- Consultez [modèle de coût pour les fichiers de NetApp Azure](azure-netapp-files-cost-model.md) pour le calcul de la consommation de capacité dans un pool de capacité 
- [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)