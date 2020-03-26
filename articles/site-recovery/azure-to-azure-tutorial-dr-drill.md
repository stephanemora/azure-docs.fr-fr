---
title: Exécuter une procédure de reprise d’activité après sinistre pour des machines virtuelles Azure à l’aide d’Azure Site Recovery
description: Découvrez comment exécuter une procédure de reprise d’activité après sinistre pour des machines virtuelles Azure vers une région secondaire à l’aide du service Azure Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76166194"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Exécuter une procédure de reprise d’activité après sinistre pour des machines virtuelles Azure vers une région secondaire

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de reprise et de continuité d’activité en garantissant le bon fonctionnement et la disponibilité de vos applications métier pendant les interruptions planifiées et non planifiées. Site Recovery gère et orchestre la récupération d’urgence des machines locales et des machines virtuelles Azure, notamment la réplication, le basculement et la récupération.

Ce didacticiel vous montre comment exécuter une simulation de récupération d’urgence pour une machine virtuelle Azure, d’une région Azure à une autre, avec un test de basculement. Une simulation valide votre stratégie de réplication sans perte de données ou temps d’arrêt et n’affecte pas votre environnement de production. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Vérifier les prérequis
> * Exécuter un test de basculement pour une seule machine virtuelle

> [!NOTE]
> Ce tutoriel vous aide à effectuer une simulation de reprise d’activité après sinistre avec des étapes minimales. Pour en savoir plus sur les différentes fonctions liées à la réalisation d’une simulation de reprise d’activité, consultez la documentation sur la [réplication](azure-to-azure-how-to-enable-replication.md), les [réseaux](azure-to-azure-about-networking.md), l’[automatisation](azure-to-azure-powershell.md) ou le [dépannage](azure-to-azure-troubleshoot-errors.md) des machines virtuelles Azure.

## <a name="prerequisites"></a>Prérequis

Avant de suivre ce tutoriel, vérifiez les éléments suivants :

- Avant d’exécuter un test de basculement, nous vous recommandons de vérifier les propriétés de la machine virtuelle afin d’être sûr qu’elle est configurée pour la reprise d’activité. Accédez à **Opérations** > **Récupération d’urgence** > **Propriétés** sur la machine virtuelle pour afficher les propriétés de réplication et de basculement.
- **Pour le test de basculement, nous vous recommandons d’utiliser un réseau de machines virtuelles Azure distinct** du réseau par défaut qui était configuré quand vous avez activé la réplication.
- En fonction des configurations réseau source de chaque carte d’interface réseau, vous pouvez spécifier le **sous-réseau**, l’**adresse IP privée**, l’**adresse IP publique**, le **groupe de sécurité réseau** ou l’**équilibreur de charge** à attacher à chaque carte réseau testée dans les paramètres de test de basculement, dans **Calcul et réseau**, avant d’exécuter la simulation de reprise d’activité.

## <a name="run-a-test-failover"></a>Exécuter un test de basculement

Cet exemple montre comment utiliser un coffre Recovery Services pour effectuer un test de basculement de machine virtuelle.

1. Sélectionnez un coffre et accédez à **Éléments protégés** > **Éléments répliqués**, puis sélectionnez une machine virtuelle.
1. Dans **Test de basculement**, sélectionnez un point de récupération à utiliser pour le basculement :
   - **Les dernières** : Traite toutes les données dans le service Site Recovery et fournit le RTO (objectif de délai de récupération) le plus faible.
   - **Dernier point traité** : Effectue un basculement de la machine virtuelle vers le dernier point de récupération traité par Site Recovery. L’horodatage est affiché. Avec cette option, aucun temps n’est consacré à traiter les données ; elle offre donc un objectif de délai de récupération faible.
   - **Dernier point de cohérence des applications** : cette option bascule toutes les machines virtuelles vers le dernier point de récupération de cohérence des applications. L’horodatage est affiché.
   - **Personnalisé** : Effectue un basculement vers un point de récupération particulier. La personnalisation est disponible uniquement quand vous effectuez le basculement d’une seule machine virtuelle, et non un basculement avec un plan de récupération.
1. Sélectionnez le réseau virtuel Azure cible auquel les machines virtuelles Azure dans la région secondaire se connecteront après le basculement.

   > [!NOTE]
   > Si les paramètres de test de basculement sont préconfigurés pour l’élément répliqué, la liste déroulante permettant de sélectionner un réseau virtuel Azure n’est pas visible.

1. Pour démarrer le basculement, sélectionnez **OK**. Pour suivre la progression à partir du coffre, accédez à **Supervision** > **Travaux Site Recovery** et sélectionnez le travail **Test de basculement**.
1. Une fois le basculement terminé, la machine virtuelle Azure de réplication apparaît dans le portail Azure, sous **Machines virtuelles**. Vérifiez que la machine virtuelle est en cours d’exécution, qu’elle est correctement dimensionnée et qu’elle est connectée au réseau approprié.
1. Pour supprimer les machines virtuelles qui ont été créées pendant le test de basculement, sélectionnez **Nettoyer le test de basculement** sur l’élément répliqué ou le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exécuter un basculement de production](azure-to-azure-tutorial-failover-failback.md)
