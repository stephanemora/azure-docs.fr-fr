---
title: Azure SQL Database - lire des requêtes sur les réplicas | Microsoft Docs
description: La base de données SQL Azure fournit la possibilité d’équilibrer la charge des charges de travail en lecture seule à l’aide de la capacité des réplicas en lecture seule - appelée lecture du Scale-Out.
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
ms.date: 04/19/2019
ms.openlocfilehash: cbcdcfd151951334246a4e85d9f521a15bb6269d
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006146"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Utilisez des réplicas en lecture seule aux charges de travail de l’équilibrage de charge des requêtes en lecture seule

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont pour le module Az.Sql. Pour ces applets de commande, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments pour les commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

Dans le cadre de la [architecture de haute disponibilité](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), chaque base de données dans le niveau de service Premium, critique pour l’entreprise ou très grande échelle est automatiquement configurée avec un réplica principal et plusieurs réplicas secondaires. Les réplicas secondaires sont configurés avec la même taille de calcul en tant que le réplica principal. Le **lecture du Scale-Out** fonctionnalité vous permet d’équilibrer la charge de la base de données SQL en lecture seule des charges de travail à l’aide de la capacité d’un des réplicas en lecture seule au lieu de partager le réplica en lecture-écriture. De cette façon, la charge de travail en lecture seule sera isolée à partir de la charge de travail principale en lecture-écriture et n’affectera pas ses performances. La fonctionnalité est conçue pour les applications qui incluent logiquement séparées en lecture seule des charges de travail, tels qu’analytique. Ils peuvent obtenir des gains de performance à l’aide de cette capacité supplémentaire à aucun coût supplémentaire.

Le diagramme suivant illustre à l’aide d’une base de données critique pour l’entreprise.

