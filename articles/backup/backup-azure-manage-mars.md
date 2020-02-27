---
title: Gérer et surveiller les sauvegardes de l’agent MARS
description: Découvrez comment gérer et surveiller les sauvegardes de l’agent Microsoft Azure Recovery Services (MARS) à l’aide du service Sauvegarde Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 26ed6fefdf95281429a8c91057195785db4f5589
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617598"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Gérer les sauvegardes de l’agent Microsoft Azure Recovery Services (MARS) à l’aide du service Sauvegarde Azure

Cet article explique comment gérer les fichiers et dossiers qui sont sauvegardés avec l’agent Microsoft Azure Recovery Services.

## <a name="modify-a-backup-policy"></a>Modifier une stratégie de sauvegarde

Lorsque vous modifiez une stratégie de sauvegarde, vous pouvez ajouter de nouveaux éléments, supprimer des éléments existants de la sauvegarde ou exclure des fichiers de la sauvegarde à l’aide des paramètres d’exclusion.

- **Ajouter des éléments** : utilisez cette option uniquement pour ajouter de nouveaux éléments à sauvegarder. Pour supprimer des éléments existants, utilisez l’option **Supprimer des éléments** ou **Paramètres d’exclusion**.  
- **Supprimer des éléments** : utilisez cette option pour supprimer des éléments de la sauvegarde.
  - Utilisez **Paramètres d’exclusion** pour supprimer tous les éléments d’un volume au lieu de **Supprimer des éléments**.
  - L’effacement de toutes les sélections d’un volume entraîne la conservation des anciennes sauvegardes des éléments en fonction des paramètres de conservation au moment de la dernière sauvegarde, sans possibilité de modification.
  - La resélection de ces éléments entraîne une première sauvegarde complète et les modifications apportées à la nouvelle stratégie ne sont pas appliquées aux anciennes sauvegardes.
  - La désélection d’un volume entier conserve la sauvegarde précédente sans possibilité de modifier la stratégie de conservation.
- **Paramètres d’exclusion** : utilisez cette option pour exclure des éléments spécifiques de la sauvegarde.

### <a name="add-new-items-to-existing-policy"></a>Ajouter de nouveaux éléments à une stratégie existante

1. Dans **Actions**, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. Dans l’onglet **Sélectionner un élément de stratégie**, sélectionnez **Modifier la planification de sauvegarde pour vos fichiers et dossiers** et cliquez sur **Suivant**.

    ![Sélectionner des éléments de stratégie](./media/backup-azure-manage-mars/select-policy-items.png)

