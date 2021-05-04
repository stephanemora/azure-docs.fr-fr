---
title: 'Tutoriel : Migration de MySQL vers Azure Database pour MySQL hors connexion à l’aide de DMS'
titleSuffix: Azure Database Migration Service
description: Découvrez comment effectuer la migration hors connexion d’une instance locale de MySQL vers Azure Database pour MySQL, à l’aide d’Azure Database Migration Service.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: d1a8cc9a615474685222d0339ec948401fb8cdff
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108128007"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-offline-using-dms"></a>Tutoriel : Migration de MySQL vers Azure Database pour MySQL hors connexion à l’aide de DMS

Vous pouvez utiliser Azure Database Migration Service pour effectuer une migration ponctuelle de base de données complète d’une instance de MySQL locale vers [Azure Database pour MySQL](../mysql/index.yml), avec une fonctionnalité de migration de données haut débit. Dans ce tutoriel, nous allons effectuer la migration d’un exemple de base de données à partir d’une instance locale de MySQL 5.7 vers Azure Database pour MySQL (v5.7), en utilisant une activité de migration hors connexion dans Azure Database Migration Service. Bien que les articles partent du principe que la source est une instance de base de données MySQL et la cible Azure Database pour MySQL, la procédure peut être utilisée pour effectuer une migration depuis une source Azure Database pour MySQL vers une même cible, en modifiant simplement le nom du serveur source et les informations d’identification. De plus, la migration de serveurs MySQL d’une version antérieure (v5.6 et versions ultérieures) vers des versions ultérieures est également prise en charge.

> [!IMPORTANT]
> Pour les migrations en ligne, vous pouvez utiliser des outils open source comme [MyDumper/MyLoader](https://centminmod.com/mydumper.html) avec [réplication des données entrantes](/azure/mysql/concepts-data-in-replication). 

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]

