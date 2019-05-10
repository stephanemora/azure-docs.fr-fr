---
title: Supprimer un coffre Recovery Services dans Azure
description: Décrit comment supprimer un coffre Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: raynew
ms.openlocfilehash: e7cea725a25d48ac9f1ffad6acc434e21145890e
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473240"
---
# <a name="delete-a-recovery-services-vault"></a>Supprimer un coffre Recovery Services

Cet article explique comment supprimer un [sauvegarde Azure](backup-overview.md) coffre Recovery Services. Il contient des instructions pour la suppression de dépendances et suppression d’un coffre et suppression d’un coffre en vigueur.


## <a name="before-you-start"></a>Avant de commencer

Avant de commencer, il est important de comprendre que vous ne pouvez pas supprimer un coffre Recovery Services contenant des serveurs inscrits qu’il contient, ou qui contient des données de sauvegarde.

- Pour supprimer un coffre de manière appropriée, vous désinscrivez les serveurs qu’il contient, supprimez les données du coffre, puis supprimez l’archivage.
- Si vous essayez de supprimer un coffre qui a toujours des dépendances, un message d’erreur est émis. et vous devez supprimer manuellement les dépendances de coffre, y compris :
    - Les éléments sauvegardés
    - Serveurs protégés
    - Sauvegarde des serveurs d’administration (serveur de sauvegarde Azure, DPM) ![Sélectionnez votre coffre pour ouvrir son tableau de bord](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Si vous ne souhaitez pas conserver les données dans le coffre Recovery Services et souhaitez supprimer le coffre, vous pouvez supprimer le coffre en vigueur.
- Si vous essayez de supprimer un coffre, sans y parvenir, le coffre est encore configuré pour recevoir des données de sauvegarde.


## <a name="delete-a-vault-from-the-azure-portal"></a>Supprimer un coffre à partir du portail Azure

1. Ouvrez le tableau de bord du coffre.  
2. Dans le tableau de bord, cliquez sur **supprimer**. Vérifiez que vous souhaitez supprimer.

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

Si vous recevez une erreur, supprimez [éléments de sauvegarde](#remove-backup-items), [serveurs d’infrastructure](#remove-backup-infrastructure-servers), et [points de récupération](#remove-azure-backup-agent-recovery-points), puis supprimez le coffre.

![supprimer l’erreur de coffre](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Supprimer le coffre Recovery Services à l’aide d’Azure Resource Manager client

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installer chocolatey à partir de [ici](https://chocolatey.org/) et installer ARMClient exécuter la commande ci-dessous :

   ` choco install armclient --source=https://chocolatey.org/api/v2/ `
2. Connexion au compte Azure, qui exécute la commande ci-dessous

    ` ARMClient.exe login [environment name] `

3. Dans le portail Azure, collectez abonnement ID et la ressource de nom de groupe pour le coffre à supprimer.

Pour plus d’informations sur la commande de ARMClient, consultez ce [document](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Client d’Azure Resource Manager permet de supprimer le coffre Recovery Services

1. Exécutez la commande suivante à l’aide de votre ID d’abonnement, le nom de groupe de ressources et le nom du coffre. W\hen vous exécutez la commande, qu'il supprime le coffre si vous n’avez pas toutes les dépendances.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Si le coffre n’est pas vide, vous recevez l’erreur « Impossible de supprimer qu’il sont a des ressources existantes au sein de ce coffre ». Pour supprimer un éléments protégés / conteneur au sein d’un coffre, procédez comme suit :

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Dans le portail Azure, vérifiez que le coffre est supprimé.


## <a name="remove-vault-items-and-delete-the-vault"></a>Supprimer des éléments du coffre, supprimez l’archivage

Ces procédure fournit quelques exemples de suppression des données de sauvegarde et de serveurs d’infrastructure. Une fois que tout est supprimé à partir d’un coffre, vous pouvez le supprimer.

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

3. Cliquez sur l’élément > **supprimer**.
4. Pour vérifier que la tâche de suppression terminée, vérifiez les Messages Azure ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/messages.png).
5. Une fois le travail terminé, le service envoie un message : **le processus de sauvegarde a été arrêté et les données de sauvegarde a été supprimées**.
6. Après la suppression d’un élément dans la liste, sur le **Infrastructure de sauvegarde** menu, cliquez sur **Actualiser** pour voir les éléments dans le coffre.


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

     ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Si vous le souhaitez fournir un motif pourquoi vous supprimez les données et ajouter des commentaires.
8. Pour vérifier que la tâche de suppression terminée, vérifiez les Messages Azure ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/messages.png).
9. Après la suppression d’un élément dans la liste, sur le **Infrastructure de sauvegarde** menu, cliquez sur **Actualiser** pour voir les éléments dans le coffre.

### <a name="delete-the-vault-after-removing-dependencies"></a>Supprimer le coffre après la suppression des dépendances

1. Lorsque toutes les dépendances ont été supprimés, faites défiler vers le **Essentials** volet dans le menu du coffre.
2. Vérifiez que n’existent pas **éléments de sauvegarde**, **sauvegarde des serveurs d’administration**, ou **éléments répliqués** répertoriés. Si les éléments apparaissent toujours dans le coffre, supprimez-les.

3. Lorsqu’il n’y a plus d’éléments dans le coffre, dans le tableau de bord de celui-ci, cliquez sur **Supprimer**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Pour confirmer la suppression du coffre, cliquez sur **Oui**. Le coffre est supprimé et le portail revient au menu de service **Nouveau** .

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Que se passe-t-il si j’arrête le processus de sauvegarde mais que je conserve les données ?

Si vous arrêtez le processus de sauvegarde mais accidentellement conservez les données, vous devez supprimer les données de sauvegarde, comme décrit dans les sections précédentes.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur](backup-azure-recovery-services-vault-overview.md) coffres Recovery Services.
