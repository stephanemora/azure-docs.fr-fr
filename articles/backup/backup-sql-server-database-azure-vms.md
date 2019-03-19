---
title: Sauvegarder des bases de données SQL Server dans Azure | Microsoft Docs
description: Ce tutoriel explique comment sauvegarder SQL Server avec Azure,
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sachdevaswati
ms.openlocfilehash: 5fe4161c688570cc3adaacc79a0dd1fe38460142
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2019
ms.locfileid: "58118899"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Sauvegarder des bases de données SQL Server sur des machines virtuelles Azure

Les bases de données SQL Server sont des charges de travail critiques nécessitant un faible objectif de point de récupération (RPO) et une conservation à long terme. Vous pouvez sauvegarder les bases de données SQL Server qui s’exécutent sur les machines virtuelles Azure par l’intermédiaire de la [sauvegarde Azure](backup-overview.md).

Cet article vous explique comment sauvegarder dans un coffre Recovery Services de la sauvegarde Azure une base de données SQL Server s’exécutant sur une machine virtuelle Azure. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer et configurer un coffre
> * Détecter des bases de données et configurer des sauvegardes
> * Configurer la protection automatique de bases de données


## <a name="prerequisites"></a>Conditions préalables

Pour pouvoir sauvegarder votre base de données SQL Server, vérifiez les conditions suivantes :

