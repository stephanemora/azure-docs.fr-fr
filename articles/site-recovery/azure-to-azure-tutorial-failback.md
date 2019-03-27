---
title: Restaurer automatiquement des machines virtuelles IaaS Azure répliquées vers une région Azure secondaire pour exécuter un plan de récupération d’urgence avec le service Azure Site Recovery.
description: Découvrez comment restaurer des machines virtuelles Azure avec le service Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: sideeksh
ms.custom: mvc
ms.openlocfilehash: d8721f313907f0e0519dca52f5565853f1c44110
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089696"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Restaurer des machines virtuelles Azure entre des régions Azure

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de reprise d’activité après sinistre en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel explique comment restaurer une machine virtuelle Azure. Après avoir effectué le basculement, vous effectuez la restauration automatique vers la région primaire quand celle-ci est disponible. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> 
> * Restaurer automatiquement la machine virtuelle secondaire
> * Reprotéger la machine virtuelle principale vers la région secondaire
> 
> [!NOTE]
> 
> Ce tutoriel a pour but de guider l’utilisateur à travers les étapes à suivre pour effectuer un basculement vers une région cible avec une personnalisation minimale. Si vous souhaitez en savoir plus sur les différents aspects associés à l’exécution d’un basculement, y compris les éléments à prendre en compte concernant la mise en réseau, l’automation ou le dépannage, consultez les documents dans « Procédure... » pour les machines virtuelles Azure.

## <a name="prerequisites"></a>Prérequis

> * Vérifiez que la machine virtuelle se trouve dans l’état Basculement validé et que la région primaire est disponible ; vous pouvez alors y créer des ressources et accéder à celles-ci.
> * Assurez-vous que la reprotection est activée.

## <a name="fail-back-to-the-primary-region"></a>Effectuer une restauration automatique vers la région primaire

Une fois les machines virtuelles reprotégées, vous pouvez effectuer une restauration automatique vers la région primaire si et quand vous le souhaitez.

1. Accédez à votre coffre Recovery Services. Cliquez sur Éléments répliqués, puis sélectionnez la machine virtuelle qui a été reprotégée.

2. Les éléments suivants doivent s’afficher. Notez les similitudes avec le panneau pour le test de basculement et le basculement à partir de la région primaire.
![Restaurer vers la région primaire](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Cliquez sur Test de basculement pour effectuer un nouveau test de basculement dans votre région primaire. Sélectionnez le point de récupération et le réseau virtuel Azure pour le test de basculement, puis sélectionnez OK. La machine virtuelle de test créée est visible dans la région primaire. Vous pouvez y accéder et l’inspecter.

4. Une fois que le test de basculement est satisfaisant, vous pouvez cliquer sur Nettoyer le test de basculement pour nettoyer les ressources créées dans la région source pour l’occasion.

5. Dans Éléments répliqués, sélectionnez la machine virtuelle que vous souhaitez basculer > Basculement.

6. Dans Basculement, sélectionnez un point de récupération vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :
    1. Dernier (par défaut) : cette option traite toutes les données dans le service Site Recovery et fournit l’objectif de point de récupération (RPO) le plus bas
    2. Dernier point traité : cette option rétablit la machine virtuelle vers le dernier point de récupération ayant été traité par le service Site Recovery
    3. Personnalisé : utilisez cette option pour effectuer un basculement vers un point de récupération spécifique. Cette option est utile pour effectuer un test de basculement

7. Sélectionnez Arrêter la machine avant de commencer le basculement si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Notez que Site Recovery ne nettoie pas la source après le basculement.

8. Suivre la progression du basculement sur la page Tâches

9. Après le basculement, validez la machine virtuelle en vous y connectant. Si vous souhaitez accéder à un autre point de récupération pour la machine virtuelle, utilisez l’option Changer le point de récupération.

10. Si vous êtes satisfait de la machine vers laquelle est effectué le basculement, vous pouvez Valider le basculement. La validation supprime tous les points de récupération disponibles avec le service. L’option Modifier le point de récupération n’est plus disponible.

![Régions primaires et secondaires de machine virtuelle](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

Dans la capture d’écran précédente, la machine virtuelle « ContosoWin2016 » a fait l’objet d’un basculement de la région USA Centre vers la région USA Est, et d’une restauration automatique de la région USA Est vers la région USA Centre.

Notez que les machines virtuelles de reprise d’activité restent dans l’état Arrêté (libéré). Ce comportement est normal, car Azure Site Recovery enregistre les informations de la machine virtuelle qui peuvent s’avérer utiles par la suite lors du basculement de la région primaire vers la région secondaire. Les machines virtuelles libérées ne vous étant pas facturées, gardez-les telles quelles.

> [!NOTE]
> Pour plus d’informations sur le flux de travail de reprotection et sur ce qui se passe pendant la reprotection, consultez [la section de procédure de cet article](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).
