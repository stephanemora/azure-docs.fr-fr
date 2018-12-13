---
title: Basculer et restaurer automatiquement des machines virtuelles Hyper-V pour la reprise d’activité sur Azure avec Azure Site Recovery | Microsoft Docs
description: Découvrez comment basculer et restaurer automatiquement des machines virtuelles Hyper-V pour la reprise d’activité sur Azure avec le service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ea372b4363ce506b926a37686b85cb36e51546eb
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833452"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Basculer et restaurer automatiquement des machines virtuelles Hyper-V répliquées sur Azure

Ce didacticiel explique comment basculer une machine virtuelle Hyper-V vers Azure. Une fois que vous avez procédé au basculement, vous restaurez automatiquement sur votre site local quand il est disponible. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Vérifier la conformité des propriétés des machines virtuelles Hyper-V aux spécifications d’Azure
> * Effectuer un basculement vers Azure
> * Restaurer automatiquement depuis Azure vers un site local
> * Inverser la réplication de machines virtuelles locales pour redémarrer la réplication vers Azure

Ce didacticiel est le cinquième d’une série. Il suppose que vous avez déjà effectué les tâches des didacticiels précédents.    

1. [Préparer Azure](tutorial-prepare-azure.md)
2. [Préparer un serveur Hyper-V local](tutorial-prepare-on-premises-hyper-v.md)
3. Configurer la récupération d’urgence pour les [machines virtuelles Hyper-V](tutorial-hyper-v-to-azure.md) ou pour les [machines virtuelles Hyper-V gérées par les clouds System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Effectuer un test de récupération d’urgence](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Préparer le basculement et la restauration automatique

Assurez-vous qu’il n’existe aucun instantané sur la machine virtuelle et que la machine virtuelle locale est arrêtée pendant la restauration automatique. Ceci permet de garantir la cohérence des données pendant la réplication. N’activez pas la machine virtuelle locale pendant la restauration automatique. 

Le basculement et la restauration automatique comportent trois étapes :

1. **Basculement vers Azure** : basculement de machines virtuelles Hyper-V du site local vers Azure.
2. **Restauration automatique sur un site local** : basculement de machines virtuelles Azure vers votre site local, lorsqu’il est disponible. La synchronisation des données à partir d’Azure vers le site local débute et une fois l’opération terminée, les machines virtuelles se trouvent en local.  
3. **Inverser la réplication de machines virtuelles locales** : une fois le basculement en local effectué, inversez la réplication des machines virtuelles locales pour démarrer leur réplication vers Azure.

## <a name="verify-vm-properties"></a>Vérifier les propriétés de la machine virtuelle

Avant le basculement, vérifiez les propriétés de la machine virtuelle et que la machine virtuelle est conforme aux [conditions requises pour Azure](hyper-v-azure-support-matrix.md#replicated-vms).

Dans **Éléments protégés**, cliquez sur **Éléments répliqués** > Machine virtuelle.

2. Dans le volet **Élément répliqué**, vous voyez un récapitulatif des informations de la machine virtuelle, son état d’intégrité et ses derniers points de récupération disponibles. Cliquez sur **Propriétés** pour obtenir plus de détails.

3. Dans **Calcul et réseau**, vous pouvez modifier le nom Azure, le groupe de ressources, la taille cible, le [groupe à haute disponibilité](../virtual-machines/windows/tutorial-availability-sets.md) et les paramètres de disque managé.

4. Vous pouvez afficher et modifier les paramètres réseau, notamment le réseau/sous-réseau dans lequel la machine virtuelle Azure se trouvera après le basculement et l’adresse IP à lui affecter.

5. Des informations sur les disques de données et du système d’exploitation de la machine virtuelle s’affichent dans **Disques**.

## <a name="failover-to-azure"></a>Basculement vers Azure

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle > **Basculer**.
2. Dans **Basculement** sélectionnez le point de récupération **le plus récent**. 
3. Sélectionnez **Arrêter la machine avant de commencer le basculement**. Site Recovery tente d’arrêter les machines virtuelles source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.
4. Après avoir vérifié le basculement, cliquez sur **Valider**. Ceci supprime tous les points de récupération disponibles.

> [!WARNING]
> **N’annulez pas un basculement en cours** : si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Restauration automatique de machine virtuelle Azure vers un site local et inverser la réplication de la machine virtuelle locale

L’opération de restauration automatique est essentiellement un basculement d’Azure vers le site local et, dans l’inversion de la réplication, elle redémarre la réplication des machines virtuelles du site local vers Azure.

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle > **Basculement planifié**.
2. Dans **Confirmer le basculement planifié**, vérifiez le sens du basculement (depuis Azure), et sélectionnez les emplacements source et cible.
3. Sélectionnez **Synchroniser les données avant le basculement (synchroniser seulement les modifications d’ordre différentiel)**. Cette option réduit le temps d’arrêt de machine virtuelle, car il synchronise la machine virtuelle sans l’arrêter.
4. Lancez le basculement. Vous pouvez suivre la progression du basculement sur l’onglet **Tâches** .
5. Quand la synchronisation initiale des données est terminée et que vous êtes prêt à arrêter les machines virtuelles Azure, cliquez sur **Tâches** > Nom de la tâche de basculement planifié > **Terminer le basculement**. Ceci arrête la machine virtuelle Azure, transfère les dernières modifications locales et démarre la machine virtuelle locale.
6. Connectez-vous à la machine virtuelle locale pour vérifier qu’elle est disponible comme prévu.
7. La machine virtuelle locale est maintenant dans l’état **Validation en attente**. Cliquez sur **Valider**. Ceci supprime les machines virtuelles Azure et leurs disques, et prépare la machine virtuelle locale pour la réplication inverse.
Pour démarrer la réplication de la machine virtuelle locale sur Azure, activez **Réplication inverse**. Ceci déclenche la réplication des modifications delta qui se sont produites depuis que la machine virtuelle Azure a été désactivée.  
