---
title: Supprimer un coffre Recovery Services dans Azure
description: Décrit comment supprimer un coffre Recovery Services.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: f98b9a02d12cc53ba23857b203ee3eaed9dd7cfa
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466651"
---
# <a name="delete-a-recovery-services-vault"></a>Supprimer un coffre Recovery Services

Cet article explique comment supprimer un coffre Recovery Services [Sauvegarde Azure](backup-overview.md). Il indique comment enlever les dépendances, puis supprimer un coffre.


## <a name="before-you-start"></a>Avant de commencer

Vous ne pouvez pas supprimer un coffre de Recovery Services qui a des dépendances telles que des serveurs protégés ou des serveurs de gestion de sauvegarde associés à l’archivage.<br/>
Le coffre contenant les données de sauvegarde ne peut pas être supprimé (c’est-à-dire, même si vous avez arrêté la protection mais que vous avez conservé les données de sauvegarde).

Si vous supprimez un coffre qui contient des dépendances, le message d’erreur suivant s’affiche :

![Erreur de suppression du coffre](./media/backup-azure-delete-vault/error.png)

Pour supprimer le coffre normalement, choisissez le scénario qui correspond à votre configuration et suivez les étapes recommandées :

Scénario | Étapes de suppression des dépendances pour supprimer un coffre |
-- | --
J’ai des fichiers et des dossiers locaux protégés à l’aide de la sauvegarde de l’agent de Sauvegarde Azure (MARS) | Exécutez les étapes de la section supprimer les données de sauvegarde - [Pour l’agent MARS](#for-mars-agent)
J’ai des machines locales protégées à l’aide de MABS (Sauvegarde de serveur Microsoft Azure) ou de DPM vers Azure (System Center Data Protection Manager) | Exécuter les étapes de la section supprimer les données de sauvegarde - [Pour l’agent MABS](#for-mabs-agent)
J’ai des éléments protégés dans le cloud (par exemple, machine virtuelle laaS, partage de fichiers Azure, etc.)  | Exécutez les étapes de la section supprimer les données de sauvegarde - [Pour les éléments protégés dans le cloud](#for-protected-items-in-cloud)
J’ai des éléments protégés en local et dans le cloud | Exécutez les étapes de la section supprimer les données de sauvegarde dans la séquence ci-dessous : <br> - [Pour les éléments protégés dans le cloud](#for-protected-items-in-cloud)<br> - [Pour l’agent MABS](#for-mars-agent) <br> - [Pour l’agent MABS](#for-mabs-agent)
Je n’ai pas d’éléments protégés localement ou dans le cloud ; toutefois, je reçois toujours le message d’erreur de suppression du coffre | Suivez les étapes dans [Supprimer le coffre Recovery Services à l’aide du client Azure Resource Manager](#delete-the-recovery-services-vault-using-azure-resource-manager-client)
Je n’ai plus ce serveur local d’origine (perdu/désactivé) et je souhaite supprimer le coffre Recovery Services | Contactez le Support Microsoft.

## <a name="delete-backup-data-and-backup-items"></a>Supprimer les données de sauvegarde et les éléments de sauvegarde

Avant de poursuivre, lisez **[cette section](#before-you-start)** pour comprendre le processus de suppression des dépendances et du coffre.

### <a name="for-protected-items-in-cloud"></a>Pour les éléments protégés dans le cloud

Pour arrêter la protection et supprimer les données de sauvegarde, procédez comme suit :

1. À partir du portail > Coffre Recovery Services > Éléments de sauvegarde, choisissez les éléments protégés dans le cloud.

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. Pour chaque élément, vous devez cliquer avec le bouton droit et choisir **Arrêter la sauvegarde**.

    ![sélectionner le type de sauvegarde](./media/backup-azure-delete-vault/stop-backup-item.png)

3. Dans **Arrêter la sauvegarde** > **Choisir une option**, sélectionnez **Supprimer les données de sauvegarde**.
4. Tapez le nom de l’élément, puis cliquez sur **Arrêter la sauvegarde**.
   - Cela permet de vérifier que vous souhaitez supprimer l’élément.
   - Le bouton **Arrêter la sauvegarde** est activé une fois la vérification effectuée.
   - Si vous conservez les données, vous ne pourrez pas supprimer le coffre.

     ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Vérifiez la **notification** ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/messages.png). Une fois l’action terminée, le service affiche le message suivant : **Arrêt de la sauvegarde et suppression des données de sauvegarde pour « *Élément de sauvegarde* »** . **L’opération s’est terminée correctement.**
6. Cliquez sur **Actualiser**dans le menu **Éléments de sauvegarde** pour vérifier si l’élément de sauvegarde est supprimé.

      ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>Pour l’agent MARS

Pour arrêter la protection et supprimer les données de sauvegarde, effectuez les étapes suivantes dans l’ordre indiqué ci-dessous :

- [Étape 1 : Supprimez des éléments de sauvegarde de la console de gestion MARS](#step-1-delete-backup-items-from-mars-management-console)
- [Étape 2 : À partir du portail, supprimez l’agent de sauvegarde Azure](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>Étape 1 : Supprimez des éléments de sauvegarde de la console de gestion MARS

Si vous ne parvenez pas à effectuer cette étape en raison de l’indisponibilité du serveur, contactez le support technique de Microsoft.

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

Les éléments de sauvegarde étant supprimés de votre environnement local, effectuez les étapes suivantes à partir du portail.

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>Étape 2 : À partir du portail, supprimez l’agent de sauvegarde Azure

Vérifiez que l’[étape 1](#step-1-delete-backup-items-from-mars-management-console) est terminée avant de continuer :

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

> [!NOTE]
> Si vous rencontrez l’erreur ci-dessous, effectuez d’abord les étapes listées dans [Suppression des éléments de sauvegarde à partir de la console de gestion](#step-1-delete-backup-items-from-mars-management-console).
>
>![échec de la suppression](./media/backup-azure-delete-vault/deletion-failed.png)
>
>Si vous ne parvenez pas à effectuer les étapes permettant de supprimer les sauvegardes à partir de la console de gestion, par exemple, en raison d’une indisponibilité du serveur avec la console de gestion, contactez le support Microsoft.

7. Vérifiez la **notification** ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/messages.png). Une fois l’action terminée, le service affiche le message suivant : **Arrêt de la sauvegarde et suppression des données de sauvegarde pour « *Élément de sauvegarde* »** . **L’opération s’est terminée correctement.**
8. Cliquez sur **Actualiser**dans le menu **Éléments de sauvegarde** pour vérifier si l’élément de sauvegarde est supprimé.


### <a name="for-mabs-agent"></a>Pour l’agent MABS

Pour arrêter la protection et supprimer les données de sauvegarde, effectuez les étapes suivantes dans l’ordre indiqué ci-dessous :

- [Étape 1 : Supprimez des éléments de sauvegarde de la console de gestion MABS](#step-1-delete-backup-items-from-mabs-management-console)
- [Étape 2 : Dans le portail, supprimez les serveurs de gestion des sauvegardes Azure](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>Étape 1 : Supprimez des éléments de sauvegarde de la console de gestion MABS

Si vous ne parvenez pas à effectuer cette étape en raison de l’indisponibilité du serveur, contactez le support technique de Microsoft.

**Méthode 1** pour arrêter la protection et supprimer les données de sauvegarde, procédez comme suit :

1.  Dans la console Administrateur DPM, cliquez sur **Protection** sur la barre de navigation.
2.  Dans le volet d’informations, sélectionnez le membre du groupe de protection que vous souhaitez supprimer. Cliquez avec le bouton droit, puis choisissez l’option **Arrêter la protection des membres du groupe**.
3.  Dans la boîte de dialogue **Arrêt de la protection**, cochez la case **Supprimer les données protégées** > **Supprimer le stockage en ligne**, puis cliquez sur **Arrêter la protection**.

    ![Supprimer le stockage en ligne](./media/backup-azure-delete-vault/delete-storage-online.png)

L’état du membre protégé est désormais **Réplica inactif disponible**.

5. Cliquez avec le bouton droit sur le groupe de protection inactif, puis sélectionnez **Supprimer la protection inactive**.

    ![Supprimer la protection inactive](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. À partir de la fenêtre **Supprimer la protection inactive**, sélectionnez **Supprimer le stockage en ligne** et cliquez sur **Ok**.

    ![Supprimer les réplicas sur le disque et en ligne](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**La méthode 2** lance la console de **gestion MABS**. Dans la section **Sélectionner la méthode de protection des données**, désélectionnez **Je voudrais une protection en ligne**.

  ![Sélectionner la méthode de protection des données](./media/backup-azure-delete-vault/data-protection-method.png)

Les éléments de sauvegarde étant supprimés de votre environnement local, effectuez les étapes suivantes à partir du portail.

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>Étape 2 : Dans le portail, supprimez les serveurs de gestion des sauvegardes Azure

Vérifiez que l’[étape 1](#step-1-delete-backup-items-from-mabs-management-console) est terminée avant de continuer :

1. Dans le menu du tableau de bord du coffre, cliquez sur **Infrastructure de sauvegarde**.
2. Cliquez sur **Serveurs de gestion des sauvegardes** pour afficher les serveurs.

    ![sélectionner le coffre pour afficher son tableau de bord](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Cliquez avec le bouton droit sur l’élément, puis choisissez **Supprimer**.
4. Dans le menu **Supprimer**, tapez le nom du serveur, puis cliquez sur **Supprimer**.

     ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Si vous rencontrez l’erreur ci-dessous, effectuez d’abord les étapes listées dans [Suppression des éléments de sauvegarde à partir de la console de gestion](#step-2-from-portal-remove-azure-backup-management-servers).
>
>![échec de la suppression](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Si vous ne parvenez pas à effectuer les étapes permettant de supprimer les sauvegardes à partir de la console de gestion, par exemple, en raison d’une indisponibilité du serveur avec la console de gestion, contactez le support Microsoft.

5. Vérifiez la **notification** ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/messages.png). Une fois l’action terminée, le service affiche le message suivant : **Arrêt de la sauvegarde et suppression des données de sauvegarde pour « *Élément de sauvegarde* »** . **L’opération s’est terminée correctement.**
6. Cliquez sur **Actualiser**dans le menu **Éléments de sauvegarde** pour vérifier si l’élément de sauvegarde est supprimé.


## <a name="delete-the-recovery-services-vault"></a>Supprimer le coffre Recovery Services

1. Une fois toutes les dépendances supprimées, faites défiler l’affichage jusqu’au volet **Bases** dans le menu du coffre.
2. Vérifiez qu’il ne contient pas d’**Éléments de sauvegarde**, de **Serveurs de gestion des sauvegardes** ou d’**Éléments répliqués** listés. Si des éléments apparaissent toujours dans le coffre, [supprimez-les](#delete-backup-data-and-backup-items).

3. Lorsqu’il n’y a plus d’éléments dans le coffre, dans le tableau de bord de celui-ci, cliquez sur **Supprimer**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Pour confirmer la suppression du coffre, cliquez sur **Oui**. Le coffre est supprimé et le portail revient au menu de service **Nouveau** .

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Supprimer le coffre Recovery Services à l’aide du client Azure Resource Manager

Cette option qui permet de supprimer le coffre de Recovery Services est recommandée uniquement lorsque toutes les dépendances sont supprimées et que vous obtenez toujours le *message d’erreur de suppression du coffre*.



- Dans le volet **Essentials** du menu du coffre, vérifiez qu’il n’y a pas d’**éléments de sauvegarde**, de **serveurs de gestion des sauvegardes** ou d’**éléments répliqués** listés. S’il existe des éléments de sauvegarde, effectuez les étapes de la section [Supprimer les données de sauvegarde et les éléments de sauvegarde](#delete-backup-data-and-backup-items).
- Réessayez [de supprimer le coffre à partir du portail](#delete-the-recovery-services-vault).
- Si toutes les dépendances sont supprimées et que vous obtenez toujours le *message d’erreur de suppression du coffre*, utilisez l’outil ARMClient pour effectuer les étapes indiquées ci-dessous ;

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
2. Si le coffre n’est pas vide, vous recevez le message d’erreur « Impossible de supprimer le coffre car celui-ci contient des ressources ». Pour supprimer un conteneur/élément protégé au sein d’un coffre, procédez comme suit :

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Dans le portail Azure, vérifiez que le coffre est supprimé.


## <a name="next-steps"></a>Étapes suivantes

[Découvrez](backup-azure-recovery-services-vault-overview.md) les coffres Recovery Services.<br/>
[En savoir plus](backup-azure-manage-windows-server.md) sur la surveillance et la gestion des coffres Recovery Services.