3. Dans l’onglet **Modifier ou arrêter la planification de sauvegarde**, sélectionnez **Apporter des modifications aux éléments ou aux heures de la sauvegarde**, puis cliquez sur **Suivant**.

    ![Modifier ou planifier une sauvegarde](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Dans l’onglet **Sélectionner les éléments à sauvegarder**, cliquez sur **Ajouter des éléments** pour ajouter les éléments à sauvegarder.

    ![Modifier ou planifier une sauvegarde, Ajouter des éléments](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Dans la fenêtre **Sélectionner les éléments**, sélectionnez les fichiers ou dossiers à ajouter, puis cliquez sur **OK**.

    ![Sélectionner les éléments](./media/backup-azure-manage-mars/select-item.png)

6. Effectuez les étapes suivantes, puis cliquez sur **Terminer** pour terminer l’opération.

### <a name="add-exclusion-rules-to-existing-policy"></a>Ajouter des règles d’exclusion à une stratégie existante

Vous pouvez ajouter des règles d’exclusion pour ignorer les fichiers et les dossiers que vous ne souhaitez pas sauvegarder. Vous pouvez faire cela pendant la définition d’une nouvelle stratégie ou la modification d’une stratégie existante.

1. Dans le volet Actions, cliquez sur **Planifier la sauvegarde**. Accédez à **Sélectionner les éléments à sauvegarder** puis cliquez sur **Paramètres d’exclusion**.

    ![Sélectionner les éléments](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Dans **Paramètres d’exclusion**, cliquez sur **Ajouter une exclusion**.

    ![Sélectionner les éléments](./media/backup-azure-manage-mars/add-exclusion.png)

3. Dans **Sélectionner les éléments à exclure**, parcourez les fichiers et les dossiers, et sélectionnez les éléments à exclure, puis cliquez sur **OK**.

    ![Sélectionner les éléments](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Par défaut, tous les **sous-dossiers** dans les dossiers sélectionnés sont exclus. Vous pouvez modifier cela en sélectionnant **Oui** ou **Non**. Vous pouvez modifier et spécifier les types de fichiers à exclure, comme indiqué ci-dessous :

    ![Sélectionner les éléments](./media/backup-azure-manage-mars/subfolders-type.png)

5. Effectuez les étapes suivantes, puis cliquez sur **Terminer** pour terminer l’opération.

### <a name="remove-items-from-existing-policy"></a>Supprimer les éléments d’une stratégie existante

1. Dans le volet Actions, cliquez sur **Planifier la sauvegarde**. Accédez à **Sélectionner les éléments à sauvegarder**. Dans la liste, sélectionnez les fichiers et les dossiers à supprimer de la planification de sauvegarde, puis cliquez sur **Supprimer les éléments**.

    ![Sélectionner les éléments](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> Prenez garde lorsque vous supprimez complètement un volume de la stratégie.  Si vous devez l’ajouter à nouveau, il sera traité comme un nouveau volume. La prochaine sauvegarde planifiée effectuera une sauvegarde initiale (sauvegarde complète) au lieu d’une sauvegarde incrémentielle. Si vous avez besoin de supprimer et d’ajouter temporairement des éléments, nous vous recommandons d’utiliser **Paramètres d’exclusions** au lieu de **Supprimer des éléments** pour garantir l’exécution une sauvegarde incrémentielle au lieu d’une sauvegarde complète.

2. Effectuez les étapes suivantes, puis cliquez sur **Terminer** pour terminer l’opération.

## <a name="stop-protecting-files-and-folder-backup"></a>Arrêter la protection de la sauvegarde de fichiers et de dossiers

Il existe deux façons d’arrêter la protection de la sauvegarde de fichiers et de dossiers :

- **Arrêter la protection et conserver les données de sauvegarde**.
  - Cette option arrête la protection sur toutes les futures tâches de sauvegarde.
  - Le service Sauvegarde Azure conserve les points de récupération qui ont été sauvegardés en fonction de la stratégie de conservation.
  - Vous pourrez restaurer les données sauvegardées pour les points de récupération non expirés.
  - Si vous décidez de reprendre la protection, vous pouvez utiliser l’option *Réactiver la planification de sauvegarde*. Après cela, les données sont conservées en fonction de la nouvelle stratégie de conservation.
- **Arrêter la protection et supprimer les données de sauvegarde**.
  - Cette option empêche toutes les futures tâches de sauvegarde de protéger vos données et supprime tous les points de récupération.
  - Vous recevrez un e-mail d’alerte de suppression des données de sauvegarde avec le message *Vos données pour cet élément de sauvegarde ont été supprimées. Ces données seront temporairement disponibles pendant 14 jours, après quoi elles seront définitivement supprimées* et l’action recommandée *Protégez à nouveau l’élément de sauvegarde dans un délai de 14 jours pour récupérer vos données.*
  - Pour reprendre la protection, réactivez la protection dans un délai de 14 jours après l’opération de suppression.

### <a name="stop-protection-and-retain-backup-data"></a>Arrêter la protection et conserver les données de sauvegarde

1. Ouvrez la console de gestion de MARS, accédez au **volet Actions** et **sélectionnez Planifier la sauvegarde**.

    ![Modifiez ou arrêtez une sauvegarde planifiée.](./media/backup-azure-manage-mars/mars-actions.png)
1. Dans la page **Sélectionner un élément de stratégie**, sélectionnez **Modifier la planification de sauvegarde pour vos fichiers et dossiers** et cliquez sur **Suivant**.

    ![Modifiez ou arrêtez une sauvegarde planifiée.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Dans la page **Modifier ou arrêter une sauvegarde planifiée**, sélectionnez **Ne plus utiliser cette panification de sauvegarde, mais conserver les sauvegardes stockées jusqu’à la réactivation de la planification**. Ensuite, sélectionnez **Suivant**.

    ![Modifiez ou arrêtez une sauvegarde planifiée.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Dans **Suspendre la sauvegarde planifiée**, vérifiez les informations, puis cliquez sur **Terminer**.

    ![Modifiez ou arrêtez une sauvegarde planifiée.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. Dans **Modifier le processus de sauvegarde**, vérifiez que la suspension de la sauvegarde de la planification est à l’état de réussite, puis cliquez sur **Fermer** pour terminer.

### <a name="stop-protection-and-delete-backup-data"></a>Arrêter la protection et supprimer les données de sauvegarde

1. Ouvrez la console de gestion de MARS, accédez au volet **Actions** et sélectionnez **Planifier la sauvegarde**.
2. Dans la page **Modifier ou arrêter une sauvegarde planifiée**, sélectionnez **Ne plus utiliser cette panification de sauvegarde et supprimer toutes les sauvegardes stockées**. Ensuite, sélectionnez **Suivant**.

    ![Modifiez ou arrêtez une sauvegarde planifiée.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Dans la page **Arrêter une sauvegarde planifiée**, sélectionnez **Terminer**.

    ![Arrêtez une sauvegarde planifiée.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Vous êtes invité à entrer un code PIN de sécurité, que vous devez générer manuellement. Pour cela, commencez par vous connecter au portail Azure.
5. Accédez à **Coffre Recovery Services** > **Paramètres** > **Propriétés**.
6. Sous **Code PIN de sécurité**, sélectionnez **Générer**. Copiez ce code PIN. Il n’est valide que pendant cinq minutes.
7. Dans la console de gestion, collez le code PIN, puis sélectionnez **OK**.

    ![Générez un code PIN de sécurité.](./media/backup-azure-delete-vault/security-pin.png)

8. Dans la page **Modifier la progression de la sauvegarde**, le message suivant s’affiche : *Les données supprimées sont conservées pendant 14 jours. Passé ce délai, les données de sauvegarde sont définitivement supprimées.*  

    ![Supprimez l’infrastructure de sauvegarde.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Une fois les éléments de sauvegarde locaux supprimés, effectuez les étapes suivantes à partir du portail.

## <a name="re-enable-protection"></a>Réactiver la protection

Si vous avez arrêté la protection tout en conservant les données et avez décidé de reprendre la protection, vous pouvez réactiver la planification de sauvegarde en modifiant la stratégie de sauvegarde.

1. Dans **Actions** sélectionnez **Planifier la sauvegarde**.
1. Sélectionnez **Réactiver la planification de sauvegarde. Vous pouvez également modifier les éléments de sauvegarde ou les heures**, puis cliquer sur **Suivant**.<br>

    ![Supprimez l’infrastructure de sauvegarde.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Dans **Sélectionner les éléments à sauvegarder**, cliquez sur **Suivant**.

    ![Supprimez l’infrastructure de sauvegarde.](./media/backup-azure-manage-mars/re-enable-next.png)
1. Dans **Spécifier la planification de sauvegarde**, spécifiez la planification de sauvegarde, puis cliquez sur **Suivant**.
1. Dans **Sélectionner une stratégie de conservation**, spécifiez la durée de conservation, puis cliquez sur **Suivant**.
1. Enfin, dans l’écran **Confirmation**, vérifiez les détails de la stratégie, puis cliquez sur **Terminer**.

## <a name="re-generate-passphrase"></a>Régénérer la phrase secrète

Une phrase secrète est utilisée pour chiffrer et déchiffrer les données lors de la sauvegarde ou de la restauration de votre ordinateur local à l’aide de l’agent MARS sur ou à partir d’Azure. Si vous avez perdu ou oublié la phrase secrète, vous pouvez la régénérer (à condition que votre machine soit toujours inscrite auprès du coffre Recovery Services et que la sauvegarde soit configurée) en procédant comme suit :

- Dans la console de l’agent MARS, accédez à **volet Actions** > **Modifier les propriétés** >. Accédez ensuite à l’**onglet Chiffrement**.<br>
- Cochez la case **Modifier la phrase secrète**.<br>
- Entrez une nouvelle phrase secrète ou cliquez sur **Générer une phrase secrète**.
- Cliquez sur **Parcourir** pour enregistrer la nouvelle phrase secrète.

    ![Générez une phrase secrète.](./media/backup-azure-manage-mars/passphrase.png)
- Cliquez sur **OK** pour appliquer les Modifications.  Si la [fonctionnalité de sécurité](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) est activée sur le portail Azure pour le coffre Recovery Services, vous êtes invité à entrer le code PIN de sécurité. Pour recevoir le code PIN, effectuez les étapes décrites dans cet [article](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations).<br>
- Collez le code PIN de sécurité à partir du portail, puis cliquez sur **OK** pour appliquer les Modifications.<br>

    ![Générez une phrase secrète.](./media/backup-azure-manage-mars/passphrase2.png)
- Assurez-vous que la phrase secrète est enregistrée en toute sécurité à un autre emplacement (autre que la machine source), de préférence dans Azure Key Vault. Suivez toutes les phrases secrètes si vous avez plusieurs machines sauvegardées avec les agents MARS.


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les scénarios pris en charge et les limitations, reportez-vous à la [Matrice de prise en charge pour l’agent MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- En savoir plus sur le [comportement de conservation de la stratégie de sauvegarde à la demande](backup-configure-vault.md#set-up-on-demand-backup-policy-retention-behavior).
