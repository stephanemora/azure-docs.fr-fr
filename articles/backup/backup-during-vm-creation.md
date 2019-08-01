---
title: Activer la sauvegarde quand vous créez une machine virtuelle Azure avec Sauvegarde Azure
description: Explique comment activer la sauvegarde quand vous créez une machine virtuelle Azure avec Sauvegarde Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: dacurwin
ms.openlocfilehash: 90f69371457bbfe37789b12971343f738ff35e8e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639719"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Activer la sauvegarde quand vous créez une machine virtuelle Azure

Utilisez le service Sauvegarde Azure pour sauvegarder des machines virtuelles Azure. Les machines virtuelles sont sauvegardées selon une planification spécifiée dans une stratégie de sauvegarde, et les points de récupération sont créés à partir des sauvegardes. Les points de récupération sont stockés dans des coffres Recovery Services.

Cet article explique comment activer la sauvegarde quand vous créez une machine virtuelle dans le portail Azure.  

## <a name="before-you-start"></a>Avant de commencer

- [Vérifiez](backup-support-matrix-iaas.md#supported-backup-actions) quels systèmes d’exploitation sont pris en charge si vous activez la sauvegarde quand vous créez une machine virtuelle.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Si vous n’êtes pas connecté à votre compte, connectez-vous sur le [portail Azure](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Créer une machine virtuelle avec sauvegarde configurée

1. Dans le portail Azure, cliquez sur **Créer une ressource**.

2. Dans la Place de marché Azure, cliquez sur **Calcul**, puis sélectionnez une image de machine virtuelle.

3. Configurez la machine virtuelle conformément aux instructions [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal).

4. Sous l’onglet **Gestion**, dans **Activer la sauvegarde**, cliquez sur **Activé**.
5. Sauvegarde Azure sauvegarde dans un coffre Recovery Services. Cliquez sur **Créer** si vous n’avez pas de coffre.
6. Acceptez le nom de coffre suggéré ou spécifiez le vôtre.
7. Spécifiez ou créez un groupe de ressources dans lequel se trouvera le coffre. Le groupe de ressources du coffre peut être différent du groupe de ressources de la machine virtuelle.

    ![Activer la sauvegarde pour une machine virtuelle](./media/backup-during-vm-creation/enable-backup.png)

8. Acceptez la stratégie de sauvegarde par défaut ou modifiez les paramètres.
    - Une stratégie de sauvegarde spécifie la fréquence de prise d’instantanés de sauvegarde de la machine virtuelle et la durée pendant laquelle ces copies de sauvegarde sont conservées.
    - La stratégie par défaut sauvegarde la machine virtuelle une fois par jour.
    - Vous pouvez personnaliser votre propre stratégie de sauvegarde pour une machine virtuelle Azure de manière à effectuer des sauvegardes quotidiennes ou hebdomadaires.
    - [Découvrez-en plus](backup-azure-vms-introduction.md#backup-and-restore-considerations) sur les considérations relatives à la sauvegarde des machines virtuelles Azure.
    - [Découvrez-en plus](backup-instant-restore-capability.md) sur la fonctionnalité de restauration instantanée.

      ![Stratégie de sauvegarde par défaut](./media/backup-during-vm-creation/daily-policy.png)


> [!NOTE]
> Pour stocker l’instantané, le service Sauvegarde Azure crée un groupe de ressources distinct (autre que le groupe de ressources de la machine virtuelle). Son nom est au format **AzureBackupRG_géographie_numéro** (par exemple, AzureBackupRG_northeurope_1). Les données dans ce groupe de ressources sont conservées pendant la durée en jours spécifiée dans la section *Conserver l’instantané de récupération instantanée* de la stratégie Sauvegarde de machines virtuelles Azure.  L’application d’un verrou à ce groupe de ressources peut entraîner des échecs de sauvegarde.<br>
Ce groupe de ressources doit également être exclu des restrictions de nom/étiquette, car une stratégie de restriction peut y empêcher la création de collections de points de ressources, entraînant de nouveau des échecs de sauvegarde.


## <a name="start-a-backup-after-creating-the-vm"></a>Démarrer une sauvegarde après la création de la machine virtuelle

Votre sauvegarde de machine virtuelle s’exécute conformément à votre stratégie de sauvegarde. Toutefois, nous vous recommandons d’exécuter une sauvegarde initiale.

Une fois la machine virtuelle créée, effectuez les étapes suivantes :

1. Dans les propriétés de la machine virtuelle, cliquez sur **Sauvegarde**. L’état de la machine virtuelle est « Sauvegarde initiale en attente » jusqu’à ce que la sauvegarde initiale s’exécute.
2. Cliquez sur **Sauvegarder maintenant** pour exécuter une sauvegarde à la demande.

    ![Exécuter une sauvegarde à la demande](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Utiliser un modèle Resource Manager pour déployer une machine virtuelle protégée

La procédure précédente explique comment utiliser le portail Azure pour créer une machine virtuelle et la protéger dans un coffre Recovery Services. Pour déployer rapidement une ou plusieurs machines virtuelles et les protéger dans un coffre Recovery Services, consultez le modèle [Déployer une machine virtuelle Windows et activer la sauvegarde](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).



## <a name="next-steps"></a>Étapes suivantes

Votre machine virtuelle étant protégée, découvrez comment la gérer et la restaurer.

- [Gérer et superviser des machines virtuelles](backup-azure-manage-vms.md)
- [Restaurer une machine virtuelle](backup-azure-arm-restore-vms.md)

Si vous rencontrez des problèmes, [consultez](backup-azure-vms-troubleshoot.md) le guide de dépannage.
