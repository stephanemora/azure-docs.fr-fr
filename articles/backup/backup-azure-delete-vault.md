---
title: Supprimer un coffre Recovery Services dans Azure
description: Décrit comment supprimer un coffre Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: raynew
ms.openlocfilehash: e195d9a4b9d2bbe21848e083dbccf864188e0790
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508462"
---
# <a name="delete-a-recovery-services-vault"></a>Supprimer un coffre Recovery Services

Cet article explique comment supprimer un coffre Recovery Services [Sauvegarde Azure](backup-overview.md). Il indique comment enlever les dépendances, supprimer un coffre et forcer la suppression d’un coffre.


## <a name="before-you-start"></a>Avant de commencer

Avant de commencer, il est important de comprendre que vous ne pouvez pas supprimer un coffre Recovery Services contenant des serveurs inscrits ou des données de sauvegarde.

- Pour supprimer un coffre correctement, annulez l’inscription des serveurs qu’il contient, enlevez les données du coffre, puis supprimez le coffre.
- Si vous essayez de supprimer un coffre qui a toujours des dépendances, un message d’erreur est émis, et vous devez supprimer manuellement les dépendances du coffre, notamment :
    - Éléments sauvegardés
    - Serveurs protégés
    - Serveurs de gestion des sauvegardes (serveur de sauvegarde Azure, DPM) ![Sélectionnez votre coffre pour ouvrir son tableau de bord](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Si vous ne souhaitez pas conserver les données dans le coffre Recovery Services et souhaitez supprimer le coffre, vous pouvez forcer la suppression de ce dernier.
- Si vous essayez de supprimer un coffre, sans y parvenir, le coffre est encore configuré pour recevoir des données de sauvegarde.


## <a name="delete-a-vault-from-the-azure-portal"></a>Supprimer un coffre à partir du portail Azure

1. Ouvrez le tableau de bord du coffre.  
2. Dans le tableau de bord, cliquez sur **Supprimer**. Vérifiez que vous souhaitez effectuer la suppression.

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

Si vous recevez une erreur, supprimez les [éléments de sauvegarde](#remove-backup-items), les [serveurs d’infrastructure](#remove-azure-backup-management-servers) et les [points de récupération](#remove-azure-backup-agent-recovery-points), puis supprimez le coffre.

![Erreur de suppression du coffre](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Supprimer le coffre Recovery Services à l’aide du client Azure Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installez chocolatey à partir d’[ici](https://chocolatey.org/), puis, pour installer ARMClient, exécutez la commande ci-dessous :

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Connectez-vous à votre compte Azure, puis exécutez cette commande :

    `ARMClient.exe login [environment name]`

3. Dans le portail Azure, rassemblez l’ID d’abonnement et le nom du groupe de ressources pour le coffre à supprimer.

Pour plus d’informations sur la commande ARMClient, consultez ce [document](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Utiliser le client Azure Resource Manager pour supprimer le coffre Recovery Services

1. Exécutez la commande suivante à l’aide de votre ID d’abonnement, du nom du groupe de ressources et du nom du coffre. Quand vous exécutez la commande, celle-ci supprime le coffre si vous n’avez pas de dépendances.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Si le coffre n’est pas vide, vous recevez l’erreur « Impossible de supprimer le coffre car celui-ci contient des ressources ». Pour supprimer un conteneur/élément protégé au sein d’un coffre, procédez comme suit :

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Dans le portail Azure, vérifiez que le coffre est supprimé.


## <a name="remove-vault-items-and-delete-the-vault"></a>Enlever les éléments du coffre, puis supprimer ce dernier

Supprimez toutes les dépendances avant que le coffre Recovery Services ne soit supprimé.

### <a name="remove-backup-items"></a>Supprimer les éléments de sauvegarde

Cette procédure fournit un exemple qui montre comment supprimer les données de sauvegarde d’Azure Files.

1. Cliquez sur **Éléments de sauvegarde** > **Stockage Azure (Azure Files)**

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. Cliquez avec le bouton droit sur chaque élément d’Azure Files à supprimer, puis cliquez sur **Arrêter la sauvegarde**.

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/stop-backup-item.png)


3. Dans **Arrêter la sauvegarde** > **Choisir une option**, sélectionnez **Supprimer les données de sauvegarde**.
4. Tapez le nom de l’élément, puis cliquez sur **Arrêter la sauvegarde**.
   - Cela permet de vérifier que vous souhaitez supprimer l’élément.
   - Le bouton **Arrêter la sauvegarde** est activé une fois la vérification effectuée.
   - Si vous conservez les données, vous ne pourrez pas supprimer le coffre.

     ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Indiquez éventuellement une raison expliquant pourquoi vous supprimez les données, et ajoutez des commentaires.
6. Pour vérifier que la tâche est terminée, vérifiez les messages Azure. ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/messages.png).
7. Une fois la tâche terminée, le service envoie un message : **le processus de sauvegarde a été arrêté et les données de sauvegarde ont été supprimées**.
8. Après la suppression d’un élément de la liste, dans le menu **Éléments de sauvegarde**, cliquez sur **Actualiser** pour voir les éléments dans le coffre.

      ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="deleting-backup-items-from-management-console"></a>Suppression des éléments de sauvegarde à partir de la console de gestion

Pour supprimer les éléments de sauvegarde de l’infrastructure de sauvegarde, accédez à la console de gestion de votre serveur local (MARS, le serveur de sauvegarde Azure ou SC DPM selon l’endroit où vos éléments de sauvegarde sont protégés).

### <a name="for-mars-agent"></a>Pour l’agent MARS

- Lancez la console de gestion de MARS, accédez au volet **Actions** et choisissez **Planifier la sauvegarde**.
- À partir de l’Assistant **Modification ou arrêt d’une sauvegarde planifiée**, choisissez l’option **Ne plus utiliser cette panification de sauvegarde et supprimer toutes les sauvegardes stockées** et cliquez sur **Suivant**.

    ![Modifier ou arrêter une sauvegarde planifiée](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- À partir de l’Assistant **Arrêt d’une sauvegarde planifiée**, cliquez sur **Terminer**.

    ![Arrêter une sauvegarde planifiée](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Vous êtes invité à entrer un code PIN de sécurité. Pour générer le code PIN, vous devez effectuer les étapes ci-dessous :
  - Connectez-vous au portail Azure.
  - Accédez à **Coffre Recovery Services** > **Paramètres** > **Propriétés**.
  - Sous **Code PIN de sécurité**, cliquez sur **Générer**. Copiez ce code PIN. (Ce dernier n’est valide que pendant cinq minutes.)
- Dans la console de gestion (application cliente), collez le code PIN et cliquez sur **Ok**.

  ![Code PIN de sécurité](./media/backup-azure-delete-vault/security-pin.png)

- Dans l’Assistant **Modification de la progression de la sauvegarde**, vous verrez le message *Les données supprimées sont conservées pendant 14 jours. Passé ce délai, les données de sauvegarde sont définitivement supprimées.*  

    ![Supprimer l’infrastructure de sauvegarde](./media/backup-azure-delete-vault/deleted-backup-data.png)

Les éléments de sauvegarde étant supprimés de votre environnement local, effectuez les étapes suivantes à partir du portail :
- Pour MARS, suivez les étapes de la section [Supprimer les points de récupération de l’agent Sauvegarde Azure](#remove-azure-backup-agent-recovery-points).

### <a name="for-mabs-agent"></a>Pour l’agent MABS

Il existe différentes méthodes pour arrêter/supprimer la protection en ligne ; effectuez l’une des méthodes ci-dessous :

**Méthode 1**

Lancez la **console de gestion MABS**. Dans la section **Sélectionner la méthode de protection des données**, désélectionnez **Je voudrais une protection en ligne**.

  ![Sélectionner la méthode de protection des données](./media/backup-azure-delete-vault/data-protection-method.png)

**Méthode 2**

Pour supprimer un groupe de protection, vous devez tout d’abord arrêter la protection du groupe. Utilisez la procédure suivante pour arrêter la protection et permettre la suppression d’un groupe de protection.

1.  Dans la console Administrateur DPM, cliquez sur **Protection** sur la barre de navigation.
2.  Dans le volet d’informations, sélectionnez le membre du groupe de protection que vous souhaitez supprimer. Cliquez avec le bouton droit, puis choisissez l’option **Arrêter la protection des membres du groupe**.
3.  Dans la boîte de dialogue **Arrêt de la protection**, cochez la case **Supprimer les données protégées** > **Supprimer le stockage en ligne**, puis cliquez sur **Arrêter la protection**.

    ![Supprimer le stockage en ligne](./media/backup-azure-delete-vault/delete-storage-online.png)

L’état du membre protégé est désormais **Réplica inactif disponible**.

5. Cliquez avec le bouton droit sur le groupe de protection inactif, puis sélectionnez **Supprimer la protection inactive**.

    ![Supprimer la protection inactive](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. À partir de la fenêtre **Supprimer la protection inactive**, sélectionnez **Supprimer le stockage en ligne** et cliquez sur **Ok**.

    ![Supprimer les réplicas sur le disque et en ligne](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

Les éléments de sauvegarde étant supprimés de votre environnement local, effectuez les étapes suivantes à partir du portail :
- Pour MABS et DPM, suivez les étapes de la section [Supprimer des serveurs de gestion Sauvegarde Azure](#remove-azure-backup-management-servers).


### <a name="remove-azure-backup-management-servers"></a>Supprimer des serveurs de gestion Sauvegarde Azure

Avant de supprimer le serveur de gestion Sauvegarde Azure, veillez à effectuer les étapes listées dans [Suppression des éléments de sauvegarde à partir de la console de gestion](#deleting-backup-items-from-management-console).

1. Dans le menu du tableau de bord du coffre, cliquez sur **Infrastructure de sauvegarde**.
2. Cliquez sur **Serveurs de gestion des sauvegardes** pour afficher les serveurs.

    ![sélectionner le coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Cliquez avec le bouton droit sur l’élément, puis choisissez **Supprimer**.
4. Dans le menu **Supprimer**, tapez le nom du serveur, puis cliquez sur **Supprimer**.

     ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)
5.  Indiquez éventuellement une raison expliquant pourquoi vous supprimez les données, et ajoutez des commentaires.

> [!NOTE]
> Si vous rencontrez l’erreur ci-dessous, effectuez d’abord les étapes listées dans [Suppression des éléments de sauvegarde à partir de la console de gestion](#deleting-backup-items-from-management-console).
>
>![échec de la suppression](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Si vous ne parvenez pas à effectuer les étapes permettant de supprimer les sauvegardes à partir de la console de gestion, par exemple, en raison d’une indisponibilité du serveur avec la console de gestion, contactez le support Microsoft.

6. Pour vérifier que la tâche est terminée, vérifiez les messages Azure. ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/messages.png).
7. Une fois la tâche terminée, le service envoie un message : **le processus de sauvegarde a été arrêté et les données de sauvegarde ont été supprimées**.
8. Après la suppression d’un élément de la liste, dans le menu **Infrastructure de sauvegarde**, cliquez sur **Actualiser** pour voir les éléments dans le coffre.


### <a name="remove-azure-backup-agent-recovery-points"></a>Supprimer les points de récupération de l’agent Sauvegarde Azure

Avant de supprimer le point de récupération de sauvegarde Azure, veillez à effectuer les étapes listées dans [Suppression des éléments de sauvegarde à partir de la console de gestion](#deleting-backup-items-from-management-console).

1. Dans le menu du tableau de bord du coffre, cliquez sur **Infrastructure de sauvegarde**.
2. Cliquez sur **Serveurs protégés** pour afficher les serveurs de l’infrastructure.

    ![sélectionnez votre coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. Dans la liste **Serveurs protégés**, cliquez sur l’agent Sauvegarde Azure.

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Cliquez sur le serveur dans la liste des serveurs protégés à l’aide de l’agent Sauvegarde Azure.

    ![sélectionner le serveur protégé spécifique](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. Dans le tableau de bord du serveur sélectionné, cliquez sur **Supprimer**.

    ![supprimer le serveur sélectionné](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Dans le menu **Supprimer**, tapez le nom du serveur, puis cliquez sur **Supprimer**.

     ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Indiquez éventuellement une raison expliquant pourquoi vous supprimez les données, et ajoutez des commentaires.

> [!NOTE]
> Si vous rencontrez l’erreur ci-dessous, effectuez d’abord les étapes listées dans [Suppression des éléments de sauvegarde à partir de la console de gestion](#deleting-backup-items-from-management-console).
>
>![échec de la suppression](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Si vous ne parvenez pas à effectuer les étapes permettant de supprimer les sauvegardes à partir de la console de gestion, par exemple, en raison d’une indisponibilité du serveur avec la console de gestion, contactez le support Microsoft. 

8. Pour vérifier que la tâche est terminée, vérifiez les messages Azure. ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/messages.png).
9. Après la suppression d’un élément de la liste, dans le menu **Infrastructure de sauvegarde**, cliquez sur **Actualiser** pour voir les éléments dans le coffre.


### <a name="delete-the-vault-after-removing-dependencies"></a>Supprimer le coffre après avoir enlevé les dépendances

1. Une fois toutes les dépendances supprimées, faites défiler l’affichage jusqu’au volet **Bases** dans le menu du coffre.
2. Vérifiez qu’il ne contient pas d’**Éléments de sauvegarde**, de **Serveurs de gestion des sauvegardes** ou d’**Éléments répliqués** listés. Si des éléments apparaissent toujours dans le coffre, supprimez-les.

3. Lorsqu’il n’y a plus d’éléments dans le coffre, dans le tableau de bord de celui-ci, cliquez sur **Supprimer**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Pour confirmer la suppression du coffre, cliquez sur **Oui**. Le coffre est supprimé et le portail revient au menu de service **Nouveau** .

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Que se passe-t-il si j’arrête le processus de sauvegarde mais que je conserve les données ?

Si vous arrêtez le processus de sauvegarde, mais que vous conservez accidentellement les données, vous devez supprimer les données de sauvegarde comme indiqué dans les sections précédentes.

## <a name="next-steps"></a>Étapes suivantes

[Découvrez](backup-azure-recovery-services-vault-overview.md) les coffres Recovery Services.
