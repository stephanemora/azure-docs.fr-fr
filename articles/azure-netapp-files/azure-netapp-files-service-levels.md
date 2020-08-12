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
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 639f1e09fdb5603965209e5b5ee6c224ad238b76
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533119"
---
# <a name="service-levels-for-azure-netapp-files"></a>Niveaux de service pour Azure NetApp Files
Les niveaux de service sont un attribut d’un pool de capacités. Les niveaux de service sont définis et différenciés par le débit maximum autorisé pour un volume dans le pool de capacités en fonction du quota attribué au volume.

## <a name="supported-service-levels"></a>Niveaux de service pris en charge

Azure NetApp Files prend en charge trois niveaux de service : *Ultra*, *Premium* et *Standard*. 

* <a name="Ultra"></a>Stockage Ultra

    Le niveau de stockage Ultra fournit jusqu’à 128 Mio/s de débit pour 1 Tio de quota de volume attribué. 

* <a name="Premium"></a>Stockage Premium

    Le niveau de stockage Premium fournit jusqu’à 64 Mio/s de débit pour 1 Tio de quota de volume attribué. 

* <a name="Standard"></a>Stockage Standard

    Le niveau de stockage Standard fournit jusqu’à 16 Mio/s de débit pour 1 Tio de quota de volume attribué.

## <a name="throughput-limits"></a>Limites de débit

La limite de débit pour un volume est déterminée par la combinaison des facteurs suivants :
* Le niveau de service du pool de capacités auquel le volume appartient
* Le quota attribué au volume  

Ce concept est illustré dans le diagramme ci-dessous :

![Illustration de niveau de service](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

Dans l’exemple 1 ci-dessus, un volume d’un pool de capacités avec le niveau de stockage Premium et un quota de 2 Tio se voit attribuer une limite de débit de 128 Mio/s (2 Tio * 64 Mio/s). Ce scénario s’applique quelle que soit la taille de pool de capacités ou la consommation de volume réelle.

Dans l’exemple 2 ci-dessus, un volume d’un pool de capacités avec le niveau de stockage Premium et un quota de 100 Gio se voit attribuer une limite de débit de 6,25 Mio/s (0,09765625 Tio * 64 Mio/s). Ce scénario s’applique quelle que soit la taille de pool de capacités ou la consommation de volume réelle.

## <a name="next-steps"></a>Étapes suivantes

- [Tarification Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Modèle de coût pour Azure NetApp Files](azure-netapp-files-cost-model.md) 
- [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)
- [Contrat de niveau de service (SLA) pour Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Changer dynamiquement le niveau de service d’un volume](dynamic-change-volume-service-level.md) 