---
title: Migrer les ressources des bases de données Azure, d'Azure Allemagne vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources de base de données Azure d’Azure Allemagne vers Azure global.
ms.topic: article
ms.date: 03/29/2021
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: 0843adb3060ca4ce8769b79eef3f05e043d4dc5d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259596"
---
# <a name="migrate-database-resources-to-global-azure"></a>Migrer des ressources de base de données vers Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Cet article contient des informations qui peuvent vous aider à migrer des ressources de base de données Azure d’Azure Allemagne vers Azure global.

## <a name="sql-database"></a>Base de données SQL

Pour migrer de plus petites charges de travail Azure SQL Database, utilisez la fonction d’exportation afin de créer un fichier BACPAC. Un fichier BACPAC est un fichier (zippé) compressé qui contient des métadonnées et des données issues de la base de données SQL Server. Après avoir créé le fichier BACPAC, vous pouvez le copier dans l’environnement cible (par exemple, en utilisant AzCopy) et utiliser la fonction d’importation pour régénérer la base de données. Tenez compte des points suivants :

- Pour qu’une exportation soit cohérente au niveau transactionnel, assurez-vous que l’une des conditions suivantes est remplie :
  - Aucune activité d’écriture ne survient lors de l’exportation.
  - Vous exportez une copie cohérente au niveau transactionnel d’une base de données SQL.
- Pour que vous puissiez l’exporter vers le stockage Blob Azure, la taille du fichier BACPAC ne doit pas dépasser 200 Go. Si vous voulez exporter un fichier BACPAC plus volumineux, utilisez le stockage local.
- Si l’opération d’exportation depuis la base de données SQL Database prend plus de 20 heures, il se peut que l’opération soit annulée. Consultez les articles suivants pour obtenir des conseils sur la façon d’améliorer les performances.

> [!NOTE]
> La chaîne de connexion change après l’opération d’exportation, car le nom DNS du serveur change pendant l’exportation.

Pour plus d’informations :

