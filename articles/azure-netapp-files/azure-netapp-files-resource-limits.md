---
title: Limites des ressources pour Azure NetApp Files | Microsoft Docs
description: Décrit les limites des ressources Azure NetApp Files, y compris les limites des comptes NetApp, des pools de capacité, des volumes et du sous-réseau délégué.
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
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: b55467d77beb8f97b8e392b72682268ae0407e54
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826370"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites des ressources pour Azure NetApp Files

Comprendre les limites des ressources pour Azure NetApp Files vous permet de gérer vos volumes.

## <a name="resource-limits"></a>Limites des ressources

Le tableau suivant décrit les limites de ressources pour les fichiers de NetApp Azure :

|  Ressource  |  Limite par défaut  |  Ajustable via une demande de support  |
|----------------|---------------------|--------------------------------------|
|  Nombre de comptes NetApp par abonnement Azure   |  10    |  Oui   |
|  Nombre de pools de capacité par compte de NetApp   |    25     |   Oui   |
|  Nombre de volumes par pool de capacité     |    500   |    Oui     |
|  Nombre de captures instantanées par volume       |    255     |    Non        |
|  Nombre de sous-réseaux déléguée à Azure NetApp Files (Microsoft.NetApp/volumes) par réseau virtuel Azure    |   1   |    Non    |
|  Nombre maximal de machines virtuelles (inclut des réseaux virtuels homologués) qui peuvent se connecter à un volume     |    1 000   |    Non   |
|  Taille minimale d’un pool de capacité unique   |  4 Tio     |    Non  |
|  Taille maximale d’un pool de capacité unique    |  500 Tio   |   Non   |
|  Taille minimale d’un volume unique    |    100 Go    |    Non    |
|  Maximale allouée de quota d’un volume unique *   |   92 TiB   |    Non   |
|  Taille maximale d’un volume unique *     |    100 TiB    |    Non       |

* Un volume peut être créé manuellement ou redimensionné sur TIO 92 au maximum. Toutefois, un volume peut augmenter jusqu'à 100 To dans un scénario de dépassement. Consultez [modèle de coût pour les fichiers de NetApp Azure](azure-netapp-files-cost-model.md) pour plus d’informations sur le dépassement de capacité. 

## <a name="request-limit-increase"></a>Demander une augmentation de limite 

Vous pouvez créer une demande de support Azure pour augmenter les limites ajustables dans le tableau ci-dessus. 

À partir du plan de navigation du portail Azure : 

1. Cliquez sur **aide + support**.
2. Cliquez sur **+ nouveau demande de support**.
3. Sous l’onglet Basics, fournissez les informations suivantes : 
    1. Type de problème : Sélectionnez **limites de Service et d’abonnement (quotas)**.
    2. Abonnements : Sélectionnez l’abonnement pour la ressource dont vous avez besoin d’augmenter le quota.
    3. Type de quota : Sélectionnez **stockage : Limites d’Azure Files NetApp**.
    4. Cliquez sur **Suivant : Solutions**.
4. Sous l’onglet Détails :
    1. Dans la zone Description, fournissez les informations suivantes pour le type de ressource correspondant :

        |  Ressource  |    Ressources parentes      |    Nouvelles limites demandées     |    Raison de l’augmentation du quota       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Compte |  *Identifiant d’abonnement*   |  *Nouveau maximum demandé **compte** nombre*    |  *Quel scénario ou cas d’usage vous y êtes invité de la demande ?*  |
        |  Pool    |  *ID d’abonnement, URI du compte*  |  *Nouveau maximum demandé **pool** nombre*   |  *Quel scénario ou cas d’usage vous y êtes invité de la demande ?*  |
        |  Volume  |  *ID d’abonnement, URI, du compte de Pool d’URI*   |  *Nouveau maximum demandé **volume** nombre*     |  *Quel scénario ou cas d’usage vous y êtes invité de la demande ?*  |

    2. Spécifiez approprié prennent en charge de la méthode et fournissez vos informations de contrat.

    3. Cliquez sur **Suivant : Vérifier + créer** pour créer la demande. 


## <a name="next-steps"></a>Étapes suivantes  

- [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modèle de coût pour les fichiers de NetApp Azure](azure-netapp-files-cost-model.md)
