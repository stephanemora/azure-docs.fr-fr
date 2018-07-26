---
title: Utiliser DMS pour migrer vers Azure SQL Database Managed Instance | Microsoft Docs
description: Découvrez comment migrer une instance locale de SQL Server vers Azure SQL Database Managed Instance à l’aide d’Azure Database Migration Service.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/12/2018
ms.openlocfilehash: c911b096af6662e11afb4c4262b92c239d252c36
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990225"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Migrer SQL Server vers Azure SQL Database Managed Instance à l’aide de DMS
Vous pouvez utiliser Azure Database Migration Service pour migrer les bases de données d’une instance SQL Server locale vers une instance [Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance.md). Pour des méthodes supplémentaires pouvant nécessiter un effort manuel, voir l’article [Migration d’une instance SQL Server vers Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Les projets de migration de SQL Server vers Azure SQL Database Managed Instance sont en préversion et soumis aux [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dans ce didacticiel, vous allez migrer la base de données **Adventureworks2012** d’une instance locale de SQL Server vers Azure SQL Database Managed Instance en utilisant Azure Database Migration Service.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration en utilisant Azure Database Migration Service.
> * Exécuter la migration.
> * Surveiller la migration.
> * Télécharger un rapport de migration.

## <a name="prerequisites"></a>Prérequis
Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- Créez un réseau virtuel pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux à l’aide de la fonction [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Découvrez les topologies de réseau pour les migrations Azure SQL DB Managed Instance à l’aide d’Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Assurez-vous que les règles de groupe de sécurité Réseau virtuel Microsoft Azure ne bloquent pas les ports de communication 443, 53, 9354, 445 et 12000. Pour plus d’informations sur le filtrage de groupe de sécurité Réseau virtuel Microsoft Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurez [l’accès au moteur de base de données source dans votre Pare-feu Windows](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Ouvrez votre Pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au serveur SQL Server source, par défaut le port TCP 1433.
- Si vous exécutez plusieurs instances nommées de SQL Server avec des ports dynamiques, vous pouvez activer le service SQL Browser et autoriser l’accès au port UDP 1434 à travers vos pare-feu, de sorte qu’Azure Database Migration Service puisse se connecter à une instance nommée sur votre serveur source.
- Si vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration, ainsi qu’aux fichiers via le port SMB 445.
- Créez une instance Azure SQL Database Managed Instance en suivant les indications de l’article [Create an Azure SQL Database Managed Instance in the Azure portal](https://aka.ms/sqldbmi) (Créer une instance Azure SQL Database Managed Instance dans le portail Azure).
- Vérifiez que les informations d’identification utilisées pour se connecter à l’instance source SQL Server et à l’instance cible Managed Instance appartiennent à des membres du rôle serveur sysadmin.
- Créez un partage réseau qu’Azure Database Migration Service peut utiliser pour sauvegarder la base de données source.
- Assurez-vous que le compte de service exécutant l’instance SQL Server source dispose de privilèges en écriture sur le partage réseau que vous avez créé, et que le compte d’ordinateur pour le serveur source a accès en lecture/écriture au même partage.
- Repérez un utilisateur Windows (et son mot de passe) disposant d’un contrôle total sur le partage réseau que vous avez créé précédemment. Azure Database Migration Service emprunte l’identité de l’utilisateur pour charger les fichiers de sauvegarde sur le conteneur de stockage Azure pour l’opération de restauration.
- Créez un conteneur d’objets blob et récupérez son URI SAS en suivant les étapes de l’article [Gérer les ressources Azure Blob Storage avec l’Explorateur de stockage (version préliminaire)](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Veillez à sélectionner toutes les autorisations (lecture, écriture, suppression, liste) dans la fenêtre de la stratégie lors de la création de l’URI SAS. Azure Database Migration Service peut ainsi accéder à votre conteneur de compte de stockage afin de charger les fichiers de sauvegarde utilisés pour migrer les bases de données vers Azure SQL Database Managed Instance.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration

1. Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.

    ![Afficher les abonnements au portail](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Sélectionnez l’abonnement dans lequel vous souhaitez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.

    ![Afficher les fournisseurs de ressources](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.

    ![S’inscrire auprès du fournisseur de ressources](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Créer une instance Azure Database Migration Service

1. Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez **Azure Database Migration Service**, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

     ![Place de marché Azure](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.

    ![Créer une instance Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. Dans l’écran **Créer un service de migration**, spécifiez un nom pour le service, l’abonnement, et un réseau virtuel nouveau ou existant.

4. Sélectionnez un réseau virtuel existant ou créez-en un.
 
    Le réseau virtuel fournit à Azure Database Migration Service un accès au serveur SQL Server source et à Azure SQL Database Managed Instance.

    Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, voir l’article [Créer un réseau virtuel à l’aide du portail Azure](https://aka.ms/DMSVnet).

    Pour plus de détails, voir l’article [Topologies de réseau pour les migrations Azure SQL DB Managed Instance à l’aide d’Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

5. Sélectionnez un niveau tarifaire.

    Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).
   
    ![Créer un service DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  Sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois le service créé, recherchez-le dans le portail Azure, ouvrez-le, puis créez un projet de migration.

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.

    ![Rechercher toutes les instances Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. Dans l’écran **Azure Database Migration Service**, recherchez le nom de l’instance que vous avez créée, puis sélectionnez-la.
 
3. Sélectionnez **+ Nouveau projet de migration**.

4. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server**. Enfin, dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database Managed Instance**.

   ![Créer un projet DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. Sélectionnez **Créer** pour créer le projet.

## <a name="specify-source-details"></a>Spécifier les détails de la source

1. Dans l’écran **Détails de la source**, spécifiez les détails de connexion de SQL Server source.

2. Si vous n’avez pas installé de certificat de confiance sur votre serveur, active la case à cocher **Faire confiance au certificat de serveur**.

    Quand aucun certificat de confiance n’est installé, SQL Server génère un certificat auto-signé au démarrage de l’instance. Ce certificat permet de chiffrer les informations d’identification des connexions clientes.

    > [!CAUTION]
    > Les connexions SSL chiffrées à l’aide d’un certificat auto-signé n’offrent pas de sécurité renforcée. Elles sont vulnérables aux attaques de l’intercepteur. Vous ne devez pas compter sur SSL utilisant des certificats auto-signés dans un environnement de production ou sur des serveurs connectés à Internet.

   ![Détails de la source](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Sélectionnez **Enregistrer**.

4. Sur l’écran **Sélectionner la base de données source**, sélectionnez la base de données **Adventureworks2012** pour la migration.

   ![Sélectionner les bases de données sources](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Sélectionnez **Enregistrer**.

## <a name="specify-target-details"></a>Spécifier les détails de la cible

1.  Dans l’écran **Détails de la cible**, spécifiez les détails de connexion de la cible, à savoir l’option Azure SQL Database Managed Instance pré-provisionnée vers laquelle la base de données **AdventureWorks2012** doit être migrée.

    Si vous n’avez pas encore approvisionné l’Azure SQL Database Managed Instance, sélectionnez **Non** pour un lien destiné à vous aider à approvisionner l’instance. Vous pouvez toujours procéder à la création du projet, puis, quand l’Azure SQL Database Managed Instance est prête, revenir à ce projet spécifique pour exécuter la migration.   
 
       ![Sélectionner la cible](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  Sélectionnez **Enregistrer**.

3.  Dans l’écran **Récapitulatif du projet**, vérifiez les détails associés au projet de migration.
 
    ![Récapitulatif du projet de migration](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  Sélectionnez **Enregistrer**.   

## <a name="run-the-migration"></a>Exécuter la migration

1.  Sélectionnez le projet récemment enregistré, sélectionnez **+ Nouvelle activité**, puis sélectionnez **Exécuter la migration**.

    ![Créer une activité](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  Lorsque vous y êtes invité, entrez les informations d’identification des serveurs source et cible, puis sélectionnez **Enregistrer**.

3.  Dans l’écran **Sélectionner les base de données sources**, sélectionnez la ou les bases de données sources que vous souhaitez migrer.

    ![Sélectionner les bases de données sources](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases2.png)

4.  Sélectionnez **Enregistrer** puis, dans l’écran **sélectionner des connexions**, sélectionnez les connexions que vous souhaitez migrer.

    La version actuelle prend uniquement en charge la migration des connexions SQL.

    ![Sélectionner des connexions](media\tutorial-sql-server-to-managed-instance\dms-select-logins.png)

5. Sélectionnez **Enregistrer**, puis, dans l’écran **Configurer les paramètres de migration**, fournissez les informations suivantes :

    | | |
    |--------|---------|
    |**Partage d’emplacement réseau** | Partage réseau local qu’Azure Database Migration Service peut utiliser pour sauvegarder la base de données source. Le compte de service qui exécute l’instance source de SQL Server doit avoir des privilèges d’écriture pour ce partage réseau. Fournissez un nom de domaine complet ou les adresses IP du serveur dans le partage réseau, par exemple, « \\\nomserveur.nomdomaine.com\dossiersauvegarde » ou « \\\adresse IP\dossiersauvegarde ».|
    |**Nom d'utilisateur** | Nom d’utilisateur Windows dont Azure Database Migration Service emprunte l’identité pour charger les fichiers de sauvegarde vers le conteneur de stockage Azure pour l’opération de restauration. |
    |**Mot de passe** | Mot de passe pour l’utilisateur. |
    |**Paramètres du compte de stockage** | L’URI SAS qui permet à Azure Database Migration Service d’accéder au conteneur de compte de stockage dans lequel le service charge les fichiers de sauvegarde et qui est utilisé pour migrer les bases de données vers Azure SQL Database Managed Instance. [Découvrez comment obtenir l’URI SAS du conteneur d’objets blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Configurer les paramètres de migration](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings2.png)

5.  Sélectionnez **Enregistrer**, puis, dans l’écran **Résumé de la migration**, dans la zone de texte **Nom de l’activité**, spécifiez un nom pour l’activité de migration.

    ![Résumé de la migration](media\tutorial-sql-server-to-managed-instance\dms-migration-summary2.png)

6. Développez la section **Option de validation** pour afficher l’écran **Choisir l’option de validation**, spécifiez s’il faut valider la base de données migrée pour l’exactitude des requêtes, puis sélectionnez **Enregistrer**.  

7. Sélectionnez **Exécuter la migration**.

    La fenêtre d’activité de migration s’affiche, dans laquelle l’état de l’activité est **En attente**.

## <a name="monitor-the-migration"></a>Surveiller la migration

1. Dans l’écran de l’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage.
 
   ![Activité de migration en cours](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-in-progress.png)

2. Vous pouvez développer davantage les catégories de bases de données et de connexions pour surveiller l’état de la migration des objets serveur respectifs.

   ![Activité de migration en cours](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-monitor.png)

3. Une fois la migration terminée, sélectionnez **Télécharger le rapport** pour obtenir un rapport mentionnant les détails associés au processus de migration.
 
4. Vérifiez la base de données cible sur l’environnement Azure SQL Database Managed Instance cible.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir un didacticiel vous expliquant comment migrer une base de données vers une instance Managed Instance à l’aide de la commande RESTORE de T-SQL, consultez [Restore a database backup to an Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md) (Restaurer une sauvegarde de base de données vers une instance Azure SQL Database Managed Instance).
- Pour plus d’informations sur l’option Managed Instance, consultez [Présentation de l’option Managed Instance](../sql-database/sql-database-managed-instance.md).
- Pour plus d’informations sur la connexion des applications à une instance Managed Instance, consultez [Connecter votre application à Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md).
