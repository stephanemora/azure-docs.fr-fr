---
title: Sauvegarder une base de données SQL Server dans Azure | Microsoft Docs
description: Ce didacticiel décrit la sauvegarde de SQL Server dans Azure, ainsi que la récupération de SQL Server.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/6/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 32f45b66c4b1d22da3ffc4310a8a47c17319301f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38302821"
---
# <a name="back-up-sql-server-database-in-azure"></a>Sauvegarder une base de données SQL Server dans Azure

Les bases de données SQL Server sont des charges de travail critiques nécessitant un faible objectif de point de récupération (RPO) et une conservation à long terme. La Sauvegarde Azure offre une solution de sauvegarde SQL Server qui ne nécessite aucune infrastructure. Cela signifie donc que vous n’avez pas à gérer de serveur de sauvegarde complexe, ni d’agent de gestion ou de stockage de sauvegarde. Sauvegarde Azure assure la gestion centralisée de vos sauvegardes entre tous les serveurs SQL Server, voire entre différentes charges de travail.

 Dans cet article, vous apprenez :

> [!div class="checklist"]
> * les prérequis pour sauvegarder SQL Server dans Azure ;
> * à créer et à utiliser un coffre Recovery Services ;
> * à configurer des sauvegardes de bases de données SQL Server ;
> * à définir une stratégie de sauvegarde (ou de rétention) des points de récupération ;
> * à restaurer la base de données.

