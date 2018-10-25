---
title: Supprimer un coffre Recovery Services dans Azure
description: Cet article explique comment supprimer un coffre Recovery Services. Il décrit les étapes de résolution des problèmes lorsque la suppression d’un coffre ne fonctionne pas.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 7/6/2018
ms.author: markgal
ms.openlocfilehash: e5bc68b55fdefc07b9764b8393ea6f4062468252
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816461"
---
# <a name="delete-a-recovery-services-vault"></a>Supprimer un coffre Recovery Services

Cet article explique comment supprimer tous les éléments d’un coffre Recovery Services, puis comment supprimer celui-ci. Vous ne pouvez pas supprimer un coffre Recovery Services s’il est inscrit sur un serveur et conserve des données de sauvegarde. Si vous essayez de supprimer un coffre, sans y parvenir, le coffre est encore configuré pour recevoir des données de sauvegarde.

Pour savoir comment supprimer un coffre, consultez la section [Supprimer un coffre à partir du portail Azure](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal). Si vous ne souhaitez pas conserver les données dans le coffre Recovery Services et souhaitez supprimer le coffre, consultez la section [Forcer la suppression du coffre](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Si vous ne savez pas ce qui est dans le coffre, et souhaitez vous assurer que vous pouvez le supprimer, consultez la section [Supprimer les dépendances du coffre et supprimer le coffre](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-azure-portal"></a>Supprimer un coffre à partir du portail Azure

Si le coffre Recovery Services est déjà ouvert, passez à la deuxième étape.

1. Ouvrez le Portail Azure et, depuis le tableau de bord, ouvrez le coffre à supprimer.

   Si le coffre Recovery Services n’est pas épinglé au tableau de bord, dans le menu Hub, cliquez sur **Tous les services** et, dans la liste des ressources, tapez **Recovery Services**. Au fur et à mesure de la saisie, la liste est filtrée. Cliquez sur **Coffres Recovery Services** pour afficher la liste des coffres de votre abonnement.

   ![Créer un coffre Recovery Services - Étape 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   La liste des coffres Recovery Services est affichée. 

   ![choisir le coffre à partir de la liste](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

1. Dans la liste, sélectionnez le coffre à supprimer. Lorsque vous sélectionnez le coffre, son tableau de bord s’affiche.

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. Pour supprimer un coffre, dans son tableau de bord, cliquez sur **Supprimer**. Vous devez confirmer la suppression du coffre.

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    Si **l’erreur de suppression du coffre** s’affiche, vous pouvez supprimer les dépendances du coffre ou utiliser PowerShell pour forcer la suppression du coffre. Les sections suivantes expliquent comment effectuer ces tâches.

    ![Erreur de suppression du coffre](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Forcer la suppression du coffre Recovery Services

Vous pouvez utiliser PowerShell pour forcer la suppression d’un coffre Recovery Services. Forcer la suppression signifie que le coffre Recovery Services, et toutes les données de sauvegarde associées, sont supprimés de manière permanente. 

> [!Warning]
> Si vous utilisez PowerShell pour supprimer un coffre Recovery Services, soyez sûr que vous voulez supprimer de manière permanente toutes les données de sauvegarde dans le coffre.
>

Pour supprimer un coffre Recovery Services :

1. Connectez-vous à votre compte Azure.

   Connectez-vous à votre abonnement Azure avec la commande `Connect-AzureRmAccount` et suivez les instructions à l’écran.

   ```powershell
    Connect-AzureRmAccount
   ```
   Si vous utilisez la sauvegarde Azure pour la première fois, vous devez inscrire le fournisseur de services Azure Recovery Services dans votre abonnement avec [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

1. Ouvrez une fenêtre PowerShell avec des privilèges d’administrateur.

1. Utilisez `Set-ExecutionPolicy Unrestricted` pour supprimer toutes les restrictions.

1. Exécutez la commande suivante pour télécharger le package client Azure Resource Manager à partir de chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

1. Utilisez la commande suivante pour installer le client API Azure Resource Manager.

   `choco.exe install armclient`

1. Dans le portail Azure, rassemblez l’ID d’abonnement et le nom du groupe de ressources associé pour le coffre Recovery Services à supprimer.

1. Dans PowerShell, exécutez la commande suivante à l’aide de votre ID d’abonnement, du nom du groupe de ressources et du nom du coffre Recovery Services. Lorsque vous exécutez la commande, celle-ci supprime le coffre et toutes les dépendances.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
   Seul un coffre vide peut être supprimé. Si le coffre n’est pas vide, vous obtenez une erreur indiquant qu’il est impossible de supprimer le coffre, car il contient des ressources. La commande suivante montre comment supprimer un conteneur d’un coffre :

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```
   
1. Connectez-vous à votre abonnement sur le portail Azure et vérifiez que le coffre est supprimé.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Supprimer les dépendances du coffre et supprimer le coffre

Pour supprimer manuellement les dépendances du coffre, supprimez la configuration entre chaque élément ou serveur et le coffre Recovery Services. Tout au long de la procédure suivante, utilisez le menu **Éléments de sauvegarde** (voir l’image) pour :

* Sauvegardes Stockage Azure (Azure Files)
* SQL Server dans les sauvegardes de machines virtuelles Azure
* Sauvegardes de machines virtuelles Azure
* Sauvegardes de l’agent Microsoft Azure Recovery Services

Utilisez le men **Infrastructure de sauvegarde** (voir l’image) pour :

* Sauvegardes du Serveur de sauvegarde Azure
* Sauvegardes System Center DPM

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. Dans le menu du tableau de bord du coffre, faites défiler jusqu’à la section Éléments protégés, puis cliquez sur **Éléments de sauvegarde**. Dans ce menu, vous pouvez arrêter et supprimer des serveurs Azure Files, des serveurs SQL Server dans une machine virtuelle Azure et des machines virtuelles Azure. Dans cet exemple, nous allons supprimer les données de sauvegarde à partir d’un serveur Azure Files.

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/selected-backup-items.png)

1. Sélectionnez un type de sauvegarde pour afficher tous les éléments de ce type.

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

1. Pour tous les éléments dans la liste, cliquez dessus avec le bouton droit, puis à partir du menu contextuel, sélectionnez **Arrêter la sauvegarde**.

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/stop-backup-item.png) 

    Le menu Arrêter la sauvegarde s’ouvre.

1. Dans la section **Choisir une option** du menu **Arrêter la sauvegarde**, sélectionnez **Supprimer les données de sauvegarde**, tapez le nom de l’élément et cliquez sur **Arrêter la sauvegarde**.

    Tapez le nom de l’élément pour confirmer que vous souhaitez le supprimer. Le bouton **Arrêter la sauvegarde** s’active après avoir vérifié l’élément. Si vous conservez les données, vous ne pourrez pas supprimer le coffre.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Si vous le souhaitez, indiquez une raison expliquant pourquoi vous supprimez les données, et ajoutez des commentaires. Pour vérifier que la tâche est terminée, vérifiez les messages Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Une fois la tâche terminée, le service envoie un message : *le processus de sauvegarde a été arrêté et les données de sauvegarde ont été supprimées*.

1. Après la suppression d’un élément de la liste, dans le menu **Éléments de sauvegarde**, cliquez sur **Actualiser** pour voir les éléments dans le coffre.

      ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/empty-items-list.png)

      Lorsque la liste ne comporte aucun élément, accédez au volet **Essentials** dans le panneau du coffre Recovery Services. Il ne doit pas contenir **d’Éléments de sauvegarde**, de **Serveurs de gestion des sauvegardes** ou **d’Éléments répliqués** répertoriés. Si des éléments apparaissent toujours dans le coffre, retournez à l’étape trois et choisissez une autre liste Type d’élément.  

1. Une fois que la barre d’outils du coffre est vide, cliquez sur **Supprimer**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Pour confirmer la suppression du coffre, cliquez sur **Oui**.

    Le coffre est supprimé et le portail revient au menu de service **Nouveau** .

## <a name="removing-azure-backup-server-or-dpm"></a>Supprimer le serveur de sauvegarde Azure ou DPM

1. Dans le menu du tableau de bord du coffre, faites défiler jusqu’à la section Gérer, puis cliquez sur **Infrastructure de sauvegarde**. 

1. Dans le sous-menu, cliquez sur **Gérer les serveurs de sauvegarde** pour afficher les serveurs de sauvegarde Azure et le serveur System Center DPM. Vous pouvez arrêter et supprimer des serveurs Azure Files, des serveurs SQL Server dans une machine virtuelle Azure et des machines virtuelles Azure. 

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

1. Cliquez avec le bouton droit sur l’élément à supprimer, puis dans le sous-menu, sélectionnez **Supprimer**.

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    Le menu Arrêter la sauvegarde s’ouvre.

1. Dans la section **Choisir une option** du menu **Arrêter la sauvegarde**, sélectionnez **Supprimer les données de sauvegarde**, tapez le nom de l’élément et cliquez sur **Arrêter la sauvegarde**.

    Pour confirmer que vous souhaitez supprimer, tapez le nom. Le bouton **Arrêter la sauvegarde** s’active après avoir vérifié l’élément. Si vous conservez les données, vous ne pouvez pas supprimer le coffre.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Si vous le souhaitez, vous pouvez indiquer une raison expliquant pourquoi vous supprimez les données, et ajouter des commentaires. Pour vérifier que la tâche est terminée, vérifiez les messages Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Une fois la tâche terminée, le service enverra un message : le processus de sauvegarde a été arrêté et les données de sauvegarde ont été supprimées.

1. Après la suppression d’un élément de la liste, dans le menu **Éléments de sauvegarde**, cliquez sur **Actualiser** pour voir les éléments restants dans le coffre.

      ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/empty-items-list.png)

      Lorsque la liste ne comporte aucun élément, accédez au volet **Essentials** dans le panneau du coffre Recovery Services. Il ne doit pas contenir **d’Éléments de sauvegarde**, de **Serveurs de gestion des sauvegardes** ou **d’Éléments répliqués** répertoriés. Si des éléments apparaissent toujours dans le coffre, retournez à l’étape trois et choisissez une autre liste Type d’élément.  
1. Lorsqu’il n’y a plus d’éléments dans le coffre, dans le tableau de bord de celui-ci, cliquez sur **Supprimer**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Pour confirmer la suppression du coffre, cliquez sur **Oui**.

    Le coffre est supprimé et le portail revient au menu de service **Nouveau** .


## <a name="removing-azure-backup-agent-recovery-points"></a>Supprimer les points de récupération de l’agent Sauvegarde Azure

1. Dans le menu du tableau de bord du coffre, faites défiler jusqu’à la section Gérer, puis cliquez sur **Infrastructure de sauvegarde**.

1. Dans le sous-menu, cliquez sur **Serveurs protégés** pour afficher la liste des types de serveurs protégés, y compris l’agent Sauvegarde Azure.

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/identify-protected-servers.png)

1. Dans la liste **Serveurs protégés**, cliquez sur l’agent Sauvegarde Azure.

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    La liste des serveurs protégés à l’aide de l’agent Sauvegarde Azure s’affiche.

    ![sélectionner le serveur protégé spécifique](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

1. Dans la liste des serveurs, cliquez sur l’un d’eux pour ouvrir son menu.

    ![afficher le tableau de bord du serveur sélectionné](./media/backup-azure-delete-vault/selected-protected-server.png)

1. Dans le menu du tableau de bord du serveur sélectionné, cliquez sur **Supprimer**.

    ![supprimer le serveur sélectionné](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

1. Dans le menu **Supprimer**, tapez le nom de l’élément, puis cliquez sur **Supprimer**.

    Tapez le nom de l’élément pour confirmer que vous souhaitez le supprimer. Le bouton **Supprimer** s’active une fois que vous avez vérifié l’élément.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    Si vous le souhaitez, vous pouvez indiquer une raison expliquant pourquoi vous supprimez les données, et ajouter des commentaires. Pour vérifier que la tâche est terminée, vérifiez les messages Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Une fois la tâche terminée, le service enverra un message : le processus de sauvegarde a été arrêté et les données de sauvegarde ont été supprimées.

1. Après la suppression d’un élément de la liste, dans le menu **Éléments de sauvegarde**, cliquez sur **Actualiser** pour voir les éléments restants dans le coffre.

      ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/empty-items-list.png)

      Lorsque la liste ne comporte aucun élément, accédez au volet **Essentials** dans le panneau du coffre Recovery Services. Il ne doit pas contenir **d’Éléments de sauvegarde**, de **Serveurs de gestion des sauvegardes** ou **d’Éléments répliqués** répertoriés. Si des éléments apparaissent toujours dans le coffre, retournez à l’étape trois et choisissez une autre liste Type d’élément.  
1. Lorsqu’il n’y a plus d’éléments dans le coffre, dans le tableau de bord de celui-ci, cliquez sur **Supprimer**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Pour confirmer la suppression du coffre, cliquez sur **Oui**.

    Le coffre est supprimé et le portail revient au menu de service **Nouveau** .

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Que se passe-t-il si j’arrête le processus de sauvegarde mais que je conserve les données ?

Si vous arrêtez le processus de sauvegarde, mais que vous *conservez* accidentellement les données, vous devez supprimer les données de sauvegarde avant de pouvoir supprimer le coffre. Pour supprimer les données de sauvegarde :

1. Dans le menu **Éléments de sauvegarde**, cliquez avec le bouton droit sur l’élément et, dans le menu contextuel, cliquez sur **Supprimer les données de sauvegarde**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    Le menu **Supprimer les données de sauvegarde** s’ouvre.
1. Dans le menu **Supprimer les données de sauvegarde**, tapez le nom de l’élément, puis cliquez sur **Supprimer**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Une fois que vous avez supprimé les données, revenez à l’étape 4c et poursuivez le processus.
