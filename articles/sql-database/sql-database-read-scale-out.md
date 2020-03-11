---
title: Lire des requêtes sur des réplicas
description: La base de données Azure SQL offre la possibilité d’équilibrer les charges de travail en lecture seule à l’aide de la capacité des réplicas en lecture seule, appelée échelle horizontale en lecture.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1a1b9907cd931716949d92d948a7d541fd2d5057
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206943"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Utiliser des réplicas en lecture seule pour équilibrer des charges de travail de requêtes en lecture seule

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dans le cadre d’une [architecture à haute disponibilité](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), chaque base de données aux niveaux de service Premium et Critique pour l’entreprise est automatiquement approvisionnée avec un réplica principal et plusieurs réplicas secondaires. Les réplicas secondaires sont approvisionnés avec la même taille de calcul que le réplica principal. La fonctionnalité **Échelle horizontale en lecture** vous permet d’équilibrer les charges de travail en lecture seule SQL Database à l’aide de la capacité de l’un des réplicas en lecture seule au lieu de partager le réplica en lecture-écriture. De cette façon, la charge de travail en lecture seule sera isolée à partir de la charge de travail principale en lecture-écriture et n’affectera pas ses performances. Cette fonctionnalité est destinée aux applications qui incluent des charges de travail en lecture seule séparées logiquement, telles que des analyses. Aux niveaux de service Premium et Critique pour l’entreprise, les applications peuvent bénéficier d’avantages en matière de performances en exploitant cette capacité supplémentaire sans coût supplémentaire.

La fonctionnalité **Échelle horizontale en lecture** est également disponible au niveau de service Hyperscale quand au moins un réplica secondaire est créé. Plusieurs réplicas secondaires peuvent être utilisés si des charges de travail en lecture seule requièrent plus de ressources qu’il n’en existe sur un réplica secondaire. L’architecture à haute disponibilité des niveaux de service De base, Standard et Usage général n’inclut pas de réplica. La fonctionnalité **Échelle horizontale en lecture** n’est pas disponible à ces niveaux de service.

Le diagramme suivant illustre cela à l’aide d’une base de données Critique pour l’entreprise.

