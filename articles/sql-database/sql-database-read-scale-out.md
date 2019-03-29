---
title: Base de données SQL Azure - lire des requêtes sur les réplicas | Microsoft Docs
description: La base de données SQL Azure offre la possibilité d’équilibrer les charges de travail en lecture seule à l’aide de la capacité des réplicas en lecture seule, appelée Lecture du Scale-out.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 03/28/2019
ms.openlocfilehash: d9ad859ef24b51dc337dc23281d2fe4e1eada1e6
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619889"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Utiliser des réplicas en lecture seule pour équilibrer les charges de travail des requêtes en lecture seule

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont pour le module Az.Sql. Pour ces applets de commande, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments pour les commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

L’**échelle horizontale en lecture** offre la possibilité d’équilibrer les charges de travail en lecture seule d’Azure SQL Database en utilisant la capacité d’un seul réplica en lecture seule.

Chaque base de données du niveau Premium ([modèle d’achat basé sur des unités DTU](sql-database-service-tiers-dtu.md)) ou du niveau Critique pour l’entreprise ([modèle d’achat basé sur vCore](sql-database-service-tiers-vcore.md)) est automatiquement configurée avec plusieurs réplicas AlwaysOn pour prendre en charge le contrat SLA de disponibilité. C’est ce qu’indique le diagramme suivant.

