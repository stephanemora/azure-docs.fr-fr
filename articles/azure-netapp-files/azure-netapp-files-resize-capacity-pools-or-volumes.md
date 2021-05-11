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
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: b57997408112466c45d6ce1364e1ac0a2c358cd1
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108321030"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Redimensionner un pool de capacités ou un volume
Vous pouvez modifier la taille d’un pool de capacités ou d’un volume si besoin, par exemple lorsqu’un volume ou pool se capacités se remplit. 

Pour plus d’informations sur la surveillance de la capacité d’un volume, consultez [Surveiller la capacité d’un volume](monitor-volume-capacity.md).

## <a name="resize-the-capacity-pool-using-the-azure-portal"></a>Redimensionner le pool de capacités à l’aide du portail Azure 

Vous pouvez modifier la taille du pool de capacités par incréments ou décréments de 1 Tio. Toutefois, la taille de pool de capacité ne peut pas être inférieure à 4 Tio. Le redimensionnement du pool de capacités change la capacité Azure NetApp Files achetée.

1. Dans la vue Compte NetApp, accédez à **Pools de capacités**, puis cliquez sur le pool de capacités que vous souhaitez redimensionner.
2. Cliquez avec le bouton droit sur le nom du pool de capacités ou cliquez sur l’icône « … » à la fin de la ligne correspondant au pool de capacités pour afficher le menu contextuel. Cliquez sur **Redimensionner**. 

    ![Capture d’écran montrant le menu contextuel du pool](../media/azure-netapp-files/resize-pool-context-menu.png)  

3. Dans la fenêtre Redimensionner le pool, spécifiez la taille du pool.  Cliquez sur **OK**.

    ![Capture d’écran montrant la fenêtre Redimensionner le pool.](../media/azure-netapp-files/resize-pool-window.png) 

## <a name="resize-a-volume-using-the-azure-portal"></a>Redimensionner un volume à l’aide du portail Azure

Vous pouvez changer la taille d’un volume selon les besoins. La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool.

1. Dans la vue Compte NetApp, accédez à **Volumes**, puis cliquez sur le volume que vous souhaitez redimensionner.
2. Cliquez avec le bouton droit sur le nom du volume ou cliquez sur l’icône « ... » à la fin de la ligne correspondant au volume pour afficher le menu contextuel. Cliquez sur **Redimensionner**.

    ![Capture d’écran montrant le menu contextuel du volume](../media/azure-netapp-files/resize-volume-context-menu.png) 
    
3. Dans la fenêtre Mettre à jour le quota de volume, spécifiez le quota du volume. Cliquez sur **OK**.   

    ![Capture d’écran montrant la fenêtre Mettre à jour le quota de volume.](../media/azure-netapp-files/resize-volume-quota-window.png) 

## <a name="resizing-the-capacity-pool-or-a-volume-using-azure-cli"></a>Redimensionnement du pool de capacités ou d’un volume à l’aide d’Azure CLI  

Vous pouvez utiliser les commandes suivantes des [outils de ligne de commande Azure (CLI)](azure-netapp-files-sdk-cli.md) pour redimensionner un pool de capacités ou un volume :

* [`az netappfiles pool`](/cli/azure/netappfiles/pool?preserve-view=true&view=azure-cli-latest)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume?preserve-view=true&view=azure-cli-latest)

## <a name="resizing-the-capacity-pool-or-a-volume-using-rest-api"></a>Redimensionnement du pool de capacités ou d’un volume à l’aide de l’API REST

Vous pouvez créer une automatisation pour gérer le pool de capacités et le changement de taille du volume.   

Consultez [API REST pour Azure NetApp Files](azure-netapp-files-develop-with-rest-api.md) et [API REST utilisant PowerShell pour Azure NetApp Files](develop-rest-api-powershell.md). 

La spécification de l’API REST et l’exemple de code pour Azure NetApp Files sont disponibles via le [répertoire GitHub du gestionnaire de ressources](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable). 

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
- [Présentation du quota de volume](volume-quota-introduction.md)
- [Surveiller la capacité d'un volume](monitor-volume-capacity.md)
- [Questions fréquentes (FAQ) sur la gestion de la capacité](azure-netapp-files-faqs.md#capacity-management-faqs)
