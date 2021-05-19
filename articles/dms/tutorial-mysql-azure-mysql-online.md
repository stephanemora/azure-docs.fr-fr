---
title: 'Tutoriel : Migrer MySQL en ligne vers Azure Database pour MySQL'
titleSuffix: Azure Database Migration Service
description: Découvrez comment effectuer la migration en ligne d’une instance locale de MySQL vers Azure Database pour MySQL, à l’aide d’Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 561cc2a32ce7c9d3fd61fafb47326ce9c95cad45
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109753198"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutoriel : Migration de MySQL vers Azure Database pour MySQL en ligne à l’aide de DMS

Vous pouvez utiliser Azure Database Migration Service pour effectuer la migration de bases de données entre une instance locale de MySQL et [Azure Database pour MySQL](../mysql/index.yml), pour un temps d’arrêt minimal. En d’autres termes, la migration peut être effectuée avec un temps d’arrêt minimal de l’application. Dans ce tutoriel, vous allez effectuer la migration de l’exemple de base de données **Employees** à partir d’une instance MySQL 5.7 locale vers Azure Database pour MySQL, à l’aide d’une activité de migration en ligne dans Azure Database Migration Service.

> [!IMPORTANT]
> Le scénario de migration en ligne « MySQL vers Azure Database pour MySQL » **ne sera plus disponible après le 1er juin 2021**. Une [fonctionnalité de migration hors connexion](./tutorial-mysql-azure-mysql-offline-portal.md), parallélisée et hautement performante, est **désormais disponible en préversion** pour prendre en charge les migrations « MySQL vers Azure Database pour MySQL ». Pour les migrations en ligne, vous pouvez utiliser des outils open source comme [MyDumper/MyLoader](https://centminmod.com/mydumper.html) avec [réplication des données entrantes](/azure/mysql/concepts-data-in-replication).

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
>
> * Migrez l’exemple de schéma à l’aide de l’utilitaire mysqldump.
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration en utilisant Azure Database Migration Service.
> * Exécuter la migration.
> * Surveiller la migration.

> [!NOTE]
> L’utilisation d’Azure Database Migration Service pour effectuer une migration en ligne nécessite la création d’une instance basée sur le niveau tarifaire Premium.

> [!IMPORTANT]
> Pour une expérience de migration optimale, Microsoft recommande de créer une instance Azure Database Migration Service dans la même région Azure que la base de données cible. Le déplacement des données entre les régions ou les zones géographiques peut ralentir le processus de migration et introduire des erreurs.

> [!NOTE]
> Communication sans stéréotype
>
> La diversité et l’inclusion sont au cœur des valeurs de Microsoft. Cet article contient des références au mot _esclave_. Le [guide de style de Microsoft sur la communication sans stéréotype](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) le reconnaît comme un mot à exclure. Le mot est utilisé dans cet article pour des raisons de cohérence, car il s’agit du mot qui figure dans le logiciel. Une fois que le mot aura été supprimé du logiciel, cet article sera mis à jour en conséquence.
>


## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

* Téléchargez et installez [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 ou 5.7. La version de MySQL locale doit correspondre à la version d’Azure Database pour MySQL. Par exemple, MySQL 5.6 ne peut migrer que vers Azure Database pour MySQL 5.6, et ne peut pas être migré vers 5.7. Les migrations vers ou depuis MySQL 8.0 ne sont pas prises en charge.
* [Créez une instance dans Azure Database pour MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Consultez l’article [Utiliser MySQL Workbench pour se connecter et interroger des données](../mysql/connect-workbench.md) pour plus d’informations sur la connexion et la création d’une base de données à l’aide de l’application Workbench.  
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
* Configurez votre [pare-feu Windows pour accéder au moteur de base de données](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Ouvrez votre pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au serveur MySQL source via le port TCP 3306 (par défaut).
* Lorsque vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.
* Créez une [règle de pare-feu](../azure-sql/database/firewall-configure.md) de niveau serveur pour Azure Database pour MySQL afin de permettre à Azure Database Migration Service d’accéder aux bases de données cibles. Fournissez la plage de sous-réseau du réseau virtuel utilisé pour Azure Database Migration Service.
* Le serveur MySQL source doit être sur une édition MySQL Community prise en charge. Pour déterminer la version de l’instance MySQL, exécutez la commande suivante dans l’utilitaire MySQL ou MySQL Workbench :

    ```
    SELECT @@version;
    ```

* Azure Database pour MySQL ne prend en charge que les tables InnoDB. Pour convertir les tables MyISAM en InnoDB, consultez l’article [Convertir des tables MyISAM en InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)

* Activez la journalisation binaire dans le fichier my.ini (Windows) ou my.cnf (Unix) dans la base de données source à l’aide de la configuration suivante :

  * **server_id** = 1 ou supérieur (uniquement pour MySQL 5.6)
  * **log-bin** =\<path>> (valable uniquement pour MySQL 5.6)    Par exemple : log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (il est recommandé de ne pas utiliser la valeur zéro ; uniquement pour MySQL 5.6)
  * **binlog_row_image** = full (uniquement pour MySQL 5.6)
  * **log_slave_updates** = 1

* L'utilisateur doit avoir le rôle ReplicationAdmin avec les privilèges suivants :

  * **CLIENT DE RÉPLICATION** : obligatoire pour les tâches Traitement des changements uniquement. En d’autres termes, les tâches Chargement complet uniquement n’ont pas besoin de ce privilège.
  * **RÉPLICA DE RÉPLICATION** : obligatoire pour les tâches Traitement des changements uniquement. En d’autres termes, les tâches Chargement complet uniquement n’ont pas besoin de ce privilège.
  * **SUPER** : obligatoire uniquement dans les versions antérieures à MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrer l’exemple de schéma

Pour compléter tous les objets de base de données tels que les schémas de table, les index et les procédures stockées, nous devons extraire le schéma à partir de la base de données source et l’appliquer à la base de données. Pour extraire le schéma, vous pouvez utiliser mysqldump avec le paramètre `--no-data`.

En supposant que vous avez l’exemple de base de données des **employés** MySQL dans le système local, la commande pour effectuer la migration du schéma en utilisant mysqldump est :

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Par exemple :

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Pour importer le schéma vers la cible Azure Database pour MySQL, exécutez la commande suivante :

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Par exemple :

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Si vous avez des clés étrangères dans votre schéma, la charge initiale et la synchronisation continue de la migration échouent.  Exécutez le script suivant dans MySQL Workbench pour extraire le script de clé étrangère Drop et ajouter le script de clé étrangère.

```sql
SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
    KCU.TABLE_NAME,
    KCU.COLUMN_NAME,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
    WHERE
      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
  AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
  GROUP BY SchemaName;
 ```

Exécutez la clé étrangère Drop (qui est la deuxième colonne) dans le résultat de la requête pour supprimer la clé étrangère.

> [!NOTE]
> Azure DMS ne prend pas en charge l’action référentielle CASCADE, qui permet de supprimer ou de mettre à jour automatiquement une ligne correspondante dans la table enfant lorsqu’une ligne est supprimée ou mise à jour dans la table parente. Pour plus d’informations, dans la documentation MySQL, consultez la section Actions référentielles de l’article [Contraintes FOREIGN KEY](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html).
> Azure DMS requiert la suppression des contraintes de clé étrangère sur le serveur de base de données cible pendant le chargement initial des données, et vous ne pouvez pas utiliser les actions référentielles. Si votre charge de travail dépend de la mise à jour d’une table enfant associée par le biais de cette action référentielle, nous vous recommandons d’effectuer [un fichier de sauvegarde et une restauration](../mysql/concepts-migrate-dump-restore.md) à la place. 


> [!IMPORTANT]
> Si vous importez des données à l’aide d’une sauvegarde, supprimez les commandes CREATE DEFINER manuellement ou à l’aide de la commande --skip-definer lors de l’exécution d’une tâche mysqldump. DEFINER requiert des privilèges de superutilisateur pour créer et est limité dans Azure Database pour MySQL.

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

1. Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.

   ![Afficher les abonnements au portail](media/tutorial-mysql-to-azure-mysql-online/01-portal-select-subscriptions.png)

2. Sélectionnez l’abonnement dans lequel vous voulez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.

    ![Afficher les fournisseurs de ressources](media/tutorial-mysql-to-azure-mysql-online/02-01-portal-select-resource-provider.png)

3. Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.

    ![S’inscrire auprès du fournisseur de ressources](media/tutorial-mysql-to-azure-mysql-online/02-02-portal-register-resource-provider.png)

## <a name="create-a-database-migration-service-instance"></a>Créer une instance de Database Migration Service

1. Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

    ![Place de marché Azure](media/tutorial-mysql-to-azure-mysql-online/03-dms-portal-marketplace.png)

2. Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.

    ![Créer une instance Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/04-dms-portal-marketplace-create.png)
  
3. Dans l’écran **Créer un service de migration**, spécifiez un nom pour le service, l’abonnement, et un réseau virtuel nouveau ou existant.

4. Sélectionnez un niveau tarifaire et accédez à l’écran de mise en réseau. La fonctionnalité de migration hors connexion est disponible dans les niveaux tarifaires Standard et Premium.

    Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    ![Configurer des paramètres de base Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/05-dms-portal-create-basic.png)

5. Sélectionnez un réseau virtuel existant dans la liste ou indiquez le nom du réseau virtuel à créer. Accédez à l’écran Vérifier + créer. Vous pouvez éventuellement ajouter des étiquettes au service à l’aide de l’écran correspondant.

    Le réseau virtuel fournit à Azure Database Migration Service un accès au serveur SQL Server source et à l’instance Azure SQL Database cible.

    ![Configurer des paramètres réseau Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/06-dms-portal-create-networking.png)

    Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez l’article [Créer un réseau virtuel au moyen du portail Azure](../virtual-network/quick-create-portal.md).

6. Passez en revue les configurations, puis sélectionnez **Créer** pour créer le service.
    
    ![Créer Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois le service créé, recherchez-le dans le portail Azure, ouvrez-le, puis créez un projet de migration.  

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.

    ![Localiser toutes les instances Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/08-01-dms-portal-search-service.png)

2. Sélectionnez votre instance de service de migration dans les résultats de la recherche, puis sélectionnez + **Nouveau projet de migration**.
    
    ![Créer un nouveau projet de migration](media/tutorial-mysql-to-azure-mysql-online/08-02-dms-portal-new-project.png)

3. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de sélection **Type de serveur source**, sélectionnez **MySQL**. Dans la zone de sélection **Type de serveur cible**, sélectionnez **Azure Database pour MySQL**, puis dans la zone de sélection **Type d’activité de migration**, sélectionnez **Migration des données en ligne**. Sélectionnez **Créer et exécuter une activité**.

    ![Créer un projet Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Une autre possibilité consiste à choisir **Créer un projet uniquement** pour créer le projet de migration à ce stade et exécuter la migration ultérieurement.

## <a name="configure-migration-project"></a>Configurer un projet de migration

1. Dans l’écran **Sélectionner la source**, spécifier les détails de connexion de l’instance de MySQL source, puis sélectionnez **Suivant : Sélectionner la cible >>** .

    ![Écran Ajouter les détails de la source](media/tutorial-mysql-to-azure-mysql-online/10-dms-portal-project-mysql-source.png)

2. Dans l’écran **Sélectionner la cible**, spécifiez les détails de connexion de l’instance cible d’Azure Database pour MySQL, puis sélectionnez **Suivant : Sélectionner les bases de données >>** .

    ![Écran Ajouter les détails de la cible](media/tutorial-mysql-to-azure-mysql-online/11-dms-portal-project-mysql-target.png)

3. Dans l’écran **Sélectionner les bases de données**, mappez la base de données source et la base de données cible concernées par la migration, puis sélectionnez **Suivant : Configurer les paramètres de migration >>** . Vous pouvez sélectionner l’option **Serveur source en lecture seule** pour que la source soit en lecture seule, mais ayez conscience qu’il s’agit d’un paramètre au niveau du serveur. Si cette option est sélectionnée, elle définit la tout le serveur en lecture seule, pas seulement les bases de données sélectionnées.
    
    Si la base de données cible porte le même nom que la base de données source, Azure Database Migration Service sélectionne la base de données cible par défaut.
    ![Écran Sélectionner les détails de la base de données](media/tutorial-mysql-to-azure-mysql-online/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
   > Bien que vous puissiez sélectionner plusieurs bases de données au cours de cette étape, chaque instance d’Azure Database Migration Service prend en charge jusqu’à 4  bases de données pour la migration simultanée. De plus, il existe une limite de 10 instances d’Azure Database Migration Service par abonnement et par région. Par exemple, si vous prévoyez la migration de 80 bases de données, vous ne pourrez déplacer que 40 d’entre elles simultanément au sein d’une même région, et uniquement si vous avez créé 10 instances d’Azure Database Migration Service.

4. Dans l’écran **Configurer les paramètres de migration**, sélectionnez les tables qui doivent faire partie de la migration, puis sélectionnez **Suivant : Résumé >>** . Si les tables cibles contiennent des données, elles ne sont pas sélectionnées par défaut, mais vous pouvez les sélectionner explicitement et elles seront tronquées avant de démarrer la migration.

    ![Écran Sélectionner des tables](media/tutorial-mysql-to-azure-mysql-online/13-dms-portal-project-mysql-select-tbl.png)

5. Dans l’écran **Résumé**, dans la zone de texte **Nom de l’activité**, spécifiez un nom pour l’activité de migration, puis examinez le résumé pour vous assurer que les détails de la source et de la cible correspondent à ceux que vous avez spécifiés précédemment.

    ![Récapitulatif du projet de migration](media/tutorial-mysql-to-azure-mysql-online/14-dms-portal-project-mysql-activity-summary.png)

6. Sélectionnez **Démarrer la migration**. La fenêtre d’activité de migration s’affiche, et le champ **État** de l’activité présente la valeur **Initialisation en cours**. La valeur **État** passe à **En cours d’exécution** au démarrage des migrations des tables.

## <a name="monitor-the-migration"></a>Surveiller la migration

1. Dans l’écran d’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage jusqu’à voir **l’état** de la migration s’afficher comme **Terminé**.

     ![État d’activité : Terminé](media/tutorial-mysql-to-azure-mysql-online/15-dms-activity-completed.png)

2. Sous **Nom de la base de données**, sélectionnez une base de données spécifique pour obtenir l’état de migration des opérations **Chargement complet des données en cours** et **Synchronisation des données incrémentielles**.

    L’opération Charge complète des données affichera l’état de migration de la charge initiale, tandis que l’opération Synchronisation des données incrémentielles indiquera l’état de capture des changements de données (CDC).

     ![État de l’activité : Charge complète terminée](media/tutorial-mysql-to-azure-mysql-online/16-dms-activity-full-load-completed.png)

     ![État de l’activité : Synchronisation des données incrémentielles](media/tutorial-mysql-to-azure-mysql-online/17-dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Effectuer le basculement de la migration

Une fois le chargement complet initial effectué, les bases de données identifiées par le libellé **Prêt pour le basculement**.

1. Lorsque vous êtes prêt à effectuer la migration de base de données, sélectionnez **Démarrer le basculement**.

    ![Démarrer le basculement](media/tutorial-mysql-to-azure-mysql-online/18-dms-start-cutover.png)

2. Veillez à arrêter toutes les transactions entrantes pour la base de données source ; attendez que le compteur **Changements en attente** indique la valeur **0**.
3. Sélectionnez **Confirmer**, puis **Appliquer**.
4. Lorsque la migration de base de données présente l’état **Terminé**, connectez vos applications à la nouvelle base de données Azure SQL cible.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les limitations et problèmes connus lors de l’exécution de migrations en ligne vers Azure Database pour MySQL, consultez l’article [Known issues and workarounds with Azure Database for MySQL online migrations](known-issues-azure-mysql-online.md) (Problèmes connus et solutions de contournement pour les migrations Azure Database pour MySQL en ligne).
* Pour plus d’informations sur Azure Database Migration Service, consultez l’article [Qu’est-ce qu’Azure Database Migration Service ?](./dms-overview.md).
* Pour plus d’informations sur Azure Database pour MySQL, voir l’article [Qu’est-ce qu’Azure Database pour MySQL ?](../mysql/overview.md).