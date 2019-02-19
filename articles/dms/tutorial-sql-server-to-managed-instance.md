---
title: 'Tutoriel : Utiliser DMS pour migrer vers une instance managée d’Azure SQL Database | Microsoft Docs'
description: Apprenez à migrer une instance locale de SQL Server vers une instance managée d’Azure SQL Database à l’aide du service Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 02/08/2019
ms.openlocfilehash: b1c635e5b4374b0369a0cf3d1cbff6d8087085a6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990238"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-managed-instance-offline-using-dms"></a>Tutoriel : Migrer SQL Server vers une instance managée d’Azure SQL Database hors connexion à l’aide de DMS

Vous pouvez utiliser le service Azure Database Migration Service pour migrer les bases de données d’une instance locale de SQL Server vers une [instance managée d’Azure SQL Database](../sql-database/sql-database-managed-instance.md). Pour connaître des méthodes supplémentaires pouvant nécessiter un effort manuel, consultez l’article [Migration d’une instance SQL Server vers une instance managée d’Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

Dans ce tutoriel, vous migrez la base de données **Adventureworks2012** d’une instance locale de SQL Server vers une instance managée d’Azure SQL Database à l’aide du service Azure Database Migration Service.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> - Créer une instance Azure Database Migration Service.
> - Créer un projet de migration en utilisant Azure Database Migration Service.
> - Exécuter la migration.
> - Surveiller la migration.
> - Télécharger un rapport de migration.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Cet article décrit une migration hors connexion de SQL Server vers une instance managée d’Azure SQL Database. Pour une migration en ligne, consultez [Migrer SQL Server vers une instance managée d’Azure SQL Database en ligne à l’aide de DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- Créez un réseau virtuel pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux à l’aide de la fonction [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Découvrez les topologies de réseau pour les migrations d’instances managées d’Azure SQL Database à l’aide du service Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Assurez-vous que les règles de groupe de sécurité Réseau virtuel Microsoft Azure ne bloquent pas les ports de communication 443, 53, 9354, 445 et 12000. Pour plus d’informations sur le filtrage de groupe de sécurité Réseau virtuel Microsoft Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurez [l’accès au moteur de base de données source dans votre Pare-feu Windows](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Ouvrez votre Pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au serveur SQL Server source, par défaut le port TCP 1433.
- Si vous exécutez plusieurs instances nommées de SQL Server avec des ports dynamiques, vous pouvez activer le service SQL Browser et autoriser l’accès au port UDP 1434 à travers vos pare-feu, de sorte qu’Azure Database Migration Service puisse se connecter à une instance nommée sur votre serveur source.
- Si vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration, ainsi qu’aux fichiers par le biais du port SMB 445.
- Créez une instance managée d’Azure SQL Database en suivant les indications détaillées de l’article [Créer une instance managée d’Azure SQL Database dans le Portail Azure](https://aka.ms/sqldbmi).
- Vérifiez que les comptes de connexion utilisés pour connecter l’instance SQL Server source et l’instance managée cible sont membres du rôle serveur sysadmin.
- Créez un partage réseau qu’Azure Database Migration Service peut utiliser pour sauvegarder la base de données source.
- Assurez-vous que le compte de service exécutant l’instance SQL Server source dispose de privilèges en écriture sur le partage réseau que vous avez créé, et que le compte d’ordinateur pour le serveur source a accès en lecture/écriture au même partage.
- Repérez un utilisateur Windows (et son mot de passe) disposant d’un contrôle total sur le partage réseau que vous avez créé précédemment. Azure Database Migration Service emprunte l’identité de l’utilisateur pour charger les fichiers de sauvegarde sur le conteneur de stockage Azure pour l’opération de restauration.
- Créez un conteneur d’objets blob et récupérez son URI SAS en suivant les étapes de l’article [Gérer les ressources Azure Blob Storage avec l’Explorateur de stockage (version préliminaire)](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Veillez à sélectionner toutes les autorisations (lecture, écriture, suppression, liste) dans la fenêtre de la stratégie lors de la création de l’URI SAS. Ces détails permettent au service Azure Database Migration Service d’accéder au conteneur de votre compte de stockage afin de charger les fichiers de sauvegarde utilisés pour migrer les bases de données vers une instance managée d’Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration

1. Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.

    ![Afficher les abonnements au portail](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)        

2. Sélectionnez l’abonnement dans lequel vous souhaitez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.

    ![Afficher les fournisseurs de ressources](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.

    ![S’inscrire auprès du fournisseur de ressources](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Créer une instance Azure Database Migration Service

1. Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez **Azure Database Migration Service**, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

     ![Place de marché Azure](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.

    ![Créer une instance Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Dans l’écran **Créer un service de migration**, spécifiez un nom pour le service, l’abonnement, et un réseau virtuel nouveau ou existant.

4. Sélectionnez l’emplacement au niveau duquel vous souhaitez créer l’instance DMS.

5. Sélectionnez un réseau virtuel existant ou créez-en un.

    Le réseau virtuel fournit au service Azure Database Migration Service un accès au serveur SQL Server source et à l’instance managée d’Azure SQL Database cible.

    Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, voir l’article [Créer un réseau virtuel à l’aide du portail Azure](https://aka.ms/DMSVnet).

    Pour plus d’informations, consultez l’article [Topologies de réseau pour les migrations d’instances managées d’Azure SQL Database à l’aide du service Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Sélectionnez un niveau tarifaire.

    Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    ![Créer un service DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois qu’une instance du service a été créée, recherchez-la dans le Portail Azure, ouvrez-la, puis créez un projet de migration.

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.

    ![Rechercher toutes les instances Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Sur l’écran **Azure Database Migration Service**, recherchez le nom de l’instance que vous avez créée, puis sélectionnez-la.

3. Sélectionnez **+ Nouveau projet de migration**.

4. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server**. Dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database Managed Instance**. Enfin, dans la zone de texte **Choisir un type d’activité**, sélectionnez **Migration de données hors connexion**.

   ![Créer un projet DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Sélectionnez **Créer** pour créer le projet.

## <a name="specify-source-details"></a>Spécifier les détails de la source

1. Dans l’écran **Détails de la source de migration**, spécifiez les détails de connexion du serveur SQL Server source.

2. Si vous n’avez pas installé de certificat approuvé sur votre serveur, cochez la case **Faire confiance au certificat de serveur**.

    Quand aucun certificat approuvé n’est installé, SQL Server génère un certificat auto-signé au démarrage de l’instance. Ce certificat permet de chiffrer les informations d’identification des connexions clientes.

    > [!CAUTION]
    > Les connexions SSL chiffrées à l’aide d’un certificat auto-signé n’offrent pas de sécurité renforcée. Elles sont vulnérables aux attaques de l’intercepteur. Vous ne devez pas compter sur SSL utilisant des certificats auto-signés dans un environnement de production ou sur des serveurs connectés à Internet.

   ![Détails de la source](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Sélectionnez **Enregistrer**.

4. Sur l’écran **Sélectionner la base de données source**, sélectionnez la base de données **Adventureworks2012** pour la migration.

   ![Sélectionner les bases de données sources](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Si vous utilisez SSIS (SQL Server Integration Services), DMS ne prend pas en charge la migration de la base de données de catalogue de vos projets/packages SSIS (SSISDB) entre SQL Server et une instance managée d’Azure SQL Database. Toutefois, vous pouvez provisionner SSIS dans ADF (Azure Data Factory) et redéployer vos projets/packages SSIS sur la base de données SSISDB de destination hébergée par l’instance managée d’Azure SQL Database. Pour plus d’informations sur la migration des packages SSIS, consultez l’article [Migrer des packages SQL Server Integration Services vers Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Sélectionnez **Enregistrer**.

## <a name="specify-target-details"></a>Spécifier les détails de la cible

1. Dans l’écran **Détails de la cible de migration**, spécifiez les détails de connexion de la cible, notamment l’instance managée d’Azure SQL Database préprovisionnée vers laquelle vous migrez la base de données **AdventureWorks2012**.

    Si vous n’avez pas encore provisionné l’instance managée d’Azure SQL Database, sélectionnez **Non** pour obtenir un lien permettant de provisionner l’instance. Vous pouvez néanmoins procéder à la création du projet. Une fois l’instance managée d’Azure SQL Database prête, retournez à ce projet spécifique pour exécuter la migration.

       ![Select Target](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Sélectionnez **Enregistrer**.

## <a name="select-source-databases"></a>Sélectionner les bases de données sources

1. Dans l’écran **Sélectionner la base de données source**, sélectionnez la base de données source que vous souhaitez migrer.

    ![Sélectionner les bases de données sources](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Sélectionnez **Enregistrer**.

## <a name="select-logins"></a>Sélectionner des connexions

1. Dans l’écran **Sélectionner des connexions**, sélectionnez les connexions que vous souhaitez migrer.

    >[!NOTE]
    >Cette version prend uniquement en charge la migration des connexions SQL.

    ![Sélectionner des connexions](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Sélectionnez **Enregistrer**.

## <a name="configure-migration-settings"></a>Configurer les paramètres de migration

1. Dans l’écran **Configurer les paramètres de migration**, fournissez les informations suivantes :

    | | |
    |--------|---------|
    |**Choisir une option de sauvegarde source** | Lorsque vous disposez déjà des fichiers de sauvegarde complète qu’Azure Database Migration Service doit utiliser pour la migration de base de données, choisissez l’option **Je fournirai les derniers fichiers de sauvegarde**. Si vous souhaitez que DMS crée la sauvegarde complète des bases de données sources et l’utilise pour la migration, choisissez l’option **J’autorise Azure Database Migration Service à créer des fichiers de sauvegarde**. |
    |**Partage d’emplacement réseau** | Partage réseau SMB local qu’Azure Database Migration Service peut utiliser pour la sauvegarde des bases de données sources. Le compte de service qui exécute l’instance source de SQL Server doit avoir des privilèges d’écriture pour ce partage réseau. Fournissez un nom de domaine complet ou les adresses IP du serveur dans le partage réseau, par exemple, « \\\nomserveur.nomdomaine.com\dossiersauvegarde » ou « \\\adresse IP\dossiersauvegarde ».|
    |**Nom d'utilisateur** | Assurez-vous que l’utilisateur Windows dispose d’un contrôle total sur le partage réseau que vous avez fourni précédemment. Azure Database Migration Service emprunte l’identité de l’utilisateur pour charger les fichiers de sauvegarde sur le conteneur de stockage Azure pour l’opération de restauration. Si vous avez sélectionné pour la migration des bases de données compatibles avec Transparent Data Encryption (TDE), l’utilisateur Windows ci-dessus doit correspondre au compte Administrateur intégré, et [Contrôle de compte d’utilisateur](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) doit être désactivé pour qu’Azure Database Migration Service puisse charger et supprimer les fichiers de certificat. |
    |**Mot de passe** | Mot de passe pour l’utilisateur. |
    |**Paramètres du compte de stockage** | URI SAP qui permet au service Azure Database Migration Service d’accéder au conteneur de votre compte de stockage vers lequel le service charge les fichiers de sauvegarde, et qui est utilisé pour migrer les bases de données vers l’instance managée d’Azure SQL Database. [Découvrez comment obtenir l’URI SAS du conteneur d’objets blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**Paramètres TDE** | Si vous migrez les bases de données sources compatibles avec TDE (Transparent Data Encryption), vous devez disposer de privilèges d’accès en écriture sur l’instance managée cible d’Azure SQL Database.  Dans le menu déroulant, sélectionnez l’abonnement dans lequel l’instance managée d’Azure SQL Database a été provisionnée.  Dans le second menu déroulant, sélectionnez **l’instance Azure SQL Database Managed Instance** cible. |

    ![Configurer les paramètres de migration](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Sélectionnez **Enregistrer**.

## <a name="review-the-migration-summary"></a>Examiner le récapitulatif de la migration

1. Dans l’écran **Récapitulatif de la migration**, spécifiez un nom pour l’activité de migration dans la zone de texte **Nom de l’activité**.

2. Développez la section **Option de validation** pour afficher l’écran **Choisir l’option de validation**, spécifiez s’il faut valider la base de données migrée pour l’exactitude des requêtes, puis sélectionnez **Enregistrer**.

3. Examinez et vérifiez les détails associés au projet de migration.

    ![Récapitulatif du projet de migration](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Sélectionnez **Enregistrer**.

## <a name="run-the-migration"></a>Exécuter la migration

- Sélectionnez **Exécuter la migration**.

  La fenêtre d’activité de migration s’affiche, dans laquelle l’état de l’activité est **En attente**.

## <a name="monitor-the-migration"></a>Surveiller la migration

1. Dans l’écran de l’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage.
 
   ![Activité de migration en cours](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Vous pouvez développer davantage les catégories de bases de données et de connexions pour surveiller l’état de la migration des objets serveur respectifs.

   ![Activité de migration en cours](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Une fois la migration terminée, sélectionnez **Télécharger le rapport** pour obtenir un rapport mentionnant les détails associés au processus de migration.

3. Vérifiez la base de données cible dans l’environnement de l’instance managée cible d’Azure SQL Database.

## <a name="next-steps"></a>Étapes suivantes

- Pour accéder à un tutoriel vous expliquant comment migrer une base de données vers une instance managée à l’aide de la commande T-SQL RESTORE, consultez [Restaurer une sauvegarde vers une instance managée à l’aide de la commande restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Pour plus d’informations sur la notion d’instance managée, consultez [Qu’est-ce qu’une instance managée](../sql-database/sql-database-managed-instance.md).
- Pour plus d’informations sur la connexion d’applications à une instance managée, consultez [Connecter des applications](../sql-database/sql-database-managed-instance-connect-app.md).