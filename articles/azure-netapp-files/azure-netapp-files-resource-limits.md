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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: b9dd89e38f5cab6dceca36201695c068e0521943
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034864"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites des ressources pour Azure NetApp Files

Comprendre les limites des ressources pour Azure NetApp Files vous permet de gérer vos volumes.

## <a name="resource-limits"></a>Limites des ressources

Le tableau suivant décrit les limites des ressources pour Azure NetApp Files :

|  Ressource  |  Limite par défaut  |  Ajustable via une demande de support  |
|----------------|---------------------|--------------------------------------|
|  Nombre de comptes NetApp par abonnement Azure   |  10    |  OUI   |
|  Nombre de pools de capacités par compte NetApp   |    25     |   OUI   |
|  Nombre de volumes par pool de capacités     |    500   |    OUI     |
|  Nombre d’instantanés par volume       |    255     |    Non        |
|  Nombre de sous-réseaux délégués à Azure NetApp Files (Microsoft.NetApp/volumes) par réseau virtuel Azure    |   1   |    Non    |
|  Nombre maximal d’adresses IP en cours d’utilisation dans un réseau virtuel (notamment les réseaux virtuels appairés) avec Azure NetApp Files    |    1 000   |    Non   |
|  Taille minimale d’un pool de capacités   |  4 Tio     |    Non  |
|  Taille maximale d’un pool de capacités    |  500 Tio   |   Non   |
|  Taille minimale d’un volume    |    100 Gio    |    Non    |
|  Taille maximale d’un volume     |    100 Tio    |    Non       |
|  Nombre maximal de fichiers (inodes) par volume     |    50 millions    |    Non    |    

## <a name="request-limit-increase"></a>Augmentation de la limite de demandes 

Vous pouvez créer une demande de support Azure pour augmenter les limites ajustables à partir du tableau ci-dessus. 

À partir du volet de navigation du portail Azure : 

1. Cliquez sur **Aide + support**.
2. Cliquez sur **Nouvelle demande de support**.
3. Sous l’onglet De base, fournissez les informations suivantes : 
    1. Type de problème : Sélectionnez **Limites du service et de l’abonnement (quotas)** .
    2. Abonnements : Sélectionnez l’abonnement pour la ressource dont vous voulez augmenter le quota.
    3. Type de quota : Sélectionnez **Stockage : limites d’Azure NetApp Files**.
    4. Cliquez sur **Suivant : Solutions**.
4. Sous l’onglet Détails :
    1. Dans la zone Description, fournissez les informations suivantes pour le type de ressource correspondant :

        |  Ressource  |    Ressources parentes      |    Nouvelles limites demandées     |    Raison de l’augmentation du quota       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Compte |  *Identifiant d’abonnement*   |  *Nouveau **nombre** maximal demandé*    |  *Quel scénario ou cas d’utilisation a motivé la demande ?*  |
        |  pool    |  *ID d’abonnement, URI du compte*  |  *Nouveau nombre maximal de **pools** demandé*   |  *Quel scénario ou cas d’utilisation a motivé la demande ?*  |
        |  Volume  |  *ID d’abonnement, URI du compte, URI du pool*   |  *Nouveau **volume** maximal demandé*     |  *Quel scénario ou cas d’utilisation a motivé la demande ?*  |

    2. Spécifiez la méthode de support appropriée et fournissez les informations relatives à votre contrat.

    3. Cliquez sur **Suivant : Vérifier + créer** pour créer la demande. 


## <a name="next-steps"></a>Étapes suivantes  

- [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modèle de coût pour Azure NetApp Files](azure-netapp-files-cost-model.md)
