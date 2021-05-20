---
title: Modèle de coût pour Azure NetApp Files | Microsoft Docs
description: Décrit le modèle de coût d’Azure NetApp Files pour faciliter la gestion des dépenses liées à ce service.
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
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: 2df332450c996c1a7b1b9b5e35b06d4fb226ed93
ms.sourcegitcommit: 89c4843ec85d1baea248e81724781d55bed86417
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108794463"
---
# <a name="cost-model-for-azure-netapp-files"></a>Modèle de coût pour Azure NetApp Files 

Une bonne compréhension du modèle de coût d’Azure NetApp Files vous aidera à gérer vos dépenses liées à ce service. 

Pour connaître le modèle de coût spécifique à la réplication interrégion, consultez la section [Modèle de coût pour la réplication interrégion](cross-region-replication-introduction.md#cost-model-for-cross-region-replication).

## <a name="calculation-of-capacity-consumption"></a>Calcul de la consommation de capacité

Azure NetApp Files est facturé en fonction de la capacité de stockage approvisionnée.  La capacité approvisionnée est allouée par la création de pools de capacité.  Les pools de capacité sont facturés en €/Gio approvisionnés/mois par incréments horaires. La taille minimale d’un pool de capacité est de 4 Tio, et les pools de capacité peuvent ensuite être étendus par incréments de 1 Tio. Des volumes sont créés dans les pools de capacité.  Chaque volume reçoit un quota qui se décrémente de la capacité approvisionnée pour les pools. Le quota pouvant être attribué aux volumes est compris entre 100 Gio et 100 Tio.  

Pour un volume actif, le calcul de la consommation de capacité par rapport au quota concerne la capacité logique (effective), soit sur les données du système de fichiers actif, soit sur les données de la capture instantanée. Un volume ne peut contenir que la taille de données définie (quota).

La capacité totale utilisée dans un pool de capacité, par rapport à la quantité provisionnée, correspond à la somme de la consommation réelle de tous les volumes du pool : 

   ![Expression indiquant le calcul de la capacité totale utilisée.](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

## <a name="capacity-consumption-of-snapshots"></a>Consommation de capacité des captures instantanées 

La consommation de capacité des captures instantanées dans Azure NetApp Files est facturée au niveau du quota du volume parent.  Par conséquent, elle applique le même taux de facturation que le pool de capacité auquel appartient le volume.  Toutefois, contrairement au volume actif, la consommation des captures instantanées est mesurée sur la base de la capacité incrémentielle consommée.  Les captures instantanées Azure NetApp Files sont différentielles par nature. Selon le taux de modification des données, les captures instantanées consomment souvent une capacité sensiblement inférieure à la capacité logique du volume actif. Par exemple, supposons que vous disposiez d’une capture instantanée d’un volume de 500 Gio contenant uniquement 10 Gio de données différentielles. La consommation de capacité comptabilisée dans le quota de volume pour le système de fichiers actif et l’instantané serait de 510 Gio, et non 1 000 Gio. En règle générale, il est recommandé de disposer d’une capacité de 20 % pour conserver une semaine de données d’instantané (en fonction de la fréquence des instantanés et des taux quotidiens de modification en mode bloc des applications). 

Le diagramme suivant illustre ces concepts. 

* Prenons un pool de capacité d’une capacité provisionnée de 40 Tio. Il comporte trois volumes :    
    * Le volume 1 se voit attribuer un quota de 20 Tio et présente une consommation de 13 Tio (12 Tio de données actives, 1 Tio d’instantanés).
    * Le volume 2 se voit attribuer un quota de 1 Tio et présente une consommation de 450 Gio.
    * Le volume 3 se voit attribuer un quota de 14 Tio, mais présente une consommation de 8,8 Tio (8 Tio de données actives, 800 Gio d’instantanés).   
* Le pool de capacité est limité à 40 Tio de capacité (quantité provisionnée). Une capacité de 22,25 Tio est consommée (13 Tio, 450 Gio et 8,8 Tio de quota des volumes 1, 2 et 3). La capacité restante du pool s’élève à 17,75 Tio.   

![Diagramme montrant le pool de capacités comportant trois volumes.](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="next-steps"></a>Étapes suivantes

* [Tarification Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Niveaux de service pour Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Modèle de coût pour la réplication inter-région](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
* [Présentation du quota de volume](volume-quota-introduction.md)
* [Surveiller la capacité d'un volume](monitor-volume-capacity.md)
* [Redimensionner le pool de capacité ou un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [Gérer la facturation en utilisant des étiquettes](manage-billing-tags.md)
* [Questions fréquentes (FAQ) sur la gestion de la capacité](azure-netapp-files-faqs.md#capacity-management-faqs)