![Réplicas en lecture seule](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Les réplicas secondaires sont configurés avec la même taille de calcul en tant que le réplica principal. La fonctionnalité **d’échelle horizontale en lecture** vous permet d’équilibrer les charges de travail en lecture seule SQL Database à l’aide de la capacité de l’un des réplicas en lecture seule au lieu de partager le réplica en lecture-écriture. De cette façon, la charge de travail en lecture seule sera isolée à partir de la charge de travail principale en lecture-écriture et n’affectera pas ses performances. La fonctionnalité est conçue pour les applications incluant des charges de travail en lecture seule séparées logiquement, comme des analyses, et peut par conséquent obtenir des avantages en termes de performance en utilisant cette capacité sans frais supplémentaires.

Pour utiliser la fonctionnalité de lecture du Scale-Out avec une base de données, vous devez explicitement l’activer lors de la création de la base de données ou par la suite en modifiant sa configuration à l’aide de PowerShell en appelant le [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) ou le [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) applets de commande ou via le REST API Azure Resource Manager à l’aide de la [bases de données - créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) (méthode).

Après lecture du Scale-Out est activé pour une base de données, les applications se connectant à cette base de données seront dirigées par la passerelle vers le réplica en lecture-écriture ou un réplica en lecture seule de cette base de données en fonction de la `ApplicationIntent` propriété configurée dans le chaîne de connexion de l’application. Pour plus d’informations sur la propriété `ApplicationIntent`, consultez [Spécification de l’intention de l’application](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Si l’échelle horizontale en lecture est désactivée ou si la propriété d’échelle lecture est définie dans un niveau de service non pris en charge, toutes les connexions sont dirigées vers le réplica en lecture-écriture, indépendamment de la propriété `ApplicationIntent`.

> [!NOTE]
> Store de données de requête, les événements étendus, SQL Profiler et les fonctionnalités d’Audit ne sont pas pris en charge sur les réplicas en lecture seule. 
## <a name="data-consistency"></a>Cohérence des données

Un des avantages des réplicas est qu’ils sont toujours dans un état cohérent au niveau transactionnel, mais il peut y avoir une petite latence à différents moments entre les différents réplicas. La fonctionnalité de lecture du Scale-out prend en charge la cohérence au niveau de la session. Ainsi, si la session en lecture seule se reconnecte après une erreur de connexion engendrée par l’indisponibilité du réplica, elle peut être redirigée vers un autre réplica qui n’est pas complètement à jour avec le réplica en lecture-écriture. De même, si une application écrit des données à l’aide d’une session en lecture-écriture et les lit immédiatement à l’aide d’une session en lecture seule, il est possible que les dernières mises à jour ne sont pas immédiatement visibles sur le réplica. La latence est due à une opération de rétablissement de journaux de transaction asynchrone.

> [!NOTE]
> Les latences de réplication dans la région sont réduites, et cette situation est rare.

## <a name="connect-to-a-read-only-replica"></a>Se connecter à un réplica en lecture seule

Lorsque vous activez la lecture du Scale-out pour une base de données, l’option `ApplicationIntent` dans la chaîne de connexion fournie par le client détermine si la connexion est routée vers le réplica en écriture ou un réplica en lecture seule. Plus précisément, si la valeur `ApplicationIntent` est `ReadWrite` (valeur par défaut), la connexion sera dirigée vers le réplica en lecture-écriture de la base de données. Le comportement existant est identique. Si la valeur `ApplicationIntent` est `ReadOnly`, la connexion est acheminée vers un réplica en lecture seule.

Par exemple, la chaîne de connexion suivante connecte le client à un réplica en lecture seule (en remplaçant les éléments entre crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

L’une des chaînes de connexion suivantes connecte le client à un réplica en lecture-écriture (en remplaçant les éléments entre crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Vérifier que la connexion à un réplica en lecture seule est établie

Vous pouvez vérifier si vous êtes connecté à un réplica en lecture seule en exécutant la requête suivante. Elle retourne READ_ONLY en cas de connexion à un réplica en lecture seule.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> À tout moment, seul l’un des réplicas AlwaysoN est accessible par les sessions en lecture seule.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Surveillance et dépannage de réplica en lecture seule

Lorsque connecté à un réplica en lecture seule, vous pouvez accéder à la métrique de performances à l’aide du `sys.dm_db_resource_stats` DMV. Pour accéder aux statistiques de plan de requête, utilisez le `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` et `sys.dm_exec_sql_text` DMV.

> [!NOTE]
> La DMV `sys.resource_stats` dans la base de données master logique retourne des données de stockage et l’utilisation du processeur du réplica principal.


## <a name="enable-and-disable-read-scale-out"></a>Activer et désactiver l’échelle horizontale en lecture

L’échelle horizontale en lecture est activée par défaut dans le niveau Critique pour l’entreprise de [Managed Instance](sql-database-managed-instance.md). Elle doit être activée explicitement dans les niveaux Premium et Critique pour l’entreprise d’une [base de données placée sur un serveur SQL Database](sql-database-servers.md). Les méthodes d’activation et de désactivation de l’échelle horizontale en lecture sont décrites ici.

### <a name="powershell-enable-and-disable-read-scale-out"></a>PowerShell : Activer et désactiver l’échelle horizontale en lecture

La gestion de la lecture du Scale-out dans Azure PowerShell nécessite la version d’Azure PowerShell de décembre 2016 ou plus récente. Pour obtenir la version de PowerShell la plus récente, consultez [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Activer ou désactiver la lecture du scale-out dans Azure PowerShell en appelant le [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) applet de commande et en passant la valeur souhaitée, `Enabled` ou `Disabled` --pour les `-ReadScale` paramètre. Vous pouvez également utiliser le [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) applet de commande pour créer une nouvelle base de données avec lecture scale-out activée.

Par exemple, pour activer la lecture du Scale-out pour une base de données existante (en remplaçant les éléments entre crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Pour désactiver la lecture du Scale-out pour une base de données existante (en remplaçant les éléments entre crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Pour créer une nouvelle base de données avec la lecture du Scale-out activée (en remplaçant les éléments entre crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="rest-api-enable-and-disable-read-scale-out"></a>API REST : Activer et désactiver l’échelle horizontale en lecture

Pour créer une base de données avec la lecture du Scale-out activée, ou pour activer ou désactiver la lecture du Scale-out dans une base de données existante, créez ou mettez à jour l’entité correspondante de la base de données avec la propriété `readScale` configurée sur `Enabled` ou `Disabled` comme dans l’exemple de requête ci-dessous.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
}
```

Pour plus d’informations, consultez [Bases de données - Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>L’utilisation de TempDB sur le réplica en lecture seule

La base de données TempDB n’est pas répliquée vers les réplicas en lecture seule. Chaque réplica a sa propre version de base de données TempDB est créé lorsque le réplica est créé. Il garantit que TempDB est modifiable et peut être modifié pendant l’exécution de votre requête. Si votre charge de travail en lecture seule dépend de l’utilisation d’objets de TempDB, vous devez créer ces objets dans le cadre de votre script de requête. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Utilisation de l’échelle horizontale en lecture avec des bases de données géorépliquées

Si vous utilisez lecture du scale-out pour les charges de travail solde en lecture seule sur une base de données géo-répliqué (par exemple, en tant que membre d’un groupe de basculement), assurez-vous que la lecture du scale-out est activé sur le serveur principal et les bases de données secondaire géo-répliquée. Cette configuration garantit que la même expérience de l’équilibrage de charge continue lorsque votre application se connecte à la nouvelle base primaire après le basculement. Si vous vous connectez à la base de données secondaire géorépliquée et que l’échelle horizontale en lecture est activée, vos sessions avec `ApplicationIntent=ReadOnly` sont routées vers l’un réplicas de la même façon que les connexions sont routées sur la base de données primaire.  Les sessions sans `ApplicationIntent=ReadOnly` sont routées vers le réplica principal de la base de données secondaire géorépliquée, qui est également en lecture seule. Le point de terminaison de la base de données secondaire géorépliquée étant différent de celui de la base de données primaire, il était par le passé inutile de définir `ApplicationIntent=ReadOnly` pour accéder à la base de données secondaire. À des fins de compatibilité descendante, la vue de gestion dynamique `sys.geo_replication_links` affiche `secondary_allow_connections=2` (toute connexion cliente est autorisée).

> [!NOTE]
> Tourniquet (Round-Robin) ou toute autre équilibré de charge le routage entre les réplicas locaux de la base de données secondaire n’est pas pris en charge.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’utilisation de PowerShell pour définir la lecture du scale-out, consultez le [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) ou [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) applets de commande.
- Pour plus d’informations sur l’utilisation de l’API REST pour définir la lecture du Scale-out, consultez [Bases de données - Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).
