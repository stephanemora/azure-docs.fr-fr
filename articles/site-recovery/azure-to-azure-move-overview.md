---
title: Déplacer des machines virtuelles IaaS Azure vers une autre région Azure à l’aide du service Azure Site Recovery | Microsoft Docs
description: Utilisez Azure Site Recovery pour déplacer des machines virtuelles IaaS Azure d’une région Azure à l’autre.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: a73eac1dea731bbf1ffb903ddf2438e791fec9d5
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726447"
---
# <a name="move-azure-vms-to-another-region"></a>Déplacer des machines virtuelles Azure vers une autre région

Azure enregistre une croissance galopante à l’image de sa base de clients et prend en charge de plus en plus de nouvelles régions pour répondre à une demande en hausse. Chaque service voit le nombre de ses nouvelles fonctionnalités augmenter tous les mois. C’est pour cela que vous pouvez être amené à déplacer vos machines virtuelles dans une autre région ou dans d’autres zones de disponibilité pour en augmenter la disponibilité.

Ce document décrit les différents scénarios de déplacement des machines virtuelles et vous guide dans la manière de configurer l’architecture dans la cible pour atteindre un meilleur niveau de disponibilité. 
> [!div class="checklist"]
> * [Pourquoi déplacer des machines virtuelles Azure ?](#why-would-you-move-azure-vms)
> * [Comment déplacer des machines virtuelles Azure ?](#how-to-move-azure-vms)
> * [Architectures classiques](#typical-architectures-for-a-multi-tier-deployment)
> * [Déplacer des machines virtuelles en l’état vers une région cible](#move-azure-vms-to-another-region)
> * [Déplacer des machines virtuelles pour augmenter la disponibilité](#move-vms-to-increase-availability)


## <a name="why-would-you-move-azure-vms"></a>Pourquoi déplacer des machines virtuelles Azure ?

Les clients déplacent les machines virtuelles pour les raisons suivantes :

- Si vous aviez déjà effectué un déploiement dans une région et qu’une nouvelle région plus proche des utilisateurs finaux de votre application ou service devenait prise en charge, vous pourriez avoir envie de **déplacer vos machines virtuelles en l’état vers cette nouvelle région** pour des questions de réduction de latence. Vous adopteriez la même approche si vous vouliez regrouper des abonnements ou si des règles de gouvernance/de l’organisation vous obligeaient à effectuer un tel déplacement. 
- Si votre machine virtuelle était déployée en tant qu’instance unique ou dans le cadre d’un groupe à haute disponibilité, vous pourriez, pour augmenter les SLA de disponibilité, **la déplacer vers une zone de disponibilité**. 

## <a name="how-to-move-azure-vms"></a>Comment déplacer des machines virtuelles Azure ?
Déplacer des machines virtuelles implique les étapes suivantes :

1. Vérifier la configuration requise 
2. Préparer les machines virtuelles sources 
3. Préparer la région cible 
4. Copier les données dans la région cible - Utiliser la technologie de réplication Azure Site Recovery pour copier les données de la machine virtuelle source vers la région cible
5. Tester la configuration : Une fois que la réplication est terminée, testez la configuration en effectuant un test de basculement vers un réseau hors production.
6. Effectuer le déplacement 
7. Supprimer les ressources dans la région source 


> [!IMPORTANT]
> Actuellement, Azure Site Recovery prend en charge le déplacement des machines virtuelles d’une région à une autre, mais pas le déplacement au sein d’une région. 

> [!NOTE]
> Des instructions détaillées sur ces étapes sont fournies dans la documentation de chaque scénario, comme indiqué [ici](#next-steps).

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Architectures classiques d’un déploiement multicouche
La section ci-dessous décrit les architectures de déploiement les plus courantes que les clients adoptent pour une application multicouche dans Azure. L’exemple auquel nous nous intéressons ici est celui d’une application à trois couches avec une adresse IP publique. Chaque couche (Web, Application et Base de données) a deux machines virtuelles et se connecte aux autres couches par le biais d’un équilibreur de charge. La couche Base de données a une réplication SQL Always ON entre les machines virtuelles à des fins de haute disponibilité (HA).

1.  **Machines virtuelles à instance unique déployées sur diverses couches** : chaque machine virtuelle d’une couche est configurée en tant qu’instance unique, connectée par des équilibreurs de charge aux autres couches. Cette configuration est la plus simple que les clients adoptent.

       ![single-VMs](media/move-vm-overview/regular-deployment.PNG)

2. **Machines virtuelles de chaque couche déployées dans un groupe à haute disponibilité** : chaque machine virtuelle d’une couche est configurée dans un groupe à haute disponibilité. Les [groupes à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) garantissent que les machines virtuelles que vous déployez sur Azure sont distribuées sur plusieurs nœuds matériels isolés dans un cluster. Leur utilisation garantit qu’en cas de défaillance matérielle ou logicielle dans Azure, seul un sous-ensemble de vos machines virtuelles est affecté et que votre solution globale reste disponible et opérationnelle. 
   
      ![avset](media/move-vm-overview/AVset.PNG)

3. **Machines virtuelles de chaque couche déployées dans un groupe à haute disponibilité** : chaque machine virtuelle d’une couche est configurée dans des [zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Une zone de disponibilité dans une région Azure est une combinaison d’un domaine d’erreur et d’un domaine de mise à jour. Par exemple, si vous créez trois ou plusieurs machines virtuelles dans trois zones d’une région Azure, vos machines virtuelles sont efficacement réparties sur trois domaines d’erreur et trois domaines de mise à jour. La plateforme Azure reconnaît cette répartition entre les domaines de mise à jour pour vous assurer que les machines virtuelles des différentes zones ne sont pas mises à jour en même temps.

      ![zone-deploymnt](media/move-vm-overview/zone.PNG)



## <a name="move-vms-as-is-to-a-target-region"></a>Déplacer des machines virtuelles en l’état vers une région cible

Selon les [architectures](#typical-architectures-for-a-multi-tier-deployment) mentionnées ci-dessus, voici ce à quoi vont ressembler les déploiements une fois que vous aurez effectué le déplacement en l’état vers la région cible.


1. **Machines virtuelles à instance unique déployées sur diverses couches** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **Machines virtuelles de chaque couche déployées dans un groupe à haute disponibilité**

     ![crossregionAset.PNG](media/move-vm-overview/crossregionAset.PNG)


3. **Machines virtuelles de chaque couche déployées dans des zones de disponibilité**
      

     ![AzoneCross.PNG](media/move-vm-overview/AzoneCross.PNG)

## <a name="move-vms-to-increase-availability"></a>Déplacer des machines virtuelles pour augmenter la disponibilité

1. **Machines virtuelles à instance unique déployées sur diverses couches** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **Machines virtuelles de chaque couche déployées dans un groupe à haute disponibilité** : vous pouvez choisir de placer vos machines virtuelles incluses dans un groupe à haute disponibilité dans des zones de disponibilité distinctes, quand vous choisissez d’activer la réplication de vos machines virtuelles à l’aide d’Azure Site Recovery. Le contrat de niveau de service (SLA) s’élèvera à 99,9 % de disponibilité quand vous aurez terminé l’opération de déplacement.

     ![aset-Azone.PNG](media/move-vm-overview/aset-Azone.PNG)


## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous trouverez des instructions générales sur le déplacement des machines virtuelles. Pour obtenir les procédures pas à pas, reportez-vous aux articles suivants :


> [!div class="nextstepaction"]
> * [Déplacer des machines virtuelles Azure vers une autre région](azure-to-azure-tutorial-migrate.md)

> * [Déplacer des machines virtuelles Azure vers des zones de disponibilité](move-azure-VMs-AVset-Azone.md)

