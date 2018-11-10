---
title: Sauvegarder des bases de données SQL Server dans Azure | Microsoft Docs
description: Ce tutoriel explique comment sauvegarder SQL Server avec Azure, ainsi que la récupération de SQL Server.
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
ms.date: 08/02/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 72d48bd1716e1b62ae92f8317f3f9611ac463453
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211500"
---
# <a name="back-up-sql-server-databases-to-azure"></a>Sauvegarder des bases de données SQL Server sur Azure

Les bases de données SQL Server sont des charges de travail critiques nécessitant un faible objectif de point de récupération (RPO) et une conservation à long terme. La Sauvegarde Azure offre une solution de sauvegarde SQL Server qui ne nécessite aucune infrastructure. Vous n’avez pas à gérer de serveur de sauvegarde complexe, ni d’agent de gestion ou de stockage de sauvegarde. Sauvegarde Azure assure la gestion centralisée de vos sauvegardes entre tous les serveurs exécutés sous SQL Server, voire entre différentes charges de travail.

Dans cet article, vous apprenez :

> [!div class="checklist"]
> * les prérequis pour sauvegarder une instance SQL Server dans Azure.
> * à créer et à utiliser un coffre Recovery Services.
> * à configurer des sauvegardes de bases de données SQL Server.
> * à définir une stratégie de sauvegarde (ou de rétention) des points de récupération.
> * à restaurer la base de données.

