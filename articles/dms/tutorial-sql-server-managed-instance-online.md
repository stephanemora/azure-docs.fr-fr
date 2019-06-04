---
title: 'Didacticiel : Utiliser Azure Database Migration Service pour effectuer une migration en ligne de SQL Server vers une instance managée d’Azure SQL Database | Microsoft Docs'
description: Découvrez comment effectuer une migration en ligne d’une instance locale de SQL Server vers une instance managée d’Azure SQL Database à l’aide d’Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/22/2019
ms.openlocfilehash: 1229ff3221deb49601dec3cd40b556ea367fc4c9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240701"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Didacticiel : Effectuer la migration en ligne de SQL Server vers une instance managée d’Azure SQL Database à l’aide de DMS

Vous pouvez utiliser le service Azure Database Migration Service pour effectuer la migration des bases de données d’une instance locale de SQL Server vers une [instance managée d’Azure SQL Database](../sql-database/sql-database-managed-instance.md), pour un temps d’arrêt minimal. Pour connaître des méthodes supplémentaires pouvant nécessiter un effort manuel, consultez l’article [Migration d’une instance SQL Server vers une instance managée d’Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

Dans ce tutoriel, vous allez effectuer la migration de la base de données **Adventureworks2012** entre une instance locale de SQL Server et une instance managée d’Azure SQL Database, avec un temps d’arrêt minimal, à l’aide d’Azure Database Migration Service.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
>
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration et lancer une migration en ligne en utilisant Azure Database Migration Service.
> * Surveiller la migration.
> * Basculer la migration lorsque vous êtes prêt.

> [!IMPORTANT]
> Pour effectuer une migration en ligne entre SQL Server et une instance managée d’Azure SQL Database à l’aide d’Azure Database Migration Service, vous devez fournir la sauvegarde de base de données complète et les sauvegardes de fichiers journaux suivantes dans le partage réseau SMB que le service peut utiliser pour la migration de vos bases de données. Azure Database Migration Service ne lance pas les sauvegardes. Il utilise les sauvegardes existantes (qui peuvent déjà faire partie de votre plan de reprise d’activité) pour la migration.
> N’oubliez pas d’effectuer des [sauvegardes à l’aide de l’option WITH CHECKSUM](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Par ailleurs, vous ne devez pas ajouter plusieurs sauvegardes (c’est-à-dire la sauvegarde complète et les journaux de transactions) sur un même support de sauvegarde. Vous devez placer chaque sauvegarde sur un fichier de sauvegarde distinct.

> [!NOTE]
> Effectuer une migration en ligne à l’aide d’Azure Database Migration Service nécessite la création d’une instance basée sur le niveau tarifaire Premium.

> [!IMPORTANT]
> Pour une expérience de migration optimale, Microsoft vous recommande de créer une instance Azure Database Migration Service dans la même région Azure que la base de données cible. Le déplacement des données entre les régions ou les zones géographiques peut ralentir le processus de migration et introduire des erreurs.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Cet article explique comment effectuer la migration en ligne de SQL Server vers une instance managée d’Azure SQL Database. Pour une migration hors connexion, consultez [Migrer SQL Server vers une instance managée d’Azure SQL Database hors connexion à l’aide de DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

* Créez un réseau virtuel Azure (VNet) pour le service Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux via [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou une passerelle [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Découvrez les topologies de réseau pour les migrations d’instances managées d’Azure SQL Database à l’aide du service Azure Database Migration Service](https://aka.ms/dmsnetworkformi). Pour plus d’informations sur la création d’un réseau virtuel, consultez la [documentation sur le réseau virtuel](https://docs.microsoft.com/azure/virtual-network/), en particulier les articles sur le démarrage rapide, qui fournissent des informations pas à pas.

    > [!NOTE]
    > Pendant la configuration du réseau virtuel, si vous utilisez ExpressRoute avec le peering réseau à Microsoft, ajoutez ces [points de terminaison](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de service au sous-réseau où doit être provisionné le service :
    >
    > * Point de terminaison de base de données cible (un point de terminaison SQL ou Cosmos DB, par exemple)
    > * Point de terminaison de stockage
    > * Point de terminaison Service Bus
    >
    > Cette configuration est nécessaire, car Azure Database Migration Service ne dispose pas d’une connectivité Internet.

* Vérifiez que les règles du groupe de sécurité réseau de votre réseau virtuel ne bloquent pas les ports de communication entrants suivants d’Azure Database Migration Service : 443, 53, 9354, 445, 12000. Pour plus d’informations sur le filtrage du trafic de groupe de sécurité réseau de réseau virtuel Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configurez [l’accès au moteur de base de données source dans votre Pare-feu Windows](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Ouvrez votre Pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au serveur SQL Server source, par défaut le port TCP 1433.
* Si vous exécutez plusieurs instances nommées de SQL Server avec des ports dynamiques, vous pouvez activer le service SQL Browser et autoriser l’accès au port UDP 1434 à travers vos pare-feu, de sorte qu’Azure Database Migration Service puisse se connecter à une instance nommée sur votre serveur source.
* Si vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration, ainsi qu’aux fichiers par le biais du port SMB 445.
* Créez une instance managée d’Azure SQL Database en suivant les indications détaillées de l’article [Créer une instance managée d’Azure SQL Database dans le Portail Azure](https://aka.ms/sqldbmi).
* Vérifiez que les comptes de connexion utilisés pour connecter l’instance SQL Server source et l’instance managée cible sont membres du rôle serveur sysadmin.
* Indiquez un partage réseau SMB qui contient tous les fichiers de sauvegarde complète de votre base de données et les fichiers de sauvegarde de fichier journal associé qu’Azure Database Migration Service peut utiliser pour la migration de base de données.
* Assurez-vous que le compte de service exécutant l’instance SQL Server source dispose de privilèges en écriture sur le partage réseau que vous avez créé, et que le compte d’ordinateur pour le serveur source a accès en lecture/écriture au même partage.
* Repérez un utilisateur Windows (et son mot de passe) disposant d’un contrôle total sur le partage réseau que vous avez créé précédemment. Azure Database Migration Service emprunte l’identité de l’utilisateur pour charger les fichiers de sauvegarde sur le conteneur de stockage Azure pour l’opération de restauration.
* Créez un ID d’application Azure Active Directory qui génère la clé d’ID d’application permettant au service DMS de se connecter à l’instance managée d’Azure SQL Database et au conteneur Stockage Azure cibles. Pour plus d’informations, consultez l’article [Utiliser le portail pour créer une application et un principal du service Azure Active Directory pouvant accéder aux ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
* Créez ou prenez note du **niveau de performance Standard**, compte Stockage Azure, qui permet à Azure Database Migration Service de charger les fichiers de sauvegarde de base de données sur les bases de données en cours de migration et de les utiliser pour celles-ci.  Veillez à créer le compte Stockage Azure dans la même région que l’instance Azure Database Migration Service créée.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration

1. Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.

    ![Afficher les abonnements au portail](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Sélectionnez l’abonnement dans lequel vous souhaitez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.

    ![Afficher les fournisseurs de ressources](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.

    ![S’inscrire auprès du fournisseur de ressources](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Créer une instance Azure Database Migration Service

1. Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez **Azure Database Migration Service**, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

     ![Place de marché Azure](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.

    ![Créer une instance Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Dans l’écran **Créer un service de migration**, spécifiez un nom pour le service, l’abonnement, et un réseau virtuel nouveau ou existant.

4. Sélectionnez l’emplacement au niveau duquel vous souhaitez créer l’instance DMS.

5. Sélectionnez un réseau virtuel existant ou créez-en un.

    Le réseau virtuel fournit au service Azure Database Migration Service un accès au serveur SQL Server source et à l’instance managée Azure SQL Database cible.

    Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez l’article [Créer un réseau virtuel à l’aide du portail Azure](https://aka.ms/DMSVnet).

    Pour plus d’informations, consultez l’article [Topologies de réseau pour les migrations d’instances managées d’Azure SQL Database à l’aide du service Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Sélectionnez une référence (SKU) dans le niveau tarifaire Premium.

    > [!NOTE]
    > Les migrations en ligne sont uniquement prises en charge lorsque vous utilisez le niveau Premium.

    Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    ![Créer un service DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois qu’une instance du service a été créée, recherchez-la dans le Portail Azure, ouvrez-la, puis créez un projet de migration.

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.

    ![Rechercher toutes les instances Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Sur l’écran **Azure Database Migration Service**, recherchez le nom de l’instance que vous avez créée, puis sélectionnez-la.

3. Sélectionnez **+ Nouveau projet de migration**.

4. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server**. Dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database Managed Instance**. Enfin, dans la zone de texte **Choisir un type d’activité**, sélectionnez **Migration de données hors connexion**.

   ![Créer un projet DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Sélectionnez **Créer et exécuter une activité** pour créer le projet.

## <a name="specify-source-details"></a>Spécifier les détails de la source

1. Dans l’écran **Détails de la source de migration**, spécifiez les détails de connexion du serveur SQL Server source.

2. Si vous n’avez pas installé de certificat approuvé sur votre serveur, cochez la case **Faire confiance au certificat de serveur**.

    Quand aucun certificat approuvé n’est installé, SQL Server génère un certificat auto-signé au démarrage de l’instance. Ce certificat permet de chiffrer les informations d’identification des connexions clientes.

    > [!CAUTION]
    > Les connexions SSL chiffrées à l’aide d’un certificat auto-signé n’offrent pas de sécurité renforcée. Elles sont vulnérables aux attaques de l’intercepteur. Vous ne devez pas compter sur SSL utilisant des certificats auto-signés dans un environnement de production ou sur des serveurs connectés à Internet.

   ![Détails de la source](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Sélectionnez **Enregistrer**.

4. Sur l’écran **Sélectionner la base de données source**, sélectionnez la base de données **Adventureworks2012** pour la migration.

   ![Sélectionner les bases de données sources](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Si vous utilisez SSIS (SQL Server Integration Services), DMS ne prend pas en charge la migration de la base de données de catalogue de vos projets/packages SSIS (SSISDB) entre SQL Server et une instance managée d’Azure SQL Database. Toutefois, vous pouvez provisionner SSIS dans ADF (Azure Data Factory) et redéployer vos projets/packages SSIS sur la base de données SSISDB de destination hébergée par l’instance managée d’Azure SQL Database. Pour plus d’informations sur la migration des packages SSIS, consultez l’article [Migrer des packages SQL Server Integration Services vers Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Sélectionnez **Enregistrer**.

## <a name="specify-target-details"></a>Spécifier les détails de la cible

1. Dans l’écran **Détails de la cible de migration**, spécifiez l’**ID d’application** et la **clé** que l’instance de DMS peut utiliser pour se connecter à l’instance managée d’Azure SQL Database et au compte Stockage Azure cibles.

    Pour plus d’informations, consultez l’article [Utiliser le portail pour créer une application et un principal du service Azure Active Directory pouvant accéder aux ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Sélectionnez l’**abonnement** contenant l’instance managée cible d’Azure SQL Database, puis sélectionnez l’instance cible.

    Si vous n’avez pas encore provisionné l’instance managée d’Azure SQL Database, sélectionnez le [lien](https://aka.ms/SQLDBMI) permettant de provisionner l’instance. Une fois l’instance managée d’Azure SQL Database prête, retournez à ce projet spécifique pour exécuter la migration.

3. Indiquez l’**utilisateur SQL** et le **mot de passe** permettant de se connecter à l’instance managée cible d’Azure SQL Database.

    ![Sélectionner la cible](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Sélectionnez **Enregistrer**.

## <a name="select-source-databases"></a>Sélectionner les bases de données sources

1. Dans l’écran **Sélectionner la base de données source**, sélectionnez la base de données source que vous souhaitez migrer.

    ![Sélectionner les bases de données sources](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Sélectionnez **Enregistrer**.

## <a name="configure-migration-settings"></a>Configurer les paramètres de migration

1. Dans l’écran **Configurer les paramètres de migration**, fournissez les informations suivantes :

    | | |
    |--------|---------|
    |**SMB Network location share** (Partage d’emplacement réseau SMB) | Le partage réseau SMB local qui contient les fichiers de sauvegarde complète de votre base de données et les fichiers de sauvegarde de fichier journal associés qu’Azure Database Migration Service peut utiliser pour la migration. Le compte de service qui exécute l’instance source de SQL Server doit avoir des privilèges de lecture et d’écriture pour ce partage réseau. Fournissez un nom de domaine complet ou les adresses IP du serveur dans le partage réseau, par exemple, « \\\nomserveur.nomdomaine.com\dossiersauvegarde » ou « \\\adresse IP\dossiersauvegarde ».|
    |**Nom d'utilisateur** | Assurez-vous que l’utilisateur Windows dispose d’un contrôle total sur le partage réseau que vous avez fourni précédemment. Azure Database Migration Service emprunte l’identité de l’utilisateur pour charger les fichiers de sauvegarde sur le conteneur de stockage Azure pour l’opération de restauration. |
    |**Mot de passe** | Mot de passe pour l’utilisateur. |
    |**Subscription of the Azure Storage Account** (Abonnement du compte Stockage Azure) | Sélectionnez l’abonnement qui contient le compte Stockage Azure. |
    |**Compte Stockage Azure** | Sélectionnez le compte Stockage Azure sur lequel Azure Database Migration Service peut charger les fichiers de sauvegarde à partir du partage réseau SMB et qu’il peut utiliser pour la migration de base de données.  Nous vous recommandons de sélectionner le compte de stockage se trouvant dans la même région qu’Azure Database Migration Service pour bénéficier de performances de chargement de fichiers optimales. |

    ![Configurer les paramètres de migration](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

2. Sélectionnez **Enregistrer**.

## <a name="review-the-migration-summary"></a>Examiner le récapitulatif de la migration

1. Dans l’écran **Récapitulatif de la migration**, spécifiez un nom pour l’activité de migration dans la zone de texte **Nom de l’activité**.

2. Examinez et vérifiez les détails associés au projet de migration.

    ![Récapitulatif du projet de migration](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Exécuter et surveiller la migration

1. Sélectionnez **Exécuter la migration**.

2. Dans l’écran de l’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage.

   ![Activité de migration en cours](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Vous pouvez développer davantage les catégories de bases de données et de connexions pour surveiller l’état de la migration des objets serveur respectifs.

   ![Activité de migration en cours](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Exécution du basculement de migration

Une fois la sauvegarde de base de données complète restaurée sur l’instance managée cible d’Azure SQL Database, la base de données est disponible pour une migration à basculement.

1. Lorsque vous êtes prêt à effectuer la migration de base de données en ligne, sélectionnez **Démarrer le basculement**.

2. Arrêtez tout le trafic entrant dans les bases de données sources.

3. Effectuez la [sauvegarde de la fin du journal], mettez le fichier de sauvegarde à disposition dans le partage réseau SMB, puis attendez que cette sauvegarde de fichier journal finale soit restaurée.

    À ce stade, vous devez voir le paramètre **Modifications en attente** défini sur 0.

4. Sélectionnez **Confirmer**, puis **Appliquer**.

    ![Préparation à la fin du basculement](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Quand l’état de la migration de base de données indique **Terminé**, connectez vos applications à la nouvelle instance managée cible d’Azure SQL Database.

    ![Basculement terminé](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour accéder à un tutoriel vous expliquant comment migrer une base de données vers une instance managée à l’aide de la commande T-SQL RESTORE, consultez [Restaurer une sauvegarde vers une instance managée à l’aide de la commande restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Pour plus d’informations sur la notion d’instance managée, consultez [Qu’est-ce qu’une instance managée](../sql-database/sql-database-managed-instance.md).
- Pour plus d’informations sur la connexion d’applications à une instance managée, consultez [Connecter des applications](../sql-database/sql-database-managed-instance-connect-app.md).