> [!NOTE]
> Pour obtenir une version scriptable PowerShell de cette expérience de migration, consultez [Migration hors connexion scriptable vers Azure Database pour MySQL](https://docs.microsoft.com/azure/dms/migrate-mysql-to-azure-mysql-powershell).

> [!NOTE]
> Amazon Relational Database Service (RDS) pour MySQL et Amazon Aurora (MySQL) sont également pris en charge en tant que sources pour la migration.

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
>
> * Migrer le schéma de la base de données à l’aide de l’utilitaire mysqldump.
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration en utilisant Azure Database Migration Service.
> * Exécuter la migration.
> * Surveiller la migration.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

* Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free).
* Vous devez disposer d’une base de données MySQL locale de version 5.7. Si ce n’est pas le cas, téléchargez et installez [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.7.
* [Créez une instance dans Azure Database pour MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Consultez l’article [Utiliser MySQL Workbench pour se connecter et interroger des données](../mysql/connect-workbench.md) pour plus d’informations sur la connexion et la création d’une base de données à l’aide de l’application Workbench. La version d’Azure Database pour MySQL doit être supérieure ou égale à la version locale de MySQL. Par exemple, MySQL 5.7 peut migrer vers Azure Database pour MySQL 5.7 ou sa mise à niveau vers la version 8. 
* Créez un réseau virtuel Microsoft Azure pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité site à site à vos serveurs sources locaux via [ExpressRoute](../expressroute/expressroute-introduction.md) ou un [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Pour plus d’informations sur la création d’un réseau virtuel, consultez la [documentation sur le réseau virtuel](../virtual-network/index.yml), en particulier les articles sur le démarrage rapide, qui fournissent des informations pas à pas.

    > [!NOTE]
    > Pendant la configuration du réseau virtuel, si vous utilisez ExpressRoute avec le peering réseau à Microsoft, ajoutez ces [points de terminaison](../virtual-network/virtual-network-service-endpoints-overview.md) au sous-réseau où doit être provisionné le service :
    >
    > * Point de terminaison de base de données cible (un point de terminaison SQL ou Cosmos DB, par exemple)
    > * Point de terminaison de stockage
    > * Point de terminaison Service Bus
    >
    > Cette configuration est nécessaire car Azure Database Migration Service ne dispose pas d’une connectivité Internet.

* Assurez-vous que les règles de groupe de sécurité réseau de votre réseau virtuel ne bloquent pas le port de sortie 443 de ServiceTag pour ServiceBus, Storage et AzureMonitor. Pour plus d’informations sur le filtrage du trafic de groupe de sécurité réseau de réseau virtuel, consultez l’article [Filtrer le trafic avec les groupes de sécurité réseau](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Ouvrez votre pare-feu Windows pour autoriser les connexions à partir de Réseau virtuel afin de permettre à Azure Database Migration Service d’accéder au serveur MySQL source via le port TCP 3306 (par défaut).
* Quand vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour autoriser les connexions à partir de Réseau virtuel afin de permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.
* Créez une [règle de pare-feu](../azure-sql/database/firewall-configure.md) au niveau du serveur ou [configurez des points de terminaison de service de réseau virtuel](../mysql/howto-manage-vnet-using-portal.md) pour la cible Azure Database pour MySQL afin d’autoriser Réseau virtuel pour Azure Database Migration Service à accéder aux bases de données cibles.
* Le serveur MySQL source doit être sur une édition MySQL Community prise en charge. Pour déterminer la version de l’instance MySQL, exécutez la commande suivante dans l’utilitaire MySQL ou MySQL Workbench :

    ```
    SELECT @@version;
    ```

* Azure Database pour MySQL ne prend en charge que les tables InnoDB. Pour convertir les tables MyISAM en InnoDB, consultez l’article [Convertir des tables MyISAM en InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)
* L’utilisateur doit disposer des privilèges nécessaires à la lecture des données sur la base de données source.

## <a name="migrate-database-schema"></a>Migrer le schéma de la base de données

Pour transférer tous les objets de base de données comme les schémas de table, les index et les procédures stockées, nous avons besoin d’extraire le schéma de la base de données source et de l’appliquer à la base de données cible. Pour extraire le schéma, vous pouvez utiliser mysqldump avec le paramètre `--no-data`. Pour cela, vous avez besoin d’un ordinateur capable de se connecter à la fois à la base de données MySQL source et au service Azure Database pour MySQL cible.

Pour exporter le schéma à l’aide de mysqldump, exécutez la commande suivante :

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Par exemple :

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Pour importer le schéma dans le service Azure Database pour MySQL cible, exécutez la commande suivante :

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Par exemple :

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Si votre schéma contient des clés étrangères, le chargement de données en parallèle pendant la migration est géré par la tâche de migration. Il n’est pas nécessaire de supprimer des clés étrangères pendant la migration du schéma.

Si la base de données contient des déclencheurs, l’intégrité des données est appliquée dans la cible avant la migration complète des données depuis la source. Nous vous recommandons de désactiver les déclencheurs dans toutes les tables au niveau de la cible pendant la migration, puis de les activer une fois la migration terminée.

Exécutez le script suivant dans MySQL Workbench sur la base de données cible pour extraire le script de suppression et le script d’ajout de déclencheurs.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Exécutez la requête de suppression de déclencheur (colonne DropQuery) générée dans le résultat pour supprimer les déclencheurs dans la base de données cible. La requête d’ajout de déclencheur peut être enregistrée afin d’être utilisée à l’issue de la migration des données.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration

L’inscription du fournisseur de ressources doit être effectuée sur chaque abonnement Azure une seule fois. Sans cette inscription, vous ne pourrez pas créer une instance d’**Azure Database Migration Service**.

1. Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.

   ![Afficher les abonnements au portail](media/tutorial-mysql-to-azure-mysql-offline-portal/01-dms-portal-select-subscription.png)

2. Sélectionnez l’abonnement dans lequel vous voulez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.

3. Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.

    ![S’inscrire auprès du fournisseur de ressources](media/tutorial-mysql-to-azure-mysql-offline-portal/02-dms-portal-register-rp.png)

## <a name="create-a-database-migration-service-instance"></a>Créer une instance de Database Migration Service

1. Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

    ![Place de marché Azure](media/tutorial-mysql-to-azure-mysql-offline-portal/03-dms-portal-marketplace.png)

2. Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.

    ![Créer une instance Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/04-dms-portal-marketplace-create.png)
  
3. Dans l’écran **Créer un service de migration**, spécifiez un nom pour le service, l’abonnement, et un réseau virtuel nouveau ou existant.

4. Sélectionnez un niveau tarifaire et accédez à l’écran de mise en réseau. La fonctionnalité de migration hors connexion est disponible dans les niveaux tarifaires Standard et Premium.

    Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    ![Configurer des paramètres de base Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/05-dms-portal-create-basic.png)

5. Sélectionnez un réseau virtuel existant dans la liste ou indiquez le nom du réseau virtuel à créer. Accédez à l’écran Vérifier + créer. Vous pouvez éventuellement ajouter des étiquettes au service à l’aide de l’écran correspondant.

    Le réseau virtuel fournit à Azure Database Migration Service un accès au serveur SQL Server source et à l’instance Azure SQL Database cible.

    ![Configurer des paramètres réseau Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/06-dms-portal-create-networking.png)

    Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez l’article [Créer un réseau virtuel au moyen du portail Azure](../virtual-network/quick-create-portal.md).

6. Passez en revue les configurations, puis sélectionnez **Créer** pour créer le service.
    
    ![Créer Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois le service créé, recherchez-le dans le portail Azure, ouvrez-le, puis créez un projet de migration.  

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.

    ![Localiser toutes les instances Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Sélectionnez votre instance de service de migration dans les résultats de la recherche, puis sélectionnez + **Nouveau projet de migration**.
    
    ![Créer un nouveau projet de migration](media/tutorial-mysql-to-azure-mysql-offline-portal/08-02-dms-portal-new-project.png)

3. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de sélection **Type de serveur source**, sélectionnez **MySQL**. Dans la zone de sélection **Type de serveur cible**, sélectionnez **Azure Database pour MySQL**, puis dans la zone de sélection **Type d’activité de migration**, sélectionnez **Migration des données\[ \]préversion**. Sélectionnez **Créer et exécuter une activité**.

    ![Créer un projet Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Une autre possibilité consiste à choisir **Créer un projet uniquement** pour créer le projet de migration à ce stade et exécuter la migration ultérieurement.

## <a name="configure-migration-project"></a>Configurer un projet de migration

1. Dans l’écran **Sélectionner la source**, spécifier les détails de connexion de l’instance de MySQL source, puis sélectionnez **Suivant : Sélectionner la cible >>** .

    ![Écran Ajouter les détails de la source](media/tutorial-mysql-to-azure-mysql-offline-portal/10-dms-portal-project-mysql-source.png)

2. Dans l’écran **Sélectionner la cible**, spécifiez les détails de connexion de l’instance cible d’Azure Database pour MySQL, puis sélectionnez **Suivant : Sélectionner les bases de données >>** .

    ![Écran Ajouter les détails de la cible](media/tutorial-mysql-to-azure-mysql-offline-portal/11-dms-portal-project-mysql-target.png)

3. Dans l’écran **Sélectionner les bases de données**, mappez la base de données source et la base de données cible concernées par la migration, puis sélectionnez **Suivant : Configurer les paramètres de migration >>** . Vous pouvez sélectionner l’option **Serveur source en lecture seule** pour que la source soit en lecture seule, mais ayez conscience qu’il s’agit d’un paramètre au niveau du serveur. Si cette option est sélectionnée, elle définit la tout le serveur en lecture seule, pas seulement les bases de données sélectionnées.
    
    Si la base de données cible porte le même nom que la base de données source, Azure Database Migration Service sélectionne la base de données cible par défaut.
    ![Écran Sélectionner les détails de la base de données](media/tutorial-mysql-to-azure-mysql-offline-portal/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
    > Bien que vous puissiez sélectionner plusieurs bases de données au cours de cette étape, le nombre de bases de données pouvant être migrées de cette manière et la vitesse de migration sont limités, puisque chaque base de données partage le calcul. Avec la configuration par défaut de la référence SKU Premium, chaque tâche de migration tente de migrer deux tables en parallèle. Ces tables peuvent provenir de n’importe quelle base de données sélectionnée. Si la migration n’est pas assez rapide, vous pouvez fractionner les activités de migration des bases de données en différentes tâches de migration et les répartir sur plusieurs services. De plus, il existe une limite de 10 instances d’Azure Database Migration Service par abonnement et par région.
    > Pour un contrôle plus précis du débit et de la parallélisation de la migration, reportez-vous à l’article [PowerShell : Exécuter une migration hors connexion à partir d’une base de données MySQL vers Azure Database pour MySQL à l’aide de DMS](./migrate-mysql-to-azure-mysql-powershell.md).

4. Dans l’écran **Configurer les paramètres de migration**, sélectionnez les tables qui doivent faire partie de la migration, puis sélectionnez **Suivant : Résumé >>** . Si les tables cibles contiennent des données, elles ne sont pas sélectionnées par défaut, mais vous pouvez les sélectionner explicitement et elles seront tronquées avant de démarrer la migration.

    ![Écran Sélectionner des tables](media/tutorial-mysql-to-azure-mysql-offline-portal/13-dms-portal-project-mysql-select-tbl.png)

5. Dans l’écran **Résumé**, dans la zone de texte **Nom de l’activité**, spécifiez un nom pour l’activité de migration, puis examinez le résumé pour vous assurer que les détails de la source et de la cible correspondent à ceux que vous avez spécifiés précédemment.

    ![Récapitulatif du projet de migration](media/tutorial-mysql-to-azure-mysql-offline-portal/14-dms-portal-project-mysql-activity-summary.png)

6. Sélectionnez **Démarrer la migration**. La fenêtre d’activité de migration s’affiche, et le champ **État** de l’activité présente la valeur **Initialisation en cours**. La valeur **État** passe à **En cours d’exécution** au démarrage des migrations des tables.
 
     ![Exécution de la migration](media/tutorial-mysql-to-azure-mysql-offline-portal/15-dms-portal-project-mysql-running.png)

## <a name="monitor-the-migration"></a>Surveiller la migration

1. Dans l’écran de l’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage et voir le nombre de tables migrées progresser. 

2. Vous pouvez cliquer sur le nom de la base de données dans l’écran de l’activité pour voir l’état de chaque table pendant la migration. Sélectionnez **Actualiser** pour mettre à jour l’affichage. 

     ![Surveillance de la migration](media/tutorial-mysql-to-azure-mysql-offline-portal/16-dms-portal-project-mysql-monitor.png)

## <a name="complete-the-migration"></a>Effectuer la migration

1. Dans l’écran d’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage jusqu’à voir **l’état** de la migration s’afficher comme **Terminé**.

    ![Terminer la migration](media/tutorial-mysql-to-azure-mysql-offline-portal/17-dms-portal-project-mysql-complete.png)

## <a name="post-migration-activities"></a>Activités à l’issue de la migration

Le basculement de la migration dans une migration hors connexion est un processus qui dépend de l’application et qui sort du cadre de ce document, mais les activités suivantes sont préconisées après la migration :

1. Créez des connexions, des rôles et des autorisations conformément à ce que demande l’application.
2. Recréez tous les déclencheurs dans la base de données cible qui ont été extraits pendant l’étape de prémigration.
3. Effectuez des tests d’intégrité de l’application dans la base de données cible pour certifier la migration. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’envisagez pas de continuer à utiliser Database Migration Service, vous pouvez supprimer ce service en effectuant les étapes suivantes :

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.

    ![Localiser toutes les instances de DMS](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Sélectionnez votre instance du service de migration dans les résultats de la recherche, puis sélectionnez **Supprimer le service**.
    
    ![Supprimer le service de migration](media/tutorial-mysql-to-azure-mysql-offline-portal/18-dms-portal-delete.png)

3. Dans la boîte de dialogue de confirmation, tapez le nom du service dans la zone de texte **TAPER LE NOM DU SERVICE DE MIGRATION DE BASE DE DONNÉES**, puis sélectionnez **Supprimer**.

    ![Confirmer la suppression du service de migration](media/tutorial-mysql-to-azure-mysql-offline-portal/19-dms-portal-deleteconfirm.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les problèmes connus et les limitations lors des migrations avec DMS, consultez l’article [Problèmes courants : Azure Database Migration Service](./known-issues-troubleshooting-dms.md).
* Pour résoudre les problèmes de connectivité des bases de données sources lors de l’utilisation de DMS, consultez l’article [Problèmes de connexion aux bases de données sources](./known-issues-troubleshooting-dms-source-connectivity.md).
* Pour plus d’informations sur Azure Database Migration Service, consultez l’article [Qu’est-ce qu’Azure Database Migration Service ?](./dms-overview.md).
* Pour plus d’informations sur Azure Database pour MySQL, voir l’article [Qu’est-ce qu’Azure Database pour MySQL ?](../mysql/overview.md).
* Pour obtenir des instructions sur l’utilisation de DMS par le biais de PowerShell, consultez l’article [PowerShell : Exécuter une migration hors connexion à partir d’une base de données MySQL vers Azure Database pour MySQL à l’aide de DMS](./migrate-mysql-to-azure-mysql-powershell.md).
