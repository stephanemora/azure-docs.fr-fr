---
title: Sauvegarde des bases de données SQL Server sur des machines virtuelles Azure | Microsoft Docs
description: Découvrez comment sauvegarder des bases de données SQL Server sur des machines virtuelles Azure
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 0307dc5c83782119f6c10279563b8b9f0a999d28
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236877"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Sauvegarder des bases de données SQL Server sur des machines virtuelles Azure

Bases de données SQL Server sont des charges de travail critiques qui nécessitent un objectif de point de récupération faible (RPO) et la rétention à long terme. Vous pouvez sauvegarder des bases de données SQL Server en cours d’exécution sur des machines virtuelles (VM) Azure à l’aide de [sauvegarde Azure](backup-overview.md).

Cet article explique comment sauvegarder une base de données SQL Server qui s’exécute sur une machine virtuelle Azure dans un coffre Azure Backup Recovery Services.

Cet article porte sur les points suivants :

> [!div class="checklist"]
> * Créer et configurer un coffre
> * Découvrez les bases de données et configurer des sauvegardes.
> * Configurer la protection automatique de bases de données


## <a name="prerequisites"></a>Conditions préalables

Avant de sauvegarder une base de données SQL Server, vérifiez les critères suivants :

1. Identifiez ou créez un [coffre Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) dans la même région ou paramètres régionaux que la machine virtuelle qui héberge l’instance de SQL Server.
2. Vérifier le [autorisations pour ordinateurs virtuels nécessitées](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) pour sauvegarder des bases de données SQL.
3. Vérifiez que la machine virtuelle a [la connectivité réseau](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Assurez-vous que les bases de données SQL Server suivent le [les instructions d’affectation de noms de base de données pour la sauvegarde Azure](#database-naming-guidelines-for-azure-backup).
5. Vérifiez que vous n’avez pas d’autres solutions de sauvegarde activées pour la base de données. Désactiver tous les autres sauvegardes de SQL Server avant de sauvegarder la base de données.

> [!NOTE]
> Vous pouvez activer la sauvegarde Azure pour une machine virtuelle Azure et également pour une base de données SQL Server en cours d’exécution sur la machine virtuelle sans conflit.


### <a name="establish-network-connectivity"></a>Établir la connectivité réseau

Pour toutes les opérations, une machine virtuelle SQL Server nécessite une connectivité vers les adresses IP publiques Azure. Les opérations de machine virtuelle (détection de la base de données, configurer des sauvegardes, planifier des sauvegardes, restaurer des points de récupération et ainsi de suite) échouent sans connectivité vers les adresses IP publiques Azure.

Établir la connectivité en utilisant l’une des options suivantes :

- **Autoriser les plages IP de centre de données Azure**. Cette option permet de [plages d’adresses IP](https://www.microsoft.com/download/details.aspx?id=41653) dans le téléchargement. Pour accéder à un groupe de sécurité réseau (NSG), utilisez l’applet de commande Set-AzureNetworkSecurityRule. Si vous êtes sûr, seules les adresses IP spécifiques à une région de liste de destinataires, vous devez également mettre à jour la liste de destinataires fiables de la balise de service Azure Active Directory (Azure AD) pour activer l’authentification.

- **Autoriser l’accès à l’aide de balises de groupe de sécurité réseau**. Si vous utilisez des groupes de sécurité réseau pour limiter la connectivité, cette option ajoute une règle à votre groupe de sécurité réseau qui autorise un accès sortant à la sauvegarde Azure à l’aide de la balise AzureBackup. En plus de cette balise, vous devez également correspondant [règles](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) pour Azure AD et le stockage Azure pour autoriser la connectivité pour le transfert de données et d’authentification. La balise AzureBackup est actuellement disponible sur PowerShell uniquement. Pour créer une règle à l’aide de la balise AzureBackup :

    - Ajouter des informations d’identification de compte Azure et mettre à jour les clouds nationaux<br/>
    `Add-AzureRmAccount`

    - Sélectionnez l’abonnement de groupe de sécurité réseau<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - Sélectionnez le groupe de sécurité réseau<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - Ajouter autoriser la règle de trafic sortant pour la balise de service de sauvegarde Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - Enregistrer le groupe de sécurité réseau<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **Autoriser l’accès à l’aide de balises de pare-feu Azure**. Si vous utilisez des pare-feu d’Azure, créer une règle d’application à l’aide de la AzureBackup [balise de nom de domaine complet](https://docs.microsoft.com/azure/firewall/fqdn-tags). Cela permet un accès sortant à sauvegarde Azure.
- **Déployer un serveur proxy HTTP pour acheminer le trafic**. Lorsque vous sauvegardez une base de données SQL Server sur une machine virtuelle Azure, l’extension de sauvegarde sur la machine virtuelle utilise les API de HTTPS pour envoyer des commandes de gestion de sauvegarde Azure et les données vers le stockage Azure. L’extension de sauvegarde utilise également Azure AD pour l’authentification. Acheminez le trafic de l’extension de sauvegarde pour ces trois services via le proxy HTTP. Les extensions sont le seul composant configuré pour l’accès à l’internet public.

Options de connectivité sont les avantages et inconvénients suivants :

**Option** | **Avantages** | **Inconvénients**
--- | --- | ---
Autoriser les plages d’adresses IP | Aucun coût supplémentaire | Difficile à gérer étant donné que les plages d’adresses IP changent au fil du temps <br/><br/> Fournit l’accès à l’ensemble d’Azure, pas seulement au stockage Azure
Utiliser des balises de service de groupe de sécurité réseau | Plus facile à gérer que les modifications de la plage sont fusionnées automatiquement <br/><br/> Aucun coût supplémentaire <br/><br/> | Peut être utilisé uniquement avec les groupes de sécurité réseau <br/><br/> Fournit l’accès à l’ensemble du service
Utiliser des balises Azure Firewall FQDN | Plus facile à gérer car les noms de domaine complets requis sont automatiquement gérées | Peut être utilisé uniquement avec le pare-feu Azure
Utiliser un proxy HTTP | Contrôle granulaire dans le proxy sur le stockage URL est autorisée. <br/><br/> Unique point d’accès internet aux machines virtuelles <br/><br/> Non soumises à des changements d’adresses IP d’Azure | Frais supplémentaires pour exécuter une machine virtuelle avec le logiciel de serveur proxy

### <a name="set-vm-permissions"></a>Définir des autorisations de machine virtuelle

Lorsque vous configurez une sauvegarde pour une base de données SQL Server, sauvegarde Azure effectue les opérations suivantes :

- Ajoute l’extension AzureBackupWindowsWorkload.
- Crée un compte NT SERVICE\AzureWLBackupPluginSvc pour découvrir les bases de données sur la machine virtuelle. Ce compte est utilisé pour une sauvegarde et restauration et nécessite des autorisations d’administrateur système SQL.
- Découvre les bases de données qui sont exécutent sur une machine virtuelle, Azure Backup utilise le compte NT AUTHORITY\SYSTEM. Ce compte doit être un public connectez-vous sur SQL.

Si vous n’avez pas créé la machine virtuelle SQL Server dans Azure Marketplace, vous pouvez recevoir une erreur UserErrorSQLNoSysadminMembership. Pour plus d’informations, consultez la section Considérations et limitations de fonctionnalité trouvée dans [à propos de sauvegarde de SQL Server sur des machines virtuelles Azure](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

### <a name="database-naming-guidelines-for-azure-backup"></a>Règles de dénomination pour la sauvegarde Azure de base de données

Évitez d’utiliser les éléments suivants dans les noms de base de données :

  * Les espaces à gauche et à droite
  * Fin des points d’exclamation ( !)
  * Crochets fermant (])
  * Point-virgule « ; »
  * Barre oblique « / »

Alias n’est disponible pour les caractères non pris en charge, mais nous vous recommandons de les éviter. Pour plus d'informations, consultez la rubrique [Présentation du modèle de données du service de Table](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Détecter les bases de données SQL Server

Comment découvrir les bases de données en cours d’exécution sur une machine virtuelle :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le coffre Recovery Services que vous utilisez pour sauvegarder la base de données.

2. Dans le **coffre Recovery Services** tableau de bord, sélectionnez **sauvegarde**.

   ![Cliquer sur Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/open-backup-menu.png)

3. Dans **objectif de sauvegarde**, affectez la valeur **où s’exécute votre charge de travail ?** à **Azure**.

4. Dans **Que souhaitez-vous sauvegarder ?** , sélectionnez **SQL Server dans une machine virtuelle Azure**.

    ![Sélectionnez SQL Server dans une machine virtuelle Azure pour la sauvegarde](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Dans **Objectif de sauvegarde** > **Découvrir les bases de données dans les machines virtuelles**, sélectionnez **Démarrer la détection** pour rechercher des machines virtuelles non protégées dans l’abonnement. Cette recherche peut prendre un certain temps, selon le nombre de machines virtuelles non protégées dans l’abonnement.

   - Après la détection, les machines virtuelles non protégées doivent apparaître dans la liste, répertoriées par nom et groupe de ressources.
   - Si une machine virtuelle n’est pas répertoriée comme prévu, consultez si elle est déjà sauvegardé dans un coffre.
   - Plusieurs machines virtuelles peuvent avoir le même nom, mais elles amèneront appartiennent à différents groupes de ressources.

     ![La sauvegarde est en attente au cours de la recherche pour des bases de données dans des machines virtuelles](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Dans la liste des machines virtuelles, sélectionnez la machine virtuelle exécutant la base de données SQL Server > **Découvrir les bases de données**.

7. Découverte de base de données de suivi dans **Notifications**. Le temps nécessaire pour cette action varie selon le nombre de bases de données de machine virtuelle. Lorsque les bases de données sélectionnées ont été détectées, un message de réussite s’affiche.

    ![Message Déploiement réussi](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. La sauvegarde Azure détecte toutes les bases de données SQL Server résidant sur la machine virtuelle. Pendant la découverte, les éléments suivants se produisent en arrière-plan :

    - Sauvegarde Azure inscrit la machine virtuelle auprès du coffre pour la sauvegarde de la charge de travail. Toutes les bases de données sur la machine virtuelle inscrite peuvent être sauvegardées à ce coffre uniquement.
    - Sauvegarde Azure installe l’extension AzureBackupWindowsWorkload sur la machine virtuelle. Aucun agent n’est installé sur une base de données SQL.
    - Sauvegarde Azure crée le compte de service NT Service\AzureWLBackupPluginSvc sur la machine virtuelle.
      - Toutes les opérations de sauvegarde et de restauration utilisent le compte de service.
      - NT Service\AzureWLBackupPluginSvc nécessite des autorisations d’administrateur système SQL. Toutes les machines virtuelles SQL Server créée dans la place de marché sont fournis avec la SqlIaaSExtension installée. L’extension AzureBackupWindowsWorkload utilise la SQLIaaSExtension à se connecter automatiquement les autorisations requises.
    - Si vous n’avez pas créé la machine virtuelle à partir de la place de marché, la machine virtuelle n’auront pas la SqlIaaSExtension installée, et l’opération de découverte échoue avec le message d’erreur UserErrorSQLNoSysAdminMembership. Pour résoudre ce problème, suivez la [instructions](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

        ![Sélectionner la machine virtuelle et la base de données](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurer une sauvegarde  

1. Dans **objectif de sauvegarde** > **étape 2 : Configurer la sauvegarde**, sélectionnez **configurer la sauvegarde**.

   ![Sélectionner Configurer la sauvegarde](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Dans **sélectionner des éléments à sauvegarder**, vous voyez tous les groupes de disponibilité inscrits et les instances de SQL Server autonome. Sélectionnez la flèche vers la gauche d’une ligne pour développer la liste de toutes les bases de données non protégées dans cette instance ou d’un groupe de disponibilité Always On.  

    ![Affichage de toutes les instances SQL Server avec des bases de données autonomes](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Choisissez toutes les bases de données vous souhaitez protéger, puis sélectionnez **OK**.

   ![Protection de la base de données](./media/backup-azure-sql-database/select-database-to-protect.png)

   Pour optimiser les charges de sauvegarde, la sauvegarde Azure définit le nombre maximal de bases de données à 50 dans une tâche de sauvegarde.

     * Pour protéger plus de 50 bases de données, configurez plusieurs sauvegardes.
     * Pour activer [ ](#enable-auto-protection) l’intégralité de l’instance ou le groupe de disponibilité Always On. Dans le **la protection automatique** liste déroulante, sélectionnez **ON**, puis sélectionnez **OK**.

    > [!NOTE]
    > Le [la protection automatique](#enable-auto-protection) fonctionnalité permet non seulement de protection sur toutes les bases de données existantes à la fois, mais protège également automatiquement les nouvelles bases de données ajoutés à cette instance ou le groupe de disponibilité.  

4. Sélectionnez **OK** pour ouvrir **stratégie de sauvegarde**.

    ![Activer la protection automatique pour le groupe de disponibilité Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Dans **stratégie de sauvegarde**, choisissez une stratégie, puis sélectionnez **OK**.

   - Sélectionnez la stratégie par défaut comme HourlyLogBackup.
   - Choisir une stratégie de sauvegarde existante créée précédemment pour SQL.
   - Définir une nouvelle stratégie selon votre RPO et la durée de rétention.

     ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-sql-database/select-backup-policy.png)

6. Dans **sauvegarde**, sélectionnez **activer la sauvegarde**.

    ![Activer la stratégie de sauvegarde choisie](./media/backup-azure-sql-database/enable-backup-button.png)

7. Vous pouvez suivre la progression de la configuration dans la zone  **Notifications**  du portail.

    ![Zone Notifications](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

Une stratégie de sauvegarde définit le moment auquel les sauvegardes sont effectuées ainsi que leur durée de rétention.

- Une stratégie est créée au niveau du coffre.
- Plusieurs coffres peuvent utiliser la même stratégie de sauvegarde, mais vous devez appliquer la stratégie de sauvegarde à chaque coffre.
- Lorsque vous créez une stratégie de sauvegarde, une sauvegarde complète quotidienne est la valeur par défaut.
- Vous pouvez ajouter une sauvegarde différentielle, mais uniquement si vous configurez les sauvegardes complètes pour qu’elles aient lieu toutes les semaines.
- En savoir plus sur [différents types de stratégies de sauvegarde](backup-architecture.md#sql-server-backup-types).

Pour créer une stratégie de sauvegarde :

1. Dans le coffre, sélectionnez **stratégies de sauvegarde** > **ajouter**.
2. Dans **ajouter**, sélectionnez **SQL Server dans Azure VM** pour définir le type de stratégie.

   ![Choisissez un type de stratégie pour la nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/policy-type-details.png)

3. Dans **Nom de la stratégie**, entrez le nom de la nouvelle stratégie.
4. Dans **stratégie de sauvegarde complète**, sélectionnez un **fréquence de sauvegarde**. Choisissez **quotidienne** ou **hebdomadaire**.

   - Si vous sélectionnez **quotidienne**, sélectionnez l’heure et le fuseau horaire de début du travail de sauvegarde.
   - Si vous choisissez la fréquence **hebdomadaire**, sélectionnez le jour de la semaine, l’heure et le fuseau horaire indiquant le début du travail de sauvegarde.
   - Exécutez une sauvegarde complète, étant donné que vous ne pouvez pas désactiver le **sauvegarde complète** option.
   - Sélectionnez **sauvegarde complète** pour afficher la stratégie.
   - Si vous choisissez des sauvegardes complètes quotidiennes, vous ne pouvez pas créer de sauvegardes différentielles.

     ![Champs de la nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Dans **durée de rétention**, toutes les options sont sélectionnées par défaut. Désactivez n’importe quelle plage de rétention limite que vous ne choix, puis définissez les intervalles à utiliser.

    - Période de rétention minimale pour n’importe quel type de sauvegarde (complète, différentielle et journaux) est de sept jours.
    - Des points de récupération sont marqués pour la rétention et varient selon la durée de rétention. Par exemple, si vous sélectionnez une sauvegarde complète quotidienne, seule une sauvegarde complète est déclenchée chaque jour.
    - La sauvegarde pour un jour spécifique est marqué et conservé selon la durée de rétention hebdomadaire et le paramètre de rétention hebdomadaire.
    - Mensuelle et annuelle de plages de rétention se comportent de manière similaire.

       ![Paramètres d’intervalle de la durée de rétention](./media/backup-azure-sql-database/retention-range-interval.png)

6. Dans le menu de **stratégie Sauvegarde complète**, cliquez sur **OK** pour accepter les paramètres.
7. Pour ajouter une stratégie de sauvegarde différentielle, sélectionnez **Sauvegarde différentielle**.

   ![Paramètres d’intervalle des durées de rétention](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Ouvrir le menu de la stratégie de sauvegarde différentielle](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Dans la stratégie **Sauvegarde différentielle**, sélectionnez **Activer** pour ouvrir les contrôles de fréquence et de rétention.

    - Vous pouvez déclencher qu’une seule sauvegarde différentielle par jour.
    - Les sauvegardes différentielles peuvent être conservées jusqu’à 180 jours. Pour une conservation à longue terme, utilisez les sauvegardes complètes.

9. Sélectionnez **OK** pour enregistrer la stratégie et revenir au menu principal **Stratégie de sauvegarde**.

10. Pour ajouter une stratégie de sauvegarde de fichier journal, cliquez sur **Sauvegarde de fichier journal**.
11. Dans **Sauvegarde de fichier journal**, sélectionnez **Activer** et définissez les contrôles de fréquence et de rétention. Sauvegardes du journal peuvent se produire aussi souvent que toutes les 15 minutes et peuvent être conservées pendant 35 jours.
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

Vous pouvez activer la protection automatique sauvegarder automatiquement toutes les bases de données existants et futurs sur une instance de SQL Server autonome ou à un groupe de disponibilité Always On.

- Il n’existe aucune limite sur le nombre de bases de données que vous pouvez sélectionner à la fois pour la protection automatique.
- Vous ne pouvez pas protéger ou exclure des bases de données de la protection dans une instance au moment où que vous activez la protection automatique de manière sélective.
- Si votre instance comprend déjà certaines bases de données protégées, ils restera protégées sous leurs stratégies respectifs même après avoir activé la protection automatique. Toutes les bases de données non protégées ajoutées par la suite aura qu’une seule stratégie que vous définissez au moment de l’activation de protection automatique, répertoriée sous **configurer la sauvegarde**. Toutefois, vous pouvez modifier la stratégie associée à une base de données protégée automatiquement plus tard.  

Pour activer la protection automatique :

  1. Dans **Éléments à sauvegarder**, sélectionnez l’instance pour laquelle vous souhaitez activer la protection automatique.
  2. Sélectionnez la liste déroulante sous **la protection automatique**, choisissez **ON**, puis sélectionnez **OK**.

      ![Activer la protection automatique sur le groupe de disponibilité](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. La sauvegarde est configurée pour toutes les bases de données et peut être suivie dans **Travaux de sauvegarde**.

Si vous devez désactiver la protection automatique, sélectionnez le nom d’instance sous **configurer la sauvegarde**, puis sélectionnez **la protection automatique de désactiver** pour l’instance. Toutes les bases de données continueront à être sauvegardées, mais les futures bases de données ne sont pas protégées automatiquement.

![Désactiver la protection automatique sur cette instance](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :

- [Restaurer les bases de données SQL Server sauvegardées](restore-sql-database-azure-vm.md)
- [Gérer des bases de données SQL Server sauvegardées](manage-monitor-sql-database-backup.md)
