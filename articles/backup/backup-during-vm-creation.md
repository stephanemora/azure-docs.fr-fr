---
title: Activer la sauvegarde lorsque vous créez une machine virtuelle Azure avec sauvegarde Azure
description: Décrit comment activer la sauvegarde lorsque vous créez une machine virtuelle Azure avec sauvegarde Azure.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: d96b898c8f72abd7e4eb3522ae046e9fc926f387
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809319"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Activer la sauvegarde quand vous créez une machine virtuelle Azure

Utiliser le service Azure Backup pour sauvegarder des machines virtuelles (VM) Azure. Machines virtuelles sont sauvegardées selon une planification spécifiée dans une stratégie de sauvegarde, et les points de récupération sont créés à partir de sauvegardes. Points de récupération sont stockés dans des coffres Recovery Services.

Cet article explique comment activer la sauvegarde lorsque vous créez une machine virtuelle (VM) dans le portail Azure.  

## <a name="before-you-start"></a>Avant de commencer

- [Vérifiez](backup-support-matrix-iaas.md#supported-backup-actions) quels systèmes d’exploitation sont pris en charge si vous activez la sauvegarde lorsque vous créez une machine virtuelle.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Si vous n’êtes pas déjà connecté à votre compte, connectez-vous à la [Azure portal](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Créer une machine virtuelle avec sauvegarde configurée

1. Dans le portail Azure, cliquez sur **créer une ressource**.

2. Dans la place de marché Azure, cliquez sur **calcul**, puis sélectionnez une image de machine virtuelle.

3. Configurer la machine virtuelle conformément à la [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) obtenir des instructions.

4. Sur le **gestion** sous l’onglet **activer la sauvegarde**, cliquez sur **sur**.
5. Sauvegardes de sauvegarde Azure dans un coffre Recovery Services. Cliquez sur **créer un nouveau** si vous n’avez pas un coffre existant.
6. Acceptez le nom suggéré coffre ou spécifiez le vôtre.
7. Spécifiez ou créez un groupe de ressources dans lequel se trouve le coffre. Le coffre de groupe de ressources peut être différent dans le groupe de ressources de machine virtuelle.

    ![Activer la sauvegarde d’une machine virtuelle](./media/backup-during-vm-creation/enable-backup.png) 

8. Accepter la stratégie de sauvegarde par défaut ou modifier les paramètres.
    - Une stratégie de sauvegarde spécifie la fréquence à laquelle prendre des instantanés de sauvegarde de la machine virtuelle et la durée de conservation de ces copies de sauvegarde. 
    - Sauvegarde de la stratégie par défaut de la machine virtuelle une fois par jour.
    - Vous pouvez personnaliser votre propre stratégie de sauvegarde pour une machine virtuelle Azure effectuer des sauvegardes quotidiennes ou hebdomadaires.
    - [En savoir plus](backup-azure-vms-introduction.md#backup-and-restore-considerations) sur les considérations relatives à la sauvegarde des machines virtuelles Azure.
    - [En savoir plus](backup-instant-restore-capability.md) sur le moment restaurer les fonctionnalités.

      ![Stratégie de sauvegarde par défaut](./media/backup-during-vm-creation/daily-policy.png) 


## <a name="start-a-backup-after-creating-the-vm"></a>Démarrer une sauvegarde après avoir créé la machine virtuelle 

Votre sauvegarde de machine virtuelle s’exécute conformément à votre stratégie de sauvegarde. Toutefois, nous vous recommandons d’exécuter une sauvegarde initiale. 

Une fois que la machine virtuelle est créée, procédez comme suit :

1. Dans les propriétés de la machine virtuelle, cliquez sur **sauvegarde**. L’état de la machine virtuelle est initiale sauvegarde en attente jusqu'à ce que la sauvegarde initiale s’exécute
2. Cliquez sur **sauvegarder maintenant** pour exécuter une sauvegarde à la demande.

    ![Exécuter une sauvegarde à la demande](./media/backup-during-vm-creation/run-backup.png) 

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Utiliser un modèle Resource Manager pour déployer une machine virtuelle protégée

Les étapes précédentes expliquent comment utiliser le portail Azure pour créer une machine virtuelle et la protéger dans un coffre Recovery Services. Pour rapidement déployer un ou plusieurs machines virtuelles et les protéger dans un coffre Recovery Services, consultez le modèle [déployer une machine virtuelle Windows et activer la sauvegarde](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).



## <a name="next-steps"></a>Étapes suivantes 

Maintenant que vous avez protégé votre machine virtuelle, vous apprenez à gérer et de les restaurer.

- [Gérer et surveiller des machines virtuelles](backup-azure-manage-vms.md) 
- [Restaurer une machine virtuelle](backup-azure-arm-restore-vms.md) 

Si vous rencontrez des problèmes, [Examinez](backup-azure-vms-troubleshoot.md) le guide de dépannage.