1. Identifier ou [créer](backup-azure-sql-database.md#create-a-recovery-services-vault) un coffre Recovery Services dans la même région ou paramètres régionaux que la machine virtuelle qui héberge l’instance de SQL Server.
2. [Vérifiez les autorisations de la machine virtuelle](#fix-sql-sysadmin-permissions) qui sont nécessaires pour sauvegarder les bases de données SQL.
3. Vérifiez que la machine virtuelle dispose d’une [connectivité réseau](backup-azure-sql-database.md#establish-network-connectivity).
4. Vérifiez que les bases de données SQL Server sont nommées conformément aux [instructions de nommage](#verify-database-naming-guidelines-for-azure-backup) pour la sauvegarde Azure.
5. Vérifiez que vous n’avez aucune autre solution de sauvegarde activée pour la base de données. Désactivez tous les autres sauvegardes SQL Server avant de configurer ce scénario. Vous pouvez activer la Sauvegarde Azure pour une machine virtuelle Azure en même temps que la Sauvegarde Azure pour une base de données SQL Server s’exécutant sur la machine virtuelle, sans aucun conflit.


### <a name="establish-network-connectivity"></a>Établir la connectivité réseau

Pour toute opération, la machine virtuelle SQL Server a besoin d’une connectivité pour accéder aux adresses IP publiques Azure. Opérations de machine virtuelle (découverte de base de données, configurer des sauvegardes, planifier des sauvegardes, restaurer des points de récupération et ainsi de suite) échouer sans connectivité vers les adresses IP publiques. Établissez une connexion à l’aide d’une des options suivantes :

- **Autoriser les plages d’adresses IP du centre de données Azure** : autorisez les [plages d’adresses IP](https://www.microsoft.com/download/details.aspx?id=41653) dans le téléchargement. Pour accéder au groupe de sécurité réseau (NSG), utilisez le **Set-AzureNetworkSecurityRule** applet de commande.
- **Déployer un serveur proxy HTTP pour le routage du trafic** : lorsque vous sauvegardez une base de données SQL Server sur une machine virtuelle Azure, l’extension de sauvegarde sur la machine virtuelle utilise les API HTTPS pour envoyer des commandes de gestion à la sauvegarde Azure, et des données au stockage Azure. L’extension de sauvegarde utilise également Azure Active Directory (Azure AD) pour l’authentification. Acheminez le trafic de l’extension de sauvegarde pour ces trois services via le proxy HTTP. L’extension est le seul composant qui est configuré pour l’accès à l’internet public.

Chaque option présente des avantages et des inconvénients

**Option** | **Avantages** | **Inconvénients**
--- | --- | ---
Autoriser les plages d’adresses IP | Aucun coût supplémentaire | Difficile à gérer, car les plages d’adresses IP changent au fil du temps. <br/><br/> Fournit un accès à l’ensemble d’Azure et pas seulement au stockage Azure.
Utiliser un proxy HTTP   | Le contrôle granulaire dans le proxy sur les URL de stockage est autorisé. <br/><br/> Un seul point d’accès Internet aux machines virtuelles. <br/><br/> Non soumis aux modifications d’adresse IP Azure. | Frais supplémentaires d’exécution de machine virtuelle avec le logiciel de serveur proxy.

### <a name="set-vm-permissions"></a>Définir des autorisations de machine virtuelle

La sauvegarde Azure effectue un certain nombre de choses lorsque vous configurez la sauvegarde pour une base de données SQL Server :

- Elle ajoute l’extension **AzureBackupWindowsWorkload**.
- Lors de la découverte des bases de données sur la machine virtuelle, la Sauvegarde Azure crée le compte **NT SERVICE\AzureWLBackupPluginSvc**. Ce compte est utilisé pour la sauvegarde et la restauration, il doit disposer d’autorisations sysadmin SQL.
- La sauvegarde Azure utilise le compte **NT AUTHORITY\SYSTEM** pour la détection et l’interrogation des bases de données. Ce compte doit donc être une connexion publique sur SQL.

Si vous n’avez pas créé la machine virtuelle SQL Server à partir de la Place de marché Azure, vous pouvez recevoir une erreur **UserErrorSQLNoSysadminMembership**. Si cela se produit, [suivez ces instructions](#fix-sql-sysadmin-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Vérifier les instructions de nommage des bases de données pour la sauvegarde Azure

Évitez les éléments suivants pour les noms de base de données :

  * Espaces au début ou à la fin
  * « ! » à la fin
  * Crochets de fermeture « ] »

Il existe bien des alias pour les caractères non pris en charge dans la table Azure, mais nous vous recommandons de les éviter. [Plus d’informations](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Détecter les bases de données SQL Server

Détectez les bases de données en cours d’exécution sur la machine virtuelle.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le coffre Recovery Services que vous utilisez pour sauvegarder la base de données.

2. Dans le tableau de bord du **coffre Recovery Services**, cliquez sur **Sauvegarde**.

   ![Cliquer sur Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/open-backup-menu.png)

3. Dans **Objectif de sauvegarde**, définissez **Où s’exécute votre charge de travail ?** sur **Azure** (la valeur par défaut).

4. Dans **Que souhaitez-vous sauvegarder ?**, sélectionnez **SQL Server dans une machine virtuelle Azure**.

    ![Sélectionnez SQL Server dans une machine virtuelle Azure pour la sauvegarde](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Dans **Objectif de sauvegarde** > **Découvrir les bases de données dans les machines virtuelles**, sélectionnez **Démarrer la détection** pour rechercher des machines virtuelles non protégées dans l’abonnement. La durée de cette opération varie selon le nombre de machines virtuelles non protégées de l’abonnement.

   - Après la détection, les machines virtuelles non protégées doivent apparaître dans la liste, répertoriées par nom et groupe de ressources.
   - Si une machine virtuelle n’est pas répertoriée contrairement à ce que vous attendez, vérifiez si elle n’est pas déjà sauvegardée dans un coffre.
   - Plusieurs machines virtuelles peuvent avoir le même nom, mais appartenir à différents groupes de ressources.

     ![La sauvegarde est en attente au cours de la recherche pour des bases de données dans des machines virtuelles](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Dans la liste des machines virtuelles, sélectionnez la machine virtuelle exécutant la base de données SQL Server > **Découvrir les bases de données**.

7. Suivez la découverte des bases de données dans la zone **Notifications**. L’exécution du travail peut prendre un certain temps, et varie en fonction du nombre de bases de données résidant sur la machine virtuelle. Lorsque les bases de données sélectionnées ont été détectées, un message de réussite s’affiche.

    ![Message Déploiement réussi](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. La sauvegarde Azure détecte toutes les bases de données SQL Server résidant sur la machine virtuelle. Lors de la découverte, les événements suivants se produisent en arrière-plan :

    - La sauvegarde Azure inscrit la machine virtuelle auprès du coffre pour la sauvegarde de la charge de travail. Les bases de données présentes sur la machine virtuelle inscrite ne peuvent être sauvegardées que sur ce coffre.
    - La sauvegarde Azure installe l’extension **AzureBackupWindowsWorkload** sur la machine virtuelle. Aucun agent n’est installé sur la base de données SQL.
    - La sauvegarde Azure crée le compte de service **NT Service\AzureWLBackupPluginSvc** sur la machine virtuelle.
      - Toutes les opérations de sauvegarde et de restauration utilisent le compte de service.
      - **NT Service\AzureWLBackupPluginSvc** nécessite des autorisations d’administrateur système SQL. **SqlIaaSExtension** est installé sur toutes les machines virtuelles SQL Server créées dans la Place de marché Azure. L’extension **AzureBackupWindowsWorkload** utilise l’extension **SQLIaaSExtension** pour obtenir automatiquement les autorisations requises.
    - Si vous n’avez pas créé la machine virtuelle à partir de la Place de marché, **SqlIaaSExtension** n’est pas installé sur la machine virtuelle, et l’opération de découverte échoue avec le message d’erreur **UserErrorSQLNoSysAdminMembership**. Suivez les instructions dans [#fix-sql-sysadmin-permissions] pour résoudre ce problème.

        ![Sélectionner la machine virtuelle et la base de données](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurer une sauvegarde  

Configurez la sauvegarde de la façon suivante :

1. Dans le menu **Objectif de sauvegarde**, sélectionnez **Configurer la sauvegarde**.

   ![Sélectionner Configurer la sauvegarde](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Cliquez sur **configurer sauvegarde**, le **sélectionner des éléments à sauvegarder** panneau s’affiche. Ce rapport répertorie tous les groupes de disponibilité inscrit et autonome serveurs SQL. Développez le chevron situé à gauche de la ligne pour voir toutes les bases de données non protégées de cette instance ou de toujours sur le groupe de disponibilité.  

    ![Affichage de toutes les instances SQL Server avec des bases de données autonomes](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Sélectionnez toutes les bases de données que vous souhaitez protéger > **OK**.

   ![Protection de la base de données](./media/backup-azure-sql-database/select-database-to-protect.png)

   Pour optimiser les charges de sauvegarde, la sauvegarde Azure définit le nombre maximal de bases de données à 50 dans une tâche de sauvegarde.

     * Pour protéger plus de 50 bases de données, configurez plusieurs sauvegardes.
     * Vous pouvez aussi activer la [protection automatique](#enable-auto-protection) sur l’ensemble de l’instance ou du groupe de disponibilité Always On en sélectionnant l’option **ACTIVÉ** dans la liste déroulante correspondant à la colonne **PROTECTION AUTOMATIQUE**. La fonctionnalité de [protection automatique](#enable-auto-protection) permet non seulement de protéger toutes les bases de données existantes en une seule étape, mais aussi de protéger automatiquement toutes les nouvelles bases de données qui seront ajoutées à cette instance ou ce groupe de disponibilité par la suite.  

4. Cliquez sur **OK** pour ouvrir le **stratégie de sauvegarde** panneau.

    ![Désactiver la protection automatique sur cette instance](./media/backup-azure-sql-database/disable-auto-protection.png)

5. Dans **choisir une stratégie de sauvegarde**, sélectionnez une stratégie, puis cliquez sur **OK**.

   - Sélectionner la stratégie par défaut : HourlyLogBackup.
   - Choisir une stratégie de sauvegarde existante créée précédemment pour SQL.
   - [Définir une nouvelle stratégie](#configure-a-backup-policy) selon votre RPO et la durée de rétention.

     ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-sql-database/select-backup-policy.png)

6. Sur **sauvegarde** menu, sélectionnez **activer la sauvegarde**.

    ![Activer la stratégie de sauvegarde choisie](./media/backup-azure-sql-database/enable-backup-button.png)

7. Suivre la progression de la configuration dans le **Notifications** zone du portail.

    ![Zone Notifications](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

Une stratégie de sauvegarde définit le moment auquel les sauvegardes sont effectuées ainsi que leur durée de rétention.

- Une stratégie est créée au niveau du coffre.
- Plusieurs coffres peuvent utiliser la même stratégie de sauvegarde, mais vous devez appliquer la stratégie de sauvegarde à chaque coffre.
- Lorsque vous créez une stratégie de sauvegarde, une sauvegarde complète quotidienne est la valeur par défaut.
- Vous pouvez ajouter une sauvegarde différentielle, mais uniquement si vous configurez les sauvegardes complètes pour qu’elles aient lieu toutes les semaines.
- [Apprenez-en davantage](backup-architecture.md#sql-server-backup-types) sur les différents types de stratégies de sauvegarde.

Pour créer une stratégie de sauvegarde :

1. Dans le coffre, cliquez sur **Stratégies de sauvegarde** > **Ajouter**.
2. Dans le menu **Ajouter**, cliquez sur **SQL Server dans une machine virtuelle Azure**. Le type de stratégie est alors défini.

   ![Choisissez un type de stratégie pour la nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/policy-type-details.png)

3. Dans **Nom de la stratégie**, entrez le nom de la nouvelle stratégie.
4. Dans **Stratégie de sauvegarde complète**, comme **Fréquence de sauvegarde**, sélectionnez **Tous les jours** ou **Toutes les semaines**.

   - Si vous sélectionnez **quotidienne**, sélectionnez l’heure et le fuseau horaire de début du travail de sauvegarde.
   - Vous devez exécuter une sauvegarde complète, vous ne pouvez pas désactiver l’option **Sauvegarde complète**.
   - Cliquez sur **Sauvegarde complète** pour afficher la stratégie.
   - Si vous choisissez des sauvegardes complètes quotidiennes, vous ne pouvez pas créer de sauvegardes différentielles.
   - Si vous choisissez la fréquence **hebdomadaire**, sélectionnez le jour de la semaine, l’heure et le fuseau horaire indiquant le début du travail de sauvegarde.

     ![Champs de la nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Pour la **Durée de rétention**, par défaut toutes les options sont sélectionnées. Désactivez les limites des plages de rétention dont vous ne souhaitez pas vous servir, puis définissez les intervalles à utiliser.

    - Période de rétention minimale pour n’importe quel type de sauvegarde (complète/différentielle/de journal) est de 7 jours.
    - Des points de récupération sont marqués pour la rétention et varient selon la durée de rétention. Par exemple, si vous sélectionnez une sauvegarde complète quotidienne, seule une sauvegarde complète est déclenchée chaque jour.
    - La sauvegarde d’un jour spécifique est marquée et conservée conformément à la durée et aux paramètres de rétention hebdomadaire.
    - Les durées de rétention mensuelle et annuelle ont le même comportement.

   ![Paramètres d’intervalle de la durée de rétention](./media/backup-azure-sql-database/retention-range-interval.png)

6. Dans le menu de **stratégie Sauvegarde complète**, cliquez sur **OK** pour accepter les paramètres.
7. Pour ajouter une stratégie de sauvegarde différentielle, sélectionnez **Sauvegarde différentielle**.

   ![Paramètres d’intervalle des durées de rétention](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Ouvrir le menu de la stratégie de sauvegarde différentielle](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Dans la stratégie **Sauvegarde différentielle**, sélectionnez **Activer** pour ouvrir les contrôles de fréquence et de rétention.

    - Vous pouvez déclencher au plus une sauvegarde différentielle par jour.
    - Les sauvegardes différentielles peuvent être conservées jusqu’à 180 jours. Si vous avez besoin d’une durée de rétention supérieure, vous devez utiliser des sauvegardes complètes.

9. Sélectionnez **OK** pour enregistrer la stratégie et revenir au menu principal **Stratégie de sauvegarde**.

10. Pour ajouter une stratégie de sauvegarde de fichier journal, cliquez sur **Sauvegarde de fichier journal**.
11. Dans **Sauvegarde de fichier journal**, sélectionnez **Activer** et définissez les contrôles de fréquence et de rétention. Les sauvegardes de fichiers journaux peuvent se produire toutes les 15 minutes et être conservées jusqu’à 35 jours.
12. Sélectionnez **OK** pour enregistrer la stratégie et revenir au menu principal **Stratégie de sauvegarde**.

    ![Modifier la stratégie de sauvegarde de fichier journal](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Dans le menu **Stratégie de sauvegarde**, choisissez s’il convient d’activer l’option **Compression de la sauvegarde SQL**,
    - qui est désactivée par défaut.
    - Sur le serveur principal, Sauvegarde Azure utilise la compression de sauvegarde native SQL.

14. Après avoir terminé les modifications apportées à la stratégie de sauvegarde, sélectionnez **OK**.


### <a name="modify-policy"></a>Modifier la stratégie
Modifier la stratégie pour modifier la plage de fréquence ou de rétention de sauvegarde.

> [!NOTE]
> Toute modification de la période de rétention s’appliqueront a posteriori à tous les points de récupération plus anciens en plus des nouveaux styles.

Dans le tableau de bord du coffre, accédez à **gérer** > **stratégies de sauvegarde** et choisissez la stratégie que vous souhaitez modifier.

  ![Gérer la stratégie de sauvegarde](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Activer la protection automatique  

Activez la protection automatique pour sauvegarder systématiquement toutes les bases de données existantes, ainsi que toutes les bases de données ajoutées ultérieurement à une instance SQL Server autonome ou à un groupe de disponibilité SQL Server Always On.

  - Lorsque vous activez la protection automatique et que vous sélectionnez une stratégie, les bases de données existantes qui sont protégées continuent d’utiliser la stratégie précédente.
  - Il n’existe aucune limite sur le nombre de bases de données que vous pouvez sélectionner pour la protection automatique d’un coup.

Activez la protection automatique de la façon suivante :

  1. Dans **Éléments à sauvegarder**, sélectionnez l’instance pour laquelle vous souhaitez activer la protection automatique.
  2. Sélectionnez la flèche déroulante sous **Protection automatique** et définissez sa valeur sur **Active**. Cliquez ensuite sur **OK**.

      ![Activer la protection automatique sur le groupe de disponibilité Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. La sauvegarde est configurée pour toutes les bases de données et peut être suivie dans **Travaux de sauvegarde**.

Si vous devez désactiver la protection automatique, cliquez sur le nom d’instance sous **configurer la sauvegarde**, puis sélectionnez **désactiver la protection automatique** pour l’instance. Toutes les bases de données continuent d’être sauvegardées. Les futures bases de données, en revanche, ne seront pas automatiquement protégées.


## <a name="fix-sql-sysadmin-permissions"></a>Correction des autorisations d’administrateur système SQL

  Si vous devez corriger des autorisations en raison d’une erreur **UserErrorSQLNoSysadminMembership**, procédez comme suit :

  1. Utilisez un compte disposant d’autorisations d’administrateur système SQL Server pour vous connecter à SQL Server Management Studio (SSMS). À moins que vous ayez besoin d’autorisations spéciales, l’authentification Windows doit fonctionner.
  2. Sur le serveur SQL Server, ouvrez le dossier **Security/Logins**.

      ![Ouvrir le dossier Security/Logins pour afficher les comptes](./media/backup-azure-sql-database/security-login-list.png)

  3. Cliquez avec le bouton droit sur le dossier **Logins** et sélectionnez **Nouvelle connexion**. Dans **Nouvelle connexion**, sélectionnez **Rechercher**.

      ![Dans la boîte de dialogue Connexion - Nouveau, sélectionnez Rechercher](./media/backup-azure-sql-database/new-login-search.png)

  4. Le compte de service virtuel Windows **NT SERVICE\AzureWLBackupPluginSvc** a été créé pendant la phase d’inscription de la machine virtuelle et de découverte SQL. Entrez le nom du compte, comme indiqué dans **Entrez le nom de l’objet à sélectionner**. Sélectionnez **Vérifier les noms** pour résoudre le nom. Cliquez sur **OK**.

      ![Cliquer sur Vérifier les noms pour résoudre le nom de service inconnu](./media/backup-azure-sql-database/check-name.png)

  5. Dans **Rôles du serveur**, assurez-vous que le rôle **sysadmin** (administrateur système) est sélectionné. Cliquez sur **OK**. Les autorisations requises doivent désormais exister.

      ![S’assurer que le rôle serveur administrateur système est sélectionné](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. À présent, associez la base de données avec le coffre Recovery Services. Dans la liste **Serveurs protégés** du portail Azure, cliquez avec le bouton droit sur le serveur en erreur > **Redécouvrir les bases de données**.

      ![Vérifier que le serveur dispose des autorisations appropriées](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Vérifiez la progression dans la zone **Notifications**. Lorsque les bases de données sélectionnées ont été détectées, un message de réussite s’affiche.

      ![Message Déploiement réussi](./media/backup-azure-sql-database/notifications-db-discovered.png)

 
## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur](restore-sql-database-azure-vm.md) la restauration des bases de données SQL Server sauvegardées.
- [En savoir plus sur](manage-monitor-sql-database-backup.md) la gestion des bases de données SQL Server sauvegardées.