![Réplicas en lecture seule](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

La fonctionnalité Échelle horizontale en lecture est activée par défaut sur les nouvelles bases de données Premium, Critiques pour l’entreprise et Hyperscale. Pour Hyperscale, un réplica secondaire est créé par défaut pour les nouvelles bases de données. Si votre chaîne de connexion SQL est configurée avec `ApplicationIntent=ReadOnly`, l’application est redirigée par la passerelle vers un réplica en lecture seule de cette base de données. Pour plus d’informations sur la manière d’utiliser la propriété `ApplicationIntent`, voir [Spécification de l’intention de l’application](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Si vous souhaitez vous assurer que l’application se connecte au réplica principal quel que soit le paramètre `ApplicationIntent` de la chaîne de connexion SQL, vous devez désactiver explicitement l’échelle horizontale en lecture lors de la création de la base de données ou de la modification de sa configuration. Par exemple, si vous mettez à niveau votre base de données de type Standard ou Usage général vers une base de données de type Premium, Critique pour l’entreprise ou Hyperscale, et souhaitez vous assurer que toutes vos connexions continuent d’aller vers le réplica principal, désactivez la fonctionnalité Échelle horizontale en lecture. Pour plus d’informations sur la façon de désactiver cette fonctionnalité, voir [Activer et désactiver l’échelle horizontale en lecture](#enable-and-disable-read-scale-out).

> [!NOTE]
> Les fonctionnalités Magasin de données des requêtes, Événements étendus et Générateur de profils SQL ne sont pas prises en charge sur les réplicas en lecture seule.

## <a name="data-consistency"></a>Cohérence des données

Un des avantages des réplicas est qu’ils sont toujours dans un état cohérent au niveau transactionnel, mais il peut y avoir une petite latence à différents moments entre les différents réplicas. La fonctionnalité de lecture du Scale-out prend en charge la cohérence au niveau de la session. Ainsi, si la session en lecture seule se reconnecte après une erreur de connexion engendrée par l’indisponibilité du réplica, elle peut être redirigée vers un autre réplica qui n’est pas complètement synchronisé avec le réplica en lecture-écriture. De même, si une application écrit des données à l’aide d’une session en lecture-écriture et les lit immédiatement à l’aide d’une session en lecture seule, il est possible que les dernières mises à jour ne soient pas visibles immédiatement sur le réplica. La latence est due à une opération de rétablissement de journal des transactions asynchrone.

> [!NOTE]
> Les latences de réplication dans la région sont réduites, et cette situation est rare.

## <a name="connect-to-a-read-only-replica"></a>Se connecter à un réplica en lecture seule

Lorsque vous activez la lecture du Scale-out pour une base de données, l’option `ApplicationIntent` dans la chaîne de connexion fournie par le client détermine si la connexion est routée vers le réplica en écriture ou un réplica en lecture seule. Plus précisément, si la valeur `ApplicationIntent` est `ReadWrite` (valeur par défaut), la connexion sera dirigée vers le réplica en lecture-écriture de la base de données. Le comportement existant est identique. Si la valeur `ApplicationIntent` est `ReadOnly`, la connexion est acheminée vers un réplica en lecture seule.

Par exemple, la chaîne de connexion suivante connecte le client à un réplica en lecture seule (en remplaçant les éléments entre crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

L’une des chaînes de connexion suivantes connecte le client à un réplica en lecture-écriture (en remplaçant les éléments entre crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Vérifier que la connexion à un réplica en lecture seule est établie

Vous pouvez vérifier si vous êtes connecté à un réplica en lecture seule en exécutant la requête suivante. Elle retourne READ_ONLY en cas de connexion à un réplica en lecture seule.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> À tout moment, seul l’un des réplicas AlwaysoN est accessible par les sessions en lecture seule.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Surveillance et dépannage de réplica en lecture seule

Une fois connecté à un réplica en lecture seule, vous pouvez accéder aux métriques de performances à l’aide de la vue de gestion dynamique (DMV) `sys.dm_db_resource_stats`. Pour accéder aux statistiques du plan de requête, utilisez les DMV `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` et `sys.dm_exec_sql_text`.

> [!NOTE]
> La DMV `sys.resource_stats` dans la base de données MASTER logique retourne les données sur l’utilisation du processeur et le stockage et du réplica principal.

## <a name="enable-and-disable-read-scale-out"></a>Activer et désactiver l’échelle horizontale en lecture

La fonctionnalité Échelle horizontale en lecture est activée par défaut sur les niveaux de service Premium, Critique pour l’entreprise et Hyperscale. La fonctionnalité Échelle horizontale en lecture ne peut pas être activée aux niveaux de service De base, Standard ou Usage général. La fonctionnalité Échelle horizontale en lecture est automatiquement désactivée sur les bases de données Hyperscale configurées avec 0 réplica.

Vous pouvez désactiver et réactiver la fonctionnalité Échelle horizontale en lecture sur des bases de données uniques et des bases de données de pool élastique aux niveaux de service Premium ou Critique pour l’entreprise à l’aide des méthodes suivantes.

> [!NOTE]
> La possibilité de désactiver la fonctionnalité Échelle horizontale en lecture est fournie à des fins de compatibilité descendante.

### <a name="azure-portal"></a>Portail Azure

Vous pouvez gérer le paramètre d’échelle horizontale en lecture sur le panneau base de données **Configurer**.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager (RM) est toujours pris en charge par Azure SQL Database, mais tous les développements à venir sont destinés au module Az.Sql. Le module AzureRM continue à recevoir des résolutions de bogues jusqu’à au moins décembre 2020.  Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Pour en savoir plus sur leur compatibilité, consultez [Présentation du nouveau module Az Azure PowerShell](/powershell/azure/new-azureps-module-az).

La gestion de la lecture du Scale-out dans Azure PowerShell nécessite la version d’Azure PowerShell de décembre 2016 ou plus récente. Pour obtenir la version de PowerShell la plus récente, consultez [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Vous pouvez activer ou désactiver la fonctionnalité Échelle horizontale en lecture dans Azure PowerShell en appelant la cmdlet [Set-AzureRmSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) et en transmettant la valeur souhaitée – `Enabled` ou `Disabled` -- pour le paramètre `-ReadScale`.

Pour désactiver la fonctionnalité Échelle horizontale en lecture sur une base de données existante (en remplaçant les éléments entre crochets angulaires par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Pour désactiver la fonctionnalité Échelle horizontale en lecture sur une base de données existante (en remplaçant les éléments entre crochets angulaires par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Pour réactiver la fonctionnalité Échelle horizontale en lecture sur une base de données existante (en remplaçant les éléments entre crochets angulaires par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Pour créer une base de données avec la fonctionnalité Échelle horizontale en lecture désactivée, ou pour modifier le paramétrage d’une base de données existante, utilisez la méthode suivante avec la propriété `readScale` définie sur `Enabled` ou `Disabled`, comme dans l’exemple de demande ci-dessous.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Pour plus d’informations, consultez [Bases de données - Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Utilisation de TempDB sur un réplica en lecture seule

La base de données TempDB n’est pas répliquée vers les réplicas en lecture seule. Chaque réplica a sa propre version de la base de données TempDB créée lors de la création du réplica. Il vérifie que TempDB peut être mise à jour et modifié pendant l’exécution de votre requête. Si votre charge de travail en lecture seule dépend de l’utilisation d’objets TempDB, vous devez intégrer ceux-ci dans votre script de requête.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Utilisation de l’échelle horizontale en lecture avec des bases de données géorépliquées

Si vous utilisez la fonctionnalité Échelle horizontale en lecture pour équilibrer des charges de travail en lecture seule sur une base de données géorépliquée (par exemple, comme membre d’un groupe de basculement), vérifiez que la fonctionnalité est activée sur la base de données primaire et les bases de données secondaires géorépliquées. Cette configuration garantit que la même expérience d’équilibrage de charge continue quand votre application se connecte à la nouvelle base de données primaire après le basculement. Si vous vous connectez à la base de données secondaire géorépliquée et que l’échelle horizontale en lecture est activée, vos sessions avec `ApplicationIntent=ReadOnly` sont routées vers l’un réplicas de la même façon que les connexions sont routées sur la base de données primaire.  Les sessions sans `ApplicationIntent=ReadOnly` sont routées vers le réplica principal de la base de données secondaire géorépliquée, qui est également en lecture seule. Le point de terminaison de la base de données secondaire géorépliquée étant différent de celui de la base de données primaire, il était par le passé inutile de définir `ApplicationIntent=ReadOnly` pour accéder à la base de données secondaire. À des fins de compatibilité descendante, la vue de gestion dynamique `sys.geo_replication_links` affiche `secondary_allow_connections=2` (toute connexion cliente est autorisée).

> [!NOTE]
> Aucun tourniquet (round robin) ou routage à charge équilibrée entre les réplicas locaux de la base de données secondaire n’est pris en charge.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’offre SQL Database Hyperscale, voir [Niveau de service Hyperscale](./sql-database-service-tier-hyperscale.md).
