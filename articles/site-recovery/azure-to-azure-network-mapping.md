---
title: Mappage de réseaux virtuels entre deux régions Azure dans Azure Site Recovery | Microsoft Docs
description: Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles et des serveurs physiques. Informez-vous sur le basculement dans Microsoft Azure ou un centre de données secondaire.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: mayg
ms.openlocfilehash: 99c7309e22d8ebe61a0a85b38c92bd3027977848
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013126"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Configurer le mappage réseau et l’adressage IP pour les réseaux virtuels

Cet article explique comment mapper deux instances de réseaux virtuels Azure situés dans différentes régions Azure et comment configurer l’adressage IP entre les réseaux. Mappage réseau fournit un comportement par défaut pour la sélection du réseau cible basée sur le réseau source au moment de l’activation de la réplication.

## <a name="prerequisites"></a>Conditions préalables

Avant de mapper des réseaux, vous devez avoir des [réseaux virtuels Azure](../virtual-network/virtual-networks-overview.md) dans les régions Azure source et cible. 

## <a name="set-up-network-mapping-manually-optional"></a>Configurer réseau mappage manuellement (facultatif)

Mappez les réseaux comme suit :

1. Dans **Infrastructure Site Recovery**, cliquez sur **+Mappage réseau**.

    ![ Créer un mappage réseau](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. Dans **Ajouter un mappage réseau**, sélectionnez les emplacements source et cible. Dans notre exemple, la machine virtuelle source est en cours d’exécution dans la région Asie Est et elle est répliquée dans la région Asie Sud-Est.

    ![Sélectionner la source et la cible](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Maintenant, créez un mappage réseau dans le répertoire opposé. Dans notre exemple, la source sera désormais la région Asie Sud-Est, et la cible dans la région Asie Est.

    ![Volet Ajouter mappage réseau - Sélectionner les emplacements source et cible pour le réseau cible](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mapper des réseaux lorsque vous activez la réplication

Si vous n’avez pas préparé le mappage réseau avant de configurer la récupération d’urgence pour les machines virtuelles Azure, vous pouvez spécifier un réseaux cible lorsque vous [configurez et activez la réplication](azure-to-azure-how-to-enable-replication.md). Quand vous procédez ainsi, voici ce qui se passe :

- En fonction de la cible sélectionnée, Site Recovery crée automatiquement des mappages réseau de la région source à la région cible et de la région cible à la région source.
- Par défaut, Site Recovery crée dans la région cible un réseau qui est identique au réseau source. Site Recovery ajoute **-asr** comme suffixe au nom du réseau source. Vous pouvez personnaliser le réseau cible.
- Si le mappage réseau a déjà eu lieu pour un réseau source, le réseau cible mappé sera toujours la valeur par défaut au moment de l’activation de réplications davantage de machines virtuelles. Vous pouvez choisir de modifier le réseau virtuel cible en choisissant d’autres options disponibles dans la liste déroulante. 
- Pour modifier le réseau virtuel de la cible par défaut pour les réplications de nouveau, vous devez modifier le mappage réseau existant.
- Si vous souhaitez modifier un mappage réseau de la région A à la région B, vérifiez que vous tout d’abord supprimer le mappage réseau à partir de la région B vers la région A. Après la suppression du mappage inverse, modifiez le mappage réseau de la région A à la région B, puis créez le mappage inverse pertinentes.

>[!NOTE]
>* Modifier le mappage réseau modifie uniquement les valeurs par défaut pour les réplications de nouvelle machine virtuelle. Il n’affecte pas les sélections de réseau virtuel cible pour les réplications existantes. 
>* Si vous souhaitez modifier le réseau cible pour une réplication existante, accédez à paramètres calcul et réseau de l’élément répliqué.

## <a name="specify-a-subnet"></a>Spécifier un sous-réseau

Le sous-réseau de la machine virtuelle cible est sélectionné en fonction du nom du sous-réseau de la machine virtuelle source.

- S’il existe sur le réseau cible un sous-réseau portant le même nom que le sous-réseau de la machine virtuelle source, il sera défini pour la machine virtuelle cible.
- S’il n’y a aucun sous-réseau du même nom sur le réseau cible, le premier sous-réseau dans l’ordre alphabétique est défini comme sous-réseau cible.
- Vous pouvez modifier cela dans les paramètres **Calcul et réseau** de la machine virtuelle.

    ![Fenêtre Propriétés de calcul et réseau](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Configurer l’adressage IP pour les machines virtuelles cibles

L’adresse IP pour chaque carte réseau sur une machine virtuelle cible est configurée comme suit :

- **DHCP** : si la carte réseau de la machine virtuelle source utilise le protocole DHCP, la carte réseau de la machine virtuelle cible est également configurée pour utiliser DHCP.
- **Adresse IP statique** : si la carte réseau de la machine virtuelle source utilise un adressage IP statique, la carte réseau de la machine virtuelle cible utilise également une adresse IP statique.


## <a name="ip-address-assignment-during-failover"></a>Affectation d’adresses IP lors du basculement

**Sous-réseaux source et cible** | **Détails**
--- | ---
Même espace d’adressage | L’adresse IP de la carte réseau de la machine virtuelle source est définie comme l’adresse IP de la carte réseau de la machine virtuelle cible.<br/><br/> Si l’adresse n’est pas disponible, l’adresse IP suivante disponible est définie comme cible.

Espace d’adressage différent<br/><br/> L’adresse IP suivante disponible dans le sous-réseau cible est définie comme adresse de la carte réseau de la machine virtuelle cible.



## <a name="ip-address-assignment-during-test-failover"></a>Affectation d’adresses IP lors du test de basculement

**Réseau cible** | **Détails**
--- | ---
Le réseau cible est le réseau virtuel pour le basculement | - L’adresse IP cible est statique, mais n’est pas la même adresse IP que celle réservée pour le basculement.<br/><br/>  - L’adresse attribuée est la prochaine adresse disponible à partir de la fin de la plage du sous-réseau.<br/><br/> Par exemple :  si l’adresse IP source est 10.0.0.19 et que le réseau de basculement utilise la plage 10.0.0.0/24, alors l’adresse IP suivante attribuée à la machine virtuelle cible est 10.0.0.254.
Le réseau cible n’est pas le réseau virtuel pour le basculement | - L’adresse IP cible est statique avec la même adresse IP réservée pour le basculement.<br/><br/>  - Si la même adresse IP est déjà attribuée, alors l’adresse IP est la prochaine disponible dans la plage du sous-réseau.<br/><br/> Par exemple :  si l’adresse IP source est 10.0.0.19 et que le basculement se trouve sur un réseau qui n’est pas le réseau de basculement, avec la plage 10.0.0.0/24, alors l’adresse IP statique cible est 10.0.0.0.19 si elle est disponible, et dans le cas contraire, 10.0.0.254.

- Le réseau virtuel de basculement est le réseau cible que vous sélectionnez lorsque vous configurez la récupération d’urgence.
- Nous vous recommandons de toujours utiliser un réseau de non production pour le test de basculement.
- Vous pouvez modifier l’adresse IP cible dans les paramètres **Calcul et réseau** de la machine virtuelle.


## <a name="next-steps"></a>Étapes suivantes

- Consultez les [conseils sur la mise en réseau](site-recovery-azure-to-azure-networking-guidance.md) pour la récupération d’urgence de machines virtuelles Azure.
- [En savoir plus](site-recovery-retain-ip-azure-vm-failover.md) sur la conservation des adresses IP après le basculement.

Si le réseau cible choisi est le réseau virtuel de basculement » et 2e point pour indiquer « Si le réseau cible choisi est différent du réseau virtuel de basculement mais avec la même plage de sous-réseau que le réseau virtuel de basculement »