---
title: Modèle de coût pour les fichiers de NetApp Azure | Microsoft Docs
description: Décrit le modèle de coût pour les fichiers de NetApp Azure pour la gestion des dépenses du service.
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
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: b06e3366224b90899dd3f9f9439edf897de82794
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524219"
---
# <a name="cost-model-for-azure-netapp-files"></a>Modèle de coût pour Azure NetApp Files 

Présentation du modèle de coût pour les fichiers de NetApp Azure vous permet de gérer vos dépenses à partir du service.

## <a name="calculation-of-capacity-consumption"></a>Calcul de la consommation de capacité

Les fichiers NetApp Azure est facturé sur la capacité de stockage configurée.  Capacité allouée est allouée en créant des pools de capacité.  Pools de capacité sont facturés en fonction $/ approvisionné-Go/mois incréments horaires. La taille minimale d’un pool de capacité unique est de 4 TIO et pools de capacité peuvent être développées par la suite en incréments de 1 TIO. Les volumes sont créés dans des pools de capacité.  Chaque volume est affecté à un quota qui décrémente à partir de la capacité d’allocation de pools. Le quota qui peut être affecté à des volumes plages à partir d’un minimum de 100 Go à un maximum de TIO 92.  

Pour un volume actif, la consommation de capacité dans le quota est basée sur la capacité (réelle) logique.

Si la consommation de la capacité réelle d’un volume dépasse son quota de stockage, le volume peut continuer à augmenter. Écritures sont toujours autorisées tant que la taille du volume réel est inférieur à la limite du système (100 To).  

La capacité totale utilisée dans un pool de capacité par rapport à son montant approvisionné est la somme des supérieure du quota attribué ou réel la consommation de tous les volumes au sein du pool : 

   ![Calcul de la capacité totale utilisée](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Le diagramme ci-dessous illustre ces concepts.  
* Nous avons un pool de capacité avec 4 TIO capacité déployée.  Le pool contient trois volumes.  
    * Volume 1 est affectée à un quota de 2 TIO et a 800 Go de consommation.  
    * Volume 2 est affecté à un quota de 1 TIO et a 100 Go de consommation.  
    * Volume 3 est affecté à un quota de 500 Go, mais a 800 Go de consommation (dépassement).  
* Le pool de capacité est limitée pour 4 To de capacité (le montant configuré).  
    3.8 TIO de capacité est consommée (2 TIO et 1 TIO de quota de Volumes 1 et 2 et 800 Go de consommation réelle pour le Volume 3). Et 200 Go de capacité restante.

   ![Pool de capacité avec trois volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Dépassement de la consommation de capacité  

Lorsque le total utilisé la capacité d’un pool dépasse sa capacité déployée, écritures de données sont toujours autorisés.  Après la période de grâce (une heure), si la capacité utilisée du pool dépasse encore sa capacité déployée, puis la taille du pool est incrémentée automatiquement par incréments de 1 TIO jusqu'à ce que la capacité configurée est supérieure à la capacité totale utilisée.  Par exemple, dans l’illustration ci-dessus, si le Volume 3 continue de croître et de la consommation réelle atteint 1,2 To, puis après la période de grâce, le pool sera automatiquement être redimensionné à 5 To.  Le résultat est que la capacité du pool approvisionné (5 To) dépasse la capacité utilisée (TIO 4.2).  

## <a name="manual-changes-of-the-pool-size"></a>Modifications manuelles de la taille du pool  

Vous pouvez augmenter ou diminuer la taille du pool manuellement. Toutefois, les contraintes suivantes s’appliquent :
* Limites minimales et maximales du service  
    Consultez l’article sur [limites de ressources](azure-netapp-files-resource-limits.md).
* Un incrément de 1 TIO après l’achat minimum de 4 TIO initiale
* Un incrément de facturation minimale d’une heure
* La taille du pool approvisionné ne peut pas être réduite à moins que le total de capacité utilisée dans le pool.

## <a name="behavior-of-maximum-size-pool-overage"></a>Comportement de dépassement de-taille maximale du pool   

La taille maximale d’un pool de capacité que vous pouvez créer ou redimensionner sur est 500 TIO.  Lorsque le total de capacité utilisée dans un pool de capacité dépasse 500 TIO, les situations suivantes seront produit :
* Écritures de données sont toujours autorisées (si le volume est inférieur au maximum de système de 100 To).
* Après la période de grâce d’une heure, le pool doit être automatiquement redimensionné par incréments de 1 TIO, jusqu'à ce que la capacité du pool approvisionné dépasse la capacité totale utilisée.
* Supplémentaires configurés et facturés de capacité du pool supérieure à 500 TIO ne peut pas être utilisée pour affecter de quota de volume. Il ne peut pas également être utilisé pour développer les limites de qualité de service de performances.  
    Consultez [les niveaux de service](azure-netapp-files-service-levels.md) sur les limites de performances et dimensionnement de la qualité de service.

Le diagramme ci-dessous illustre ces concepts :
* Nous avons un pool de capacité avec un niveau de stockage Premium et une capacité de 500 TIO. Le pool contient neuf volumes.
    * Volumes 1 à 8 sont affectés à un quota de 60 TIO chacune.  La capacité totale utilisée est to 480.  
        Chaque volume a une limite de qualité de service de 3,75 Gio/s de débit (TIO 60 * 64 Mio/s).  
    * Volume 9 est assigné un quota de 20 To.  
        Volume 9 a une limite de qualité de service de 1,25 Gio/s de débit (TIO 60 * 64 Mio/s).
* Volume 9 est un scénario de dépassement. Elle possède 25 TIO de la consommation réelle.  
    * Après la période de grâce d’une heure, le pool de capacité doit être redimensionné sur TIO 505.  
        Autrement dit, nombre total de capacité utilisées = 8 * 60-quota TIO pour les Volumes 1 à 8 et 25 TIO de la consommation réelle pour Volume 9.
    * La capacité facturée est to 505.
    * Impossible d’augmenter le quota de volume pour le Volume 9 (car le quota total attribué pour le pool ne doit pas dépasse 500 TIO).
    * Débit de qualité de service supplémentaire ne peut pas être assigné (étant donné que la qualité de service total pour le pool est toujours basée sur 500 TIO).

   ![Pool de capacité avec neuf des volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Consommation de la capacité de captures instantanées 

La consommation de capacité de captures instantanées de fichiers NetApp de Azure est facturée par rapport au quota du volume parent.  Par conséquent, il partage le même taux de facturation en tant que le pool de capacité à laquelle appartient le volume.  Toutefois, contrairement au volume actif, la consommation de la capture instantanée est mesurée en fonction de la capacité incrémentielle utilisée.  Les instantanés de fichiers de NetApp Azure sont différentielles par nature. Selon le taux de modification des données, les instantanés consomment souvent beaucoup moins puissant que la capacité logique du volume actif. Par exemple, supposons que vous disposez d’un instantané d’un volume de 500 Gio qui contient uniquement les 10 Go de données différentielles. La capacité est facturée par rapport au quota de volume pour cet instantané serait de 10 Gio, pas de 500 Go. 

## <a name="next-steps"></a>Étapes suivantes

* [Azure Files NetApp page de tarification](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Niveaux de service pour Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