Avant de commencer les procédures décrites dans cet article, vous devez disposer d’une instance SQL Server s’exécutant dans Azure. [Vous pouvez utiliser des machines virtuelles de la Place de marché SQL pour créer rapidement une instance SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Limitations de la préversion publique

Les éléments suivants constituent des limitations connues de la préversion publique :

- Une machine virtuelle SQL (VM) requiert une connectivité Internet pour accéder aux adresses IP publiques Azure. Pour plus de détails, consultez [Établir la connectivité réseau](backup-azure-sql-database.md#establish-network-connectivity).
- Vous pouvez protéger jusqu’à 2 000 bases de données SQL dans un coffre Recovery Services. Vous devez stocker les bases de données SQL supplémentaires dans un autre coffre Recovery Services.
- [La sauvegarde des groupes de disponibilité distribués](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) présente des limitations.
- Les instances de cluster de basculement (FCI) Always On de SQL Server ne sont pas prises en charge.
- Utilisez le portail Azure pour configurer Sauvegarde Azure afin de protéger des bases de données SQL Server. Azure PowerShell, l’interface de ligne de commande Azure et les API REST ne sont pas pris en charge actuellement.

Reportez-vous à la [section FAQ](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq) pour plus d’informations sur les scénarios pris en charge et non pris en charge.

## <a name="support-for-azure-geos"></a>Prise en charge des zones géographiques Azure

La sauvegarde Azure est prise en charge pour les zones géographiques suivantes :

- Sud-Est de l’Australie (ASE) 
- Brésil Sud (BRS)
- Centre du Canada (CNC)
- Est du Canada (CE)
- USA Centre (CUS)
- Asie Est (EA)
- Est d’Australie (AE) 
- USA Est (EUS)
- USA Est 2 (EUS2)
- Inde Centre (INC) 
- Sud de l’Inde (INS)
- Japon Est (JPE)
- Japon Ouest (JPW)
- Corée Centre (KRC)
- Corée du Sud (KRS)
- USA Centre Nord (NCUS) 
- Europe Nord (NE) 
- USA Centre Sud (SCUS) 
- Asie Sud-Est (SEA)
- Sud du Royaume-Uni (UKS) 
- Ouest du Royaume-Uni (UKW) 
- USA Centre-Ouest (WCUS)
- Europe Ouest (WE) 
- USA Ouest (WUS)
- USA Ouest 2 (WUS 2) 

## <a name="support-for-operating-systems-and-sql-server-versions"></a>Prise en charge des systèmes d’exploitation et versions de SQL Server

Cette section décrit la prise en charge de la sauvegarde Azure pour les systèmes d’exploitation et les versions de SQL Server. Les machines virtuelles Azure de la Place de marché SQL et les machines virtuelles autres que celles de la Place de marché (sur lesquelles SQL Server est installé manuellement) sont prises en charge.

### <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux n’est pas actuellement pris en charge.

### <a name="supported-sql-server-versions-and-editions"></a>Versions et éditions de SQL Server prises en charge

- SQL Server 2012 Enterprise, Standard, Web, Developer, Express
- SQL Server 2014 Enterprise, Standard, Web, Developer, Express
- SQL Server 2016 Enterprise, Standard, Web, Developer, Express
- SQL Server 2017 Enterprise, Standard, Web, Developer, Express

## <a name="prerequisites"></a>Prérequis

Pour pouvoir sauvegarder votre base de données SQL Server, vérifiez les conditions suivantes :

- Identifiez ou [créez un coffre Recovery Services](backup-azure-sql-database.md#create-a-recovery-services-vault) dans la même région ou avec les mêmes paramètres régionaux que la machine virtuelle qui héberge votre instance SQL Server.
- [Vérifiez les autorisations existant sur la machine virtuelle](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) nécessaires à la sauvegarde des bases de données SQL.
- Vérifiez que [la machine virtuelle SQL dispose d’une connectivité réseau](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> Vous ne pouvez disposer que d’une solution de sauvegarde à la fois pour sauvegarder les bases de données SQL Server. Désactivez toutes les autres sauvegardes SQL avant d’utiliser cette fonctionnalité ; sinon, les sauvegardes causeront des interférences et des échecs. Vous pouvez activer Sauvegarde Azure pour la machine virtuelle IaaS ainsi que la sauvegarde SQL sans aucun conflit.
>

Si ces conditions existent dans votre environnement, passez à [configurer la sauvegarde des bases de données SQL Server](backup-azure-sql-database.md#configure-backup-for-sql-server-databases). Si l’un des prérequis n’existe pas, poursuivez la lecture.


## <a name="establish-network-connectivity"></a>Établir la connectivité réseau

Pour toutes les opérations, la machine virtuelle SQL requiert une connectivité pour accéder aux adresses IP publiques Azure. Les opérations des machines virtuelles SQL (par exemple, la détection de bases de données, la configuration de sauvegardes, les sauvegardes planifiées, la restauration des points de récupération, etc.) échouent en cas d’absence de connectivité aux adresses IP publiques. Pour fournir un chemin d’accès clair pour le trafic de sauvegarde, utilisez l’une des options suivantes :

- Placez les plages d’adresses IP des centres de données Azure dans une liste verte : pour ce faire, consultez le [page du Centre de téléchargement concernant les plages d’adresses IP](https://www.microsoft.com/download/details.aspx?id=41653). 
- Déployez un serveur proxy HTTP pour le routage du trafic : lorsque vous sauvegardez une base de données SQL sur une machine virtuelle, l’extension de sauvegarde sur la machine virtuelle utilise les API HTTPS pour envoyer des commandes de gestion à Sauvegarde Azure et des données à Stockage Azure. L’extension de sauvegarde utilise également Azure Active Directory (Azure AD) pour l’authentification. Acheminez le trafic de l’extension de sauvegarde pour ces trois services via le proxy HTTP. L’extension est le seul composant qui est configuré pour l’accès à l’internet public.

Les compromis entre les options sont les suivants : facilité de gestion, contrôle granulaire et coût.

> [!NOTE]
> Les balises de service pour Sauvegarde Azure doivent être disponibles en disponibilité générale.
>

| Option | Avantages | Inconvénients |
| ------ | ---------- | ------------- |
| Plages IP de liste blanche | Aucun coût supplémentaire <br/> Pour l’ouverture d’un accès à un groupe de sécurité réseau, utilisez le cmdlet **Set-AzureNetworkSecurityRule**. | Difficile à gérer, car les plages d’adresses IP concernées changent au fil du temps. <br/>Fournit un accès à l’ensemble d’Azure et pas seulement au stockage Azure.|
| Utiliser un proxy HTTP   | Le contrôle granulaire dans le proxy sur les URL de stockage est autorisé. <br/>Un seul point d’accès Internet aux machines virtuelles. <br/> Non soumis aux modifications d’adresse IP Azure. | Frais supplémentaires d’exécution de machine virtuelle avec le logiciel de serveur proxy. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Définir des autorisations pour les machines virtuelles SQL autres que celles de la Place de marché

Pour sauvegarder une machine virtuelle, Sauvegarde Azure nécessite l’installation de l’extension **AzureBackupWindowsWorkload**. Si vous utilisez des machines virtuelles de la Place de marché Azure, continuez avec [Détecter les bases de données SQL Server](backup-azure-sql-database.md#discover-sql-server-databases). Si la machine virtuelle hébergeant vos bases de données SQL n’a pas été créée à partir de la Place de marché Azure, complétez la section suivante pour installer l’extension et définir les autorisations appropriées. Outre l’extension **AzureBackupWindowsWorkload**, Sauvegarde Azure nécessite des privilèges d’administrateur système SQL pour protéger les bases de données SQL. Lors de la détection des bases de données sur la machine virtuelle, Sauvegarde Azure crée un compte **NT Service\AzureWLBackupPluginSvc**. Pour que la Sauvegarde Azure découvre les bases de données SQL, le compte **NT Service\AzureWLBackupPluginSvc** doit disposer des autorisations SQL et des autorisations d’administrateur système SQL. La procédure suivante explique comment fournir ces autorisations.

Pour configurer des autorisations :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le coffre Recovery Services que vous utilisez pour protéger les bases de données SQL.

2. Dans le tableau de bord du **coffre Recovery Services**, cliquez sur **Sauvegarde**. Le menu **Objectif de sauvegarde** s’ouvre.

   ![Cliquer sur Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/open-backup-menu.png)

3. Dans le menu **Objectif de sauvegarde**, définissez **Où s’exécute votre charge de travail ?** sur la valeur par défaut **Azure**.

4. Développez le menu déroulant **What do you want to backup** (Que voulez-vous sauvegarder ?), et sélectionnez **SQL Server in Azure VM** (SQL Server dans une machine virtuelle Azure).

    ![Sélectionnez SQL Server dans une machine virtuelle Azure pour la sauvegarde](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Le menu **Objectif de sauvegarde** affiche deux étapes : **Discover DBs in VMs** (Détecter les bases de données dans les machines virtuelles) et **Configurer la sauvegarde**. L’étape **Détecter les bases de données dans les machines virtuelles** lance une recherche pour les machines virtuelles Azure.

    ![Passez en revue les deux étapes de l’objectif de sauvegarde](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Dans **Discover DBs in VMs** (Détecter les bases de données dans les machines virtuelles), cliquez sur **Démarrer la détection** pour rechercher des machines virtuelles non protégées dans l’abonnement. Rechercher tous les ordinateurs virtuels peut prendre un certain temps. La durée de recherche varie selon le nombre de machines virtuelles non protégées de l’abonnement.

    ![La sauvegarde est en attente au cours de la recherche pour des bases de données dans des machines virtuelles](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Une machine virtuelle non protégée s’affiche dans la liste dès qu’elle est détectée. Les machines virtuelles non protégées sont répertoriées par leur nom de machine virtuelle et leur groupe de ressources. Plusieurs machines virtuelles peuvent avoir le même nom. Toutefois, les machines virtuelles portant le même nom appartiennent à différents groupes de ressources. Si une machine virtuelle attendue n’est pas répertoriée, vérifiez si elle est déjà protégée dans un coffre.

6. Dans la liste des machines virtuelles, sélectionnez la machine virtuelle qui contient la base de données SQL que vous souhaitez sauvegarder, puis cliquez sur **Discover DBs in VMs** (Détecter les bases de données dans les machines virtuelles). 

    Le processus de détection installe l’extension **AzureBackupWindowsWorkload** sur la machine virtuelle. L’extension permet au service Sauvegarde Azure de communiquer avec la machine virtuelle afin qu’il puisse sauvegarder les bases de données SQL. Une fois l’extension installée, Sauvegarde Azure crée le compte de service virtuel Windows **NT Service\AzureWLBackupPluginSvc** sur la machine virtuelle. Le compte de service virtuel requiert une autorisation d’administrateur système SQL. Pendant le processus d’installation de compte service virtuel, si vous recevez l’erreur `UserErrorSQLNoSysadminMembership`, consultez [Correction des autorisations d’administrateur système SQL](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

    La zone **Notifications** affiche la progression de la détection de la base de données. La tâche peut prendre un certain temps à se terminer. Le temps de travail dépend du nombre de bases de données sur l’ordinateur virtuel. Lorsque les bases de données sélectionnées ont été détectées, un message de réussite s’affiche.

    ![Message Déploiement réussi](./media/backup-azure-sql-database/notifications-db-discovered.png)

Dès que vous avez associé la base de données au coffre Recovery Services, l’étape suivante consiste à [configurer la tâche de sauvegarde](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="fix-sql-sysadmin-permissions"></a>Correction des autorisations d’administrateur système SQL

Pendant l’installation, si vous rencontrez l’erreur `UserErrorSQLNoSysadminMembership`, utilisez un compte disposant d’autorisations d’administrateur système SQL Server pour vous connecter à SQL Server Management Studio (SSMS). À moins que vous ayez besoin d’autorisations spéciales, l’authentification Windows doit fonctionner.

1. Sur le serveur SQL Server, ouvrez le dossier Security/Logins.

    ![Ouvrir le dossier Security/Logins pour afficher les comptes](./media/backup-azure-sql-database/security-login-list.png)

2. Cliquez avec le bouton droit sur le dossier Logins et sélectionnez **Nouvelle connexion**. Dans la boîte de dialogue **Connexion - Nouveau**, sélectionnez **Rechercher**.

    ![Dans la boîte de dialogue Connexion - Nouveau, sélectionnez Rechercher](./media/backup-azure-sql-database/new-login-search.png)

3. Le compte de service virtuel Windows **NT Service\AzureWLBackupPluginSvc** a été créé pendant la phase d’inscription de la machine virtuelle et de découverte SQL. Entrez le nom du compte, comme indiqué dans la case **Enter the object name to select**  (Entrez le nom de l’objet à sélectionner). Sélectionnez **Vérifier les noms** pour résoudre le nom. 

    ![Cliquer sur Vérifier les noms pour résoudre le nom de service inconnu](./media/backup-azure-sql-database/check-name.png)

4. Cliquez sur **OK** pour fermer la boîte de dialogue.

5. Dans la boîte de dialogue **Rôles serveur**, assurez-vous que le rôle **administrateur système** est sélectionné, puis cliquez sur **OK** pour fermer la boîte de dialogue.

    ![S’assurer que le rôle serveur administrateur système est sélectionné](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Les autorisations requises doivent désormais exister.

6. Même si vous avez corrigé l’erreur liée aux autorisations, vous devez toujours associer la base de données au coffre Recovery Services. Dans la liste **Serveurs protégés** du portail Azure, cliquez avec le bouton droit sur le serveur en erreur, puis sélectionnez **Redécouvrir les bases de données**.

    ![Vérifier que le serveur dispose des autorisations appropriées](./media/backup-azure-sql-database/check-erroneous-server.png)

    La zone **Notifications** affiche la progression de la détection de la base de données. La tâche peut prendre un certain temps à se terminer. Le temps de travail dépend du nombre de bases de données sur l’ordinateur virtuel. Lorsque les bases de données sélectionnées ont été détectées, un message de réussite s’affiche.

    ![Message Déploiement réussi](./media/backup-azure-sql-database/notifications-db-discovered.png)

Dès que vous avez associé la base de données au coffre Recovery Services, l’étape suivante consiste à [configurer la tâche de sauvegarde](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Détecter les bases de données SQL Server

Sauvegarde Azure permet de découvrir toutes les bases de données sur une instance SQL Server. Vous pouvez protéger les bases de données selon vos besoins de sauvegarde. Pour identifier la machine virtuelle qui héberge les bases de données SQL, procédez comme suit. Une fois que vous avez identifié la machine virtuelle, Sauvegarde Azure installe une extension légère pour détecter les bases de données SQL Server.

1. Connectez-vous à votre abonnement sur le [portail Azure](https://portal.azure.com/).

2. Dans le menu de gauche, sélectionnez **Tous les services**.

    ![Sélectionner Tous les services](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. Dans la boîte de dialogue **Tous les services**, entrez **Recovery Services**. Au fur et à mesure des caractères saisis, la liste des ressources est filtrée. Sélectionnez **Coffres Recovery Services** dans la liste.

    ![Entrée et choisir Coffres Recovery Services](./media/backup-azure-sql-database/all-services.png) <br/>

    La liste des coffres Recovery Services de l’abonnement s’affiche. 

4. Dans la liste des coffres Recovery Services, sélectionnez le coffre que vous souhaitez utiliser pour protéger vos bases de données SQL.

5. Dans le tableau de bord du **coffre Recovery Services**, cliquez sur **Sauvegarde**. Le menu **Objectif de sauvegarde** s’ouvre.

   ![Cliquer sur Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/open-backup-menu.png)

6. Dans le menu **Objectif de sauvegarde**, définissez **Où s’exécute votre charge de travail ?** sur la valeur par défaut **Azure**.

7. Développez le menu déroulant **What do you want to backup** (Que voulez-vous sauvegarder ?), et sélectionnez **SQL Server in Azure VM** (SQL Server dans une machine virtuelle Azure).

    ![Sélectionnez SQL Server dans une machine virtuelle Azure pour la sauvegarde](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Le menu **Objectif de sauvegarde** affiche deux étapes : **Discover DBs in VMs** (Détecter les bases de données dans les machines virtuelles) et **Configurer la sauvegarde**.
    
    ![Passez en revue les deux étapes de l’objectif de sauvegarde](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Dans **Discover DBs in VMs** (Détecter les bases de données dans les machines virtuelles), cliquez sur **Démarrer la détection** pour rechercher des machines virtuelles non protégées dans l’abonnement. Rechercher toutes les machines virtuelles peut prendre un certain temps. La durée de recherche varie selon le nombre de machines virtuelles non protégées de l’abonnement.

    ![La sauvegarde est en attente au cours de la recherche pour des bases de données dans des machines virtuelles](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Une machine virtuelle non protégée s’affiche dans la liste dès qu’elle est détectée. Plusieurs machines virtuelles peuvent avoir le même nom. Toutefois, les machines virtuelles portant le même nom appartiennent à différents groupes de ressources. Les machines virtuelles non protégées sont répertoriées par leur nom de machine virtuelle et leur groupe de ressources. Si une machine virtuelle attendue n’est pas répertoriée, vérifiez si elle est déjà protégée dans un coffre.

9. Dans la liste des machines virtuelles, sélectionnez la machine virtuelle qui contient la base de données SQL que vous souhaitez sauvegarder, puis cliquez sur **Discover DBs in VMs** (Détecter les bases de données dans les machines virtuelles).

    Sauvegarde Azure détecte toutes les bases de données SQL résidant sur la machine virtuelle. Pour plus d’informations sur ce qui se passe pendant la phase de découverte de base de données, consultez [Opérations en arrière-plan](backup-azure-sql-database.md#background-operations). Vous êtes prêt à [configurer le travail de sauvegarde](backup-azure-sql-database.md#configure-backup-for-sql-server-databases) une fois que vous avez détecté les bases de données SQL.

### <a name="background-operations"></a>Opérations en arrière-plan

Lorsque vous utilisez l’outil **Découvrir les bases de données**, le service Sauvegarde Azure exécute les opérations suivantes en arrière-plan :

- Il inscrit la machine virtuelle auprès du coffre Recovery Services pour la sauvegarde des charges de travail. Toutes les bases de données résidant sur la machine virtuelle inscrite peuvent être sauvegardées dans ce coffre Recovery Services uniquement. 

- Il installe l’extension **AzureBackupWindowsWorkload** sur la machine virtuelle. La sauvegarde des bases de données SQL est une solution sans agent. L’extension est installée sur la machine virtuelle et aucun agent n’est installé sur la base de données SQL.

- Il crée le compte de service, **NT Service\AzureWLBackupPluginSvc**, sur la machine virtuelle. Toutes les opérations de sauvegarde et de restauration utilisent le compte de service. **NT Service\AzureWLBackupPluginSvc** nécessite des autorisations d’administrateur système SQL. Toutes les machines virtuelles de la place de marché SQL sont fournis avec l’extension **SqlIaaSExtension**. L’extension **AzureBackupWindowsWorkload** utilise l’extension **SQLIaaSExtension** pour obtenir automatiquement les autorisations requises. Si l’extension **SQLIaaSExtension** n’est pas installée sur votre machine virtuelle, la détection des bases de données échoue et le message d’erreur `UserErrorSQLNoSysAdminMembership` s’affiche. Pour ajouter l’autorisation d’administrateur système pour la sauvegarde, suivez les instructions indiquées dans [Configuration des autorisations Sauvegarde Azure pour les machines virtuelles SQL autres que de la Place de marché](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

    ![Sélectionner la machine virtuelle et la base de données](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-databases"></a>Configurer la sauvegarde des bases de données SQL Server

Sauvegarde Azure fournit des services de gestion afin de protéger vos bases de données SQL Server et de gérer les travaux de sauvegarde. Les fonctionnalités de gestion et de surveillance dépendent de votre coffre Recovery Services. 

> [!NOTE]
> Vous ne pouvez disposer que d’une solution de sauvegarde à la fois pour sauvegarder les bases de données SQL Server. Désactivez toutes les autres sauvegardes SQL avant d’utiliser cette fonctionnalité ; sinon, les sauvegardes causeront des interférences et des échecs. Vous pouvez activer Sauvegarde Azure pour la machine virtuelle IaaS ainsi que la sauvegarde SQL sans aucun conflit.
>

Pour configurer la protection d’une base de données SQL :

1. Ouvrez le coffre Recovery Services inscrit auprès de la machine virtuelle SQL.

2. Dans le tableau de bord du **coffre Recovery Services**, cliquez sur **Sauvegarde**. Le menu **Objectif de sauvegarde** s’ouvre.

   ![Cliquer sur Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/open-backup-menu.png)

3. Dans le menu **Objectif de sauvegarde**, définissez **Où s’exécute votre charge de travail ?** sur la valeur par défaut **Azure**.

4. Développez le menu déroulant **What do you want to backup** (Que voulez-vous sauvegarder ?), et sélectionnez **SQL Server in Azure VM** (SQL Server dans une machine virtuelle Azure).

    ![Sélectionnez SQL Server dans une machine virtuelle Azure pour la sauvegarde](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Le menu **Objectif de sauvegarde** affiche deux étapes : **Discover DBs in VMs** (Détecter les bases de données dans les machines virtuelles) et **Configurer la sauvegarde**.
    
    Si vous avez parcouru cet article dans l’ordre, vous avez déjà détecté les machines virtuelles non protégées, et ce coffre est inscrit auprès d’une machine virtuelle. Vous êtes maintenant prêt à configurer la protection des bases de données SQL.
    
5. Dans le menu **Objectif de sauvegarde**, cliquez sur **Configurer la sauvegarde**.

    ![Sélectionner Configurer la sauvegarde](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Le service Sauvegarde Azure affiche toutes les instances SQL Server avec des bases de données autonomes, ainsi que les groupes de disponibilité SQL Server Always On. Pour afficher les bases de données autonomes dans l’instance SQL Server, cliquez sur le chevron situé à gauche du nom de l’instance. Les images suivantes illustrent des exemples d’une instance autonome et d’un groupe de disponibilité Always On.

    > [!NOTE]
    > En présence d’un groupe de disponibilité Always On SQL Server, la sauvegarde SQL est prioritaire. Toutefois, en raison de limitations de la plateforme SQL, les sauvegardes complètes et différentielles doivent être effectuées sur le nœud principal. La sauvegarde du journal se produit en fonction de vos préférences de sauvegarde. En raison de cette limitation, le nœud principal doit toujours être inscrit pour les groupes de disponibilité.
    >

    ![Liste des bases de données de l’instance SQL](./media/backup-azure-sql-database/discovered-databases.png)

    Sélectionnez le chevron à gauche du groupe de disponibilité Always On pour afficher la liste des bases de données.

    ![Liste des bases de données dans le groupe de disponibilité Always On](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. Dans la liste des bases de données, sélectionnez toutes les bases de données à protéger, puis sélectionnez **OK**.

    ![Sélectionner plusieurs bases de données à protéger](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Vous pouvez sélectionner jusqu’à 50 bases de données en même temps. Si vous souhaitez en protéger davantage, effectuez plusieurs passes. Après que vous avez protégé les 50 premières bases de données, répétez cette étape pour protéger l’ensemble de bases de données suivant.

    > [!Note] 
    > Pour optimiser les charges des sauvegardes, Sauvegarde Azure fractionne les travaux de sauvegarde volumineux en plusieurs lots. Le nombre maximal de bases de données contenues dans un travail de sauvegarde est égal à 50.
    >
    >

7. Pour créer ou choisir une stratégie de sauvegarde, dans le menu **Sauvegarde**, sélectionnez **Stratégie de sauvegarde**. Le menu **Stratégie de sauvegarde** s’ouvre.

    ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-sql-database/select-backup-policy.png)

8. Dans le menu déroulant **Choisir une stratégie de sauvegarde**, choisissez une stratégie de sauvegarde, puis sélectionnez **OK**. Pour plus d’informations sur la création d’une stratégie de sauvegarde, consultez [Créer une stratégie de sauvegarde](backup-azure-sql-database.md#define-a-backup-policy).

   > [!NOTE]
   > Dans la préversion, vous ne pouvez pas modifier les stratégies de sauvegarde. Si vous souhaitez une autre stratégie que celles disponibles dans la liste, vous devez créer cette stratégie. Pour plus d’informations sur la création de votre propre stratégie de sauvegarde, consultez la section [Créer une stratégie de sauvegarde](backup-azure-sql-database.md#define-a-backup-policy).

    ![Choisir une stratégie de sauvegarde dans la liste](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    Dans le menu déroulant **Choisir une stratégie de sauvegarde** du menu **Stratégie de sauvegarde**, vous pouvez : 
    - Sélectionner la stratégie par défaut : **HourlyLogBackup**.
    - Choisir une stratégie de sauvegarde existante créée précédemment pour SQL.
    - [Définir une nouvelle stratégie](backup-azure-sql-database.md#define-a-backup-policy) selon votre RPO et la durée de rétention. 

    > [!Note]
    > Sauvegarde Azure prend en charge la rétention à long terme qui est basée sur le schéma de sauvegarde grand-père-père-fils. Le schéma optimise la consommation de stockage back-end tout en répondant aux besoins de conformité.
    >

9. Après avoir choisi une stratégie de sauvegarde, dans le **menu Sauvegarde**, sélectionnez **Activer la sauvegarde**.

    ![Activer la stratégie de sauvegarde choisie](./media/backup-azure-sql-database/enable-backup-button.png)

    Vous pouvez suivre la progression de la configuration dans la zone **Notifications** du portail.

    ![Zone Notifications](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>Créer une stratégie de sauvegarde

Une stratégie de sauvegarde définit une matrice indiquant le moment auquel les sauvegardes sont effectuées ainsi que la durée de leur rétention. Utilisez Sauvegarde Azure pour planifier trois types de sauvegarde pour les bases de données SQL :

* Sauvegarde complète : une sauvegarde complète de base de données sauvegarde l’intégralité de la base de données. Une sauvegarde complète contient toutes les données d’une base de données spécifique ou d’un ensemble de fichiers ou de groupes de fichiers, ainsi qu’un nombre de journaux suffisant pour récupérer ces données. Vous pouvez déclencher au plus une sauvegarde complète par jour. Vous pouvez choisir d’effectuer une sauvegarde complète tous les jours ou toutes les semaines. 
* Sauvegarde différentielle : une sauvegarde différentielle est basée sur la sauvegarde de données complète précédente la plus récente. Elle capture uniquement les données qui ont changé depuis la sauvegarde complète. Vous pouvez déclencher au plus une sauvegarde différentielle par jour. Vous ne pouvez pas configurer une sauvegarde complète et une sauvegarde différentielle le même jour.
* Sauvegarde de fichier journal : une sauvegarde de fichier journal permet d’effectuer une restauration ponctuelle à la seconde donnée. Au plus, vous pouvez configurer des sauvegardes du journal des transactions toutes les 15 minutes.

Les stratégies sont créées au niveau du coffre Recovery Services. Plusieurs coffres peuvent utiliser la même stratégie de sauvegarde, mais vous devez appliquer la stratégie de sauvegarde à chaque coffre. Lorsque vous créez une stratégie de sauvegarde, la sauvegarde complète quotidienne est la valeur par défaut. Vous pouvez ajouter une sauvegarde différentielle, mais uniquement si vous configurez les sauvegardes complètes pour qu’elles aient lieu toutes les semaines. La procédure suivante décrit la création d’une stratégie de sauvegarde pour une instance de serveur SQL Server dans une machine virtuelle Azure. 

> [!NOTE]
> Dans la préversion, vous ne pouvez pas modifier une stratégie de sauvegarde. Au lieu de cela, vous devez créer une nouvelle stratégie avec les détails désirés.  
 
Pour créer une stratégie de sauvegarde :

1. Dans le coffre Recovery Services qui protège la base de données SQL, cliquez sur **Stratégies de sauvegarde**, puis cliquez sur **Ajouter**. 

   ![Ouvrez la boîte de dialogue de la stratégie de sauvegarde créée](./media/backup-azure-sql-database/new-policy-workflow.png)

   Le menu **Ajouter** s’affiche.

2. Dans le menu **Ajouter**, cliquez sur **SQL Server dans les machines virtuelles Azure**.

   ![Choisissez un type de stratégie pour la nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/policy-type-details.png)

   Sélectionner SQL Server dans les machines virtuelles Azure définit le type de stratégie et ouvre le menu Stratégie de sauvegarde. Le menu **Stratégie de sauvegarde** affiche les champs nécessaires pour toute nouvelle stratégie de sauvegarde SQL Server.

3. Dans **Nom de la stratégie**, entrez le nom de la nouvelle stratégie.

4. Une sauvegarde complète est obligatoire ; vous ne pouvez pas désactiver l’option **Sauvegarde complète**. Cliquez sur **Sauvegarde complète** pour afficher et modifier la stratégie. Même si vous ne modifiez pas la stratégie de sauvegarde, il est conseillé d’afficher les détails de la stratégie.

    ![Champs de la nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/full-backup-policy.png)

    Dans le menu **Stratégie de sauvegarde complète**, choisissez **quotidienne** ou **hebdomadaire** pour le paramètre **Fréquence de sauvegarde**. Si vous sélectionnez **quotidienne**, sélectionnez l’heure et le fuseau horaire de début du travail de sauvegarde. Si vous choisissez des sauvegardes complètes quotidiennes, vous ne pouvez pas créer de sauvegardes différentielles.

   ![Paramètre d’intervalle quotidien](./media/backup-azure-sql-database/daily-interval.png)

    Si vous choisissez la fréquence **hebdomadaire**, sélectionnez le jour de la semaine, l’heure et le fuseau horaire indiquant le début du travail de sauvegarde.

   ![Paramètre d’intervalle hebdomadaire](./media/backup-azure-sql-database/weekly-interval.png)

5. Par défaut, toutes les options figurant dans la section **Durée de rétention** sont sélectionnées : quotidienne, hebdomadaire, mensuelle et annuelle. Désélectionnez les limites de plage de rétention non désirées. Définissez les intervalles à utiliser. Dans le menu de **stratégie Sauvegarde complète**, cliquez sur **OK** pour accepter les paramètres.

   ![Paramètres d’intervalle de la durée de rétention](./media/backup-azure-sql-database/retention-range-interval.png)

    Des points de récupération sont marqués pour la rétention et varient selon la durée de rétention. Par exemple, si vous sélectionnez une sauvegarde complète quotidienne, seule une sauvegarde complète est déclenchée chaque jour. La sauvegarde d’un jour spécifique est marquée et conservée conformément à la durée et aux paramètres de rétention hebdomadaire. Les durées de rétention mensuelle et annuelle ont le même comportement.

6. Pour ajouter une stratégie de sauvegarde différentielle, sélectionnez **Sauvegarde différentielle**. Le menu **Stratégie de sauvegarde différentielle** s’ouvre. 

   ![Ouvrir le menu de stratégie de sauvegarde différentielle](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    Dans le menu de **stratégie Sauvegarde différentielle**, sélectionnez **Activer** pour ouvrir les contrôles de fréquence et de rétention. Vous pouvez déclencher au plus une sauvegarde différentielle par jour.
    
    > [!Important] 
    > Les sauvegardes différentielles peuvent être conservées jusqu’à 180 jours. Si vous avez besoin d’une durée de rétention supérieure, vous devez utiliser des sauvegardes complètes.
    >

   ![Modifier la stratégie de sauvegarde différentielle](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Sélectionnez **OK** pour enregistrer la stratégie et revenir au menu principal **Stratégie de sauvegarde**.

7. Pour ajouter une stratégie de sauvegarde de fichier journal, cliquez sur **Sauvegarde de fichier journal**. Le menu **Sauvegarde de fichier journal** s’ouvre.

    Dans le menu **Sauvegarde de fichier journal**, sélectionnez **Activer** et définissez les contrôles de fréquence et de rétention. Les sauvegardes de fichiers journaux peuvent se produire toutes les 15 minutes et être conservées jusqu’à 35 jours. Sélectionnez **OK** pour enregistrer la stratégie et revenir au menu principal **Stratégie de sauvegarde**.

   ![Modifier la stratégie de sauvegarde de fichier journal](./media/backup-azure-sql-database/log-backup-policy-editor.png)

8. Dans le menu **Stratégie de sauvegarde**, choisissez s’il convient d’activer l’option **Compression de la sauvegarde SQL**, qui est désactivée par défaut.

    Sur le serveur principal, Sauvegarde Azure utilise la compression de sauvegarde native SQL.

9. Après avoir terminé les modifications apportées à la stratégie de sauvegarde, sélectionnez **OK**. 

   ![Accepter une nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Restaurer une base de données SQL
Sauvegarde Azure fournit les fonctionnalités permettant de restaurer des bases de données particulières à une date ou à une heure spécifiques (jusqu’à la seconde près) à l’aide des sauvegardes du journal des transactions. En fonction des heures de restauration que vous indiquez, Sauvegarde Azure détermine automatiquement la sauvegarde complète, la sauvegarde différentielle et la chaîne des sauvegardes de fichiers journaux requis pour restaurer vos données.

Vous pouvez également sélectionner une sauvegarde complète ou différentielle spécifique à restaurer à un point de récupération spécifique plutôt qu’à un moment donné.

### <a name="pre-requisite-before-triggering-a-restore"></a>Conditions préalables avant de déclencher une restauration

1. Vous pouvez restaurer la base de données vers une instance SQL Server dans la même région Azure. Le serveur de destination doit être enregistré dans le même coffre Recovery Services que la source.  
2. Pour restaurer une base de données chiffrée TDE vers un autre serveur SQL Server, veuillez d’abord restaurer le certificat dans le serveur de destination en suivant les étapes présentées [ici](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
3. Avant de déclencher la restauration de la base de données « Master », démarrez l’instance SQL Server en mode mono-utilisateur, avec l’option de démarrage `-m AzureWorkloadBackup`. L’argument lié à l’option `-m` correspond au nom du client. Seul ce client est autorisé à ouvrir la connexion. Pour toutes les bases de données système (model, master, msdb), arrêtez le service SQL Agent avant de déclencher la restauration. Fermez toutes les applications qui tentent de voler une connexion à l’une de ces bases de données.

### <a name="steps-to-restore-a-database"></a>Étapes pour restaurer une base de données :

1. Ouvrez le coffre Recovery Services inscrit auprès de la machine virtuelle SQL.

2. Sur le tableau de bord **Coffre Recovery Services**, dans **Utilisation**, sélectionnez **Éléments de sauvegarde** pour ouvrir ce menu.

    ![Ouvrir le menu Éléments de sauvegarde](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Dans le menu **Éléments de sauvegarde**, sélectionnez le **type de gestion des sauvegardes** **SQL in Azure VM** (SQL dans une machine virtuelle Azure). 

    ![Sélectionner SQL in Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Le menu des **éléments de sauvegarde** affiche la liste des bases de données SQL. 

4. Dans la liste des bases de données SQL, sélectionnez la base de données à restaurer.

    ![Sélectionner la base de données à restaurer](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Lorsque vous sélectionnez la base de données, le menu correspondant s’ouvre. Ce menu contient les informations de sauvegarde de la base de données, notamment :

    * Le point de restauration le plus ancien et le point de restauration le plus récent.
    * L’état de la sauvegarde de fichier journal des dernières 24 heures (pour les bases de données en mode de récupération Complet et utilisant les journaux de transactions, si la configuration est définie pour les sauvegardes du journal des transactions).

5. Dans le menu de la base de données choisie, sélectionnez **Restore DB**. Le menu **Restaurer** s’ouvre.

    ![Sélectionner Restore DB](./media/backup-azure-sql-database/restore-db-button.png)

    Le menu **Configuration de la restauration** s’ouvre en même temps que le menu **Restauration**. Le menu **Configuration de la restauration** est la première étape de la configuration de la restauration. Ce menu vous sert à indiquer où vous voulez restaurer les données. Les options sont :
    - **Autre emplacement** : restaure la base de données vers un autre emplacement et conserver la base de données source d’origine.
    - **Remplacer la base de données** : restaure les données dans la même instance SQL Server que la source d’origine. Cette option permet de remplacer la base de données d’origine.

    > [!Important]
    > Si la base de données sélectionnée fait partie d’un groupe de disponibilité Always On, SQL Server n’autorise pas le remplacement de la base de données. Dans ce cas, seule l’option **Autre emplacement** est activée.
    >

    ![Menu Configuration de la restauration](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Restaurer à un autre emplacement

Cette procédure décrit la restauration des données à un autre emplacement. Pour remplacer la base de données pendant la restauration, continuez à [Restaurer et écraser la base de données](backup-azure-sql-database.md#restore-and-overwrite-the-database). À ce stade, votre coffre Recovery Services est ouvert et le menu **Configuration de la restauration** est visible. Si vous n’êtes pas à ce stade, commencez par [restaurer une base de données SQL](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Vous pouvez restaurer la base de données vers une instance SQL Server dans la même région Azure. Le serveur de destination doit être enregistré auprès du coffre Recovery Services. 
>

Dans le menu **Configuration de restauration**, la liste déroulante **Serveur** affiche uniquement les instances SQL Server enregistrées auprès du coffre Recovery Services. Si le serveur souhaité ne figure pas dans la liste, consultez la section [Détecter les bases de données SQL Server](backup-azure-sql-database.md#discover-sql-server-databases) pour rechercher le serveur. Pendant le processus de détection, les nouveaux serveurs sont inscrits auprès du coffre Recovery Services.

1. Dans le menu **Configuration de la restauration** :

    * Sous **Où effectuer la restauration**, sélectionnez **Autre emplacement**.
    * Ouvrez la liste déroulante **Serveur** et sélectionnez l’instance SQL Server pour restaurer la base de données.
    * Ouvrez la liste déroulante **Instance** et sélectionnez une instance SQL Server.
    * Dans la boîte de dialogue **Nom de la base de données restaurée**, indiquez le nom de la base de données cible.
    * Le cas échéant, sélectionnez **Overwrite if the DB with the same name already exists on selected SQL instance** (Remplacer si une base de données du même nom existe déjà dans l’instance SQL sélectionnée).
    * Sélectionnez **OK** pour terminer la configuration de la destination et choisissez ensuite un point de restauration.

    ![Indiquer des valeurs pour le menu de Configuration de la restauration](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Dans le menu **Sélectionner un point de restauration**, choisissez un point de restauration **Logs (Point in Time)** (Journaux [point dans le temps]) ou **Full & Differential** (Complète et différentielle). Si vous souhaitez procéder à une restauration à partir d’un journal ponctuel spécifique, poursuivez avec l’étape suivante. Pour restaurer un point de restauration complète et différentielle, passez à l’étape 3.

    ![Menu Sélectionner un point de restauration](./media/backup-azure-sql-database/recovery-point-menu.png)

    La restauration à un point dans le temps est disponible uniquement pour les sauvegardes de journaux des bases de données dont le mode de récupération est Complet et utilisant les journaux de transactions. Pour restaurer à un point spécifique dans le temps :

    1. Sélectionnez le type de restauration **Logs (Point in Time)** (Journaux [point dans le temps]).

        ![Sélectionner un type de point de restauration](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Sous **Date/heure de la restauration**, sélectionnez le mini-calendrier pour ouvrir le **Calendrier**. Sur le **Calendrier**, les dates indiquées en gras contiennent des points de récupération et la date du jour est mise en surbrillance. Sélectionnez une date avec des points de récupération. Les dates sans points de récupération ne peuvent pas être sélectionnées.

        ![Ouvrir le calendrier](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Une fois que vous avez sélectionné une date, le graphique chronologique affiche les points de récupération disponibles dans une plage continue.

    3. Utilisez le graphique de chronologie ou la boîte de dialogue **Chronologie** pour spécifier une heure spécifique pour le point de récupération. Sélectionnez **OK** pour terminer l’étape de point de restauration.
    
       ![Ouvrir le calendrier](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Le menu **Sélectionner un point de restauration** se ferme et le menu **Configuration avancée** s’ouvre.

       ![Menu Configuration avancée](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Dans le menu **Configuration avancée** :

        * Pour maintenir la base de données non opérationnelle après la restauration, paramétrez l’option **Restore with NORECOVERY** (Restaurer avec NORECOVERY) sur **Activé**.
        * Pour modifier l’emplacement de restauration sur le serveur de destination, indiquez un nouveau chemin d’accès dans la colonne **Cible**.
        * Sélectionnez **OK** pour valider les paramètres. Fermez le menu **Configuration avancée**.

    5. Dans le menu **Restaurer**, sélectionnez **Restaurer** pour démarrer le travail de restauration. Suivez la progression de la restauration dans la zone de **Notifications** ou sélectionnez **Restaurer les travaux** dans le menu de la base de données.

       ![Progression du travail de restauration](./media/backup-azure-sql-database/restore-job-notification.png)

3. Dans le menu **Sélectionner un point de restauration**, choisissez un point de restauration **Logs (Point in Time)** (Journaux [point dans le temps]) ou **Full & Differential** (Complète et différentielle). Pour restaurer un journal ponctuel, revenez à l’étape 2. Cette étape restaure un point spécifique de restauration complète ou différentielle. Vous pouvez voir l’ensemble des points de récupération complète et différentielle des 30 derniers jours. Pour observer les points de récupération antérieurs à 30 jours, sélectionnez **Filtre** pour ouvrir le menu **Filtrer les points de restauration**. Si vous choisissez un point de récupération différentiel, Sauvegarde Azure commence par restaurer le point de récupération complète approprié, puis applique le point de récupération différentielle sélectionné.

    ![Menu Sélectionner un point de restauration](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Dans le menu **Sélectionner un point de restauration**, sélectionnez **Complète et différentielle**.

       ![Sélectionner Complète et différentielle](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Le menu montre la liste des points de récupération disponibles.

    2. Sélectionnez un point de récupération dans la liste, puis cliquez sur **OK** pour terminer la procédure de sélection du point de restauration. 

        ![Choisir un point de récupération complète](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Le menu **Point de restauration** se ferme et le menu **Configuration avancée** s’ouvre.

        ![Menu Configuration avancée](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Dans le menu **Configuration avancée** :

        * Pour maintenir la base de données non opérationnelle après la restauration, paramétrez l’option **Restore with NORECOVERY** (Restaurer avec NORECOVERY) sur **Activé**. Par défaut, l’option **Restore with NORECOVERY** (Restaurer avec NORECOVERY) est désactivée.
        * Pour modifier l’emplacement de restauration sur le serveur de destination, indiquez un nouveau chemin d’accès dans la colonne **Cible**.
        * Sélectionnez **OK** pour valider les paramètres. Fermez le menu **Configuration avancée**.

    4. Dans le menu **Restaurer**, sélectionnez **Restaurer** pour démarrer le travail de restauration. Suivez la progression de la restauration dans la zone de **Notifications** ou sélectionnez **Restaurer les travaux** dans le menu de la base de données.

       ![Progression du travail de restauration](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Restaurer et remplacer la base de données

Cette procédure décrit la restauration de données et le remplacement d’une base de données. Si vous souhaitez restaurer à un autre emplacement, passez à la section [Restaurer à un autre emplacement](backup-azure-sql-database.md#restore-to-an-alternate-location). À ce stade, votre coffre Recovery Services est ouvert et le menu **Configuration de la restauration** est visible (voir l’image suivante). Si vous n’êtes pas à ce stade, commencez par [restaurer une base de données SQL](backup-azure-sql-database.md#restore-a-sql-database).

![Menu Configuration de la restauration](./media/backup-azure-sql-database/restore-overwrite-db.png)

Dans le menu **Configuration de restauration**, la liste déroulante **Serveur** affiche uniquement les instances SQL Server enregistrées auprès du coffre Recovery Services. Si le serveur souhaité ne figure pas dans la liste, consultez la section [Détecter les bases de données SQL Server](backup-azure-sql-database.md#discover-sql-server-databases) pour rechercher le serveur. Pendant le processus de détection, les nouveaux serveurs sont inscrits auprès du coffre Recovery Services.

1. Dans le menu **Configuration de la restauration**, sélectionnez **Remplacer la base de données**, puis sélectionnez **OK** pour terminer la configuration de la destination. 

   ![Sélectionner Remplacer la base de données](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    Les paramètres **Serveur**, **Instance** et **Nom de la base de données restaurée** ne sont pas nécessaires.

2. Dans le menu **Sélectionner un point de restauration**, choisissez un point de restauration **Logs (Point in Time)** (Journaux [point dans le temps]) ou **Full & Differential** (Complète et différentielle). Si vous souhaitez procéder à une restauration à partir d’un journal ponctuel spécifique, poursuivez avec l’étape suivante. Pour restaurer un point de restauration complète et différentielle, passez à l’étape 3.

    ![menu sélectionner un point de restauration](./media/backup-azure-sql-database/recovery-point-menu.png)

    La restauration à un point dans le temps est disponible uniquement pour les sauvegardes de journaux des bases de données dont le mode de récupération est Complet et utilisant les journaux de transactions. Pour procéder à une restauration à un temps spécifique à la seconde près :

    1. Sélectionnez le point de restauration **Logs (Point in Time)** (Journaux [point dans le temps]).

        ![Sélectionner un type de point de restauration](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Sous **Date/heure de la restauration**, sélectionnez le mini-calendrier pour ouvrir le **Calendrier**. Sur le **Calendrier**, les dates indiquées en gras contiennent des points de récupération et la date du jour est mise en surbrillance. Sélectionnez une date avec des points de récupération. Les dates sans points de récupération ne peuvent pas être sélectionnées.

        ![Ouvrir le calendrier](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Une fois que vous avez sélectionné une date, le graphique chronologique affiche les points de récupération disponibles.

    3. Utilisez le graphique de chronologie ou la boîte de dialogue **Chronologie** pour spécifier une heure spécifique pour le point de récupération. Sélectionnez **OK** pour terminer l’étape de point de restauration.
    
       ![Ouvrir le calendrier](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Le menu **Sélectionner un point de restauration** se ferme et le menu **Configuration avancée** s’ouvre.

       ![Menu Configuration avancée](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Dans le menu **Configuration avancée** :

        * Pour maintenir la base de données non opérationnelle après la restauration, paramétrez l’option **Restore with NORECOVERY** (Restaurer avec NORECOVERY) sur **Activé**.
        * Pour modifier l’emplacement de restauration sur le serveur de destination, indiquez un nouveau chemin d’accès dans la colonne **Cible**.
        * Sélectionnez **OK** pour valider les paramètres. Fermez le menu **Configuration avancée**.

    5. Dans le menu **Restaurer**, sélectionnez **Restaurer** pour démarrer le travail de restauration. Suivez la progression de la restauration dans la zone de **Notifications** ou sélectionnez **Restaurer les travaux** dans le menu de la base de données.

       ![Progression du travail de restauration](./media/backup-azure-sql-database/restore-job-notification.png)

3. Dans le menu **Sélectionner un point de restauration**, choisissez un point de restauration **Logs (Point in Time)** (Journaux [point dans le temps]) ou **Full & Differential** (Complète et différentielle). Pour restaurer un journal ponctuel, revenez à l’étape 2. Cette étape restaure un point spécifique de restauration complète ou différentielle. Vous pouvez voir l’ensemble des points de récupération complète et différentielle des 30 derniers jours. Pour observer les points de récupération antérieurs à 30 jours, sélectionnez **Filtre** pour ouvrir le menu **Filtrer les points de restauration**. Si vous choisissez un point de récupération différentiel, Sauvegarde Azure commence par restaurer le point de récupération complète approprié, puis applique le point de récupération différentielle sélectionné.

    ![Menu Sélectionner un point de restauration](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. Dans le menu **Sélectionner un point de restauration**, sélectionnez **Complète et différentielle**.

       ![Sélectionner Complète et différentielle](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Le menu montre la liste des points de récupération disponibles.

    2. Sélectionnez un point de récupération dans la liste, puis cliquez sur **OK** pour terminer la procédure de sélection du point de restauration. 

        ![Choisir un point de récupération complète](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Le menu **Point de restauration** se ferme et le menu **Configuration avancée** s’ouvre.

        ![Menu Configuration avancée](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Dans le menu **Configuration avancée** :

        * Pour maintenir la base de données non opérationnelle après la restauration, paramétrez l’option **Restore with NORECOVERY** (Restaurer avec NORECOVERY) sur **Activé**. Par défaut, l’option **Restore with NORECOVERY** (Restaurer avec NORECOVERY) est désactivée.
        * Pour modifier l’emplacement de restauration sur le serveur de destination, indiquez un nouveau chemin d’accès dans la colonne **Cible**.
        * Sélectionnez **OK** pour valider les paramètres. Fermez le menu **Configuration avancée**.

    4. Dans le menu **Restaurer**, sélectionnez **Restaurer** pour démarrer le travail de restauration. Suivez la progression de la restauration dans la zone de **Notifications** ou sélectionnez **Restaurer les travaux** dans le menu de la base de données.

       ![Progression du travail de restauration](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Gérer les opérations du service Sauvegarde Azure pour SQL sur les machines virtuelles Azure

Cette section fournit des informations sur les différentes opérations de gestion du service Sauvegarde Azure disponibles pour SQL sur les machines virtuelles Azure. Les opérations principales suivantes existent :

* Surveiller des travaux
* Alertes de sauvegarde
* Arrêter la protection d’une base de données SQL
* Rétablir la protection d’une base de données SQL
* Déclencher un travail de sauvegarde ad hoc
* Désinscrire un serveur qui exécute SQL Server

### <a name="monitor-backup-jobs"></a>Surveiller les travaux de sauvegarde
Sauvegarde Azure est une solution d’entreprise qui fournit des alertes et des notifications de sauvegarde avancées en cas d’échec. (Consultez [Afficher les alertes de sauvegarde](backup-azure-sql-database.md#view-backup-alerts).) Pour surveiller des tâches spécifiques, utilisez l’une des options suivantes selon vos exigences.

#### <a name="use-the-azure-portal-for-adhoc-operations"></a>Utiliser le portail Azure pour les opérations ad hoc
Sauvegarde Azure affiche tous les travaux déclenchés manuellement, ou ad hoc, dans le portail des **travaux de sauvegarde**. Les travaux qui sont disponibles dans le portail des **travaux de sauvegarde** incluent :
- Toutes les opérations de configuration de sauvegarde.
- Opérations de sauvegarde déclenchées manuellement.
- Opérations de restauration.
- Inscription et découverte des opérations de base de données.
- Arrêt des opérations de sauvegarde. 

![Portail des Travaux de sauvegarde](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Tous les travaux de sauvegarde planifiés, y compris les sauvegardes complète, différentielle et du journal, ne sont pas visibles dans le portail des **travaux de sauvegarde**. Utilisez SQL Server Management Studio pour surveiller les travaux de sauvegarde planifiés, comme décrit dans la section suivante.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Utiliser SQL Server Management Studio pour les travaux de sauvegarde
Le service Sauvegarde Azure utilise des API natives de SQL pour toutes les opérations de sauvegarde. Utilisez les API natives pour extraire toutes les informations des travaux à partir de la [table backupset SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) de la base de données msdb.

L’exemple suivant est une requête permettant d’extraire tous les travaux de sauvegarde pour la base de données nommée **DB1**. Personnalisez la requête pour une surveillance plus poussée.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  
 
```

### <a name="view-backup-alerts"></a>Afficher les alertes de sauvegarde

Étant donné que les sauvegardes de fichiers journaux se produisent toutes les 15 minutes, la surveillance des travaux de sauvegarde peut se révéler parfois fastidieuse. Sauvegarde Azure vous aide dans ce type de situation. Des alertes par e-mail se déclenchent pour tous les échecs de sauvegarde. Les alertes sont consolidées au niveau de la base de données par code d’erreur. Une alerte par e-mail est envoyée uniquement pour le premier échec de sauvegarde pour une base de données. Connectez-vous au portail Azure pour surveiller tous les échecs d’une base de données. 

Pour surveiller les alertes de sauvegarde :

1. Connectez-vous à votre abonnement Azure sur le [portail Azure](https://portal.azure.com).

2. Ouvrez le coffre Recovery Services inscrit auprès de la machine virtuelle SQL.

3. Dans le tableau de bord **Coffre Recovery Services**, sélectionnez **Alertes et événements**. 

   ![Sélectionner Alertes et événements](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. Dans le menu **Alertes et événements**, sélectionnez **Alertes de sauvegarde** pour afficher la liste des alertes.

   ![Sélectionner Alertes de sauvegarde](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-for-a-sql-server-database"></a>Arrêter la protection d’une base de données SQL Server

Si vous arrêtez la protection d’une base de données SQL Server, Sauvegarde Azure demande si vous souhaitez conserver les points de récupération. Il existe deux façons d’arrêter la protection d’une base de données SQL :

* Arrêter tous les travaux de sauvegarde à venir et supprimer tous les points de récupération.
* Arrêter tous les travaux de sauvegarde à venir en conservant les points de récupération.

Si vous choisissez d’arrêter la sauvegarde en conservant les données, les points de récupération sont nettoyés conformément à la stratégie de sauvegarde. Le coût de l’instance SQL protégée et l’espace de stockage utilisé vous sont facturés jusqu’à ce que tous les points de récupération soient nettoyés. Pour plus d’informations sur la tarification du service Sauvegarde Azure, consultez la [page de la tarification de la sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/). 

Pour arrêter la protection de la base de données :

1. Ouvrez le coffre Recovery Services inscrit auprès de la machine virtuelle SQL.

2. Sur le tableau de bord **Coffre Recovery Services**, dans **Utilisation**, sélectionnez **Éléments de sauvegarde** pour ouvrir ce menu.

    ![Ouvrir le menu Éléments de sauvegarde](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Dans le menu **Éléments de sauvegarde**, sélectionnez le **type de gestion des sauvegardes** **SQL in Azure VM** (SQL dans une machine virtuelle Azure). 

    ![Sélectionner SQL in Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Le menu des **éléments de sauvegarde** affiche la liste des bases de données SQL. 

4. Dans la liste des bases de données SQL, sélectionnez la base de données que vous souhaitez arrêter de protéger.

    ![Sélectionner la protection de la base de données a arrêter](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Lorsque vous sélectionnez la base de données, le menu correspondant s’ouvre.

5. Dans le menu de la base de données choisie, sélectionnez **Arrêter la sauvegarde**. 

    ![Sélectionner Arrêter la sauvegarde](./media/backup-azure-sql-database/stop-db-button.png)

    Le menu **Arrêter la sauvegarde** s’ouvre.

6. Dans le menu **Arrêter la sauvegarde**, choisissez de **conserver les données de sauvegarde** ou de **supprimer les données de sauvegarde**. Si vous le souhaitez, vous pouvez indiquer une raison motivant l’arrêt de la protection ainsi qu’un commentaire.

    ![Menu Arrêter la sauvegarde](./media/backup-azure-sql-database/stop-backup-button.png)

7. Cliquez sur **Arrêter la sauvegarde** pour arrêter la protection de la base de données. 

### <a name="resume-protection-for-a-sql-database"></a>Rétablir la protection d’une base de données SQL

Si vous avez sélectionné l’option **Conserver les données de sauvegarde** au moment de l’arrêt de la protection de la base de données SQL, vous avez la possibilité de rétablir la protection. Si les données de sauvegarde n’ont pas été conservées, la protection ne peut pas reprendre. 

1. Pour rétablir la protection de la base de données SQL, ouvrez l’élément de sauvegarde, puis cliquez sur **Reprendre la sauvegarde**.

    ![Sélectionner Reprendre la sauvegarde pour relancer la protection de la base de données](./media/backup-azure-sql-database/resume-backup-button.png)

   Le menu **Stratégie de sauvegarde** s’ouvre.

2. Dans le menu **Stratégie de sauvegarde**, sélectionnez une stratégie, puis sélectionnez **Enregistrer**.

### <a name="trigger-an-adhoc-backup"></a>Déclencher une sauvegarde ad hoc

Déclenche des sauvegardes ad hoc en fonction des besoins. Il existe quatre types de sauvegardes ad hoc :

* Sauvegarde complète
* Sauvegarde de copie uniquement
* Sauvegarde différentielle
* Sauvegarde du journal

Pour plus d’informations sur chaque type, consultez [Types de sauvegardes](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

### <a name="unregister-a-sql-server-instance"></a>Supprimer l'inscription d'une instance SQL Server

Pour annuler l’inscription d’une instance SQL Server après avoir supprimé la protection, mais avant de supprimer le coffre :

1. Ouvrez le coffre Recovery Services inscrit auprès de la machine virtuelle SQL.

2. Sur le tableau de bord **Coffre Recovery Services**, sous **Gestion**, sélectionnez **Infrastructure de sauvegarde**.  

   ![Choisir Infrastructure de sauvegarde](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. Sous **Management Servers** (Serveurs d’administration), sélectionnez **Protected Servers** (serveurs protégés).

   ![Sélectionner Serveurs protégés](./media/backup-azure-sql-database/protected-servers.png)

    Le menu **Serveurs protégés** s’ouvre. 

4. Dans le menu **Serveurs protégés**, sélectionnez le serveur dont vous voulez annuler l’inscription. Si vous souhaitez supprimer le coffre, vous devez annuler l’inscription de tous les serveurs.

5. Dans le menu **Serveurs protégés**, cliquez avec le bouton droit sur le serveur protégé, puis sélectionnez **Supprimer**. 

   ![Sélectionner Supprimer](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="faq"></a>Forum Aux Questions

La section suivante fournit des informations supplémentaires sur la sauvegarde de base de données SQL.

### <a name="can-i-throttle-the-speed-of-the-sql-server-backup-policy"></a>Puis-je limiter la vitesse de la stratégie de sauvegarde de SQL Server ?

Oui. Vous pouvez limiter la fréquence à laquelle la stratégie de sauvegarde s’exécute pour minimiser l’impact sur une instance SQL Server.

Pour modifier ce paramètre :

1. Sur l’instance SQL Server, dans le dossier C:\Program Files\Azure Workload Backup\bin, ouvrez le fichier **TaskThrottlerSettings.json**.

2. Dans le fichier TaskThrottlerSettings.json, remplacez la valeur du paramètre **DefaultBackupTasksThreshold** par une valeur inférieure (par exemple, 5).

3. Enregistrez vos modifications. Fermer le fichier.

4. Sur l’instance SQL Server, ouvrez le **Gestionnaire des tâches**. Redémarrez le **Service de coordinateur des charges de travail de sauvegarde Azure**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Puis-je effectuer la sauvegarde complète d’un réplica secondaire ?

Non. Cette fonctionnalité n'est pas prise en charge.

### <a name="do-successful-backup-jobs-create-alerts"></a>La réussite des travaux de sauvegarde génère-t-elle des alertes ?

Non. Les travaux de sauvegarde réussis ne génèrent pas d’alertes. Les alertes ne sont envoyées qu’en cas d’échec de la sauvegarde.

### <a name="can-i-see-details-for-scheduled-backup-jobs-in-the-jobs-menu"></a>Les détails concernant les travaux de sauvegarde planifiés sont-ils affichés dans le menu Travaux ?

Non. Le menu **Travaux de sauvegarde** affiche les détails des travaux ad hoc, mais pas ceux des travaux de sauvegarde planifiés. Si un travail de sauvegarde échoue, les détails concernant cet échec sont fournis dans l’alerte d’échec. Si vous souhaitez surveiller tous les travaux de sauvegarde ad hoc et planifiés, utilisez [SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="when-i-select-a-sql-server-instance-are-future-databases-automatically-added"></a>Si je sélectionne une instance de serveur SQL Server, les futures bases de données y seront-elles automatiquement ajoutées ?

Non. Lorsque vous configurez la protection pour une instance SQL Server, si vous sélectionnez l’option au niveau du serveur, toutes les bases de données sont ajoutées. Toutefois, si vous ajoutez des bases de données à une instance SQL Server après avoir configuré la protection, vous devez ajouter manuellement les nouvelles bases de données pour les protéger. Les bases de données ne sont pas automatiquement incluses dans la protection configurée.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Si je modifie le mode de récupération, comment redémarrer la protection ?

Déclenchez une sauvegarde complète. Les sauvegardes de fichier journal commencent comme prévu.

### <a name="can-i-protect-sql-always-on-availability-groups-where-the-primary-replica-is-on-premises"></a>Puis-je protéger les groupes de disponibilité SQL Always On dans lesquels le réplica principal est sur site ?

Non. Sauvegarde Azure protège les serveurs SQL en cours d’exécution dans Azure. Si un groupe de disponibilité (AG) est réparti entre les machines Azure et sur site, l’AG peut être protégé uniquement si le réplica principal est en cours d’exécution dans Azure. En outre, Sauvegarde Azure protège uniquement les nœuds en cours d’exécution dans la même région Azure que le coffre Recovery Services.

### <a name="can-i-protect-sql-always-on-availability-groups-which-are-spread-across-azure-regions"></a>Puis-je protéger des groupes de disponibilité AlwaysOn SQL qui sont répartis entre des régions Azure ?
Le coffre Recovery Services de Sauvegarde Azure peut détecter et protéger tous les nœuds qui se trouvent dans la même région que le coffre Recovery Services. Si vous avez un groupe de disponibilité AlwaysOn SQL s’étendant sur plusieurs régions Azure, vous devez configurer la sauvegarde à partir de la région où se trouve le nœud principal. Sauvegarde Azure pourra alors détecter et protéger toutes les bases de données du groupe de disponibilité selon la préférence de sauvegarde. Si la préférence de sauvegarde n’est pas satisfaite, les sauvegardes échouent et vous recevez l’alerte d’échec.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la sauvegarde Azure, consultez l’exemple Azure PowerShell sur la sauvegarde des machines virtuelles chiffrées.

> [!div class="nextstepaction"]
> [Sauvegarde d’une machine virtuelle chiffrée](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
