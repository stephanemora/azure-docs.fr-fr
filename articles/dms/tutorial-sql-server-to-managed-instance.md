---
title: 'Tutoriel : Migrer SQL Server vers SQL Managed Instance'
titleSuffix: Azure Database Migration Service
description: Apprenez à migrer de SQL Server vers une instance managée SQL Azure à l’aide d’Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit
ms.topic: tutorial
ms.date: 08/16/2021
ms.openlocfilehash: 0ff725703d96b524b9c6a45c84e95ae45af33cf8
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214821"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-dms"></a>Tutoriel : Migrer SQL Server vers une instance managée SQL Azure hors connexion à l’aide de DMS

Vous pouvez utiliser Azure Database Migration Service pour procéder à la migration des bases de données d’une instance de SQL Server vers une [instance managée SQL Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Pour connaître des méthodes supplémentaires pouvant nécessiter un effort manuel, consultez l’article [SQL Server vers Azure SQL Managed Instance](../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).

Dans ce tutoriel, vous allez migrer la base de données [AdventureWorks2016](/sql/samples/adventureworks-install-configure#download-backup-files) à partir d’une instance de SQL Server locale vers SQL Managed Instance en utilisant Azure Database Migration Service.

Vous apprendrez à :
> [!div class="checklist"]
>
> - Inscrivez le fournisseur de ressources Azure DataMigration.
> - Créer une instance Azure Database Migration Service.
> - Créer un projet de migration en utilisant Azure Database Migration Service.
> - Exécuter la migration.
> - Surveiller la migration.

> [!IMPORTANT]
> Pour les migrations hors connexion à partir de SQL Server vers une instance managée SQL, Azure Database Migration Service peut créer les fichiers de sauvegarde pour vous. Vous pouvez également fournir la dernière sauvegarde complète de la base de données dans le partage réseau SMB que le service utilisera pour migrer vos bases de données. Chaque sauvegarde peut être enregistrée dans un fichier de sauvegarde distinct ou dans plusieurs fichiers de sauvegarde. Toutefois, l’ajout de plusieurs sauvegardes sur un seul support de sauvegarde n’est pas pris en charge. Notez que vous pouvez également utiliser des sauvegardes compressées pour réduire le risque de problèmes liés à la migration de sauvegardes volumineuses.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Cet article décrit une migration hors connexion de SQL Server vers une instance managée SQL. Pour une migration en ligne, consultez [Migrer SQL Server vers une instance managée SQL en ligne à l’aide de DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- Téléchargez et installez [SQL Server 2016 ou une version ultérieure](https://www.microsoft.com/sql-server/sql-server-downloads).
- Activez le protocole TCP/IP, qui est désactivé par défaut pendant l’installation de SQL Server Express, en suivant les instructions de l’article [Activer ou désactiver un protocole réseau de serveur](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- [Restaurez la base de données AdventureWorks2016 sur l’instance SQL.](/sql/samples/adventureworks-install-configure#restore-to-sql-server)
- Créez un Réseau virtuel Microsoft Azure pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité site à site à vos serveurs sources locaux via [ExpressRoute](../expressroute/expressroute-introduction.md) ou un [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). [Découvrez les topologies de réseau pour les migrations d’instance managée SQL à l’aide d’Azure Database Migration Service](./resource-network-topologies.md). Pour plus d’informations sur la création d’un réseau virtuel, consultez la [documentation sur le réseau virtuel](../virtual-network/index.yml), en particulier les articles sur le démarrage rapide, qui fournissent des informations pas à pas.

    > [!NOTE]
    > Pendant la configuration du réseau virtuel, si vous utilisez ExpressRoute avec le peering réseau à Microsoft, ajoutez ces [points de terminaison](../virtual-network/virtual-network-service-endpoints-overview.md) au sous-réseau où doit être provisionné le service :
    > - Point de terminaison de base de données cible (un point de terminaison SQL ou Cosmos DB, par exemple)
    > - Point de terminaison de stockage
    > - Point de terminaison Service Bus
    >
    > Cette configuration est nécessaire car Azure Database Migration Service ne dispose pas d’une connectivité Internet.

- Assurez-vous que les règles de groupe de sécurité réseau de votre réseau virtuel ne bloquent pas le port de sortie 443 de ServiceTag pour ServiceBus, Stockage et AzureMonitor. Pour plus d’informations sur le filtrage du trafic de groupe de sécurité réseau de réseau virtuel, consultez l’article [Filtrer le trafic avec les groupes de sécurité réseau](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Configurez [l’accès au moteur de base de données source dans votre Pare-feu Windows](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Ouvrez votre pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au serveur SQL Server source via le port TCP 1433 (par défaut). Si votre instance par défaut écoute sur un autre port, ajoutez ce dernier au pare-feu.
- Si vous exécutez plusieurs instances nommées de SQL Server avec des ports dynamiques, vous pouvez activer le service SQL Browser et autoriser l’accès au port UDP 1434 à travers vos pare-feu, de sorte qu’Azure Database Migration Service puisse se connecter à une instance nommée sur votre serveur source.
- Si vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration ainsi qu’aux fichiers, via le port SMB 445.
- Créez une instance managée SQL en suivant les indications de l’article [Créer une instance managée SQL dans le portail Azure](../azure-sql/managed-instance/instance-create-quickstart.md).
- Vérifiez que les comptes de connexion utilisés pour connecter le serveur SQL Server source et l’instance SQL Managed Instance cible sont membres du rôle serveur sysadmin.

    >[!NOTE]
    >Par défaut, Azure Database Migration Service prend uniquement en charge la migration des connexions SQL. Toutefois, vous pouvez rendre possible la migration des connexions Windows en procédant comme suit :
    >
    >- Vérifiez que le service SQL Managed Instance cible dispose d’un accès en lecture à AAD, et que cet accès peut être configuré via le portail Azure par un utilisateur doté du rôle **Administrateur général**.
    >- Configurez votre instance Azure Database Migration Service pour activer la migration des connexions d’utilisateur ou de groupe Windows, qui est configurée via le Portail Azure, dans la page Configuration. Après avoir activé ce paramètre, redémarrez le service pour que les modifications prennent effet.
    >
    > Après le redémarrage du service, les connexions d’utilisateur ou de groupe Windows apparaissent dans la liste des connexions disponibles pour la migration. Pour toutes les connexions d’utilisateur ou de groupe Windows que vous migrez, vous êtes invité à fournir le nom de domaine associé. Les comptes d’utilisateur de service (comptes avec le nom de domaine NT AUTHORITY) et les comptes d’utilisateur virtuels (nom de compte avec le nom de domaine NT SERVICE) ne sont pas pris en charge.

- Créez un partage réseau que le service Azure Database Migration Service pourra utiliser pour sauvegarder la base de données source.
- Assurez-vous que le compte de service exécutant l’instance SQL Server source dispose de privilèges en écriture sur le partage réseau que vous avez créé, et que le compte d’ordinateur pour le serveur source a accès en lecture/écriture au même partage.
- Repérez un utilisateur Windows (et son mot de passe) disposant d’un contrôle total sur le partage réseau que vous avez créé précédemment. Azure Database Migration Service emprunte les informations d’identification de l’utilisateur pour charger les fichiers de sauvegarde sur le conteneur de Stockage Azure pour l’opération de restauration.
- Créez un conteneur d’objets blob et récupérez son URI SAS en suivant les étapes de l’article [Gérer les ressources Azure Blob Storage avec l’Explorateur de stockage (version préliminaire)](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Veillez à sélectionner toutes les autorisations (lecture, écriture, suppression, liste) dans la fenêtre de la stratégie lors de la création de l’URI SAS. Ces détails permettent au service Azure Database Migration Service d’accéder au conteneur de votre compte de stockage afin de charger les fichiers de sauvegarde utilisés pour migrer les bases de données vers une instance managée SQL.

    > [!NOTE]
    > Quand vous configurez les paramètres du compte de stockage à l’étape [Configurer les paramètres de migration](#configure-migration-settings), notez qu’Azure Database Migration Service ne prend pas en charge l’utilisation de jeton SAS.
    
[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]

## <a name="create-an-azure-database-migration-service-instance"></a>Créer une instance Azure Database Migration Service

1. Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**. Recherchez et sélectionnez **Azure Database Migration Service**.

    ![Place de marché Azure](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.

    ![Créer une instance Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create-service-1.png)

3. Sous l’onglet Informations de base de la page **Créer un service de migration** :

     - Sélectionnez l’abonnement.
     - Créez un groupe de ressources ou sélectionnez-en un déjà existant.
     - Spécifiez un nom pour l’instance d’Azure Database Migration Service.
     - Sélectionnez l’emplacement dans lequel vous souhaitez créer l’instance Azure Database Migration Service.
     - Choisissez **Azure** comme mode de service.
     - Sélectionnez un niveau tarifaire. Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    ![Configurer des paramètres de base pour l’instance Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create-service-2.png)

     - Sélectionnez **Suivant : Réseau**.

4. Sous l’onglet Réseau de la page **Créer un service de migration** :

    - Sélectionnez un réseau virtuel existant ou créez-en un. Le réseau virtuel fournit à Azure Database Migration Service un accès à SQL Server source et à Azure SQL Managed Instance cible.
     
    - Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez l’article [Créer un réseau virtuel au moyen du portail Azure](../virtual-network/quick-create-portal.md).
    
    - Pour plus d’informations, consultez l’article [Topologies de réseau pour des migrations Azure SQL Managed Instance à l’aide du service Azure Database Migration Service](./resource-network-topologies.md).

    ![Configurer des paramètres réseau pour l’instance Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create-service-3.png)

    - Sélectionnez **Vérifier + créer** pour vérifier les détails, puis sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois qu’une instance du service a été créée, recherchez-la dans le Portail Azure, ouvrez-la, puis créez un projet de migration.

1. Dans le menu du portail Azure, sélectionnez **Tous les services**. Recherchez et sélectionnez **Azure Database Migration Services**.

    ![Localiser toutes les instances Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Dans l’écran **Azure Database Migration Services**, sélectionnez l’instance Azure Database Migration Service que vous avez créée.

3. Sélectionnez **Nouveau projet de migration**.

     ![Localisez votre instance Azure Database Migration Service.](media/tutorial-sql-server-to-managed-instance/dms-create-project-1.png)

4. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server**. Dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database Managed Instance**. Enfin, dans la zone de texte **Choisir un type d’activité**, sélectionnez **Migration de données hors connexion**.

   ![Créer un projet Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create-project-2.png)

5. Sélectionnez **Créer et exécuter une activité** pour créer le projet et exécuter l’activité de migration.

## <a name="specify-source-details"></a>Spécifier les détails de la source

1. Dans l’écran **Sélectionner la source**, spécifiez les détails de connexion de l’instance SQL Server source.

    Veillez à utiliser un nom de domaine complet pour le nom de l’instance SQL Server source. Vous pouvez également utiliser l’adresse IP quand la résolution de noms DNS est impossible.

2. Si vous n’avez pas installé de certificat approuvé sur votre serveur, cochez la case **Faire confiance au certificat de serveur**.

    Quand aucun certificat approuvé n’est installé, SQL Server génère un certificat auto-signé au démarrage de l’instance. Ce certificat permet de chiffrer les informations d’identification des connexions clientes.

    > [!CAUTION]
    > Les connexions TLS chiffrées à l’aide d’un certificat auto-signé n’offrent pas de sécurité renforcée. Elles sont vulnérables aux attaques de l’intercepteur. Ne faites jamais confiance à une connexion TLS utilisant des certificats autosignés dans un environnement de production ou sur des serveurs connectés à Internet.

   ![Détails de la source](media/tutorial-sql-server-to-managed-instance/dms-source-details.png)

3. Sélectionnez **Suivant : sélectionner la cible**

## <a name="specify-target-details"></a>Spécifier les détails de la cible

1. Dans l’écran **Sélectionner la cible**, spécifiez les informations de connexion de la cible, notamment SQL Managed Instance pré-approvisionné vers laquelle vous migrez la base de données **AdventureWorks2016**.

    Si vous n’avez pas encore provisionné l’instance managée SQL, sélectionnez le [lien](../azure-sql/managed-instance/instance-create-quickstart.md) vous permettant de la provisionner. Vous pouvez néanmoins procéder à la création du projet. Quand l’instance managée SQL prête, revenez à ce projet pour exécuter la migration.

    ![Sélectionner la cible](media/tutorial-sql-server-to-managed-instance/dms-target-details.png)

2. Sélectionnez **Suivant : Sélectionner des bases de données**. Sur l’écran **Sélectionner des bases de données**, sélectionnez la base de données **AdventureWorks2016** pour la migration.

   ![Sélectionner les bases de données sources](media/tutorial-sql-server-to-managed-instance/dms-source-database.png)

    > [!IMPORTANT]
    > Si vous utilisez SSIS (SQL Server Integration Services), DMS ne prend pas en charge la migration de la base de données de catalogue de vos projets/packages SSIS (SSISDB) entre SQL Server et SQL Managed Instance. Toutefois, vous pouvez provisionner SSIS dans ADF (Azure Data Factory) et redéployer vos projets/packages SSIS sur la base de données SSISDB de destination hébergée par SQL Managed Instance. Pour plus d’informations sur la migration des packages SSIS, consultez l’article [Migrer des packages SQL Server Integration Services vers Azure](./how-to-migrate-ssis-packages.md).

3. Sélectionnez **Suivant : sélectionner les connexions**

## <a name="select-logins"></a>Sélectionner des connexions

1. Dans l’écran **Sélectionner des connexions**, sélectionnez les connexions que vous souhaitez migrer.

    >[!NOTE]
    >Par défaut, Azure Database Migration Service prend uniquement en charge la migration des connexions SQL. Pour activer la prise en charge de la migration des connexions Windows, consultez la section **Conditions préalables requises** de ce tutoriel.

    ![Sélectionner des connexions](media/tutorial-sql-server-to-managed-instance/dms-select-logins.png)

2. Sélectionnez **Suivant : configurer les paramètres de migration**.

## <a name="configure-migration-settings"></a>Configurer les paramètres de migration

1. Dans l’écran **Configurer les paramètres de migration**, fournissez les détails suivants :

    | Paramètre | Description |
    |--------|---------|
    |**Choisir une option de sauvegarde source** | Lorsque vous disposez déjà des fichiers de sauvegarde complète qu’Azure Database Migration Service doit utiliser pour la migration de base de données, choisissez l’option **Je fournirai les derniers fichiers de sauvegarde**. Si vous souhaitez que DMS crée la sauvegarde complète des bases de données sources et l’utilise pour la migration, choisissez l’option **J’autorise Azure Database Migration Service à créer des fichiers de sauvegarde**. |
    |**Partage d’emplacement réseau** | Partage réseau SMB local qu’Azure Database Migration Service peut utiliser pour les sauvegardes de base de données source. Le compte de service qui exécute l’instance source de SQL Server doit avoir des privilèges d’écriture pour ce partage réseau. Fournissez un nom de domaine complet ou les adresses IP du serveur dans le partage réseau, par exemple, « \\\nomserveur.nomdomaine.com\dossiersauvegarde » ou « \\\adresse IP\dossiersauvegarde ».|
    |**Nom d'utilisateur** | Assurez-vous que l’utilisateur Windows dispose d’un contrôle total sur le partage réseau que vous avez fourni précédemment. Azure Database Migration Service utilise les informations d’identification de l’utilisateur afin de charger les fichiers de sauvegarde dans le conteneur de stockage Azure pour l'opération de restauration. Si vous avez sélectionné pour la migration des bases de données compatibles avec Transparent Data Encryption (TDE), l’utilisateur Windows ci-dessus doit correspondre au compte Administrateur intégré, et [Contrôle de compte d’utilisateur](/windows/security/identity-protection/user-account-control/user-account-control-overview) doit être désactivé pour qu’Azure Database Migration Service puisse charger et supprimer les fichiers de certificat. |
    |**Mot de passe** | Mot de passe pour l’utilisateur. |
    |**Paramètres du compte de stockage** | URI SAS qui permet au service Azure Database Migration Service d’accéder au conteneur de votre compte de stockage dans lequel le service charge les fichiers de sauvegarde et qui est utilisé pour effectuer la migration des bases de données vers l’instance managée SQL. [Découvrez comment obtenir l’URI SAS du conteneur d’objets blob](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Cet URI SAS doit être destiné au conteneur d’objets blob et non au compte de stockage.|
    |**Paramètres TDE** | Si vous migrez les bases de données sources compatibles avec TDE (Transparent Data Encryption), vous devez disposer de privilèges d’accès en écriture sur l’instance managée SQL cible.  Dans le menu déroulant, sélectionnez l’abonnement dans lequel l’instance managée SQL a été provisionnée.  Dans le second menu déroulant, sélectionnez **l’instance Azure SQL Database Managed Instance** cible. |

    ![Configurer les paramètres de migration](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings.png)

2. Sélectionnez **Suivant : Résumé**.

## <a name="review-the-migration-summary"></a>Examiner le récapitulatif de la migration

1. Dans l’écran **Résumé**, spécifiez un nom pour l’activité de migration dans la zone de texte **Nom de l’activité**.

2. Examinez et vérifiez les détails associés au projet de migration.

    ![Récapitulatif du projet de migration](media/tutorial-sql-server-to-managed-instance/dms-project-summary.png)

## <a name="run-the-migration"></a>Exécuter la migration

- Sélectionnez **Démarrer la migration**.

  La fenêtre d’activité de migration qui s’ouvre affiche l’état actuel de la migration des bases de données et des connexions.

## <a name="monitor-the-migration"></a>Surveiller la migration

1. Dans l’écran de l’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage.

   ![Capture d’écran montrant l’écran de l’activité de migration et le bouton Actualiser](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration.png)

2. Vous pouvez développer davantage les catégories de bases de données et de connexions pour surveiller l’état de la migration des objets serveur respectifs.

   ![Activité de migration en cours](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

3. Une fois la migration terminée, vérifiez la base de données cible présente sur l’environnement SQL Managed Instance.

## <a name="additional-resources"></a>Ressources supplémentaires

- Pour accéder à un tutoriel vous expliquant comment migrer une base de données vers SQL Managed Instance à l’aide de la commande T-SQL RESTORE, consultez [Restaurer une sauvegarde vers SQL Managed Instance à l’aide de la commande restore](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
- Pour obtenir des informations sur SQL Managed Instance, consultez [Présentation de SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
- Pour plus d’informations sur la connexion d’applications à SQL Managed Instance , consultez [Connecter des applications](../azure-sql/managed-instance/connect-application-instance.md).
