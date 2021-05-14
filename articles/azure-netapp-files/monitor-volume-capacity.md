---
title: Surveiller la capacité d’un volume Azure NetApp Files | Microsoft Docs
description: Cet article décrit les méthodes permettant de surveiller l’utilisation de la capacité d’un volume Azure NetApp Files.
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
ms.openlocfilehash: 43605f8dfdcac8fe545e5464c13d9a50ae8ed82c
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108294014"
---
# <a name="monitor-the-capacity-of-a-volume"></a>Surveiller la capacité d'un volume  

Cet article décrit les méthodes permettant de surveiller l’utilisation de la capacité d’un volume Azure NetApp Files.  

## <a name="using-windows-or-linux-clients"></a>Utilisation de clients Windows ou Linux

Cette section montre comment utiliser un client Windows ou Linux pour surveiller la capacité du volume. Les scénarios décrits dans cette section supposent un volume configuré à une taille de 1 Tio (quota) sur un pool de capacités de niveau de service Ultra de 4 Tio. 

### <a name="windows-smb-clients"></a>Clients Windows (SMB)

Vous pouvez utiliser les clients Windows pour vérifier la capacité utilisée et disponible d’un volume à l’aide des propriétés du lecteur mappé. Vous pouvez utiliser l’une des deux méthodes suivantes : 

* Accédez à l’Explorateur de fichiers, cliquez avec le bouton droit sur le lecteur mappé, puis sélectionnez **Propriétés** pour afficher la capacité.  

    [ ![Capture d’écran montrant les propriétés du lecteur et du volume de l’Explorateur.](../media/azure-netapp-files/monitor-explorer-drive-properties.png) ](../media/azure-netapp-files/monitor-explorer-drive-properties.png#lightbox)

* Utilisez la commande `dir` à partir de l’invite de commandes : 

    ![Capture d’écran montrant l’utilisation de la commande dir pour afficher la capacité.](../media/azure-netapp-files/monitor-volume-properties-dir-command.png) 

L'*espace disponible* est exact à l’aide de l’Explorateur de fichiers ou de la commande `dir`. Toutefois, l'*espace consommé/utilisé* est une estimation lorsque des instantanés sont générés sur le volume. La [capacité d’instantané consommée](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots)  est comptabilisée dans l’espace total consommé sur le volume. Pour bénéficier de la consommation de volume absolue, y compris de la capacité utilisée par les instantanés, utilisez les [Métriques NetApp Azure](azure-netapp-files-metrics.md#volumes) dans le portail Azure. 

### <a name="linux-nfs-clients"></a>Clients Linux (NFS) 

Les clients Linux peuvent vérifier la capacité utilisée et disponible d’un volume à l’aide de la [commande df](https://linux.die.net/man/1/df).  

L’option `-h` indique la taille, l’espace utilisé et l’espace disponible dans un format lisible (à l’aide des tailles d’unités M, G et T).

L’instantané suivant illustre les rapports relatifs à la capacité de volume dans Linux :

![Capture d’écran illustrant les rapports relatifs à la capacité de volume dans .](../media/azure-netapp-files/monitor-volume-properties-linux-command.png) 

La commande  *permet de déterminer précisément l’* espace disponible`df`. Toutefois, l'*espace consommé/utilisé* est une estimation lorsque des instantanés sont générés sur le volume. La [capacité d’instantané consommée](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots)  est comptabilisée dans l’espace total consommé sur le volume. Pour bénéficier de la consommation de volume absolue, y compris de la capacité utilisée par les instantanés, utilisez les [Métriques NetApp Azure](azure-netapp-files-metrics.md#volumes) dans le portail Azure. 

## <a name="using-azure-portal"></a>En passant par le portail Azure
Azure NetApp Files tire parti de la fonctionnalité [Azure Monitor](/azure/azure-monitor/overview) standard. Dès lors, vous pouvez utiliser Azure Monitor pour surveiller des volumes Azure NetApp Files.  

## <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure  

Vous pouvez utiliser les commandes [`az netappfiles volume`](/cli/azure/netappfiles/volume?view=azure-cli-latest&preserve-view=true) des [outils de ligne de commande Azure (CLI)](azure-netapp-files-sdk-cli.md) pour surveiller un volume.
 
## <a name="using-rest-api"></a>Utilisation de l'API REST  

Consultez [API REST pour Azure NetApp Files](azure-netapp-files-develop-with-rest-api.md) et [API REST utilisant PowerShell pour Azure NetApp Files](develop-rest-api-powershell.md). 

La spécification de l’API REST et l’exemple de code pour Azure NetApp Files sont disponibles via le [répertoire GitHub du gestionnaire de ressources](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable). 

## <a name="next-steps"></a>Étapes suivantes

* [Présentation du quota de volume](volume-quota-introduction.md)
* [Modèle de coût pour Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Redimensionner le pool de capacité ou un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [Questions fréquentes (FAQ) sur la gestion de la capacité](azure-netapp-files-faqs.md#capacity-management-faqs)