Avant de commencer les procédures décrites dans cet article, vous devez disposer d’un serveur SQL Server s’exécutant dans Azure. [Vous pouvez utiliser des machines virtuelles de la Place de marché SQL pour créer rapidement un serveur SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Limitations de la préversion publique

Les éléments suivants sont les limitations connues de la préversion publique.

- Une machine virtuelle SQL requiert une connectivité Internet pour accéder aux adresses IP publiques Azure. Pour plus d’informations, consultez la section [Établir la connectivité réseau](backup-azure-sql-database.md#establish-network-connectivity).
- Vous pouvez protéger jusqu’à 2 000 bases de données SQL dans un coffre Recovery Services. Vous devez stocker les bases de données SQL supplémentaires dans un autre coffre Recovery Services.
- [La sauvegarde des groupes de disponibilité distribués présente des limitations](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017).
- Les instances de cluster de basculement SQL ne sont pas prises en charge.
- Utilisez le portail Azure pour configurer Sauvegarde Azure afin de protéger des bases de données SQL Server. Pour le moment, la prise en charge d’Azure PowerShell, d’Azure CLI et des API REST n’est pas disponible.

## <a name="supported-azure-geos"></a>Zones géographiques Azure prises en charge

- Sud-Est de l’Australie (ASE) 
- Sud du Brésil (BRS)
- Centre du Canada (CNC)
- Est du Canada (CE)
- Centre des États-Unis (CUS)
- Est de l’Asie (EA)
- Est d’Australie (AE) 
- Est des États-Unis (EUS)
- Est des États-Unis 2 (EUS2)
- Est du Japon (JPE)
- Ouest du Japon (JPW)
- Centre de l’Inde (INC) 
- Sud de l’Inde (INS)
- Corée Centre (KRC)
- Corée du Sud (KRS)
- Centre-Nord des États-Unis (NCUS) 
- Europe du Nord (NE) 
- Centre-Sud des États-Unis (SCUS) 
- Asie du Sud-Est (SEA)
- Sud du Royaume-Uni (UKS) 
- Ouest du Royaume-Uni (UKW) 
- Europe de l’Ouest (WE) 
- Ouest des États-Unis (WUS)
- Centre-Ouest des États-Unis (WCUS)
- Ouest des États-Unis 2 (WUS 2) 

## <a name="supported-operating-systems-and-versions-of-sql-server"></a>Systèmes d’exploitation et versions de SQL server pris en charge

Les systèmes d’exploitation suivants sont pris en charge. Les machines virtuelles Azure de la Place de marché SQL et les machines virtuelles autres que celles de la Place de marché (sur lesquelles SQL Server est installé manuellement) sont prises en charge.

### <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Pour le moment, Linux n’est pas pris en charge.

### <a name="supported-versionseditions-of-sql-server"></a>Éditions/Versions de SQL Server prises en charge

- SQL 2012 Enterprise, Standard, Web, Developer, Express
- SQL 2014 Enterprise, Standard, Web, Developer, Express
- SQL 2016 Enterprise, Standard, Web, Developer, Express
- SQL 2017 Enterprise, Standard, Web, Developer, Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Prérequis pour utiliser Sauvegarde Azure afin de protéger SQL Server 

Pour pouvoir sauvegarder votre base de données SQL Server, vérifiez les conditions suivantes :

- Identifiez ou [créez un coffre Recovery Services](backup-azure-sql-database.md#create-a-recovery-services-vault) dans la même région ou avec les mêmes paramètres régionaux que la machine virtuelle qui héberge SQL Server.
- [Vérifiez les autorisations existant sur la machine virtuelle](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms), nécessaires à la sauvegarde des bases de données SQL.
- [La machine virtuelle SQL dispose d’une connectivité réseau](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> Vous ne pouvez disposer que d’une solution de sauvegarde à la fois pour sauvegarder les bases de données SQL Server. Désactivez toute autre solution de sauvegarde SQL avant d’utiliser cette fonctionnalité, faute de quoi les sauvegardes interféreront et échoueront. Vous pouvez activer Sauvegarde Azure pour la machine virtuelle IaaS ainsi que la sauvegarde SQL sans aucun conflit. 
>

Si ces conditions existent dans votre environnement, passez à la section intitulée [Configurer votre coffre pour protéger une base de données SQL](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database). Si l’un des prérequis n’existe pas, poursuivez la lecture de cette section.


## <a name="establish-network-connectivity"></a>Établir la connectivité réseau

Pour toutes les opérations, la machine virtuelle SQL requiert une connectivité pour accéder aux adresses IP publiques Azure. Les opérations des machines virtuelles SQL (par exemple, la détection de bases de données, la configuration de sauvegardes, les sauvegardes planifiées, la restauration des points de récupération, etc.) échouent sans connectivité aux adresses IP publiques. Pour fournir un chemin d’accès clair pour le trafic de sauvegarde, utilisez l’une des options suivantes :

- Placez les plages d’adresses IP des centres de données Azure dans une liste verte : pour ce faire, consultez le [site web Azure](https://www.microsoft.com/download/details.aspx?id=41653). 
- Déployez un serveur proxy HTTP pour le routage du trafic : lorsque vous sauvegardez une base de données SQL sur une machine virtuelle, l’extension de sauvegarde sur la machine virtuelle utilise des API HTTPS pour envoyer des commandes de gestion à Sauvegarde Azure et des données à Stockage Azure. L’extension de sauvegarde utilise également Azure Active Directory (AAD) pour l’authentification. Acheminez le trafic de l’extension de sauvegarde pour ces trois services via le proxy HTTP, car il s’agit du seul composant configuré pour l’accès Internet public.

Les compromis entre les choix sont les suivants : facilité de gestion, contrôle granulaire et coût.

> [!NOTE]
>Les balises de service pour Sauvegarde Azure doivent être disponibles en disponibilité générale.
>

| Option | Avantages | Inconvénients |
| ------ | ---------- | ------------- |
| Plages IP de liste blanche | Aucun coût supplémentaire <br/> Pour l’ouverture d’accès à un groupe de sécurité réseau, utilisez la cmdlet **Set-AzureNetworkSecurityRule**. | Difficile à gérer, car les plages d’adresses IP concernées changent au fil du temps. <br/>Fournit un accès à l’ensemble d’Azure et pas seulement au stockage.|
| Utiliser un proxy HTTP   | Le contrôle granulaire dans le proxy sur les URL de stockage est autorisé. <br/>Un seul point d’accès Internet aux machines virtuelles. <br/> Non soumis aux modifications d’adresse IP Azure. | Frais supplémentaires d’exécution de machine virtuelle avec le logiciel de serveur proxy. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Définir des autorisations pour les machines virtuelles SQL autres que de la Place de marché

Pour sauvegarder une machine virtuelle, Sauvegarde Azure nécessite l’installation de l’extension **AzureBackupWindowsWorkload**. Si vous utilisez des machines virtuelles de la Place de marché Azure, passez directement à [Détecter les bases de données SQL server](backup-azure-sql-database.md#discover-sql-server-databases). Si la machine virtuelle hébergeant vos bases de données SQL n’a pas été créée à partir de la Place de marché Azure, complétez la section suivante pour installer l’extension et définir les autorisations appropriées. Outre l’extension **AzureBackupWindowsWorkload**, Sauvegarde Azure nécessite des privilèges d’administrateur système SQL pour protéger les bases de données SQL. Lors de la détection des bases de données sur la machine virtuelle, Sauvegarde Azure crée un compte NT Service\AzureWLBackupPluginSvc. Pour que la Sauvegarde Azure découvre les bases de données SQL, le compte NT Service\AzureWLBackupPluginSvc doit disposer des autorisations SQL et des autorisations d’administrateur système SQL. La procédure suivante explique comment fournir ces autorisations.

Pour configurer des autorisations :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le coffre Recovery Services que vous utilisez pour protéger les bases de données SQL.
2. Dans le menu du tableau de bord du coffre, cliquez sur **+Sauvegarder** pour ouvrir le menu **Objectif de sauvegarde**.

   ![Cliquer sur +Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/open-backup-menu.png)

3. Dans le menu **Objectif de sauvegarde**, dans le menu **Où s’exécute votre charge de travail ?**, laissez la valeur **Azure** par défaut.

4. Dans le menu **What do you want to backup** (Que voulez-vous sauvegarder ?), développez le menu déroulant et sélectionnez **SQL Server in Azure VM** (SQL Server dans une machine virtuelle Azure).

    ![Cliquer sur +Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Le menu **Objectif de sauvegarde** affiche deux étapes : **Discover DBs in VMs** (Détecter les bases de données dans les machines virtuelles) et **Configurer la sauvegarde**. **Discover DBs in VMs (Détecter les bases de données dans les machines virtuelles)** lance une recherche pour les machines virtuelles Azure.

    ![Affiche les nouvelles étapes du menu Objectif de sauvegarde](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Cliquez sur **Démarrer la détection** pour rechercher des machines virtuelles non protégées dans l’abonnement. Selon le nombre de machines virtuelles non protégées dans l’abonnement, la navigation dans l’ensemble des machines virtuelles peut prendre un certain temps.

    ![Sauvegarde en attente](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Une machine virtuelle non protégée s’affiche dans la liste dès qu’elle est détectée. Les machines virtuelles non protégées sont répertoriées par leur nom de machine virtuelle et leur groupe de ressources. Plusieurs machines virtuelles peuvent avoir le même nom. Toutefois, les machines virtuelles portant le même nom appartiennent à différents groupes de ressources. Si une machine virtuelle attendue n’apparaît pas dans la liste, vérifiez si elle est déjà protégée dans un coffre.

6. Dans la liste des machines virtuelles, sélectionnez la machine virtuelle qui contient la base de données SQL que vous souhaitez sauvegarder, puis cliquez sur **Découvrir les bases de données**. 

    Le processus de détection installe l’extension **AzureBackupWindowsWorkload** sur la machine virtuelle. L’extension permet au service Sauvegarde Azure de communiquer avec la machine virtuelle afin qu’il puisse sauvegarder les bases de données SQL. Une fois l’extension installée, Sauvegarde Azure crée le compte de service virtuel Windows, **NT Service\AzureWLBackupPluginSvc**, sur la machine virtuelle. Le compte de service virtuel requiert une autorisation d’administrateur système SQL. Pendant le processus d’installation de compte de service virtuel, si vous rencontrez l’erreur **UserErrorSQLNoSysadminMembership**, consultez la section [Correction des autorisations d’administrateur système SQL](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions).

    La zone Notifications affiche la progression de la détection de la base de données. En fonction du nombre de bases de données résidant sur la machine virtuelle, l’exécution du travail peut prendre un certain temps. Lorsque les bases de données sélectionnées ont été détectés, un message de réussite s’affiche.

    ![message de notification de réussite du déploiement](./media/backup-azure-sql-database/notifications-db-discovered.png)

Dès que vous avez associé la base de données au coffre Recovery Services, l’étape suivante consiste à [configurer la sauvegarde](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="fixing-sql-sysadmin-permissions"></a>Correction des autorisations d’administrateur système SQL

Pendant l’installation, si vous rencontrez l’erreur **UserErrorSQLNoSysadminMembership**, utilisez un compte disposant d’autorisations d’administrateur système SQL pour vous connecter à SQL Server Management Studio (SSMS). À moins que vous ayez besoin d’autorisations spéciales, l’authentification Windows doit fonctionner.

1. Sur le serveur SQL Server, ouvrez le dossier **Security/Logins**.

    ![Ouvrir les dossiers SQL Server, de sécurité et de connexion pour afficher les comptes](./media/backup-azure-sql-database/security-login-list.png)

2. Dans le dossier Connexions, cliquez avec le bouton droit et sélectionnez **Nouvelle connexion**, puis, dans la boîte de dialogue Connexion - Nouveau, cliquez sur **Rechercher**.

    ![Ouvrir Rechercher dans la boîte de dialogue Connexion - Nouveau](./media/backup-azure-sql-database/new-login-search.png)

3. Puisque le compte de service virtuel Windows **NT Service\AzureWLBackupPluginSvc** a déjà été créé lors de l’inscription de la machine virtuelle et de la phase de détection SQL, entrez le nom du compte tel qu’il apparaît dans la boîte de dialogue  **Entrez le nom de l’objet à sélectionner**. Cliquez sur **Vérifier les noms** pour résoudre le nom. 

    ![Cliquer sur le bouton Vérifier les noms pour résoudre le nom de service inconnu](./media/backup-azure-sql-database/check-name.png)

4. Cliquez sur **OK** pour fermer la boîte de dialogue Sélectionner un utilisateur ou un groupe.

5. Dans la boîte de dialogue **Rôles serveur**, assurez-vous que le rôle **administrateur système** est sélectionné, puis cliquez sur **OK** pour fermer la boîte de dialogue **Connexion - Nouveau**.

    ![S’assurer que le rôle serveur administrateur système est sélectionné](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Les autorisations requises doivent désormais exister.

6. Même si vous avez corrigé l’erreur liée aux autorisations, vous devez toujours associer la base de données au coffre Recovery Services. Dans la liste **Serveurs protégés** du portail Azure, cliquez avec le bouton droit sur le serveur en erreur, puis sélectionnez **Redécouvrir les bases de données**.

    ![Vérifier que le serveur dispose des autorisations appropriées](./media/backup-azure-sql-database/check-erroneous-server.png)

    La zone Notifications affiche la progression de la détection de la base de données. En fonction du nombre de bases de données résidant sur la machine virtuelle, l’exécution du travail peut prendre un certain temps. Lorsque les bases de données sélectionnées ont été trouvées, un message de réussite s’affiche dans la zone Notifications.

    ![message de notification de réussite du déploiement](./media/backup-azure-sql-database/notifications-db-discovered.png)

Dès que vous avez associé la base de données au coffre Recovery Services, l’étape suivante consiste à [configurer la sauvegarde](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Détecter les bases de données SQL Server

Comme Sauvegarde Azure peut détecter toutes les bases de données d’une instance SQL Server, vous pouvez les protéger conformément à vos besoins de sauvegarde. Pour identifier la machine virtuelle qui héberge les bases de données SQL, procédez comme suit. Une fois que vous avez identifié la machine virtuelle, Sauvegarde Azure installe une extension légère pour détecter les bases de données SQL Server.

1. Connectez-vous à votre abonnement sur le [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, sélectionnez **Tous les services**.

    ![Choisir l’option Tous les services dans le menu principal](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. Dans la boîte de dialogue Tous les services, tapez *Recovery Services*. Au fur et à mesure des caractères saisis, la liste des ressources est filtrée. Cliquez sur l’option **Coffres Recovery Services** dès qu’elle apparaît.

    ![Dans la boîte de dialogue Tous les services, taper Recovery Services](./media/backup-azure-sql-database/all-services.png) <br/>

    La liste des coffres Recovery Services de l’abonnement s’affiche. 

4. Dans la liste des coffres Recovery Services, sélectionnez le coffre que vous souhaitez utiliser pour protéger vos bases de données SQL.

5. Dans le menu du tableau de bord du coffre, cliquez sur **+Sauvegarder** pour ouvrir le menu **Objectif de sauvegarde**.

   ![Cliquer sur +Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/open-backup-menu.png)

6. Dans le menu **Objectif de sauvegarde**, dans le menu **Où s’exécute votre charge de travail ?**, laissez la valeur **Azure** par défaut.

7. Dans le menu **What do you want to backup** (Que voulez-vous sauvegarder ?), développez le menu déroulant et sélectionnez **SQL Server in Azure VM** (SQL Server dans une machine virtuelle Azure).

    ![Cliquer sur +Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Une fois sélectionné, le menu **Objectif de sauvegarde** affiche deux étapes : Discover DBs in VMs (Détecter les bases de données dans les machines virtuelles) et Configurer la sauvegarde. 

    ![Affiche les nouvelles étapes du menu Objectif de sauvegarde](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Cliquez sur **Démarrer la détection** pour rechercher des machines virtuelles non protégées dans l’abonnement. Selon le nombre de machines virtuelles non protégées dans l’abonnement, la navigation dans l’ensemble des machines virtuelles peut prendre un certain temps.

    ![Sauvegarde en attente](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Une machine virtuelle non protégée s’affiche dans la liste dès qu’elle est détectée. Plusieurs machines virtuelles peuvent avoir le même nom. Toutefois, plusieurs machines virtuelles portant le même nom appartiennent à différents groupes de ressources. Les machines virtuelles non protégées sont répertoriées par leur nom de machine virtuelle et leur groupe de ressources. Si une machine virtuelle attendue n’est pas répertoriée, vérifiez si elle est déjà protégée dans un coffre.

9. Dans la liste des machines virtuelles, cochez la case de la machine virtuelle qui contient les bases de données SQL que vous souhaitez protéger, puis cliquez sur **Découvrir les bases de données**.

    Sauvegarde Azure détecte toutes les bases de données SQL résidant sur la machine virtuelle. Pour plus d’informations sur ce qui se produit pendant la phase de détection des bases de données, consultez la section suivante, [Opérations principales lors de la détection des bases de données SQL](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases). Vous êtes prêt à [configurer le travail de sauvegarde](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database) une fois que vous avez détecté les bases de données SQL.

### <a name="backend-operations-when-discovering-sql-databases"></a>Opérations principales lors de la détection des bases de données SQL

Lorsque vous utilisez l’outil **Découvrir les bases de données**, le service Sauvegarde Azure exécute les opérations suivantes en arrière-plan :

- Il inscrit la machine virtuelle auprès du coffre Recovery Services pour la sauvegarde des charges de travail. Toutes les bases de données résidant sur la machine virtuelle inscrite peuvent uniquement être sauvegardées dans ce coffre Recovery Services. 

- Il installe l’extension **AzureBackupWindowsWorkload** sur la machine virtuelle. La sauvegarde d’une base de données SQL est une solution sans agent ; autrement dit, avec l’extension installée sur la machine virtuelle, aucun agent n’est installé dans la base de données SQL.

- Il crée le compte de service, **NT Service\AzureWLBackupPluginSvc**, sur la machine virtuelle. Toutes les opérations de sauvegarde et de restauration utilisent le compte de service. **NT Service\AzureWLBackupPluginSvc** nécessite des autorisations d’administrateur système SQL. Toutes les machines virtuelles de la Place de marché SQL sont fournies avec l’extension SQLIaaSExtension installée, et AzureBackupWindowsWorkload l’utilise pour obtenir automatiquement les autorisations requises. Si l’extension SQLIaaSExtension n’est pas installée sur votre machine virtuelle, la détection des bases de données échoue et le message d’erreur **UserErrorSQLNoSysAdminMembership** s’affiche. Pour ajouter l’autorisation d’administrateur système pour la sauvegarde, suivez les instructions indiquées dans [Setting up Azure Backup permissions for non-marketplace SQL VMs](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms) (Configuration des autorisations Sauvegarde Azure pour les machines virtuelles SQL autres que de la Place de marché).

    ![sélectionner la machine virtuelle et la base de données](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>Configurer la sauvegarde des bases de données SQL Server

Sauvegarde Azure fournit des services de gestion afin de protéger vos bases de données SQL Server et de gérer les travaux de sauvegarde. Les fonctionnalités de gestion et de surveillance dépendent de votre coffre Recovery Services. 

> [!NOTE]
> Vous ne pouvez disposer que d’une solution de sauvegarde à la fois pour sauvegarder les bases de données SQL Server. Désactivez toute autre solution de sauvegarde SQL avant d’utiliser cette fonctionnalité, faute de quoi les sauvegardes interféreront et échoueront. Vous pouvez activer Sauvegarde Azure pour la machine virtuelle IaaS ainsi que la sauvegarde SQL sans aucun conflit. 
>

Pour configurer la protection de votre base de données SQL :

1. Ouvrez le coffre Recovery Services inscrit auprès de la machine virtuelle SQL.

2. Dans le menu du tableau de bord du coffre, cliquez sur **+Sauvegarder** pour ouvrir le menu **Objectif de sauvegarde**.

    ![Cliquer sur +Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/open-backup-menu.png)

3. Dans le menu **Objectif de sauvegarde**, dans le menu **Où s’exécute votre charge de travail ?**, laissez la valeur **Azure** par défaut.

4. Dans le menu **What do you want to backup** (Que voulez-vous sauvegarder ?), développez le menu déroulant et sélectionnez **SQL Server in Azure VM** (SQL Server dans une machine virtuelle Azure).

    ![Cliquer sur +Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Une fois sélectionné, le menu **Objectif de sauvegarde** affiche deux étapes : Discover DBs in VMs (Détecter les bases de données dans les machines virtuelles) et Configurer la sauvegarde. Si vous avez parcouru cet article dans l’ordre, vous avez déjà détecté les machines virtuelles non protégées, et ce coffre est inscrit auprès d’une machine virtuelle. Vous êtes maintenant prêt à configurer la protection des bases de données SQL.

5. Dans le menu Objectif de sauvegarde, cliquez sur **Configurer la sauvegarde**.

    ![Affiche les nouvelles étapes du menu Objectif de sauvegarde](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Le service Sauvegarde Azure affiche toutes les instances SQL avec des bases de données autonomes, ainsi que les groupes de disponibilité SQL AlwaysOn. Pour afficher les bases de données autonomes dans l’instance SQL, cliquez sur le chevron situé en regard du nom de l’instance. Les images suivantes illustrent des exemples d’une instance autonome et d’un groupe de disponibilité Always On.

    > [!NOTE]
    > En présence d’un groupe de disponibilité AlwaysOn SQL, la sauvegarde SQL est prioritaire. Toutefois, en raison de limitations de la plateforme SQL, les sauvegardes complètes et différentielles doivent être effectuées sur le nœud principal. La sauvegarde de fichier journal peut se produire en fonction de votre préférence de sauvegarde. En raison de ces limitations, le nœud principal doit toujours être inscrit pour les groupes de disponibilité.
    >

    ![Liste des bases de données de l’instance SQL](./media/backup-azure-sql-database/discovered-databases.png)

    Cliquez sur le chevron situé en regard des groupes de disponibilité AlwaysOn pour afficher la liste des bases de données.

    ![Liste des bases de données dans le groupe de disponibilité AlwaysOn](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. Dans la liste des bases de données, sélectionnez toutes celles que vous souhaitez protéger, puis cliquez sur **OK**.

    ![sélectionner plusieurs bases de données pour les protéger](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Vous pouvez sélectionner jusqu’à 50 bases de données en même temps. Si vous souhaitez en protéger davantage, effectuez plusieurs passages. Après que vous avez protégé les 50 premières bases de données, répétez cette étape pour protéger l’ensemble de bases de données suivant.
    > [!Note] 
    > Pour optimiser les charges des sauvegardes, Sauvegarde Azure fractionne les travaux de sauvegarde volumineux en plusieurs lots. Le nombre maximal de bases de données contenues dans un travail de sauvegarde est égal à 50.
    >
    >

7. Pour créer ou choisir une stratégie de sauvegarde, dans le menu **Sauvegarde**, sélectionnez **Stratégie de sauvegarde** pour ouvrir le menu.

    ![sélectionner l’option stratégie de sauvegarde](./media/backup-azure-sql-database/select-backup-policy.png)

8. Dans le menu déroulant **Choisir une stratégie de sauvegarde**, choisissez une stratégie de sauvegarde, puis cliquez sur **OK**. Pour plus d’informations sur la création de votre propre stratégie de sauvegarde, consultez la section [Créer une stratégie de sauvegarde](backup-azure-sql-database.md#define-a-backup-policy).

    ![choisir une stratégie de sauvegarde dans le menu déroulant](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    Dans le menu déroulant **Choisir une stratégie de sauvegarde** du menu Stratégie de sauvegarde, vous pouvez choisir : 
    - la stratégie HourlyLogBackup par défaut ; 
    - une stratégie de sauvegarde existante créée précédemment pour SQL ;
    - de [définir une nouvelle stratégie](backup-azure-sql-database.md#define-a-backup-policy) selon votre objectif de point de récupération (RPO) et la durée de rétention. 

    > [!Note]
    > Sauvegarde Azure prend en charge la conservation à long terme basé sur le modèle de sauvegarde grand-père-père-fils afin d’optimiser la consommation du stockage principal tout en respectant les exigences de conformité.
    >

9. Une fois que vous avez choisi une stratégie de sauvegarde, dans le menu **Sauvegarde** cliquez sur **Activer la sauvegarde**.

    ![activer la stratégie de sauvegarde choisie](./media/backup-azure-sql-database/enable-backup-button.png)

    Vous pouvez suivre la progression de la configuration dans la zone Notifications du portail.

    ![afficher la zone notifications](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>Créer une stratégie de sauvegarde

Une stratégie de sauvegarde définit une matrice indiquant le moment auquel les sauvegardes sont effectuées ainsi que la durée de leur rétention. Vous pouvez utiliser Sauvegarde Azure pour planifier trois types de sauvegarde pour les bases de données SQL :

* Sauvegarde complète : une sauvegarde complète de base de données sauvegarde l’intégralité de la base de données. Une sauvegarde complète contient toutes les données d’une base de données, ou d’un ensemble de groupes de fichiers ou de fichiers, ainsi qu’un journal suffisant pour récupérer ces données. Vous pouvez déclencher au plus une sauvegarde complète par jour. Vous pouvez choisir d’effectuer une sauvegarde complète tous les jours ou toutes les semaines. 
* Sauvegarde différentielle : une sauvegarde différentielle est basée sur la sauvegarde de données complète précédente la plus récente. Une sauvegarde différentielle capture uniquement les données qui ont changé depuis la sauvegarde complète. Vous pouvez déclencher au plus une sauvegarde différentielle par jour. Vous ne pouvez pas configurer une sauvegarde complète et une sauvegarde différentielle le même jour.
* Sauvegarde du journal des transactions : une sauvegarde de fichier journal permet d’effectuer une restauration ponctuelle à la seconde donnée. Au plus, vous pouvez configurer des sauvegardes du journal des transactions toutes les 15 minutes.

Les stratégies sont créées au niveau du coffre Recovery Services. Si vous avez plusieurs coffres, ils peuvent utiliser la même stratégie de sauvegarde, mais vous devez appliquer la stratégie de sauvegarde à chaque coffre. Lorsque vous créez une stratégie de sauvegarde, la sauvegarde complète quotidienne est la valeur par défaut. Vous pouvez ajouter une sauvegarde différentielle, mais uniquement si vous activez les sauvegardes complètes pour qu’elles aient lieu toutes les semaines. La procédure suivante décrit la création d’une stratégie de sauvegarde pour un serveur SQL Server dans une machine virtuelle Azure.

Pour créer une stratégie de sauvegarde

1. Dans le menu Stratégie de sauvegarde, dans le menu déroulant **Choisir une stratégie de sauvegarde**, sélectionnez **Créer un nouveau**.

   ![créer une stratégie de sauvegarde](./media/backup-azure-sql-database/create-new-backup-policy.png)

    Le menu Stratégie de sauvegarde est modifié et affiche les champs nécessaires pour toute nouvelle stratégie de sauvegarde de serveur SQL Server.

   ![champs de la nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/blank-new-policy.png)

2. Dans le champ **Nom de la stratégie**, indiquez un nom. 

3. Une sauvegarde complète est obligatoire. Vous pouvez accepter les valeurs par défaut de la sauvegarde complète ou cliquer sur **Sauvegarde complète** pour modifier la stratégie.

    ![champs de la nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/full-backup-policy.png)

    Dans la stratégie de sauvegarde complète, choisissez la fréquence quotidienne ou hebdomadaire. Si vous choisissez la fréquence quotidienne, sélectionnez l’heure et le fuseau horaire indiquant le début du travail de sauvegarde. Si vous choisissez des sauvegardes complètes quotidiennes, vous ne pouvez pas créer de sauvegardes différentielles.

   ![paramètre d’intervalle quotidien](./media/backup-azure-sql-database/daily-interval.png)

    Si vous choisissez la fréquence hebdomadaire, sélectionnez le jour de la semaine, l’heure et le fuseau horaire indiquant le début du travail de sauvegarde.

   ![paramètre d’intervalle hebdomadaire](./media/backup-azure-sql-database/weekly-interval.png)

4. Par défaut, toutes les options figurant dans la section Durée de rétention (quotidienne, hebdomadaire, mensuelle et annuelle) sont sélectionnées. Décochez la limite de la durée de rétention que vous ne souhaitez pas appliquer, puis définissez les intervalles à utiliser. Dans le menu de stratégie Sauvegarde complète, cliquez sur **OK** pour accepter les paramètres.

   ![paramètre d’intervalle de la durée de rétention](./media/backup-azure-sql-database/retention-range-interval.png)

    Des points de récupération sont marqués pour la rétention et varient selon la durée de rétention. Par exemple, si vous sélectionnez une sauvegarde complète quotidienne, seule une sauvegarde complète est déclenchée chaque jour. La sauvegarde d’un jour spécifique est marquée et conservée conformément à la durée de rétention hebdomadaire. Les durées de rétention mensuelle et annuelle ont le même comportement.

5. Pour ajouter une stratégie de sauvegarde différentielle, cliquez sur **Sauvegarde différentielle** pour ouvrir le menu. 

   ![ouvrir une stratégie différentielle](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    Dans le menu de stratégie Sauvegarde différentielle, sélectionnez **Activer** pour ouvrir les contrôles de fréquence et de rétention. Vous pouvez déclencher au plus une sauvegarde différentielle par jour.
    > [!Important] 
    > Les sauvegardes différentielles peuvent être conservées jusqu’à 180 jours. Si vous avez besoin d’une durée de rétention supérieure, vous devez utiliser des sauvegardes complètes et ne pouvez pas utiliser les sauvegardes différentielles.
    >

   ![modifier une stratégie différentielle](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Cliquez sur **OK** pour enregistrer la stratégie et revenir au menu principal Stratégie de sauvegarde.

6. Pour ajouter une stratégie de sauvegarde du journal des transactions, cliquez sur **Sauvegarde de fichier journal** pour ouvrir le menu correspondant. Dans le menu Sauvegarde de fichier journal, sélectionnez **Activer** et définissez les contrôles de fréquence et de rétention. Les sauvegardes de fichiers journaux peuvent se produire toutes les 15 minutes et être conservées jusqu’à 35 jours. Cliquez sur **OK** pour enregistrer la stratégie et revenir au menu principal Stratégie de sauvegarde.

   ![modifier la stratégie de sauvegarde de fichier journal](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. Choisissez s’il convient d’activer l’option Compression de la sauvegarde SQL, qui est désactivée par défaut.

    Sur le serveur principal, Sauvegarde Azure utilise la compression de sauvegarde native SQL.

8. Lorsque vous avez apporté toutes les modifications à la stratégie de sauvegarde, cliquez sur **OK**. 

   ![accepter la nouvelle stratégie](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Restaurer une base de données SQL

Sauvegarde Azure fournit les fonctionnalités permettant de restaurer des bases de données particulières à une date ou à une heure spécifiques, jusqu’à la seconde près, à l’aide des sauvegardes du journal des transactions. En fonction des heures de restauration que vous indiquez, Sauvegarde Azure détermine automatiquement la sauvegarde complète, la sauvegarde différentielle et la chaîne des sauvegardes de fichiers journaux requis pour restaurer vos données.

Vous pouvez également sélectionner une sauvegarde complète ou différentielle spécifique à restaurer à un point de récupération spécifique plutôt qu’à un moment donné.
 > [!Note]
 > Avant de déclencher la restauration de la base de données « Master », démarrez SQL Server en mode mono-utilisateur, avec l’option de démarrage « -m AzureWorkloadBackup ». L’argument -m correspond au nom du client. Seul ce client est autorisé à établir la connexion. Pour toutes les bases de données système (model, master, msdb), arrêtez le service SQL Agent avant de déclencher la restauration. Fermez toutes les applications qui tentent de voler une connexion à l’une de ces bases de données.
>

Pour restaurer une base de données

1. Ouvrez le coffre Recovery Services inscrit auprès de la machine virtuelle SQL.

2. Dans le tableau de bord du coffre, sélectionnez Éléments de sauvegarde sous **Utilisation** pour ouvrir le menu Éléments de sauvegarde.

    ![Cliquer sur +Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Dans le menu **Éléments de sauvegarde**, sélectionnez le type de gestion des sauvegardes, **SQL in Azure VM** (SQL dans une machine virtuelle Azure). 

    ![Cliquer sur +Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    La liste des éléments de sauvegarde s’ajuste pour afficher la liste des bases de données SQL. 

4. Dans la liste des bases de données SQL, sélectionnez la base de données que vous souhaitez restaurer.

    ![sélectionner SQL in Azure VM (SQL dans une machine virtuelle Azure) dans la liste](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Lorsque vous sélectionnez la base de données, le menu correspondant s’ouvre. Ce menu contient les informations de sauvegarde de la base de données, notamment :

    * le point de restauration le plus ancien et le point de restauration le plus récent ;
    * l’état de la sauvegarde de fichier journal des dernières 24 heures (pour les bases de données en mode de récupération Complet et utilisant les journaux de transactions, si la configuration est définie pour les sauvegardes du journal des transactions).

5. Dans le menu de la base de données sélectionnée, cliquez sur **Restaurer la base de données** pour ouvrir le menu Restauration.

    ![sélectionner restaurer la base de données](./media/backup-azure-sql-database/restore-db-button.png)

    Le menu **Restauration** s’ouvre tout comme le menu **Configuration de la restauration**. Le menu **Configuration de la restauration** est la première étape de la configuration de la restauration. Dans ce menu, vous devez indiquer où vous voulez restaurer les données. Les options sont :
    * Autre emplacement : utilisez cette option si vous souhaitez restaurer la base de données à un autre emplacement tout en conservant la base de données source d’origine.
    * Remplacer la base de données : restaure les données dans la même instance SQL Server que la source d’origine. Ce faisant, vous remplacez la base de données d’origine.

    > [!Important]
    > Si la base de données sélectionnée fait partie d’un groupe de disponibilité Always On, SQL n’autorise pas le remplacement de la base de données. Dans ce cas, seule l’option **Autre emplacement** est activée.
    >

    ![cliquer sur Configurer pour ouvrir le menu Configuration de la restauration](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Restaurer à un autre emplacement

Cette procédure décrit la restauration des données à un autre emplacement. Si vous souhaitez remplacer la base de données lors de la restauration, passez à la section [Restaurer et remplacer la base de données](backup-azure-sql-database.md#restore-and-overwrite-the-database). Pour cette procédure, vous devez avoir ouvert votre coffre Recovery Services et le menu Recovery Services doit être actif. Si vous ne l’êtes pas, commencez par la section [Restaurer une base de données SQL](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Vous pouvez restaurer la base de données vers un serveur SQL Server appartenant à la même région Azure. En outre, le serveur de destination doit être inscrit auprès du coffre Recovery Services. 
>

Le menu déroulant **Serveur** affiche uniquement les serveurs SQL inscrits auprès du coffre Recovery Services. Si le serveur souhaité ne figure pas dans la liste **Serveur**, consultez la section [Détecter les bases de données SQL Server](backup-azure-sql-database.md#discover-sql-server-databases) pour rechercher le serveur. Pendant le processus de détection des bases de données, tous les nouveaux serveurs sont inscrits auprès du coffre Recovery Services.

1. Dans le menu **Configuration de la restauration** :

    * Sélectionnez **Autre emplacement**.
    * Pour **Serveur**, choisissez le serveur SQL Server où vous souhaitez restaurer la base de données.
    * Dans le menu déroulant **Instance**, choisissez une instance SQL.
    * Dans la boîte de dialogue **Nom de la base de données restaurée**, indiquez le nom de la base de données cible.
    * Le cas échéant, sélectionnez **Overwrite if the DB with the same name already exists on selected SQL instance** (Remplacer si une base de données du même nom existe déjà dans l’instance SQL sélectionnée).
    * Cliquez sur **OK** pour terminer la configuration de la destination, puis choisissez un point de restauration.

    ![sélectionner un autre emplacement, une instance et un nom dans le menu Configuration de la restauration](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Dans le menu **Sélectionner un point de restauration**, vous pouvez choisir un point de restauration Logs (Point in Time) (Journaux [point dans le temps]) ou Full & Differential (Complète et différentielle). Si vous souhaitez procéder à une restauration dans un journal ponctuel spécifique, poursuivez cette étape. Si vous souhaitez restaurer un point de récupération complète ou différentielle, passez directement à l’étape 3.

    ![menu sélectionner un point de restauration](./media/backup-azure-sql-database/recovery-point-menu.png)

    La limite de restauration dans le temps est disponible uniquement pour les sauvegardes de journaux des bases de données dont le mode de récupération est Complet et utilisant les journaux de transactions. Pour procéder à une restauration à un moment spécifique :

    1. Sélectionnez l’option de restauration **Logs (Point in Time)** (Journaux [point dans le temps]).

        ![sélectionner un type de point de restauration](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Sous **Date/heure de la restauration**, cliquez sur l’icône de calendrier pour ouvrir celui-ci. Les dates indiquées en gras contiennent des points de récupération, et la date du jour est mise en surbrillance. Sélectionnez une date dans le calendrier avec des points de récupération. Vous ne pouvez pas sélectionner de dates sans point de récupération.

        ![ouvrir le calendrier](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Une fois que vous avez sélectionné une date, le graphique chronologique affiche les points de récupération disponibles dans une plage continue.

    3. À l’aide du graphique chronologique ou de la boîte de dialogue Heure, spécifiez une heure spécifique pour le point de récupération, puis cliquez sur **OK** pour terminer l’étape Point de restauration.
    
       ![ouvrir le calendrier](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Le menu **Sélectionner un point de restauration** se ferme et le menu **Configuration avancée** s’ouvre.

       ![menu configuration avancée](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Dans le menu **Configuration avancée** :

        * Pour maintenir la base de données non opérationnelle après la restauration, dans le menu **Restore with NORECOVERY** (Restaurer avec NORECOVERY), sélectionnez **Activé**.
        * Si vous souhaitez modifier l’emplacement de restauration sur le serveur de destination, indiquez un nouveau chemin d’accès dans la colonne **Cible**.
        * cliquez sur **OK** pour approuver les paramètres, puis fermez **Configuration avancée**.

    5. Dans le menu **Restaurer**, cliquez sur **Restaurer** pour démarrer le travail de restauration. Dans la zone Notifications, vous pouvez suivre la progression. Vous pouvez également suivre la progression des travaux de restauration de base de données.

       ![menu configuration avancée](./media/backup-azure-sql-database/restore-job-notification.png)

3. Dans le menu **Sélectionner un point de restauration**, choisissez un point de récupération. Vous pouvez choisir l’option Logs (Point in Time) (Journaux [point dans le temps]) ou Full & Differential (Complète et différentielle). Si vous souhaitez restaurer un journal ponctuel, revenez à l’étape 2. Cette étape restaure un point de restauration spécifique complète ou différentielle. Cette option permet de voir l’ensemble des points de récupération complète et différentielle des 30 derniers jours. Si vous souhaitez observer les points de récupération antérieurs à 30 jours, cliquez sur **Filtre** pour ouvrir le menu **Filtrer les points de restauration**. Si vous choisissez un point de récupération différentiel, Sauvegarde Azure commence par restaurer le point de récupération complète approprié, puis applique le point de récupération différentielle sélectionné.

    ![menu sélectionner un point de restauration](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Dans le menu **Sélectionner un point de restauration**, sélectionnez **Complète et différentielle**.

       ![menu sélectionner un point de restauration](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        La liste des points de récupération disponibles s’affiche.

    2. Dans la liste des points de récupération, sélectionnez un point de récupération, puis cliquez sur **OK** pour terminer la procédure de sélection du point de restauration. 

        ![choisir le point de récupération complète](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Le menu **Point de restauration** se ferme et le menu **Configuration avancée** s’ouvre.

        ![menu configuration avancée](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Dans le menu **Configuration avancée** :

        * Pour maintenir la base de données non opérationnelle après la restauration, dans le menu **Restore with NORECOVERY** (Restaurer avec NORECOVERY), sélectionnez **Activé**. Par défaut, l’option **Restore with NORECOVERY** (Restaurer avec NORECOVERY) est désactivée.
        * Si vous souhaitez modifier l’emplacement de restauration sur le serveur de destination, indiquez un nouveau chemin d’accès dans la colonne **Cible**.
        * Cliquez sur **OK** pour approuver les paramètres, puis fermez **Configuration avancée**.

    4. Dans le menu **Restaurer**, cliquez sur **Restaurer** pour démarrer le travail de restauration. Dans la zone Notifications, vous pouvez suivre la progression. Vous pouvez également suivre la progression des travaux de restauration de base de données.

       ![menu configuration avancée](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Restaurer et remplacer la base de données

Cette procédure décrit la restauration de données et le remplacement de la base de données. Si vous souhaitez les restaurer à un autre emplacement, passez à la section [Restaurer à un autre emplacement](backup-azure-sql-database.md#restore-to-an-alternate-location). Pour cette procédure, vous devez avoir ouvert votre coffre Recovery Services, et le menu **Configuration de la restauration** doit être actif (voir l’image suivante). Si vous ne l’êtes pas, commencez par la section [Restaurer une base de données SQL](backup-azure-sql-database.md#restore-a-sql-database).

![cliquer sur Configurer pour ouvrir le menu Configuration de la restauration](./media/backup-azure-sql-database/restore-overwrite-db.png)

Le menu déroulant **Serveur** affiche uniquement les serveurs SQL inscrits auprès du coffre Recovery Services. Si le serveur souhaité ne figure pas dans la liste **Serveur**, consultez la section [Détecter les bases de données SQL Server](backup-azure-sql-database.md#discover-sql-server-databases) pour rechercher le serveur. Pendant le processus de détection des bases de données, tous les nouveaux serveurs sont inscrits auprès du coffre Recovery Services.

1. Dans le menu **Configuration de la restauration**, sélectionnez **Remplacer la base de données**, puis cliquez sur **OK** pour terminer la configuration de la destination. 

   ![cliquer sur Remplacer la base de données](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    Les boîtes de dialogue **Serveur**, **Instance** et **Nom de la base de données restaurée** ne sont pas nécessaires.

2. Dans le menu **Sélectionner un point de restauration**, vous pouvez choisir un point de restauration Logs (Point in Time) (Journaux [point dans le temps]) ou Full & Differential (Complète et différentielle). Si vous souhaitez procéder à une restauration dans un journal ponctuel, poursuivez cette étape. Si vous souhaitez restaurer un point de récupération complète ou différentielle, passez directement à l’étape 3.

    ![menu sélectionner un point de restauration](./media/backup-azure-sql-database/recovery-point-menu.png)

    La limite de restauration dans le temps est disponible uniquement pour les sauvegardes de journaux des bases de données dont le mode de récupération est Complet et utilisant les journaux de transactions. Pour choisir une limite de restauration dans le temps à une seconde près :

    1. Sélectionnez l’option de restauration **Logs (Point in Time)** (Journaux [point dans le temps]).

        ![sélectionner un type de point de restauration](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Sous **Date/heure de la restauration**, cliquez sur l’icône de calendrier pour ouvrir celui-ci. Les dates indiquées en gras contiennent des points de récupération, et la date du jour est mise en surbrillance. Sélectionnez une date dans le calendrier avec des points de récupération. Vous ne pouvez pas sélectionner de dates sans point de récupération.

        ![ouvrir le calendrier](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Une fois que vous avez sélectionné une date, le graphique chronologique affiche les points de récupération disponibles.

    3. À l’aide du graphique chronologique ou de la boîte de dialogue Heure, spécifiez une heure spécifique pour le point de récupération, puis cliquez sur **OK** pour terminer l’étape Point de restauration.
    
       ![ouvrir le calendrier](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Le menu **Sélectionner un point de restauration** se ferme et le menu **Configuration avancée** s’ouvre.

       ![menu configuration avancée](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Dans le menu **Configuration avancée** :

        * Pour maintenir la base de données non opérationnelle après la restauration, dans le menu **Restore with NORECOVERY** (Restaurer avec NORECOVERY), sélectionnez **Activé**.
        * Si vous souhaitez modifier l’emplacement de restauration sur le serveur de destination, indiquez un nouveau chemin d’accès dans la colonne **Cible**.
        * cliquez sur **OK** pour approuver les paramètres, puis fermez **Configuration avancée**.

    5. Dans le menu **Restaurer**, cliquez sur **Restaurer** pour démarrer le travail de restauration. Dans la zone Notifications, vous pouvez suivre la progression. Vous pouvez également suivre la progression des travaux de restauration de base de données.

       ![menu configuration avancée](./media/backup-azure-sql-database/restore-job-notification.png)

3. Dans le menu **Sélectionner un point de restauration**, choisissez un point de récupération. Vous pouvez choisir l’option Logs (Point in Time) (Journaux [point dans le temps]) ou Full & Differential (Complète et différentielle). Si vous souhaitez restaurer un journal ponctuel, revenez à l’étape 2. Cette étape restaure un point de restauration spécifique complète ou différentielle. Cette option permet de voir l’ensemble des points de récupération complète et différentielle des 30 derniers jours. Si vous souhaitez observer les points de récupération antérieurs à 30 jours, cliquez sur **Filtre** pour ouvrir le menu **Filtrer les points de restauration**. Si vous choisissez un point de récupération différentiel, Sauvegarde Azure commence par restaurer le point de récupération complète approprié, puis applique le point de récupération différentielle sélectionné.

    ![menu sélectionner un point de restauration](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Dans le menu **Sélectionner un point de restauration**, sélectionnez **Complète et différentielle**.

       ![menu sélectionner un point de restauration](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        La liste des points de récupération disponibles s’affiche.

    2. Dans la liste des points de récupération, sélectionnez un point de récupération, puis cliquez sur **OK** pour terminer la procédure de sélection du point de restauration. 

        ![choisir le point de récupération complète](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Le menu **Point de restauration** se ferme et le menu **Configuration avancée** s’ouvre.

        ![menu configuration avancée](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Dans le menu **Configuration avancée** :

        * Pour maintenir la base de données non opérationnelle après la restauration, dans le menu **Restore with NORECOVERY** (Restaurer avec NORECOVERY), sélectionnez **Activé**. Par défaut, l’option **Restore with NORECOVERY** (Restaurer avec NORECOVERY) est désactivée.
        * Si vous souhaitez modifier l’emplacement de restauration sur le serveur de destination, indiquez un nouveau chemin d’accès dans la colonne **Cible**.
        * Cliquez sur **OK** pour approuver les paramètres, puis fermez **Configuration avancée**.

    4. Dans le menu **Restaurer**, cliquez sur **Restaurer** pour démarrer le travail de restauration. Dans la zone Notifications, vous pouvez suivre la progression. Vous pouvez également suivre la progression des travaux de restauration de base de données.

       ![menu configuration avancée](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Gérer les opérations du service Sauvegarde Azure pour SQL sur les machines virtuelles Azure

Cette section fournit des informations sur les différentes opérations de gestion du service Sauvegarde Azure disponibles pour SQL sur les machines virtuelles Azure. Les opérations principales suivantes existent :

* Surveiller des travaux
* Alertes de sauvegarde
* Arrêter la protection d’une base de données SQL
* Rétablir la protection d’une base de données SQL
* Déclencher un travail de sauvegarde ad hoc
* Annuler l’inscription d’un serveur SQL Server

### <a name="monitor-jobs"></a>Surveiller des travaux
Étant donné que la Sauvegarde Azure est une solution d’entreprise, elle fournit des alertes et des notifications de sauvegarde avancées en cas d’échec (reportez-vous à la section Alertes de sauvegarde ci-dessous). Si vous souhaitez toujours surveiller certains travaux, vous pouvez utiliser l’une des options suivantes selon vos besoins :

#### <a name="use-azure-portal-for-all-adhoc-operations"></a>Utiliser le portail Azure pour toutes les opérations ad hoc
La Sauvegarde Azure affiche tous les travaux déclenchés manuellement, ou ad hoc, dans le portail des travaux de sauvegarde. Les travaux disponibles dans le portail sont les suivants : ensemble des opérations de configuration de sauvegarde, des opérations de sauvegarde déclenchées manuellement, des opérations de restauration, des opérations d’inscription et de découverte des bases de données, et des opérations d’arrêt de sauvegarde. 
![menu configuration avancée](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Tous les travaux de sauvegarde planifiés, y compris les sauvegardes complètes, les sauvegardes différentielles et les sauvegardes de fichiers journaux, ne s’affichent pas dans le portail, et peuvent être surveillés à l’aide de SQL Server Management Studio, comme décrit ci-dessous.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Utiliser SQL Server Management Studio pour les travaux de sauvegarde
Le service Sauvegarde Azure utilise des API natives de SQL pour toutes les opérations de sauvegarde. Avec les API natives, vous pouvez extraire toutes les informations des travaux à partir de la [table backupset SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) de la base de données msdb.

L’exemple suivant est une requête permettant d’extraire tous les travaux de sauvegarde pour la base de données nommée **DB1**. Personnalisez la requête pour une surveillance plus avancée.
```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  
 
```

### <a name="backup-alerts"></a>Alertes de sauvegarde

Avec des sauvegardes de fichiers journaux qui se produisent toutes les 15 minutes, la surveillance des travaux de sauvegarde peut se révéler parfois fastidieuse. Le service Sauvegarde Azure est prévu pour ces situations potentiellement fastidieuses en fournissant des alertes par e-mail déclenchées par tout échec de sauvegarde. Les alertes sont consolidées au niveau de la base de données par code d’erreur. Par exemple, si une base de données compte plusieurs échecs de sauvegarde, au lieu de recevoir une alerte pour chaque échec, vous recevez un e-mail pour le premier échec. Vous pouvez ensuite vous connecter au portail Azure pour surveiller les échecs suivants de cette base de données. 

Pour surveiller les alertes de sauvegarde :

1. Connectez-vous à votre abonnement Azure sur le [portail Azure](https://portal.azure.com).

2. Ouvrez le coffre Recovery Services inscrit auprès de la machine virtuelle SQL.

3. Dans le menu Coffre Recovery Services, sélectionnez **Alertes et événements**. 

   ![menu configuration avancée](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. Dans le menu **Alertes et événements**, sélectionnez **Alertes de sauvegarde** pour afficher la liste des alertes.

   ![menu configuration avancée](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>Arrêter la protection d’une base de données SQL Server

Si vous arrêtez la protection d’une base de données SQL Server, Sauvegarde Azure vous demande si vous souhaitez conserver les points de récupération. Il existe deux façons d’arrêter la protection d’une base de données SQL :

* Arrêter tous les travaux de sauvegarde à venir et supprimer tous les points de récupération
* Arrêter tous les travaux de sauvegarde à venir en conservant les points de récupération 

Laisser les points de récupération implique un coût, car les points de récupération de SQL entraînent la tarification de l’instance SQL protégée ainsi que de l’espace de stockage utilisé. Pour plus d’informations sur la tarification du service Sauvegarde Azure, consultez la [page de la tarification de la sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/). Pour arrêter la protection de la base de données :

1. Ouvrez le coffre Recovery Services inscrit auprès de la machine virtuelle SQL.

2. Dans le tableau de bord du coffre, sélectionnez Éléments de sauvegarde sous **Utilisation** pour ouvrir le menu Éléments de sauvegarde.

    ![Cliquer sur +Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Dans le menu **Éléments de sauvegarde**, sélectionnez le type de gestion des sauvegardes, **SQL in Azure VM** (SQL dans une machine virtuelle Azure). 

    ![Cliquer sur +Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    La liste des éléments de sauvegarde s’ajuste pour afficher la liste des bases de données SQL. 

4. Dans la liste des bases de données SQL, sélectionnez la base de données dont vous voulez arrêter la protection.

    ![sélectionner SQL in Azure VM (SQL dans une machine virtuelle Azure) dans la liste](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Lorsque vous sélectionnez la base de données, le menu correspondant s’ouvre. 

5. Dans le menu de la base de données sélectionnée, cliquez sur **Arrêter la sauvegarde** pour arrêter la protection de la base de données.

    ![sélectionner restaurer la base de données](./media/backup-azure-sql-database/stop-db-button.png)

    Le menu **Arrêter la sauvegarde** s’ouvre.

6. Dans le menu **Arrêter la sauvegarde**, choisissez de conserver ou de supprimer les données de sauvegarde. Si vous le souhaitez, vous pouvez indiquer une raison motivant l’arrêt de la protection ainsi qu’un commentaire.

    ![sélectionner restaurer la base de données](./media/backup-azure-sql-database/stop-backup-button.png)

7. Cliquez sur **Arrêter la sauvegarde** pour arrêter la protection de la base de données. 

### <a name="resume-protection-for-a-sql-database"></a>Rétablir la protection d’une base de données SQL

Si vous avez sélectionné l’option **Conserver les données de sauvegarde** au moment de l’arrêt de la protection de la base de données SQL, vous avez la possibilité de rétablir la protection. Si les données de sauvegarde n’ont pas été conservées, la protection ne peut pas reprendre. 

1. Pour rétablir la protection de la base de données SQL, ouvrez l’élément de sauvegarde, puis cliquez sur **Reprendre la sauvegarde**.

    ![rétablir la protection d’une base de données](./media/backup-azure-sql-database/resume-backup-button.png)

   Le menu Stratégie de sauvegarde s’ouvre.

2. Dans le menu **Stratégie de sauvegarde**, sélectionnez une stratégie, puis cliquez sur **Enregistrer**.

### <a name="trigger-an-adhoc-backup"></a>Déclencher une sauvegarde ad hoc

Vous pouvez déclencher une sauvegarde ad hoc quand vous le souhaitez. Il existe quatre types de sauvegardes ad hoc. Pour plus d’informations sur chaque type, consultez l’article [Types de sauvegardes](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

* Sauvegarde complète
* Sauvegarde de copie uniquement
* Sauvegarde différentielle
* Sauvegarde de fichier journal

### <a name="unregister-a-sql-server"></a>Annuler l’inscription d’un serveur SQL Server

Pour annuler l’inscription d’un serveur SQL Server après la suppression de la protection, mais avant celle du coffre

1. Ouvrez le coffre Recovery Services inscrit auprès de la machine virtuelle SQL.

2. Dans la section **Gérer** du menu du coffre, cliquez sur **Infrastructure de sauvegarde**.  

   ![rétablir la protection d’une base de données](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. Dans la section **Serveurs d’administration**, cliquez sur **Serveurs protégés**.

   ![rétablir la protection d’une base de données](./media/backup-azure-sql-database/protected-servers.png)

    Le menu Serveurs protégés s’ouvre. 

4. Dans le menu **Serveurs protégés**, sélectionnez le serveur dont vous voulez annuler l’inscription. Si vous souhaitez supprimer le coffre, vous devez annuler l’inscription de tous les serveurs.

5. Dans le menu Serveurs protégés, cliquez avec le bouton droit sur le serveur protégé, puis sélectionnez **Supprimer**. 

   ![rétablir la protection d’une base de données](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="sql-database-backup-faq"></a>Questions fréquentes (FAQ) sur la sauvegarde de bases de données SQL

La section suivante fournit des informations supplémentaires sur la sauvegarde de base de données SQL.

### <a name="can-i-throttle-the-speed-of-the-sql-backup-policy-so-it-minimizes-impact-on-the-sql-server"></a>Puis-je limiter la vitesse de la stratégie de sauvegarde SQL afin de réduire l’impact sur le serveur SQL ?

Oui, vous pouvez limiter la fréquence d’exécution de la stratégie de sauvegarde. Pour modifier ce paramètre :

1. Sur le serveur SQL Server, dans le dossier `C:\Program Files\Azure Workload Backup\bin`, ouvrez **TaskThrottlerSettings.json**.

2. Dans le fichier **TaskThrottlerSettings.json**, remplacez la valeur de **DefaultBackupTasksThreshold** par une valeur inférieure, par exemple, 5.

3. Enregistrez vos modifications, puis fermez le fichier.

4. Sur le serveur SQL Server, ouvrez le Gestionnaire des tâches, puis redémarrez le **service Coordinateur des charges de travail de la Sauvegarde Azure**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Puis-je effectuer la sauvegarde complète d’un réplica secondaire ?

Non, cette fonctionnalité n’est pas prise en charge.

### <a name="do-successful-backup-jobs-create-alerts"></a>La réussite des sauvegardes de travaux génère-t-elle des alertes ?

Non. Les travaux de sauvegarde réussis ne génèrent pas d’alertes. Les alertes ne sont envoyées qu’en cas d’échec de la sauvegarde.

### <a name="are-scheduled-backup-job-details-shown-in-the-jobs-menu"></a>Les détails concernant les travaux de sauvegarde planifiés sont-ils affichés dans le menu Travaux ?

Non. Le menu Travaux affiche les détails des travaux ad hoc, mais pas ceux des travaux de sauvegarde planifiés. Si un travail de sauvegarde échoue, tous les détails concernant cet échec sont fournis dans l’alerte d’échec. Si vous souhaitez surveiller tous les travaux de sauvegarde ad hoc et planifiés, [utilisez SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="if-i-select-a-sql-server-will-future-databases-automatically-be-added"></a>Si je sélectionne un serveur SQL Server, les futures bases de données y seront-elles automatiquement ajoutées ?

Non. Quand vous configurez la protection pour un serveur SQL, si vous cochez la case au niveau du serveur, cela ajoute toutes les bases de données. Toutefois, si vous ajoutez des bases de données au serveur SQL Server après avoir configuré la protection, vous devez ajouter manuellement les nouvelles bases de données pour les protéger. Les bases de données ne sont pas automatiquement incluses dans la protection configurée.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Si je modifie le modèle de récupération, comment redémarrer la protection ?

Si vous modifiez le modèle de récupération, déclenchez une sauvegarde complète pour démarrer la sauvegarde des fichiers journaux.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la sauvegarde Azure, consultez l’exemple PowerShell sur la sauvegarde des machines virtuelles chiffrées.

> [!div class="nextstepaction"]
> [Sauvegarder une machine virtuelle chiffrée](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
