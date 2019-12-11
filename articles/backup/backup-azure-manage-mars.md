---
title: Gérer et surveiller les sauvegardes de l’agent MARS
description: Découvrez comment gérer et surveiller les sauvegardes de l’agent Microsoft Azure Recovery Services (MARS) à l’aide du service Sauvegarde Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: f299bdeebab4f42721255d462101f0065a640fab
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665591"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Gérer les sauvegardes de l’agent Microsoft Azure Recovery Services (MARS) à l’aide du service Sauvegarde Azure

Cet article explique comment gérer les fichiers et dossiers qui sont sauvegardés avec l’agent Microsoft Azure Recovery Services.

## <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

La stratégie de sauvegarde spécifie à quel moment prendre des instantanés des données pour créer des points de récupération et la durée de conservation des points de récupération. Vous configurez la stratégie de sauvegarde à l’aide de l’agent MARS.

Créez une stratégie comme suit :

1. Après le téléchargement et l’inscription de l’agent MARS, lancez la console de l’agent. Vous pouvez le trouver en recherchant **Sauvegarde Microsoft Azure** sur votre ordinateur.  
2. Dans **Actions**, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
3. Dans l’Assistant Planifier la sauvegarde > **Prise en main**, cliquez sur **Suivant**.
4. Dans **Sélectionner les éléments à sauvegarder**, cliquez sur **Ajouter des éléments**.

    ![Sélectionner les éléments à sauvegarder](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. Dans **Sélectionner des éléments**, sélectionnez ce que vous souhaitez sauvegarder, puis cliquez sur **OK**.

    ![Éléments sélectionnés à sauvegarder](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. Dans **Sélectionner les éléments à sauvegarder**, cliquez sur **Suivant**.
7. Sur la page **Spécifier la planification de la sauvegarde**, indiquez à quel moment vous souhaitez effectuer des sauvegardes quotidiennes ou hebdomadaires. Cliquez ensuite sur **Suivant**.

    - Un point de récupération est créé lorsqu’une sauvegarde est effectuée.
    - Le nombre de points de récupération créés dans votre environnement dépend de votre planification de sauvegarde.

8. Vous pouvez planifier des sauvegardes quotidiennes, jusqu’à trois fois par jour. Par exemple, la capture d’écran montre deux sauvegardes quotidiennes, l’une à minuit et l’autre à 18h.

    ![Planification quotidienne](./media/backup-configure-vault/day-schedule.png)

9. Vous pouvez également exécuter des sauvegardes hebdomadaires. Par exemple, la capture d’écran montre des sauvegardes effectuées un dimanche et un mercredi sur deux à 9h30 et 1h.

    ![Planification hebdomadaire](./media/backup-configure-vault/week-schedule.png)

10. Sur la page **Sélectionner la stratégie de conservation**, spécifiez la façon dont vous stockez les copies historiques de vos données. Cliquez ensuite sur **Suivant**.

    - Les paramètres de conservation spécifient les points de récupération à stocker, ainsi que la durée pendant laquelle ils doivent être stockés.
    - Par exemple, lorsque vous définissez un paramètre de conservation quotidienne, vous indiquez qu’à l’heure spécifiée pour la conservation quotidienne, le dernier point de récupération sera conservé pendant le nombre de jours spécifié. Ou, autre exemple, vous pouvez spécifier une stratégie de conservation mensuelle pour indiquer que le point de récupération créé le 30 de chaque mois doit être stocké pendant 12 mois.
    - La conservation quotidienne et hebdomadaire des points de récupération coïncide généralement avec la planification de sauvegarde. Cela signifie que lorsque la sauvegarde est déclenchée en fonction de la planification, le point de récupération créé par la sauvegarde est stocké pendant la durée indiquée dans la stratégie de conservation quotidienne ou hebdomadaire.
    - Par exemple, dans la capture d’écran suivante : - Les sauvegardes quotidiennes à minuit et 18h sont conservées pendant sept jours.
            - Les sauvegardes effectuées le samedi à minuit et 18h sont conservées pendant quatre semaines.
            - Les sauvegardes effectuées le samedi de la dernière semaine du mois à minuit et 18h sont conservées pendant 12 mois.
            - Les sauvegardes effectuées le samedi au cours de la dernière semaine du mois de mars sont conservées pendant 10 ans.

    ![Exemple de conservation](./media/backup-configure-vault/retention-example.png)

11. Dans **Choisir le type de sauvegarde initiale**, décidez si vous souhaitez effectuer la sauvegarde initiale sur le réseau ou utiliser la sauvegarde en mode hors connexion (pour plus d’informations sur la sauvegarde en mode hors connexion, consultez cet [article](backup-azure-backup-import-export.md)). Pour effectuer la sauvegarde initiale sur le réseau, sélectionnez **Sur le réseau automatiquement** et cliquez sur **Suivant**.

    ![Type de sauvegarde initiale](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

12. Dans **Confirmation**, passez en revue les informations, puis cliquez sur **Terminer**.
    ![Confirmer le type de sauvegarde](./media/backup-azure-manage-mars/confirm-backup-type.png)

13. Lorsque l’Assistant a terminé la création de la planification de la sauvegarde, cliquez sur **Fermer**.
  ![Confirmer la modification du processus de sauvegarde](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Vous devez créer une stratégie sur chaque ordinateur sur lequel l’agent est installé.

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
1. Dans **Suspendre la sauvegarde planifiée**, vérifiez les informations, puis cliquez sur **Terminer** ![Modifier ou arrêter une sauvegarde planifiée.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
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
1. Sélectionnez **Réactiver la planification de sauvegarde. Vous pouvez également modifier les éléments de sauvegarde ou les heures**, puis cliquer sur **Suivant**.
    ![Supprimez l’infrastructure de sauvegarde.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Dans **Sélectionner les éléments à sauvegarder**, cliquez sur **Suivant**.
    ![Supprimez l’infrastructure de sauvegarde.](./media/backup-azure-manage-mars/re-enable-next.png)
1. Dans **Spécifier la planification de sauvegarde**, spécifiez la planification de sauvegarde, puis cliquez sur **Suivant**.
1. Dans **Sélectionner une stratégie de conservation**, spécifiez la durée de conservation, puis cliquez sur **Suivant**.
1. Enfin, dans l’écran **Confirmation**, vérifiez les détails de la stratégie, puis cliquez sur **Terminer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les scénarios pris en charge et les limitations, reportez-vous à la [Matrice de prise en charge pour MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- En savoir plus sur le [comportement de conservation de la stratégie de sauvegarde à la demande](backup-configure-vault.md#on-demand-backup-policy-retention-behavior).
