---
title: Exécuter une récupération d’urgence pour les machines virtuelles locales vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs
description: En savoir plus sur l’exécution d’un exercice d’extraction de récupération d’urgence à partir d’une machine locale vers Azure, avec Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: c706474018bd0751872381c6d28f0ad579ba772b
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286577"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Effectuer un exercice de récupération d'urgence vers Azure

Dans cet article, nous vous montrons comment effectuer un exercice de récupération d’urgence pour une machine locale vers Azure, à l’aide d’un test de basculement. Un exercice valide votre stratégie de réplication sans perte de données.

Il s’agit du quatrième didacticiel dans une série qui vous montre comment configurer la récupération d’urgence sur Azure pour des machines virtuelles VMware locales.

Ce tutoriel suppose que vous avez effectué les trois premiers tutoriels : 
    - Dans le [premier didacticiel](tutorial-prepare-azure.md), nous avons configuré les composants Azure nécessaires pour la récupération d’urgence de VMware.
    - Dans le [deuxième didacticiel](vmware-azure-tutorial-prepare-on-premises.md), nous avons préparé des composants locaux pour la récupération d’urgence et nous avons passé en revue les conditions préalables.
    - Dans le [troisième didacticiel](vmware-azure-tutorial.md), nous avons configuré et activé la réplication pour notre machine virtuelle VMware locale.
- Les didacticiels sont conçus pour vous montrer le chemin de déploiement le plus simple pour un scénario. Ils utilisent les options par défaut lorsque cela est possible et n’affichent pas tous les paramètres et chemins d’accès possibles. Tous les tutoriels configurent Site Recovery avec les paramètres les plus simples, à l’aide des valeurs par défaut le cas échéant. Si vous souhaitez en savoir plus sur les étapes de basculement de test, lisez le [guide de procédure](site-recovery-test-failover-to-azure.md).

Dans ce tutoriel, vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Configurer un réseau isolé pour le test de basculement
> * Préparer la connexion à la machine virtuelle Azure après le basculement
> * Exécuter un test de basculement pour une seule machine

Ce tutoriel

## <a name="verify-vm-properties"></a>Vérifier les propriétés de la machine virtuelle

Avant d’exécuter un test de basculement, vérifiez les propriétés de la machine virtuelle VMware et assurez-vous que la [machine virtuelle Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms), ou la [machine virtuelle VMware/serveur physique](vmware-physical-azure-support-matrix.md#replicated-machines) soient conformes aux exigences d’Azure.

1. Dans **Éléments protégés**, cliquez sur **Éléments répliqués**, puis sur la machine virtuelle.
2. Dans le volet **Élément répliqué**, vous voyez un récapitulatif des informations de la machine virtuelle, son état d’intégrité et ses derniers points de récupération disponibles. Cliquez sur **Propriétés** pour obtenir plus de détails.
3. Dans **Calcul et réseau**, vous pouvez modifier le nom Azure, le groupe de ressources, la taille cible, le groupe à haute disponibilité et les paramètres de disque managé.
4. Vous pouvez afficher et modifier les paramètres réseau, notamment le réseau/sous-réseau dans lequel la machine virtuelle Azure se trouvera après le basculement et l’adresse IP à lui affecter.
5. Des informations sur les disques de données et du système d’exploitation de la machine virtuelle s’affichent dans **Disques** .

## <a name="run-a-test-failover-for-a-single-vm"></a>Exécuter un test de basculement pour une seule machine virtuelle

Quand vous effectuez un test de basculement, voici ce qui se produit :

1. Une vérification des prérequis est effectuée pour garantir que toutes les conditions nécessaires pour le basculement sont en place.
2. Le basculement traite les données pour permettre la création d’une machine virtuelle Azure. Si vous avez sélectionné le dernier point de récupération, un point de récupération est créé à partir des données.
3. Une machine virtuelle Azure est créée en utilisant les données traitées à l’étape précédente.

Exécutez un test de basculement, en procédant comme suit :

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur Machine virtuelle > **+Test de basculement**.
2. Sélectionnez le **dernier point de récupération traité** dans ce didacticiel. Cela bascule l’ordinateur virtuel vers la dernière version disponible à un moment donné. L’horodatage est affiché. Cette option, avec laquelle aucun temps n’est passé à traiter les données, offre un objectif de délai de récupération faible.
3. Dans **Tester le basculement**, sélectionnez le réseau Azure cible auquel les machines virtuelles Azure seront connectées après le basculement.
4. Cliquez sur **OK** pour commencer le basculement. Vous pouvez suivre la progression en cliquant sur la machine virtuelle pour ouvrir ses propriétés. Vous pouvez également cliquer sur le travail **Test de basculement** dans le nom du coffre > **Paramètres** > **Travaux** >
   **Travaux Site Recovery**.
5. Une fois le basculement terminé, la machine virtuelle Azure de réplication apparaît dans le portail Azure > **Machines virtuelles**. Vérifiez que la machine virtuelle est de la taille appropriée, qu’elle est connectée au réseau approprié et qu’elle est en cours d’exécution.
6. Vous devriez à présent pouvoir vous connecter à la machine virtuelle répliquée dans Azure.
7. Pour supprimer les machines virtuelles créées lors du test de basculement, cliquez sur **Nettoyer le test de basculement** sur la machine virtuelle. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement.

Dans certains scénarios, le basculement nécessite un traitement supplémentaire qui dure environ huit à dix minutes. Vous constaterez peut-être des délais de basculement plus longs pour les machines VMware Linux, les machines virtuelles VMware pour lesquelles le service DHCP n’est pas activé et les machines virtuelles VMware qui ne disposent pas des pilotes de démarrage suivants : storvsc, vmbus, storflt, intelide, atapi.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exécuter un basculement et une restauration pour des machines virtuelles VMware locales](vmware-azure-tutorial-failover-failback.md).