- Découvrez comment [exporter une base de données vers un fichier BACPAC](../azure-sql/database/database-export.md).
- Découvrez comment [importer un fichier BACPAC dans une base de données](../azure-sql/database/database-import.md).
- Consultez la [documentation relative à Azure SQL Database](/azure/sql-database/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="migrate-sql-database-using-active-geo-replication"></a>Migrer une base de données SQL en utilisant la géo-réplication active

Pour les bases de données trop volumineuses pour les fichiers BACPAC, ou pour migrer d'un cloud à l'autre et rester en ligne avec un minimum de temps d'arrêt, vous pouvez configurer une géo-réplication active d'Azure Allemagne vers Azure global.

> [!IMPORTANT]
> La configuration de la géo-réplication active pour migrer des bases de données vers Azure global est uniquement prise en charge à l’aide de Transact-SQL (T-SQL) et avant la migration, vous devez demander l’activation de votre abonnement pour prendre en charge la migration vers Azure global. Pour soumettre une demande, vous devez utiliser [ce lien de demande d'assistance](#requesting-access). 

> [!Note]
> Les régions Azure global Cloud, Allemagne Centre-Ouest et Allemagne Nord, sont les régions prises en charge pour la géo-réplication Active avec le Cloud Azure Allemagne. Si une autre région Azure globale est souhaitée comme destination de la ou des bases de données finales, la recommandation après l’achèvement de la migration vers Azure global consiste à configurer un lien de géo-réplication supplémentaire à partir de Allemagne Centre-Ouest ou Allemagne Nord vers la région de Cloud global Azure requise.

Pour plus de détails sur les coûts de la géo-réplication active, voir la section intitulée **Géo-réplication active** dans la [tarification d'Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

La migration de bases de données avec la géo-réplication active nécessite un serveur logique Azure SQL dans Azure global. Vous pouvez créer le serveur à l’aide du portail, Azure PowerShell, Azure CLI, etc., mais la configuration de la géo-réplication active pour migrer d’Azure Allemagne vers Azure global est uniquement prise en charge à l’aide de Transact-SQL (T-SQL).

> [!IMPORTANT]
> Lors de la migration entre des clouds, les préfixes de nom du serveur principal (Azure Allemagne) et secondaire (Azure global) doivent être différents. Si les noms de serveur sont identiques, l’exécution de l’instruction ALTER DATABASE réussira, mais la migration échouera. Par exemple, si le préfixe du nom du serveur principal est `myserver` ( `myserver.database.cloudapi.de` ), le préfixe du nom du serveur secondaire dans Global Azure ne peut pas être `myserver`.


L' `ALTER DATABASE` instruction vous permet de spécifier un serveur cible dans Azure global en utilisant son nom de serveur DNS complet côté cible. 


```sql
ALTER DATABASE [sourcedb] add secondary on server [public-server.database.windows.net]
```


- *`sourcedb`* représente le nom de la base de données dans un serveur Azure SQL dans Azure Allemagne. 
- *`public-server.database.windows.net`* représente le nom du serveur de Azure SQL qui existe dans Azure global, où la base de données doit être migrée. L'espace de nom "database.windows.net" est requis, remplacez *public-server* par le nom de votre serveur SQL logique dans global Azure. Le serveur dans Azure global doit avoir un nom différent de celui du serveur principal dans Azure Allemagne.


La commande est exécutée sur la base de données MASTER, située sur le serveur qui héberge la base de données locale qui devient la base de données primaire. 
- L'API T-SQL start-copy authentifie l'utilisateur connecté sur le serveur du cloud public en trouvant un utilisateur avec le même nom de login/utilisateur SQL dans la base de données principale de ce serveur. Cette approche est indépendante du Cloud ; ainsi, l’API T-SQL est utilisée pour démarrer des copies entre cloud. Pour les autorisations et de plus amples informations sur ce sujet, voir [Création et utilisation de la géo-réplication active](../azure-sql/database/active-geo-replication-overview.md) et [ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql/).
- À l’exception de l’extension de commande T-SQL initiale indiquant un serveur logique Azure SQL dans Azure global, le reste du processus de géo-réplication active est identique à l’exécution existante dans le cloud local. Pour obtenir des instructions détaillées sur la création de la géo-réplication Active, consultez [création et utilisation de la géo-réplication Active](../azure-sql/database/active-geo-replication-overview.md) avec une exception. la base de données secondaire est créée dans le serveur logique secondaire créé dans Global Azure. 
- Une fois que la base de données secondaire existe dans Azure global (en tant que copie en ligne de la base de données Azure Allemagne), le client peut lancer un basculement de base de données d’Azure Allemagne vers Azure global pour cette base de données à l’aide de la commande ALTER database T-SQL (voir le tableau ci-dessous).
- Après le basculement, une fois que la base de données secondaire devient une base de données primaire dans Azure global, vous pouvez arrêter la géo-réplication Active et supprimer la base de données secondaire du côté Azure Allemagne à tout moment (voir le tableau ci-dessous et les étapes présentes dans le diagramme). 
- Après le basculement, la base de données secondaire dans Azure Allemagne continue à entraîner des coûts jusqu’à ce qu’elle soit supprimée.
      
- L’utilisation de la `ALTER DATABASE` commande est la seule façon de configurer la géo-réplication Active pour migrer une base de données Azure Allemagne vers Azure global. 
- Aucune Portail Azure, Azure Resource Manager, PowerShell ou CLI n’est disponible pour configurer la géo-réplication Active pour cette migration. 

Pour migrer une base de données d’Azure Allemagne vers Azure global :   

1.  Choisissez la base de données utilisateur dans Azure Allemagne, par exemple, `azuregermanydb`
2.  Créez un serveur logique dans Global Azure (le cloud public), par exemple, `globalazureserver`. Son nom de domaine pleinement qualifié (FQDN) est `globalazureserver.database.windows.net`.
3.  Démarrez la géo-réplication active d’Azure Allemagne vers Azure global en exécutant cette commande T-SQL sur le serveur dans Azure Allemagne. Notez que le nom DNS complet est utilisé pour le serveur public `globalazureserver.database.windows.net`. Cela permet d’indiquer que le serveur cible est dans Azure global et non dans Azure Allemagne.

    ```sql
    ALTER DATABASE [azuregermanydb] ADD SECONDARY ON SERVER [globalazureserver.database.windows.net];
    ```

4.  Lorsque la réplication est prête à déplacer la charge de travail en lecture-écriture sur le serveur Azure global, lancez un basculement planifié vers Azure global en exécutant cette commande T-SQL sur le serveur Azure global.

    ```sql
    ALTER DATABASE [azuregermanydb] FAILOVER;
    ```

5.  Le lien de géo-réplication Active peut être arrêté avant ou après le processus de basculement. L’exécution de la commande T-SQL suivante après le basculement planifié supprime le lien de géo-réplication avec la base de données dans Azure global en tant que copie en lecture-écriture. Elle doit être exécutée sur le serveur logique de la base de données géo-primaire actuelle (par exemple, sur le serveur Azure global). Le processus de migration sera ainsi terminé.

    ```sql
    ALTER DATABASE [azuregermanydb] REMOVE SECONDARY ON SERVER [azuregermanyserver];
    ```

    La commande T-SQL suivante, lorsqu’elle est exécutée avant le basculement planifié, arrête également le processus de migration, mais dans ce cas, la base de données dans Azure Allemagne restera la copie en lecture-écriture. Cette commande T-SQL doit également être exécutée sur le serveur logique de la base de données géo-primaire active, dans ce cas sur le serveur Azure Allemagne.

    ```sql
    ALTER DATABASE [azuregermanydb] REMOVE SECONDARY ON SERVER [globalazureserver];
    ```

Ces étapes pour migrer des bases de données Azure SQL d’Azure Allemagne vers Azure global peuvent également être suivies à l’aide de la géo-réplication active.


Pour plus d’informations, les tableaux suivants indiquent les commandes T-SQL pour la gestion du basculement. Les commandes suivantes sont prises en charge pour la géo-réplication Active entre les clouds entre Azure Allemagne et global Azure : 
 
|Commande |Description|
|:--|:--| 
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Utilise l’argument ADD SECONDARY ON SERVER afin de créer une base de données secondaire pour une base de données existante puis lance la réplication des données| 
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Utilise l’argument FAILOVER ou FORCE_FAILOVER_ALLOW_DATA_LOSS pour basculer d’une base de données secondaire à une base de données principale afin de lancer le basculement  |
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Utilise l’argument REMOVE SECONDARY ON SERVER pour mettre fin à une réplication de données entre une base de données SQL et la base de données secondaire spécifiée.  |
 
### <a name="active-geo-replication-monitoring-system-views"></a>Vues système de la surveillance de la géo-réplication Active 
 
|Commande |Description|
|:--|:--| 
|[sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database?view=azuresqldb-current&preserve-view=true)|Retourne des informations concernant tous les liens de réplication existants pour chaque base de données sur le serveur Azure SQL Database. |
|[sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database?view=azuresqldb-current&preserve-view=true) |Obtient l’heure de la dernière réplication, le dernier décalage de la réplication et d’autres informations sur le lien de réplication pour une base de données SQL spécifique. |
|[sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?view=azuresqldb-current&preserve-view=true) | Affiche l’état de toutes les opérations de base de données, y compris l’état des liens de réplication. | 
|[sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync?view=azuresqldb-current&preserve-view=true) | Oblige l’application à attendre que toutes les transactions validées soient répliquées et acceptées par la base de données secondaire active. |
 

## <a name="migrate-sql-database-long-term-retention-backups"></a>Migration des sauvegardes de rétention à long terme des bases de données SQL

La migration d’une base de données avec la géo-réplication ou le fichier BACPAC n’est pas copiée sur les sauvegardes de rétention à long terme, que la base de données peut avoir dans Azure Allemagne. Pour migrer des sauvegardes de rétention à long terme existantes vers la région Azure globale cible, vous pouvez utiliser la procédure de sauvegarde de rétention à long terme. 

>[!Note]
>Les méthodes de copie de sauvegarde LTR documentées ici peuvent uniquement copier les sauvegardes LTR d’Azure Allemagne vers Azure global. La copie de sauvegardes PITR à l’aide de ces méthodes n’est pas prise en charge. 
>

### <a name="pre-requisites"></a>Conditions préalables

1. La base de données cible dans laquelle vous copiez les sauvegardes LTR, dans Global Azure doit exister avant de commencer la copie des sauvegardes. Il est recommandé de commencer par migrer la base de données source à l’aide de la [géo-réplication Active](#migrate-sql-database-using-active-geo-replication) , puis de lancer la copie de sauvegarde LTR. Cela permet de s’assurer que les sauvegardes de base de données sont copiées dans la base de données de destination correcte. Cette étape n’est pas obligatoire si vous effectuez une copie sur des sauvegardes LTR d’une base de données supprimée. Lors de la copie de sauvegardes LTR d’une base de données supprimée, un DatabaseID factice est créé dans la région cible. 
2. Installer ce [module PowerShell Az](https://www.powershellgallery.com/packages/Az.Sql/3.0.0-preview)
3. Avant de commencer, assurez-vous que les [rôles Azure RBAC](../azure-sql/database/long-term-backup-retention-configure.md#prerequisites) requis sont accordés au niveau de l'**abonnement** ou du **groupe de ressources**. Remarque : pour accéder aux sauvegardes LTR qui appartiennent à un serveur abandonné, l'autorisation doit être accordée dans la portée de l'abonnement de ce serveur. . 


### <a name="limitations"></a>Limites  

- Les groupes de basculement ne sont pas pris en charge. Cela signifie que les clients qui migrent la ou les bases de données Azure Allemagne devront gérer les chaînes de connexion elles-mêmes pendant le basculement.
- Aucune prise en charge des Portail Azure, des API Azure Resource Manager, de PowerShell ou de l’interface CLI. Cela signifie que chaque migration Azure Allemagne devra gérer la configuration de la géo-réplication Active et le basculement via T-SQL.
- Les clients ne peuvent pas créer plusieurs zones géographiques secondaires dans Azure global pour les bases de données dans Azure Allemagne.
- La création d’une zone géographique secondaire doit être lancée à partir de la région Azure Allemagne.
- Les clients peuvent migrer des bases de données depuis Azure Allemagne uniquement vers Azure global. Actuellement aucune autre migration entre Cloud n’est prise en charge. 
- Les utilisateurs Azure AD des bases de données utilisateur Azure Allemagne sont migrés mais ne sont pas disponibles dans le nouveau locataire Azure AD où réside la base de données migrée. Pour activer ces utilisateurs, ceux-ci doivent être supprimés et recréés manuellement à l’aide de l’Azure AD les utilisateurs actuels disponibles dans le nouveau client Azure AD où réside la base de données nouvellement migrée.  


### <a name="copy-long-term-retention-backups-using-powershell"></a>Copie des sauvegardes de rétention à long terme à l'aide de PowerShell

Une nouvelle commande PowerShell **Copy-AzSqlDatabaseLongTermRetentionBackup** a été introduite, qui peut être utilisée pour copier les sauvegardes de rétention à long terme d’Azure Allemagne vers les régions globales Azure. 

1. **Copier la sauvegarde LTR à l’aide du nom de sauvegarde** L’exemple suivant montre comment vous pouvez copier une sauvegarde LTR d’Azure Allemagne vers une région Azure globale à l’aide de BackupName.  

```powershell
# Source database and target database info
$location = "<location>"
$sourceRGName = "<source resourcegroup name>"
$sourceServerName = "<source server name>"
$sourceDatabaseName = "<source database name>"
$backupName = "<backup name>"
$targetDatabaseName = "<target database name>"
$targetSubscriptionId = "<target subscriptionID>"
$targetRGName = "<target resource group name>"
$targetServerFQDN = "<targetservername.database.windows.net>"

Copy-AzSqlDatabaseLongTermRetentionBackup 
    -Location $location 
    -ResourceGroupName $sourceRGName 
    -ServerName $sourceServerName 
    -DatabaseName $sourceDatabaseName
    -BackupName $backupName
    -TargetDatabaseName $targetDatabaseName 
    -TargetSubscriptionId $targetSubscriptionId
    -TargetResourceGroupName $targetRGName
    -TargetServerFullyQualifiedDomainName $targetServerFQDN 
```

2. **Copie de la sauvegarde LTR à l'aide de l'ID de ressource de sauvegarde** L'exemple suivant montre comment vous pouvez copier la sauvegarde LTR d'Azure Allemagne vers la région globale d'Azure, à l'aide d'un ID de ressource de sauvegarde. Cet exemple peut également être utilisé pour copier des sauvegardes d’une base de données supprimée. 

```powershell
$location = "<location>"
# list LTR backups for All databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $location -DatabaseState All

# select the LTR backup you want to copy
$ltrBackup = $ltrBackups[0]
$resourceID = $ltrBackup.ResourceId

# Source Database and target database info
$targetDatabaseName = "<target database name>"
$targetSubscriptionId = "<target subscriptionID>"
$targetRGName = "<target resource group name>"
$targetServerFQDN = "<targetservername.database.windows.net>"

Copy-AzSqlDatabaseLongTermRetentionBackup 
    -ResourceId $resourceID 
    -TargetDatabaseName $targetDatabaseName 
    -TargetSubscriptionId $targetSubscriptionId
    -TargetResourceGroupName $targetRGName
    -TargetServerFullyQualifiedDomainName $targetServerFQDN
```


### <a name="limitations"></a>Limites  

- Les sauvegardes de [limite de restauration dans le temps (PITR)](../azure-sql/database/recovery-using-backups.md#point-in-time-restore) sont uniquement effectuées sur la base de données primaire. Cela est dû à la conception. Lors de la migration de bases de données à partir d’Azure Allemagne à l’aide de la géo-récupération d’urgence, les sauvegardes PITR démarrent sur le nouveau réplica principal après le basculement. Toutefois, les sauvegardes PITR existantes (sur le serveur principal précédent dans Azure Allemagne) ne seront pas migrées. Si vous avez besoin de sauvegardes PITR pour prendre en charge des scénarios de restauration jusqu’à une date et heure, vous devez restaurer la base de données à partir de sauvegardes PITR dans Azure Allemagne, puis migrer la base de données Récupérée vers Azure global. 
- Les stratégies de rétention à long terme ne sont pas migrées avec la base de données. Si vous avez une stratégie de [rétention à long terme (LTR)](../azure-sql/database/long-term-retention-overview.md) sur votre base de données dans Azure Allemagne, vous devez copier et recréer manuellement la stratégie LTR sur la nouvelle base de données après la migration. 


### <a name="requesting-access"></a>Demande d’accès

Pour migrer une base de données d’Azure Allemagne vers Azure global à l’aide de la géo-réplication, vous devez activer votre abonnement *dans Azure Allemagne* pour configurer correctement la migration entre Clouds.

Pour activer votre abonnement Azure Allemagne, vous devez utiliser le lien suivant pour créer une demande de support de migration :   

1. Parcourez la [demande de support de migration](https://portal.microsoftazure.de/#create/Microsoft.Support/Parameters/%7B%0D%0A++++%22pesId%22%3A+%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0D%0A++++%22supportTopicId%22%3A+%229fc72ed5-805f-3894-eb2b-b1f1f6557d2d%22%2C%0D%0A++++%22contextInfo%22%3A+%22Migration+from+cloud+Germany+to+Azure+global+cloud+%28Azure+SQL+Database%29%22%2C%0D%0A++++%22caller%22%3A+%22NoSupportPlanCloudGermanyMigration%22%2C%0D%0A++++%22severity%22%3A+%223%22%0D%0A%7D) suivante.

2. Dans l'onglet Basics, entrez *Geo-DR migration* comme **Résumé** , puis sélectionnez **Suivant : Solutions**
 
   :::image type="content" source="media/germany-migration-databases/support-request-basics.png" alt-text=" nouveau formulaire de demande d'assistance":::

3. Passez en revue les **étapes recommandées**, puis sélectionnez **Suivant : Détails**. 

   :::image type="content" source="media/germany-migration-databases/support-request-solutions.png" alt-text="informations de demande de support requises":::

4. Sur la page de détails, fournissez les informations suivantes :

   1. Dans la zone Description, entrez l’ID d’abonnement Azure global à migrer vers. Pour migrer des bases de données vers plusieurs abonnements, ajoutez une liste des ID Azure globaux vers lesquels vous voulez migrer les bases de données.
   1. Fournissez les informations de contact : nom, nom de la société, adresse e-mail ou numéro de téléphone.
   1. Remplissez le formulaire, puis sélectionnez **Suivant : Réviser + créer**.

   :::image type="content" source="media/germany-migration-databases/support-request-details.png" alt-text="Détails de la demande de support":::


5. Examinez la demande d'assistance, puis sélectionnez **Créer**. 


Une fois la demande traitée, vous êtes contacté.



## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Vous pouvez utiliser l’outil de migration de données Azure Cosmos DB pour migrer vos données vers Azure Cosmos DB. Azure Cosmos DB data Migration Tool est une solution open source qui importe des données vers Azure Cosmos DB à partir de différentes sources, notamment : fichiers JSON, MongoDB, SQL Server, fichiers CSV, stockage Table Azure, Amazon DynamoDB, HBase et conteneurs Azure Cosmos.


L’outil de migration de données Azure Cosmos DB est disponible sous la forme d’un outil d’interface graphique ou d’un outil de ligne de commande. Le code source est disponible dans le référentiel GitHub [de l’outil de migration de données Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool). Une [version compilée de l’outil](https://www.microsoft.com/download/details.aspx?id=46436) est disponible dans le Centre de téléchargement Microsoft.

Pour migrer des ressources Azure Cosmos DB, nous vous recommandons de procéder comme suit :

1. Passez en revue les exigences relatives à la durée de fonctionnement et aux configurations de compte, afin de déterminer le meilleur plan d’action.
1. Clonez les configurations de compte à partir d’Azure Germany vers la nouvelle région, en exécutant l’outil de migration de données.
1. Si vous pouvez utiliser une fenêtre de maintenance, copiez les données de la source sur la destination, via l’outil de migration de données.
1. Sinon, copiez les données de la source sur la destination via l’outil, puis procédez comme suit :
   1. Utilisez une approche basée sur la configuration pour modifier les lectures/écritures dans une application.
   1. Effectuez une synchronisation de type synchronisation initiale.
   1. Configurez une synchronisation incrémentielle et rattrapez le flux de modification.
   1. Faites pointer les lectures vers le nouveau compte et validez l’application.
   1. Arrêtez les écritures sur l’ancien compte, vérifiez que vous avez rattrapé le flux de modification, puis faites pointer les écritures vers le nouveau compte.
   1. Arrêtez l’outil et supprimez l’ancien compte.
1. Exécutez l’outil pour valider la cohérence des données entre l’ancien et le nouveau compte.

Pour plus d’informations :

- Pour savoir comment utiliser l’outil de migration de données, consultez [Didacticiel : utiliser l’outil de migration de données pour migrer vos données vers Azure Cosmos DB](../cosmos-db/import-data.md).
- Pour en savoir plus sur Azure Cosmos DB, consultez la page [Bienvenue dans Azure Cosmos DB](../cosmos-db/introduction.md).


## <a name="azure-cache-for-redis"></a>Cache Azure pour Redis

Plusieurs options s’offrent à vous pour migrer une instance de cache Azure pour Redis d’Azure Allemagne vers Azure global. L’option choisie dépend de vos besoins.

### <a name="option-1-accept-data-loss-create-a-new-instance"></a>Option 1 : Accepter la perte de données, créer une nouvelle instance

Cette approche est idéale lorsque les deux conditions suivantes sont remplies :

- Vous utilisez le cache Azure pour Redis comme cache de données temporaire.
- Votre application remplit à nouveau les données du cache automatiquement dans la nouvelle région.

Pour migrer avec une perte de données et créer une nouvelle instance :

1. Créez une nouvelle instance de cache Azure pour Redis dans la nouvelle région cible.
1. Mettez à jour votre application de manière à utiliser la nouvelle instance dans la nouvelle région.
1. Supprimez l’ancienne instance de cache Azure pour Redis dans la région source.

### <a name="option-2-copy-data-from-the-source-instance-to-the-target-instance"></a>Option 2 : Copier les données de l'instance source vers l'instance cible

Un membre de l’équipe Cache Azure pour Redis a développé un outil open source qui copie les données à partir d’une instance de cache Azure pour Redis vers une autre sans avoir recours à la fonctionnalité d’importation ou d’exportation. Consultez l’étape 4 ci-dessous pour en savoir plus sur l’outil.

Pour copier des données de l’instance source vers l’instance cible :

1. Créez une machine virtuelle dans la région source. Si le jeu de données du cache Azure pour Redis est volumineux, sélectionnez une taille de machine virtuelle relativement puissante pour réduire le temps de copie.
1. Créez une nouvelle instance de cache Azure pour Redis dans la région cible.
1. Videz les données de l’instance **cible**. Assurez-vous de ne *pas* procéder au vidage à partir de l’instance **source**. Le vidage est nécessaire, car l’outil de copie *ne remplace pas* les clés existantes dans l’emplacement cible.
1. Utilisez l'outil suivant pour copier automatiquement les données de l'instance Azure Cache for Redis source vers l'instance Azure Cache for Redis cible : [Source de l'outil](https://github.com/deepakverma/redis-copy) et [téléchargement de l'outil](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip).

> [!NOTE]
> Ce processus peut prendre un certain temps selon la taille de votre jeu de données.

### <a name="option-3-export-from-the-source-instance-import-to-the-destination-instance"></a>Option 3 : Exporter à partir de l’instance source et importer vers l’instance de destination

Cette approche tire parti des fonctionnalités uniquement disponibles au niveau Premium.

Pour exporter à partir de l’instance source et importer vers l’instance de destination :

1. Créez une nouvelle instance de cache Azure pour Redis de niveau Premium dans la région cible. Utiliser la même taille que l’instance de cache Azure pour Redis source.
1. [Exporter des données à partir du cache source](../azure-cache-for-redis/cache-how-to-import-export-data.md) ou utiliser la [cmdlet PowerShell Export-AzRedisCache](/powershell/module/az.rediscache/export-azrediscache).

   > [!NOTE]
   > Le compte de stockage Azure d’exportation doit se trouver dans la même région que l’instance de cache.

1. Copiez les objets BLOB exportés vers un compte de stockage dans la région de destination (par exemple, en utilisant AzCopy).
1. [Importez des données dans le cache de destination](../azure-cache-for-redis/cache-how-to-import-export-data.md) ou utilisez la [Import-AzRedisCAche PowerShell cmdlet](/powershell/module/az.rediscache/import-azrediscache).
1. Reconfigurez votre application de manière à utiliser l’instance de cache Azure pour Redis cible.

### <a name="option-4-write-data-to-two-azure-cache-for-redis-instances-read-from-one-instance"></a>Option 4 : Écrire des données dans deux instances d’Azure Cache pour Redis et lire à partir d’une seule instance

Pour cette approche, vous devez modifier votre application. L’application doit écrire des données dans plusieurs instances de cache lors de la lecture à partir d’une des instances de cache. Cette approche est appropriée si les données stockées dans le cache Azure pour Redis répondent aux critères suivants :
- Les données sont actualisées régulièrement. 
- Toutes les données sont écrites dans l’instance de cache Azure pour Redis cible.
- Vous avez suffisamment de temps pour actualiser toutes les données.

Pour plus d’informations :

- Examinez la [vue d’ensemble du cache Azure pour Redis](../azure-cache-for-redis/cache-overview.md).

## <a name="postgresql-and-mysql"></a>PostgreSQL et MySQL

Pour plus d'informations, voir les articles de la section "Sauvegarde et migration des données" de [PostgreSQL](../postgresql/index.yml) et [MySQL](../mysql/index.yml).

![PostgreSQL et MySQL](./media/germany-migration-main/databases.png)

## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur les outils, techniques et suggestions pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils d'administration](./germany-migration-management-tools.md)
- [Média](./germany-migration-media.md)
