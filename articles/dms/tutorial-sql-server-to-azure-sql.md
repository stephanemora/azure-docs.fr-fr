---
title: 'Tutoriel : Migrer SQL Server hors connexion vers Azure SQL Database'
titleSuffix: Azure Database Migration Service
description: Découvrez comment effectuer une migration de SQL Server vers Azure SQL Database hors connexion à l’aide d’Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/03/2021
ms.openlocfilehash: 0383b76a08c5c56c27bfbc7f83bf786b4b452849
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110796703"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-using-dms"></a>Tutoriel : Migrer SQL Server vers Azure SQL Database à l’aide de DMS

Vous pouvez utiliser Azure Database Migration Service pour migrer les bases de données d’une instance SQL Server vers [Azure SQL Database](/azure/sql-database/). Dans ce tutoriel, vous allez effectuer la migration de la base de données [Adventureworks2016](/sql/samples/adventureworks-install-configure#download-backup-files), qui a été restaurée sur une instance locale de SQL Server 2016 (ou ultérieur), vers une base de données unique ou mise en pool dans Azure SQL Database à l’aide d’Azure Database Migration Service.

Vous apprendrez à :
> [!div class="checklist"]
>
> - À l’aide de l’Assistant Migration de données, évaluez votre base de données locale afin de vérifier la présence d’éventuels problèmes bloquants.
> - Utilisez l’Assistant Migration de données pour effectuer la migration du schéma de l’exemple de base de données. 
> - Inscrivez le fournisseur de ressources Azure DataMigration.
> - Créer une instance Azure Database Migration Service.
> - Créer un projet de migration en utilisant Azure Database Migration Service.
> - Exécuter la migration.
> - Surveiller la migration.


## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- Téléchargez et installez [SQL Server 2016 ou une version ultérieure](https://www.microsoft.com/sql-server/sql-server-downloads).
- Activez le protocole TCP/IP, qui est désactivé par défaut pendant l’installation de SQL Server Express, en suivant les instructions de l’article [Activer ou désactiver un protocole réseau de serveur](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Créez une base de données dans Azure SQL Database, en suivant les indications de l’article [Créer une base de données dans Azure SQL Database à l’aide du portail Azure](../azure-sql/database/single-database-create-quickstart.md). Dans ce tutoriel, le nom de la base de données Azure SQL devrait être **AdventureWorksAzure**, mais vous pouvez lui attribuer un autre nom si vous le souhaitez.

    > [!NOTE]
    > Si vous utilisez SQL Server Integration Services (SSIS) pour migrer la base de données de catalogue de vos projets ou de vos packages SSIS (SSISDB) entre SQL Server et Azure SQL Database, la base de données SSISDB de destination sera créée et gérée automatiquement lorsque vous provisionnerez SSIS dans Azure Data Factory (ADF). Pour plus d’informations sur la migration des packages SSIS, consultez l’article [Migrer des packages SQL Server Integration Services vers Azure](./how-to-migrate-ssis-packages.md).
  
- Téléchargez et installez la dernière version de l’[Assistant Migration de données](https://www.microsoft.com/download/details.aspx?id=53595).
- Créez un Réseau virtuel Microsoft Azure pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité site à site à vos serveurs sources locaux via [ExpressRoute](../expressroute/expressroute-introduction.md) ou un [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Pour plus d’informations sur la création d’un réseau virtuel, consultez la [documentation sur le réseau virtuel](../virtual-network/index.yml), en particulier les articles sur le démarrage rapide, qui fournissent des informations pas à pas.

    > [!NOTE]
    > Pendant la configuration du réseau virtuel, si vous utilisez ExpressRoute avec le peering réseau à Microsoft, ajoutez ces [points de terminaison](../virtual-network/virtual-network-service-endpoints-overview.md) au sous-réseau où doit être provisionné le service :
    >
    > - Point de terminaison de base de données cible (un point de terminaison SQL ou Cosmos DB, par exemple)
    > - Point de terminaison de stockage
    > - Point de terminaison Service Bus
    >
    > Cette configuration est nécessaire car Azure Database Migration Service ne dispose pas d’une connectivité Internet.
    >
    >En l'absence de connectivité de site à site entre le réseau local et Azure, ou si la bande passante de la connectivité de site à site est limitée, vous pouvez utiliser Azure Database Migration Service en mode hybride (préversion). Le mode hybride utilise un Worker de migration local et une instance d'Azure Database Migration Service exécutée dans le cloud. Pour créer une instance d'Azure Database Migration Service en mode hybride, consultez l'article [Créer une instance d'Azure Database Migration Service en mode hybride à l'aide du portail Azure](./quickstart-create-data-migration-service-hybrid-portal.md).

- Vérifiez que vos règles de sécurité de sortie du groupe de sécurité réseau de réseau virtuel ne bloquent pas le port de sortie 443 de ServiceTag pour ServiceBus, Storage et AzureMonitor. Pour plus d’informations sur le filtrage du trafic de groupe de sécurité réseau de réseau virtuel Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Configurez votre [pare-feu Windows pour accéder au moteur de base de données](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Ouvrez votre pare-feu Windows pour permettre à Azure Database Migration Service d'accéder à l'instance source de SQL Server via le port TCP 1433 (par défaut). Si votre instance par défaut écoute sur un autre port, ajoutez ce dernier au pare-feu.
- Si vous exécutez plusieurs instances nommées de SQL Server avec des ports dynamiques, vous pouvez activer le service SQL Browser et autoriser l’accès au port UDP 1434 à travers vos pare-feu, de sorte qu’Azure Database Migration Service puisse se connecter à une instance nommée sur votre serveur source.
- Lorsque vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.
- Créez une [règle de pare-feu](../azure-sql/database/firewall-configure.md) IP au niveau du serveur pour Azure SQL Database afin de permette à Azure Database Migration Service d’accéder aux bases de données cibles. Fournissez la plage de sous-réseau du réseau virtuel utilisé pour Azure Database Migration Service.
- Assurez-vous que les informations d’identification utilisées pour se connecter à une instance SQL Server source disposent des autorisations [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Vérifiez que les informations d’identification utilisées pour se connecter à l’instance Azure SQL Database cible disposent des autorisations [CONTROL DATABASE](/sql/t-sql/statements/grant-database-permissions-transact-sql) pour les bases de données cibles.

    > [!IMPORTANT]
    > La création d’une instance d’Azure Database Migration Service nécessite l’accès à des paramètres de réseau virtuel qui ne font normalement pas partie du même groupe de ressources. Par conséquent, l’utilisateur qui crée une instance de DMS a besoin d’une autorisation au niveau de l’abonnement. Pour créer les rôles requis, que vous pouvez affecter selon vos besoins, exécutez le script suivant :
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="assess-your-on-premises-database"></a>Évaluer votre base de données locale

Avant de pouvoir migrer les données d’une instance SQL Server vers une base de données unique ou mise en pool dans Azure SQL Database, vous devez évaluer la base de données SQL Server pour détecter les problèmes bloquants susceptibles d’empêcher la migration. Une fois que vous avez téléchargé et installé l’Assistant Migration de données, suivez les étapes décrites dans l’article [Évaluation de la migration de SQL Server](/sql/dma/dma-assesssqlonprem) pour évaluer la base de données locale. Un résumé de la procédure est présenté ci-dessous :

1. Dans l’Assistant Migration des données, sélectionnez l’icône Nouveau (+), puis sélectionnez le type de projet **Évaluation**.
2. Spécifiez un nom de projet SDK. Dans la liste déroulante **Type d’évaluation**, sélectionnez **Moteur de base de données**. Dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server**. Dans la zone de texte **Type de serveur source**, sélectionnez **Azure SQL Database**, puis sélectionnez **Créer** pour créer le projet.

    Quand vous évaluez la base de données SQL Server source qui doit faire l’objet d’une migration vers une base de données unique ou mise en pool dans Azure SQL Database, vous pouvez choisir l’un des types de rapport d’évaluation suivants, ou les deux :

   - Vérifier la compatibilité de la base de données
   - Vérifier la parité de fonctionnalité

    Les deux types de rapports sont sélectionnés par défaut.

3. Dans l’Assistant Migration des données, dans l’écran **Options**, sélectionnez **suivant**.
4. Dans l’écran **Sélectionner les sources**, dans la boîte de dialogue **Se connecter à un serveur**, fournissez les détails de connexion à votre SQL Server, puis sélectionnez **Se connecter**.
5. Dans la boîte de dialogue **Ajouter des sources**, sélectionnez **Adventureworks2016**, **Ajouter**, puis **Démarrer l’évaluation**.

    > [!NOTE]
    > Si vous utilisez SSIS, sachez que l’Assistant Migration de données ne prend pas en charge l’évaluation de la SSISDB source. Toutefois, les projets ou packages SSIS seront évalués/validés lors de leur redéploiement vers la SSISDB de destination qui est hébergée par Azure SQL Database. Pour plus d’informations sur la migration des packages SSIS, consultez l’article [Migrer des packages SQL Server Integration Services vers Azure](./how-to-migrate-ssis-packages.md).

    Une fois l’évaluation terminée, les résultats s’affichent comme illustré dans le graphique suivant :

    ![Évaluer la migration des données](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Pour les bases de données Azure SQL Database, les évaluations vous permettent d’identifier les problèmes de parité des fonctionnalités, ainsi que les problèmes de blocage de la migration pour un déploiement sur une base de données unique ou mise en pool.

    - La catégorie de **parité de fonctionnalité SQL Server** fournit un ensemble complet de recommandations, d’approches alternatives disponibles dans Azure et de procédures d’atténuation pour vous aider à planifier les efforts de vos projets de migration.
    - La catégorie de **problèmes de compatibilité** indique les fonctionnalités partiellement prises en charge ou non prises en charge qui reflètent les problèmes de compatibilité susceptibles de bloquer la migration des bases de données SQL Server vers Azure SQL Database. Des recommandations sont également fournies pour vous aider à résoudre ces problèmes.

6. Passez en revue les résultats d’évaluation en ce qui concerne les problèmes de blocage de migration et les problèmes de parité de fonctionnalité en sélectionnant les options spécifiques.

## <a name="migrate-the-sample-schema"></a>Migrer l’exemple de schéma

Une fois que vous êtes à l’aise avec l’évaluation et que vous pensez que la base de données sélectionnée est un candidat viable pour la migration vers une base de données unique ou mise en pool dans Azure SQL Database, utilisez DMA pour migrer le schéma vers Azure SQL Database.

> [!NOTE]
> Avant de créer un projet de migration dans l’Assistant Migration de données, vérifiez que vous avez bien provisionné une base de données dans Azure, comme indiqué dans les prérequis. 

> [!IMPORTANT]
> Si vous utilisez SSIS, sachez que l’Assistant Migration de données ne prend pas en charge la migration de la SSISDB source. Toutefois, vous pouvez redéployer vos projets ou packages SSIS vers la SSISDB de destination qui est hébergée par Azure SQL Database. Pour plus d’informations sur la migration des packages SSIS, consultez l’article [Migrer des packages SQL Server Integration Services vers Azure](./how-to-migrate-ssis-packages.md).

Pour effectuez la migration du schéma **Adventureworks2016** vers une base de données unique ou une base de données mise en pool Azure SQL Database, effectuez les étapes suivantes :

1. Dans Data Migration Assistant, sélectionnez l’icône Nouveau (+), puis sous **Type de projet**, sélectionnez **Migration**.
2. Spécifiez un nom de projet, dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server** puis, dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database**.
3. Sous **Étendue de la migration**, sélectionnez **Schéma uniquement**.

    Une fois les étapes précédentes effectuées, l’interface de l’Assistant Migration des données doit apparaître comme indiqué dans le graphique suivant :

    ![Créer un projet d’Assistant Migration des données](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Sélectionnez **Créer** pour créer le projet.
5. Dans l’Assistant Migration des données, spécifiez les détails de connexion source de votre SQL Server, sélectionnez **Se connecter**, puis sélectionnez la base de données **Adventureworks2016**.

    ![Détails de connexion source de l’Assistant Migration des données](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Sélectionnez **Suivant**, sous **Se connecter au serveur cible**, spécifiez les détails de connexion cible de la base de données Azure SQL, sélectionnez **Se connecter**, puis sélectionnez la base de données **AdventureWorksAzure** que vous avez pré-approvisionnée dans Azure SQL Database.

    ![Détails de connexion cible de l’Assistant Migration des données](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Sélectionnez **Suivant** pour accéder à l’écran **Sélectionner des objets** dans lequel vous pouvez spécifier les objets de schéma dans la base de données **Adventureworks2016** à déployer sur Azure SQL Database.

    Par défaut, tous les objets sont sélectionnés.

    ![Générer des scripts SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Sélectionnez **Générer un script SQL** pour créer les scripts SQL, puis recherchez d’éventuelles erreurs dans les scripts.

    ![Script de schéma](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Sélectionnez **Déployer le schéma** pour déployer sur Azure SQL Database puis, une fois le schéma déployé, recherchez d’éventuelles anomalies sur le serveur cible.

    ![Déployer le schéma](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration

1. Connectez-vous au portail Azure. Recherchez et sélectionnez **Abonnements**.

   ![Afficher les abonnements au portail](media/tutorial-sql-server-to-azure-sql/portal-select-subscription-1.png)

2. Sélectionnez l’abonnement dans lequel vous voulez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.

    ![Afficher les fournisseurs de ressources](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Recherchez « migration », puis sélectionnez **Inscrire** pour **Microsoft.DataMigration**.

    ![S’inscrire auprès du fournisseur de ressources](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Créer une instance

1. Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**. Recherchez et sélectionnez **Azure Database Migration Service**.

    ![Place de marché Azure](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.

    ![Créer une instance Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-1.png)
  
3. Sous l’onglet Informations de base de la page **Créer un service de migration** :

     - Sélectionnez l’abonnement.
     - Créez un groupe de ressources ou sélectionnez-en un déjà existant.
     - Spécifiez un nom pour l’instance d’Azure Database Migration Service.
     - Sélectionnez l’emplacement dans lequel vous souhaitez créer l’instance Azure Database Migration Service.
     - Choisissez **Azure** comme mode de service.
     - Sélectionnez un niveau tarifaire. Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    ![Configurer des paramètres de base pour l’instance Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-settings-2.png)

     - Sélectionnez **Suivant : Réseau**.

4. Sous l’onglet Réseau de la page **Créer un service de migration** :

    - Sélectionnez un réseau virtuel existant ou créez-en un. Le réseau virtuel fournit à Azure Database Migration Service un accès au serveur SQL Server source et à l’instance Azure SQL Database cible. Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez l’article [Créer un réseau virtuel au moyen du portail Azure](../virtual-network/quick-create-portal.md).

    ![Configurer des paramètres réseau pour l’instance Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-settings-3.png)

    - Sélectionnez **Vérifier + créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois le service créé, recherchez-le dans le portail Azure, ouvrez-le, puis créez un projet de migration.

1. Dans le menu du portail Azure, sélectionnez **Tous les services**. Recherchez et sélectionnez **Azure Database Migration Services**.

     ![Localiser toutes les instances Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Dans l’écran **Azure Database Migration Services**, sélectionnez l’instance Azure Database Migration Service que vous avez créée.

3. Sélectionnez **Nouveau projet de migration**.

     ![Localisez votre instance Azure Database Migration Service.](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server**. Dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database**. Enfin, dans la zone de texte **Choisir un type d’activité de migration**, sélectionnez **Migration de données**.

    ![Créer un projet Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-project-2.png)

5. Sélectionnez **Créer et exécuter une activité** pour créer le projet et exécuter l’activité de migration.

## <a name="specify-source-details"></a>Spécifier les détails de la source

1. Dans l’écran **Sélectionner la source**, spécifiez les détails de connexion de l’instance SQL Server source.

    Veillez à utiliser un nom de domaine complet pour le nom de l’instance SQL Server source. Vous pouvez également utiliser l’adresse IP quand la résolution de noms DNS est impossible.

2. Si vous n’avez pas installé de certificat de confiance sur votre serveur source, cochez la case **Faire confiance au certificat de serveur**.

    Quand aucun certificat de confiance n’est installé, SQL Server génère un certificat auto-signé au démarrage de l’instance. Ce certificat permet de chiffrer les informations d’identification des connexions clientes.

    > [!CAUTION]
    > Les connexions TLS chiffrées à l’aide d’un certificat auto-signé n’offrent pas de sécurité renforcée. Elles sont vulnérables aux attaques de l’intercepteur. Ne faites jamais confiance à une connexion TLS utilisant des certificats autosignés dans un environnement de production ou sur des serveurs connectés à Internet.

    > [!IMPORTANT]
    > Si vous utilisez SSIS, sachez que Database Migration Service ne prend pas en charge la migration de la SSISDB source. Toutefois, vous pouvez redéployer vos projets ou packages SSIS vers la SSISDB de destination qui est hébergée par Azure SQL Database. Pour plus d’informations sur la migration des packages SSIS, consultez l’article [Migrer des packages SQL Server Integration Services vers Azure](./how-to-migrate-ssis-packages.md).

   ![Détails de la source](media/tutorial-sql-server-to-azure-sql/dms-source-details-2.png)
   
3. Sélectionnez **Suivant : Sélectionner des bases de données**.

## <a name="select-databases-for-migration"></a>Sélectionner des bases de données pour la migration

Sélectionnez l’ensemble des bases de données ou des bases de données spécifiques que vous souhaitez migrer vers Azure SQL Database. DMS vous fournit l’heure de migration prévue pour les bases de données sélectionnées. Si les temps d’arrêt de la migration sont acceptables, poursuivez la migration. Si les temps d’arrêt de la migration ne sont pas acceptables, envisagez de migrer vers [SQL Managed instance avec un temps d’arrêt proche de zéro](tutorial-sql-server-managed-instance-online.md) ou contactez l’[équipe DMS](mailto:DMSFeedback@microsoft.com) pour obtenir d’autres options. 

1. Choisissez la ou les bases de données que vous souhaitez migrer à partir de la liste des bases de données disponibles. 
1. Évaluez le temp d’arrêt attendu. S’il est acceptable, sélectionnez **Suivant : Sélectionner cible >>**

   ![Bases de données sources](media/tutorial-sql-server-to-azure-sql/select-database.png)



## <a name="specify-target-details"></a>Spécifier les détails de la cible

1. Dans l’écran **Sélectionner la cible**, fournissez les paramètres d’authentification à votre Azure SQL Database. 

   ![Sélectionner la cible](media/tutorial-sql-server-to-azure-sql/select-target.png)
   
   > [!NOTE]
   > L’authentification SQL est le seul type d’authentification pris en charge.

1. Sélectionnez **Suivant : Mapper aux bases de données cibles**, mappez les bases de données source et cible pour la migration.

    Si la base de données cible porte le même nom que la base de données source, Azure Database Migration Service sélectionne la base de données cible par défaut.

    ![Mapper aux bases de données cibles](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity-2.png)

1. Sélectionnez **Suivant : Configuration des paramètres de migration**, développez la liste des tables et passez en revue la liste des champs concernés.

    Azure Database Migration Service sélectionne automatiquement toutes les tables sources vides qui existent sur l’instance Azure SQL Database cible. Si vous souhaitez migrer à nouveau les tables qui contiennent déjà des données, vous devez sélectionner explicitement les tables sur ce panneau.

    ![Sélectionner des tables](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity-2.png)

1. Sélectionnez **Suivant : Récapitulatif**, passez en vue la configuration de la migration, puis, dans la zone de texte **Nom de l’activité**, spécifiez un nom pour l’activité de migration.

    ![Choisir l’option de validation](media/tutorial-sql-server-to-azure-sql/dms-configuration-2.png)

## <a name="run-the-migration"></a>Exécuter la migration

- Sélectionnez **Démarrer la migration**.

    La fenêtre d’activité de migration s’affiche et l’**état** de l’activité est **En attente**.

    ![État de l’activité](media/tutorial-sql-server-to-azure-sql/dms-activity-status-1.png)

## <a name="monitor-the-migration"></a>Surveiller la migration

1. Dans l’écran d’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage jusqu’à voir **l’état** de la migration s’afficher comme **Completed** (Terminé).

    ![État d’activité terminé](media/tutorial-sql-server-to-azure-sql/dms-completed-activity-1.png)

2. Vérifiez les bases de données cibles de l’instance cible **Azure SQL Database**.

### <a name="additional-resources"></a>Ressources supplémentaires

- Pour plus d’informations sur Azure Database Migration Service, consultez l’article [Qu’est-ce qu’Azure Database Migration Service ?](./dms-overview.md).
- Pour plus d’informations sur Azure SQL Database, consultez l’article [Qu’est-ce que le service Azure SQL Database ?](../azure-sql/database/sql-database-paas-overview.md).
