---
title: Exécuter une procédure de reprise d’activité après sinistre pour des machines virtuelles Azure à l’aide d’Azure Site Recovery
description: Découvrez comment exécuter une procédure de reprise d’activité après sinistre pour des machines virtuelles Azure vers une région secondaire à l’aide du service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 817a220e36ac250b1d5a5aa90d0bddbfb155cc26
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091330"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Exécuter une procédure de reprise d’activité après sinistre pour des machines virtuelles Azure vers une région secondaire 

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de reprise et de continuité d’activité en garantissant le bon fonctionnement et la disponibilité de vos applications métier pendant les interruptions planifiées et non planifiées. Site Recovery gère et orchestre la récupération d’urgence des machines locales et des machines virtuelles Azure, y compris la réplication, le basculement et la récupération.

Ce didacticiel vous montre comment exécuter une simulation de récupération d’urgence pour une machine virtuelle Azure, d’une région Azure à une autre, avec un test de basculement. Une simulation valide votre stratégie de réplication sans perte de données ou temps d’arrêt et n’affecte pas votre environnement de production. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Vérifier les prérequis
> * Exécuter un test de basculement pour une seule machine virtuelle

> [!NOTE]
> Ce tutoriel vous aide à exécuter une procédure DR (reprise d’activité après sinistre) en un minimum d’étapes. Si vous souhaitez en savoir plus sur les divers aspects associés à l’exécution d’une procédure DR, notamment en ce qui concerne le réseau, l’automatisation ou la résolution des problèmes, consultez les documents situés sous « Procédure » pour les machines virtuelles Azure.

## <a name="prerequisites"></a>Prérequis

- Avant que vous n’exécutiez un test de basculement, nous vous recommandons de vérifier les propriétés de la machine virtuelle pour vérifier que tout fonctionne comme prévu.  Accédez aux propriétés de la machine virtuelle dans les **éléments répliqués**. Le panneau **Bases** affiche des informations sur les paramètres et l’état des machines.
- **Pour le test de basculement, nous vous recommandons d’utiliser un réseau de machines virtuelles Azure distinct** du réseau par défaut qui était configuré quand vous avez activé la réplication.
- Selon les configurations réseau source de chaque carte d’interface réseau, vous pouvez éventuellement spécifier le **sous-réseau, l’adresse IP, l’adresse IP publique, le groupe de sécurité réseau ou l’équilibreur de charge interne** à attacher à chaque carte réseau testée dans les paramètres de test de basculement, dans Calcul et réseau, avant d’exécuter la procédure DR.


## <a name="run-a-test-failover"></a>Exécuter un test de basculement

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur l’icône **+Test de basculement** de la machine virtuelle.

2. Dans **Test de basculement**, sélectionnez un point de récupération à utiliser pour le basculement :

    - **Les dernières** : Traite toutes les données dans le service Site Recovery et fournit le RTO (objectif de délai de récupération) le plus faible.
    - **Dernier point traité** : Effectue un basculement de la machine virtuelle vers le dernier point de récupération traité par Site Recovery. L’horodatage est affiché. Cette option, qui n’implique aucun traitement de données, offre un objectif de délai de récupération faible.
   - **Dernier point de cohérence des applications** : cette option bascule toutes les machines virtuelles vers le dernier point de récupération de cohérence des applications. L’horodatage est affiché.
   - **Personnalisé** : Effectue un basculement vers un point de récupération particulier. La personnalisation est disponible uniquement quand vous effectuez le basculement d’une seule machine virtuelle, et non un basculement avec un plan de récupération.

3. Sélectionnez le réseau virtuel Azure cible auquel les machines virtuelles Azure dans la région secondaire seront connectées après le basculement.

    > [!NOTE]
    > La liste déroulante permettant de sélectionner le réseau virtuel Azure n’est pas visible si les paramètres de test de basculement sont préconfigurés pour l’élément répliqué.

4. Pour démarrer le basculement, cliquez sur **OK**. Pour suivre la progression, cliquez sur la machine virtuelle pour ouvrir ses propriétés. Vous pouvez également cliquer sur le travail **Test de basculement** dans le nom du coffre > **Paramètres** > **Travaux** > **Travaux Site Recovery**.
5. Une fois le basculement terminé, la machine virtuelle Azure de réplication apparaît dans le portail Azure > **Machines virtuelles**. Vérifiez que la machine virtuelle est en cours d’exécution, qu’elle est correctement dimensionnée et qu’elle est connectée au réseau approprié.
6. Pour supprimer les machines virtuelles qui ont été créées pendant le test de basculement, cliquez sur **Nettoyer le test de basculement** sur l’élément répliqué ou le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exécuter un basculement de production](azure-to-azure-tutorial-failover-failback.md)
