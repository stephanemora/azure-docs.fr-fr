---
title: Supprimer un coffre Recovery Services dans Azure
description: Décrit comment supprimer un coffre Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: raynew
ms.openlocfilehash: e83698af6bb1caab1568375b726753d34a8c8467
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57861347"
---
# <a name="delete-a-recovery-services-vault"></a>Supprimer un coffre Recovery Services

Cet article explique comment supprimer un [sauvegarde Azure](backup-overview.md) coffre Recovery Services. Il contient des instructions pour la suppression de dépendances et suppression d’un coffre et suppression d’un coffre en vigueur.




## <a name="before-you-start"></a>Avant de commencer

Avant de commencer, il est important de comprendre que vous ne pouvez pas supprimer un coffre Recovery Services contenant des serveurs inscrits qu’il contient, ou qui contient des données de sauvegarde.


- Pour supprimer un coffre de manière appropriée, annuler l’inscription des serveurs qu’il contient et supprimer les données d’archivage.
- Si vous ne souhaitez pas conserver les données dans le coffre Recovery Services et souhaitez supprimer le coffre, vous pouvez supprimer le coffre en vigueur.
- Si vous essayez de supprimer un coffre, sans y parvenir, le coffre est encore configuré pour recevoir des données de sauvegarde.

Pour savoir comment supprimer un coffre, consultez la section [Supprimer un coffre à partir du portail Azure](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal). Si la section, [supprimer le coffre de force](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Si vous ne savez pas ce qui est dans le coffre, et souhaitez vous assurer que vous pouvez le supprimer, consultez la section [Supprimer les dépendances du coffre et supprimer le coffre](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-the-azure-portal"></a>Supprimer un coffre à partir du portail Azure

1. Ouvrez votre liste des coffres Recovery Services dans le portail.
2. Dans la liste, sélectionnez le coffre à supprimer. Le tableau de bord du coffre s’ouvre.

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. Dans le tableau de bord du coffre, cliquez sur **supprimer**. Vérifiez que vous souhaitez supprimer.

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

2. S’il existe des dépendances de coffre, le **erreur de suppression du coffre** s’affiche : 

    ![Erreur de suppression du coffre](./media/backup-azure-delete-vault/vault-delete-error.png)

    - Suivez ces instructions pour supprimer les dépendances avant de supprimer le coffre, passez en revue
    - [Suivez ces instructions](#delete-the-recovery-services-vault-by-force) à utiliser PowerShell pour supprimer le coffre en vigueur. 

## <a name="delete-the-recovery-services-vault-by-force"></a>Forcer la suppression du coffre Recovery Services

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vous pouvez utiliser PowerShell pour forcer la suppression d’un coffre Recovery Services. Cela signifie que le coffre et toutes les données de sauvegarde est définitivement supprimé. 

> [!Warning]
> Lorsque vous utilisez PowerShell pour supprimer un coffre Recovery Services, vérifiez que vous souhaitez supprimer définitivement toutes les données de sauvegarde dans le coffre.
>

Pour supprimer un coffre Recovery Services :

1. Connectez-vous à votre abonnement Azure avec la `Connect-AzAccount` et suivez l’à l’écran directions.

   ```powershell
    Connect-AzAccount
   ```
2. La première fois, vous utilisez la sauvegarde Azure, vous devez inscrire le fournisseur Azure Recovery Service dans votre abonnement avec [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider).

   ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

3. Ouvrez une fenêtre PowerShell avec des privilèges d’administrateur.
4. Utilisez `Set-ExecutionPolicy Unrestricted` pour supprimer toutes les restrictions.
5. Exécutez la commande suivante pour télécharger le package client Azure Resource Manager à partir de chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

6. Utilisez la commande suivante pour installer le client API Azure Resource Manager.

   `choco.exe install armclient`

7. Dans le portail Azure, collectez abonnement ID et la ressource de nom de groupe pour le coffre à supprimer.
8. Dans PowerShell, exécutez la commande suivante à l’aide de votre ID d’abonnement, le nom de groupe de ressources et le nom du coffre. Lorsque vous exécutez la commande, celle-ci supprime le coffre et toutes les dépendances.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
9. Si le coffre n’est pas vide, vous recevez l’erreur « Impossible de supprimer qu’il sont a des ressources existantes au sein de ce coffre ». Pour supprimer une relation contenant-contenu dans un coffre, procédez comme suit :

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

10. Connectez-vous à votre abonnement dans le portail Azure et vérifiez que le coffre est supprimé.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Supprimer les dépendances du coffre et supprimer le coffre

Vous pouvez supprimer manuellement les dépendances de coffre, comme suit :

- Dans le **éléments de sauvegarde** menu, les dépendances de la suppression :
    - Sauvegardes Stockage Azure (Azure Files)
    - SQL Server dans les sauvegardes de machines virtuelles Azure
    - Sauvegardes de machines virtuelles Azure
- Dans le **Infrastructure de sauvegarde** menu, les dépendances de la suppression :
    - Sauvegardes de serveur de sauvegarde Microsoft Azure (MABS)
    - Sauvegardes System Center DPM

![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

### <a name="remove-backup-items"></a>Supprimer des éléments de sauvegarde

Cette procédure fournit un exemple qui montre comment supprimer les données de sauvegarde à partir d’Azure Files.

1. Cliquez sur **éléments de sauvegarde** > **Azure stockage (fichiers Azure)**

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. Cliquez sur chaque élément d’Azure Files pour supprimer, puis cliquez sur **arrêter la sauvegarde**.

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/stop-backup-item.png)


3. Dans **arrêter la sauvegarde** > **choisissez une option**, sélectionnez **supprimer les données de sauvegarde**.
4. Tapez le nom de l’élément, puis cliquez sur **arrêter la sauvegarde**. 
   - Cela permet de vérifier que vous souhaitez supprimer l’élément.
   - Le **arrêter la sauvegarde** bouton est activé après avoir vérifié.
   - Si vous conservez et que vous ne supprimez pas les données, vous ne pourrez pas supprimer le coffre.

     ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Si vous le souhaitez fournir un motif pourquoi vous supprimez les données et ajouter des commentaires.
6. Pour vérifier que la tâche de suppression terminée, vérifiez les Messages Azure ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/messages.png).
7. Une fois le travail terminé, le service envoie un message : **le processus de sauvegarde a été arrêté et les données de sauvegarde a été supprimées**.
8. Après la suppression d’un élément de la liste, dans le menu **Éléments de sauvegarde**, cliquez sur **Actualiser** pour voir les éléments dans le coffre.

      ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-backup-infrastructure-servers"></a>Supprimer des serveurs d’infrastructure de sauvegarde

1. Dans le menu du tableau de bord du coffre, cliquez sur **Infrastructure de sauvegarde**.
2. Cliquez sur **serveurs d’administration de sauvegarde** pour afficher les serveurs. 

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

2. Cliquez sur l’élément > **supprimer**.

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. . Dans **arrêter la sauvegarde** > **choisissez une option**, sélectionnez **supprimer les données de sauvegarde**.
4. Tapez le nom de l’élément, puis cliquez sur **arrêter la sauvegarde**. 
   - Cela permet de vérifier que vous souhaitez supprimer l’élément.
   - Le **arrêter la sauvegarde** bouton est activé après avoir vérifié.
   - Si vous conservez et que vous ne supprimez pas les données, vous ne pourrez pas supprimer le coffre.

     ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Si vous le souhaitez fournir un motif pourquoi vous supprimez les données et ajouter des commentaires.
6. Pour vérifier que la tâche de suppression terminée, vérifiez les Messages Azure ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/messages.png).
7. Une fois le travail terminé, le service envoie un message : **le processus de sauvegarde a été arrêté et les données de sauvegarde a été supprimées**.
8. Après la suppression d’un élément de la liste, dans le menu **Éléments de sauvegarde**, cliquez sur **Actualiser** pour voir les éléments dans le coffre.


### <a name="remove-azure-backup-agent-recovery-points"></a>Supprimer des points de récupération de l’agent de sauvegarde Azure

1. Dans le menu du tableau de bord du coffre, cliquez sur **Infrastructure de sauvegarde**.
2. Cliquez sur **serveurs d’administration de sauvegarde** pour afficher les serveurs d’infrastructure.

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. Dans la liste **Serveurs protégés**, cliquez sur l’agent Sauvegarde Azure.

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Cliquez sur le serveur dans la liste des serveurs protégés à l’aide de l’agent de sauvegarde Azure.

    ![sélectionner le serveur protégé spécifique](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. Dans le tableau de bord du serveur sélectionné, cliquez sur **supprimer**.

    ![supprimer le serveur sélectionné](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Dans le menu **Supprimer**, tapez le nom de l’élément, puis cliquez sur **Supprimer**.
   - Cela permet de vérifier que vous souhaitez supprimer l’élément.
   - Le **arrêter la sauvegarde** bouton est activé après avoir vérifié.
   - Si vous conservez et que vous ne supprimez pas les données, vous ne pourrez pas supprimer le coffre.

     ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Si vous le souhaitez fournir un motif pourquoi vous supprimez les données et ajouter des commentaires.
8. Pour vérifier que la tâche de suppression terminée, vérifiez les Messages Azure ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/messages.png).
7. Une fois le travail terminé, le service envoie un message : **le processus de sauvegarde a été arrêté et les données de sauvegarde a été supprimées**.
8. Après la suppression d’un élément de la liste, dans le menu **Éléments de sauvegarde**, cliquez sur **Actualiser** pour voir les éléments dans le coffre.



### <a name="delete-the-vault-after-removing-dependencies"></a>Supprimer le coffre après la suppression des dépendances

1. Lorsque toutes les dépendances ont été supprimés, faites défiler vers le **Essentials** volet dans le menu du coffre.

    - Il ne doit pas contenir **d’Éléments de sauvegarde**, de **Serveurs de gestion des sauvegardes** ou **d’Éléments répliqués** répertoriés.
    - Si les éléments apparaissent toujours dans le coffre, supprimez-les.

2. Lorsqu’il n’y a plus d’éléments dans le coffre, dans le tableau de bord de celui-ci, cliquez sur **Supprimer**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Pour confirmer la suppression du coffre, cliquez sur **Oui**. Le coffre est supprimé et le portail revient au menu de service **Nouveau** .

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Que se passe-t-il si j’arrête le processus de sauvegarde mais que je conserve les données ?

Si vous arrêtez le processus de sauvegarde mais accidentellement conservez les données, vous devez supprimer les données de sauvegarde, comme décrit dans les sections précédentes.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur](backup-azure-recovery-services-vault-overview.md) coffres Recovery Services.
