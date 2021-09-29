---
title: 'Tutoriel : Migrer SQL Server en ligne vers une instance gérée SQL'
titleSuffix: Azure Database Migration Service
description: Apprenez à effectuer une migration en ligne entre SQL Server et Azure SQL Managed Instance à l'aide d'Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/20/2021
ms.openlocfilehash: 4c9028b559c537c0707b0ab3264fbfa11d26a350
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664107"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>Tutoriel : Procéder à la migration en ligne de SQL Server vers Azure SQL Managed Instance à l'aide de DMS

Vous pouvez utiliser Azure Database Migration Service pour procéder à la migration des bases de données d'une instance de SQL Server vers [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md), pour un temps d'arrêt minimal. Pour connaître des méthodes supplémentaires pouvant nécessiter un effort manuel, consultez l'article [Migration d'une instance de SQL Server vers Azure SQL Managed Instance](../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).

Dans ce tutoriel, vous allez migrer la base de données [AdventureWorks2016](/sql/samples/adventureworks-install-configure#download-backup-files) d'une instance locale de SQL Server vers SQL Managed Instance, avec un temps d'arrêt minimal, en utilisant Azure Database Migration Service.

Vous apprendrez à :
> [!div class="checklist"]
>
> * Inscrivez le fournisseur de ressources Azure DataMigration.
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration et lancer une migration en ligne en utilisant Azure Database Migration Service.
> * Surveiller la migration.
> * Effectuer le basculement de la migration lorsque vous êtes prêt.

> [!IMPORTANT]
> Pour les migrations en ligne de SQL Server vers SQL Managed Instance à l’aide d’Azure Database Migration Service, vous devez fournir la sauvegarde de la base de données complète et les sauvegardes des journaux associés dans le partage réseau SMB que le service peut utiliser pour la migration de vos bases de données. Azure Database Migration Service ne lance pas les sauvegardes. Il utilise les sauvegardes existantes (qui peuvent déjà faire partie de votre plan de récupération d'urgence) pour la migration.
> N’oubliez pas d’effectuer des [sauvegardes à l’aide de l’option WITH CHECKSUM](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017). Chaque sauvegarde peut être enregistrée dans un fichier de sauvegarde distinct ou dans plusieurs fichiers de sauvegarde. Toutefois, l’ajout de plusieurs sauvegardes (par exemple, full et t-log) sur un seul support de sauvegarde n’est pas pris en charge. Enfin, vous pouvez utiliser des sauvegardes compressées pour réduire le risque de problèmes liés à la migration de sauvegardes volumineuses.

> [!NOTE]
> L’utilisation d’Azure Database Migration Service pour effectuer une migration en ligne nécessite la création d’une instance basée sur le niveau tarifaire Premium.

> [!IMPORTANT]
> Pour une expérience de migration optimale, Microsoft recommande de créer une instance d’Azure Database Migration Service dans la même région Azure que la base de données cible. Le déplacement des données entre les régions ou les zones géographiques peut ralentir le processus de migration et introduire des erreurs.

> [!IMPORTANT]
> Réduisez autant que possible la durée du processus de migration en ligne afin de minimiser le risque d’interruption due à la reconfiguration d’une instance ou à une maintenance planifiée. Dans le cas d’un tel événement, le processus de migration reprend dès le début. En cas de maintenance planifiée, il existe une période de grâce de 36 heures avant le redémarrage du processus de migration.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Cet article décrit une migration en ligne de SQL Server vers SQL Managed Instance. Pour une migration hors connexion, consultez [Migrer une instance de SQL Server vers une instance de SQL Managed Instance hors connexion à l'aide de DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

* Téléchargez et installez [SQL Server 2016 ou une version ultérieure](https://www.microsoft.com/sql-server/sql-server-downloads).
* Activez le protocole TCP/IP, qui est désactivé par défaut pendant l’installation de SQL Server Express, en suivant les instructions de l’article [Activer ou désactiver un protocole réseau de serveur](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* [Restaurez la base de données AdventureWorks2016 sur l’instance SQL.](/sql/samples/adventureworks-install-configure#restore-to-sql-server)
* Créez un Réseau virtuel Microsoft Azure pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité site à site à vos serveurs sources locaux via [ExpressRoute](../expressroute/expressroute-introduction.md) ou un [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). [Découvrez les topologies de réseau pour les migrations d’instance managée SQL à l’aide d’Azure Database Migration Service](./resource-network-topologies.md). Pour plus d’informations sur la création d’un réseau virtuel, consultez la [documentation sur le réseau virtuel](../virtual-network/index.yml), en particulier les articles sur le démarrage rapide, qui fournissent des informations pas à pas.

    > [!NOTE]
    > Pendant la configuration du réseau virtuel, si vous utilisez ExpressRoute avec le peering réseau à Microsoft, ajoutez ces [points de terminaison](../virtual-network/virtual-network-service-endpoints-overview.md) au sous-réseau où doit être provisionné le service :
    >
    > * Point de terminaison de base de données cible (un point de terminaison SQL ou Cosmos DB, par exemple)
    > * Point de terminaison de stockage
    > * Point de terminaison Service Bus
    >
    > Cette configuration est nécessaire car Azure Database Migration Service ne dispose pas d’une connectivité Internet.
    >
    >En l'absence de connectivité de site à site entre le réseau local et Azure, ou si la bande passante de la connectivité de site à site est limitée, vous pouvez utiliser Azure Database Migration Service en mode hybride (préversion). Le mode hybride utilise un Worker de migration local et une instance d'Azure Database Migration Service exécutée dans le cloud. Pour créer une instance d'Azure Database Migration Service en mode hybride, consultez l'article [Créer une instance d'Azure Database Migration Service en mode hybride à l'aide du portail Azure](./quickstart-create-data-migration-service-portal.md).

    > [!IMPORTANT]
    > En ce qui concerne le compte de stockage utilisé dans le cadre de la migration, vous devez :
    > * Choisir d’autoriser tout le réseau à accéder au compte de stockage.
    > * Activez [Délégation de sous-réseau](../virtual-network/manage-subnet-delegation.md) sur le sous-réseau MI et mettez à jour les règles de pare-feu du compte de stockage pour autoriser ce sous-réseau.

* Assurez-vous que les règles de groupe de sécurité réseau de votre réseau virtuel ne bloquent pas le port de sortie 443 de ServiceTag pour ServiceBus, Storage et AzureMonitor. Pour plus d’informations sur le filtrage du trafic de groupe de sécurité réseau de réseau virtuel, consultez l’article [Filtrer le trafic avec les groupes de sécurité réseau](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurez [l’accès au moteur de base de données source dans votre Pare-feu Windows](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Ouvrez votre pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au serveur SQL Server source via le port TCP 1433 (par défaut). Si votre instance par défaut écoute sur un autre port, ajoutez ce dernier au pare-feu.
* Si vous exécutez plusieurs instances nommées de SQL Server avec des ports dynamiques, vous pouvez activer le service SQL Browser et autoriser l’accès au port UDP 1434 à travers vos pare-feu, de sorte qu’Azure Database Migration Service puisse se connecter à une instance nommée sur votre serveur source.
* Si vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration ainsi qu’aux fichiers, via le port SMB 445.
* Créez une instance managée SQL en suivant les indications de l’article [Créer une instance managée SQL dans le portail Azure](../azure-sql/managed-instance/instance-create-quickstart.md).
* Vérifiez que les comptes de connexion utilisés pour connecter le serveur SQL Server source et l’instance SQL Managed Instance cible sont membres du rôle serveur sysadmin.
* Spécifiez un partage réseau SMB qui contient tous les fichiers de sauvegarde complète de votre base de données et les fichiers de sauvegarde des journaux des transactions associés qu'Azure Database Migration Service peut utiliser pour la migration de base de données.
* Assurez-vous que le compte de service exécutant l’instance SQL Server source dispose de privilèges en écriture sur le partage réseau que vous avez créé, et que le compte d’ordinateur pour le serveur source a accès en lecture/écriture au même partage.
* Repérez un utilisateur Windows (et son mot de passe) disposant d’un contrôle total sur le partage réseau que vous avez créé précédemment. Azure Database Migration Service emprunte les informations d’identification de l’utilisateur pour charger les fichiers de sauvegarde sur le conteneur de Stockage Azure pour l’opération de restauration.
* Créez un ID d’application Azure Active Directory qui génère la clé de l’ID d’application permettant à Azure Database Migration Service de se connecter à l’instance managée Azure SQL Database et au conteneur Stockage Azure cibles. Pour plus d’informations, consultez l’article [Utiliser le portail pour créer une application et un principal du service Azure Active Directory pouvant accéder aux ressources](../active-directory/develop/howto-create-service-principal-portal.md).

  > [!NOTE]
  > L’ID d’application utilisé par Azure Database Migration Service prend en charge l’authentification par secret (basée sur un mot de passe) pour les principaux de service. Il ne prend pas en charge l’authentification basée sur les certificats.

  > [!NOTE]
  > Azure Database Migration Service nécessite les autorisations de contributeur sur l’abonnement pour l’ID d’application spécifié. Vous pouvez également créer des rôles personnalisés qui accordent les autorisations spécifiques requises par Azure Database Migration Service. Pour obtenir des instructions pas à pas sur l'utilisation des rôles personnalisés, consultez l'article [Rôles personnalisés pour la migration en ligne de SQL Server vers SQL Managed Instance](./resource-custom-roles-sql-db-managed-instance.md).

* Créez ou prenez note du **niveau de performance Standard**, compte Stockage Azure, qui permet à Azure Database Migration Service de charger les fichiers de sauvegarde de base de données sur les bases de données en cours de migration et de les utiliser pour celles-ci.  Veillez à créer le compte de stockage Azure dans la même région que celle où l’instance d’Azure Database Migration Service est créée.

  > [!NOTE]
  > Lorsque vous migrez une base de données protégée par [Transparent Data Encryption](../azure-sql/database/transparent-data-encryption-tde-overview.md) vers une instance gérée à l’aide d’une migration en ligne, le certificat correspondant du serveur local ou de l’instance SQL Server de la machine virtuelle Azure doit être migré avant la restauration de la base de données. Pour des instructions détaillées, voir [Migrer un certificat TDE vers une instance gérée](../azure-sql/database/transparent-data-encryption-tde-overview.md).

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]

## <a name="create-an-azure-database-migration-service-instance"></a>Créer une instance Azure Database Migration Service

1. Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**. Recherchez et sélectionnez **Azure Database Migration Service**.

    ![Place de marché Azure](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.

    ![Créer une instance Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create-service-1.png)

3. Sous l’onglet Informations de base de la page **Créer un service de migration** :

     - Sélectionnez l’abonnement.
     - Créez un groupe de ressources ou sélectionnez-en un déjà existant.
     - Spécifiez un nom pour l’instance d’Azure Database Migration Service.
     - Sélectionnez l’emplacement dans lequel vous souhaitez créer l’instance Azure Database Migration Service.
     - Choisissez **Azure** comme mode de service.
     - Sélectionnez une référence SKU dans le niveau tarifaire Premium. 
     
      > [!NOTE]
      > Les migrations en ligne sont uniquement prises en charge lorsque vous utilisez le niveau Premium.

     - Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    ![Configurer des paramètres de base pour l’instance Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create-service-2.png)

     - Sélectionnez **Suivant : Réseau**.

4. Sous l’onglet Réseau de la page **Créer un service de migration** :

    - Sélectionnez un réseau virtuel existant ou créez-en un. Le réseau virtuel fournit à Azure Database Migration Service un accès à SQL Server source et à Azure SQL Managed Instance cible.
     
    - Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez l’article [Créer un réseau virtuel au moyen du portail Azure](../virtual-network/quick-create-portal.md).
    
    - Pour plus d’informations, consultez l’article [Topologies de réseau pour des migrations Azure SQL Managed Instance à l’aide du service Azure Database Migration Service](./resource-network-topologies.md).

      ![Configurer des paramètres réseau pour l’instance Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create-service-3.png)

    - Sélectionnez **Vérifier + créer** pour vérifier les détails, puis sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois qu’une instance du service a été créée, recherchez-la dans le Portail Azure, ouvrez-la, puis créez un projet de migration.

1. Dans le menu du portail Azure, sélectionnez **Tous les services**. Recherchez et sélectionnez **Azure Database Migration Services**.

    ![Localiser toutes les instances Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Dans l’écran **Azure Database Migration Services**, sélectionnez l’instance Azure Database Migration Service que vous avez créée.

3. Sélectionnez **Nouveau projet de migration**.

     ![Localisez votre instance Azure Database Migration Service.](media/tutorial-sql-server-to-managed-instance-online/dms-create-project-1.png)

4. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server**. Dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database Managed Instance**. Enfin, dans la zone de texte **Choisir un type d’activité**, sélectionnez **Migration de données hors connexion**.

   ![Créer un projet Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create-project-2.png)

5. Sélectionnez **Créer et exécuter une activité** pour créer le projet et exécuter l’activité de migration.

## <a name="specify-source-details"></a>Spécifier les détails de la source

1. Dans l’écran **Sélectionner la source**, spécifiez les détails de connexion de l’instance SQL Server source.

    Veillez à utiliser un nom de domaine complet pour le nom de l’instance SQL Server source. Vous pouvez également utiliser l’adresse IP quand la résolution de noms DNS est impossible.

2. Si vous n’avez pas installé de certificat approuvé sur votre serveur, cochez la case **Faire confiance au certificat de serveur**.

    Quand aucun certificat approuvé n’est installé, SQL Server génère un certificat auto-signé au démarrage de l’instance. Ce certificat permet de chiffrer les informations d’identification des connexions clientes.

    > [!CAUTION]
    > Les connexions TLS chiffrées à l’aide d’un certificat auto-signé n’offrent pas de sécurité renforcée. Elles sont vulnérables aux attaques de l’intercepteur. Ne faites jamais confiance à une connexion TLS utilisant des certificats autosignés dans un environnement de production ou sur des serveurs connectés à Internet.

    ![Détails de la source](media/tutorial-sql-server-to-managed-instance-online/dms-source-details.png)

3. Sélectionnez **Suivant : sélectionner la cible**

## <a name="specify-target-details"></a>Spécifier les détails de la cible

1. Sur l'écran **Sélectionner la cible**, spécifiez l'**ID d'application** et la **Clé** que l'instance DMS peut utiliser pour se connecter à l'instance cible de SQL Managed Instance et au compte de stockage Azure.

    Pour plus d’informations, consultez l’article [Utiliser le portail pour créer une application et un principal du service Azure Active Directory pouvant accéder aux ressources](../active-directory/develop/howto-create-service-principal-portal.md).

2. Sélectionnez l'**Abonnement** contenant l'instance cible de SQL Managed Instance, puis sélectionnez SQL Managed Instance cible.

    Si vous n'avez pas encore approvisionné SQL Managed Instance, sélectionnez le [lien](../azure-sql/managed-instance/instance-create-quickstart.md) destiné à vous aider à approvisionner l'instance. Une fois l'instance de SQL Managed Instance prête, retournez à ce projet spécifique pour exécuter la migration.

3. Indiquez l'**Utilisateur SQL** et le **Mot de passe** permettant de se connecter à SQL Managed Instance.

    ![Sélectionner la cible](media/tutorial-sql-server-to-managed-instance-online/dms-target-details.png)

4. Sélectionnez **Suivant : Sélectionner des bases de données**.

## <a name="specify-source-databases"></a>Spécifier les bases de données sources

1. Dans l’écran **Sélectionner les bases de données**, sélectionnez les bases de données sources que vous souhaitez migrer.

  ![Sélectionner les bases de données sources](media/tutorial-sql-server-to-managed-instance-online/dms-source-database.png)

  > [!IMPORTANT]
  > Si vous utilisez SSIS (SQL Server Integration Services), DMS ne prend pas en charge la migration de la base de données de catalogue de vos projets/packages SSIS (SSISDB) entre SQL Server et SQL Managed Instance. Toutefois, vous pouvez provisionner SSIS dans ADF (Azure Data Factory) et redéployer vos projets/packages SSIS sur la base de données SSISDB de destination hébergée par SQL Managed Instance. Pour plus d’informations sur la migration des packages SSIS, consultez l’article [Migrer des packages SQL Server Integration Services vers Azure](./how-to-migrate-ssis-packages.md).

2. Sélectionnez **Suivant : configurer les paramètres de migration**.

## <a name="configure-migration-settings"></a>Configurer les paramètres de migration

1. Dans l’écran **Configurer les paramètres de migration**, fournissez les détails suivants :

    | Paramètre | Description |
    |--------|---------|
    |**SMB Network location share** (Partage d’emplacement réseau SMB) | Le partage réseau SMB local ou le partage de fichiers Azure contenant les fichiers de sauvegarde complète de la base de données et les fichiers de sauvegarde de fichier journal qu’Azure Database Migration Service peut utiliser pour la migration. Le compte de service qui exécute l’instance source de SQL Server doit avoir des privilèges de lecture et d’écriture pour ce partage réseau. Fournissez un nom de domaine complet ou les adresses IP du serveur dans le partage réseau, par exemple, « \\\nomserveur.nomdomaine.com\dossiersauvegarde » ou « \\\adresse IP\dossiersauvegarde ». Pour améliorer les performances, il est recommandé d’utiliser un dossier distinct pour chaque base de données à migrer. Vous pouvez fournir le chemin d’accès du partage de fichiers au niveau de la base de données à l’aide de l’option **Paramètres avancés**. Si vous avez des difficultés à vous connecter au partage SMB, consultez [Partage SMB](known-issues-azure-sql-db-managed-instance-online.md#smb-file-share-connectivity). |
    |**Nom d'utilisateur** | Assurez-vous que l’utilisateur Windows dispose d’un contrôle total sur le partage réseau que vous avez fourni précédemment. Azure Database Migration Service utilise les informations d’identification de l’utilisateur afin de charger les fichiers de sauvegarde dans le conteneur de stockage Azure pour l'opération de restauration. Si vous choisissez le partage de fichiers Azure, utilisez le nom de compte de stockage commençant par AZURE\ en tant que nom d’utilisateur. |
    |**Mot de passe** | Mot de passe pour l’utilisateur. Si vous choisissez le partage de fichiers Azure, utilisez une clé de compte de stockage en tant que mot de passe. |
    |**Subscription of the Azure Storage Account** (Abonnement du compte Stockage Azure) | Sélectionnez l’abonnement qui contient le compte Stockage Azure. |
    |**Compte Stockage Azure** | Sélectionnez le compte Stockage Azure sur lequel Azure Database Migration Service peut charger les fichiers de sauvegarde à partir du partage réseau SMB et qu’il peut utiliser pour la migration de base de données.  Nous vous recommandons de sélectionner le compte de stockage se trouvant dans la même région qu’Azure Database Migration Service pour bénéficier de performances de chargement de fichiers optimales. |

    ![Configurer les paramètres de migration](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings.png)

    > [!NOTE]
    > Si Azure Database Migration Service indique l’erreur « Erreur système 53 » ou « Erreur système 57 », cela peut signifier qu’Azure Database Migration Service ne peut pas accéder au partage de fichiers Azure. Si vous êtes confronté à l’une de ces erreurs, accordez l’accès au compte de stockage depuis le réseau virtuel en utilisant les instructions [ici](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Si la fonctionnalité du contrôle de bouclage est activée et que le SQL Server source et le partage de fichiers se trouvent sur le même ordinateur, la source ne peut pas accéder au partage de fichiers à l’aide du FQDN. Pour résoudre ce problème, désactivez la fonctionnalité du contrôle de bouclage à l’aide des instructions indiquées [ici](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

2. Sélectionnez **Suivant : Résumé**.

## <a name="review-the-migration-summary"></a>Examiner le récapitulatif de la migration

1. Dans l’écran **Résumé**, spécifiez un nom pour l’activité de migration dans la zone de texte **Nom de l’activité**.

2. Examinez et vérifiez les détails associés au projet de migration.

    ![Récapitulatif du projet de migration](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary.png)

## <a name="run-and-monitor-the-migration"></a>Exécuter et surveiller la migration

1. Sélectionnez **Démarrer la migration**.

2. La fenêtre d’activité de migration apparaît et affiche l’état de migration des bases de données actuelles. Sélectionnez **Actualiser** pour mettre à jour l’affichage.

   ![Activité de migration en cours](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration.png)

    Vous pouvez développer davantage les catégories de bases de données et de connexions pour surveiller l’état de la migration des objets serveur respectifs.

   ![État d’activité de migration](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend.png)

## <a name="performing-migration-cutover"></a>Exécution du basculement de migration

Une fois la sauvegarde de la base de données complète restaurée sur l'instance cible de SQL Managed Instance, la base de données est disponible pour une migration à basculement.

1. Lorsque vous êtes prêt à effectuer la migration de base de données en ligne, sélectionnez **Démarrer le basculement**.

2. Arrêtez tout le trafic entrant dans les bases de données sources.

3. Effectuez la [sauvegarde de la fin du journal], mettez le fichier de sauvegarde à disposition dans le partage réseau SMB, puis attendez que cette sauvegarde de fichier journal finale soit restaurée.

    À ce stade, vous devez voir le paramètre **Modifications en attente** défini sur 0.

4. Sélectionnez **Confirmer**, puis **Appliquer**.

    ![Préparation à la fin du basculement](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

    > [!IMPORTANT]
    > Après le basculement, la disponibilité de SQL Managed Instance avec le niveau de service critique pour l’entreprise peut prendre beaucoup plus de temps que l’usage général, car trois réplicas secondaires doivent être amorcés pour le groupe de disponibilité Always On. La durée de cette opération dépend de la taille des données. Pour plus d’informations, consultez [Durée des opérations de gestion](../azure-sql/managed-instance/management-operations-overview.md#duration).

5. Lorsque l'état de la migration de la base de données indique **Terminé**, connectez vos applications à la nouvelle instance cible de SQL Managed Instance.

    ![Basculement terminé](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="additional-resources"></a>Ressources supplémentaires

* Pour accéder à un tutoriel vous expliquant comment migrer une base de données vers SQL Managed Instance à l’aide de la commande T-SQL RESTORE, consultez [Restaurer une sauvegarde vers SQL Managed Instance à l’aide de la commande restore](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
* Pour obtenir des informations sur SQL Managed Instance, consultez [Présentation de SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
* Pour plus d’informations sur la connexion d’applications à SQL Managed Instance , consultez [Connecter des applications](../azure-sql/managed-instance/connect-application-instance.md).