![Réplicas en lecture seule](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

La fonctionnalité de lecture du Scale-Out est activée par défaut sur Nouvelle Premium, critique pour l’entreprise et les bases de données de très grande échelle. Si votre chaîne de connexion SQL est configuré avec `ApplicationIntent=ReadOnly`, l’application est redirigée par la passerelle pour un réplica en lecture seule de cette base de données. Pour plus d’informations sur l’utilisation de la `ApplicationIntent` propriété, consultez [intention de l’Application en spécifiant](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Si vous souhaitez vous assurer que l’application se connecte au réplica principal, quel que soit le `ApplicationIntent` définition dans la chaîne de connexion SQL, vous devez explicitement désactiver lecture du scale-out lors de la création de la base de données ou lors de la modification de sa configuration. Par exemple, si vous mettez à niveau votre base de données à partir du niveau Standard ou usage général au niveau Premium, critique pour l’entreprise ou très grande échelle et souhaitez vous assurer que toutes vos connexions continuent à accéder au réplica principal, désactivez la lecture du Scale-out. Pour plus d’informations sur la façon de le désactiver, consultez [activer et désactiver la lecture du Scale-Out](#enable-and-disable-read-scale-out).

> [!NOTE]
> Store de données de requête, les événements étendus, SQL Profiler et les fonctionnalités d’Audit ne sont pas pris en charge sur les réplicas en lecture seule. 

## <a name="data-consistency"></a>Cohérence des données

Un des avantages des réplicas est qu’ils sont toujours dans un état cohérent au niveau transactionnel, mais il peut y avoir une petite latence à différents moments entre les différents réplicas. La fonctionnalité de lecture du Scale-out prend en charge la cohérence au niveau de la session. Cela signifie que, si la session en lecture seule se reconnecte après une erreur de connexion engendrée par l’indisponibilité du réplica, il peut être redirigé vers un réplica qui n’est pas à jour avec le réplica en lecture-écriture à 100 %. De même, si une application écrit des données à l’aide d’une session en lecture-écriture et les lit immédiatement à l’aide d’une session en lecture seule, il est possible que les dernières mises à jour ne sont pas immédiatement visibles sur le réplica. La latence est due à une opération de rétablissement de journaux de transaction asynchrone.

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

Lecture du Scale-Out est activé par défaut sur les niveaux de service Premium, critique pour l’entreprise et très grande échelle. Lecture du Scale-Out ne peut pas être activé dans les niveaux de service Basic, Standard ou usage général. Lecture du Scale-Out est automatiquement désactivé sur les bases de données Hyperscale configurés avec des réplicas 0. 

Vous pouvez désactiver et réactiver le lecture du Scale-Out sur les bases de données uniques et pool élastique dans le niveau de service Premium ou critique pour l’entreprise à l’aide des méthodes suivantes.

> [!NOTE]
> La possibilité de désactiver la lecture du Scale-Out est fournie pour la compatibilité descendante.

### <a name="azure-portal"></a>Portail Azure

Vous pouvez gérer les paramètres de lecture Sacle-out sur le **configurer** panneau base de données. 

### <a name="powershell"></a>PowerShell

La gestion de la lecture du Scale-out dans Azure PowerShell nécessite la version d’Azure PowerShell de décembre 2016 ou plus récente. Pour obtenir la version de PowerShell la plus récente, consultez [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Vous pouvez désactiver ou réactiver le lecture du Scale-Out dans Azure PowerShell en appelant le [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) applet de commande et en passant la valeur souhaitée, `Enabled` ou `Disabled` --pour les `-ReadScale` paramètre. 

Pour désactiver la lecture du scale-out sur une base de données existante (en remplaçant les éléments entre les crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
Pour désactiver la lecture du scale-out sur une base de données (en remplaçant les éléments entre les crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

Pour réactiver la lecture du scale-out sur une base de données existante (en remplaçant les éléments entre les crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Pour créer une base de données avec la lecture du scale-out désactivé, ou pour modifier le paramètre pour une base de données existante, utilisez la méthode suivante avec la `readScale` propriété définie sur `Enabled` ou `Disabled` comme dans l’exemple de requête ci-dessous.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

Pour plus d’informations, consultez [Bases de données - Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>L’utilisation de TempDB sur le réplica en lecture seule

La base de données TempDB n’est pas répliquée vers les réplicas en lecture seule. Chaque réplica a sa propre version de base de données TempDB est créé lorsque le réplica est créé. Il garantit que TempDB est modifiable et peut être modifié pendant l’exécution de votre requête. Si votre charge de travail en lecture seule dépend de l’utilisation d’objets de TempDB, vous devez créer ces objets dans le cadre de votre script de requête. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Utilisation de l’échelle horizontale en lecture avec des bases de données géorépliquées

Si vous utilisez lecture du Scale-Out pour équilibrer la charge en lecture seule des charges de travail sur une base de données géo-répliqué (par exemple, en tant que membre d’un groupe de basculement), assurez-vous que cette lecture du scale-out est activé sur le serveur principal et les bases de données secondaire géo-répliquée. Cette configuration garantit que la même expérience de l’équilibrage de charge continue lorsque votre application se connecte à la nouvelle base primaire après le basculement. Si vous vous connectez à la base de données secondaire géorépliquée et que l’échelle horizontale en lecture est activée, vos sessions avec `ApplicationIntent=ReadOnly` sont routées vers l’un réplicas de la même façon que les connexions sont routées sur la base de données primaire.  Les sessions sans `ApplicationIntent=ReadOnly` sont routées vers le réplica principal de la base de données secondaire géorépliquée, qui est également en lecture seule. Étant donné que la base de données secondaire géo-répliquée a un point de terminaison autre que la base de données primaire, par le passé pour accéder à la base de données secondaire il n’était pas requis pour définir `ApplicationIntent=ReadOnly`. À des fins de compatibilité descendante, la vue de gestion dynamique `sys.geo_replication_links` affiche `secondary_allow_connections=2` (toute connexion cliente est autorisée).

> [!NOTE]
> Tourniquet (Round-Robin) ou n’importe quel autre équilibrée de routage entre les réplicas locaux de la base de données secondaire n’est pas pris en charge.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’offre de très grande échelle de la base de données SQL, consultez [niveau de service Hyperscale](./sql-database-service-tier-hyperscale.md).
