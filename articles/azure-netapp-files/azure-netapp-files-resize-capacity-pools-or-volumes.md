---
title: Redimensionner le pool de capacités ou un volume pour Azure NetApp Files | Microsoft Docs
description: Apprenez à modifier la taille d’un pool de capacités ou d’un volume. Le redimensionnement du pool de capacités change la capacité Azure NetApp Files achetée.
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
ms.topic: how-to
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594476"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Redimensionner un pool de capacités ou un volume
Vous pouvez modifier la taille d’un pool de capacités ou d’un volume selon les besoins. 

## <a name="resize-the-capacity-pool"></a>Redimensionner le pool de capacités 

Vous pouvez modifier la taille du pool de capacités par incréments ou décréments de 1 Tio. Toutefois, la taille de pool de capacité ne peut pas être inférieure à 4 Tio. Le redimensionnement du pool de capacités change la capacité Azure NetApp Files achetée.

1. Dans le panneau Gérer le compte NetApp, cliquez sur le pool de capacités que vous voulez redimensionner. 
2. Cliquez avec le bouton droit sur le nom du pool de capacités ou cliquez sur l’icône « ... » à la fin de la ligne correspondant au pool de capacités pour afficher le menu contextuel. 
3. Utilisez les options du menu contextuel pour redimensionner ou pour supprimer le pool de capacités.

## <a name="resize-a-volume"></a>Redimensionner un volume

Vous pouvez changer la taille d’un volume selon les besoins. La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool.

1. Dans le panneau Gérer le compte NetApp, cliquez sur **Volumes**. 
2. Cliquez avec le bouton droit sur le nom du volume ou cliquez sur l’icône « ... » à la fin de la ligne correspondant au volume pour afficher le menu contextuel.
3. Utilisez les options du menu contextuel pour redimensionner ou pour supprimer le volume.

## <a name="resize-a-cross-region-replication-destination-volume"></a>Redimensionner un volume de destination de réplication entre régions 

Dans une relation de [réplication entre les régions](cross-region-replication-introduction.md), un volume de destination est automatiquement redimensionné en fonction de la taille du volume source. Par conséquent, vous n’avez pas besoin de redimensionner le volume de destination séparément. Ce comportement de redimensionnement automatique est applicable lorsque les volumes sont dans une relation de réplication active ou lorsque le peering de réplication est interrompu avec l'[opération de resynchronisation](cross-region-replication-manage-disaster-recovery.md#resync-replication). 

Le tableau suivant décrit le comportement de redimensionnement du volume de destination en fonction de l'[État du miroir](cross-region-replication-display-health-status.md) :

|  État du miroir  | Comportement de redimensionnement du volume de destination |
|-|-|
| *En miroir* | Lorsque le volume de destination a été initialisé et qu’il est prêt à recevoir des mises à jour de mise en miroir, le redimensionnement du volume source redimensionne automatiquement les volumes de destination. |
| *Rompu* | Lorsque vous redimensionnez le volume source et que l’état du miroir est *rompu*, le volume de destination est automatiquement redimensionné avec l'[opération de resynchronisation](cross-region-replication-manage-disaster-recovery.md#resync-replication).  |
| *Initialisation annulée* | Lorsque vous redimensionnez le volume source et que l’initialisation de l’état du miroir est toujours *annulée*, le redimensionnement du volume de destination doit être effectué manuellement. Par conséquent, il est recommandé d’attendre que l’initialisation se termine (autrement dit, lorsque l’état du miroir est *mis en miroir*) pour redimensionner le volume source. | 

> [!IMPORTANT]
> Vérifiez que vous disposez de suffisamment d’espace dans les pools de capacité pour les volumes source et de destination de la réplication entre les régions. Lorsque vous redimensionnez le volume source, le volume de destination est automatiquement redimensionné. Toutefois, si le pool de capacité hébergeant le volume de destination n’a pas assez de marge, le redimensionnement des volumes source et de destination échouera.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)
- [Gérer un pool de capacités de QoS manuel](manage-manual-qos-capacity-pool.md)
- [Changer dynamiquement le niveau de service d’un volume](dynamic-change-volume-service-level.md) 