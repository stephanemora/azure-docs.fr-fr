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
ms.date: 03/12/2019
ms.openlocfilehash: 8f34b3ed91e4b470fdfa7c2ffad401e7890abe1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57886454"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Utiliser des réplicas en lecture seule pour équilibrer des charges de travail de requêtes en lecture seule (version préliminaire)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont pour le module Az.Sql. Pour ces applets de commande, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments pour les commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

L’**échelle horizontale en lecture** offre la possibilité d’équilibrer les charges de travail en lecture seule d’Azure SQL Database en utilisant la capacité d’un seul réplica en lecture seule.

Chaque base de données du niveau Premium ([modèle d’achat basé sur des unités DTU](sql-database-service-tiers-dtu.md)) ou du niveau Critique pour l’entreprise ([modèle d’achat basé sur vCore](sql-database-service-tiers-vcore.md)) est automatiquement configurée avec plusieurs réplicas AlwaysOn pour prendre en charge le contrat SLA de disponibilité.

![Réplicas en lecture seule](media/sql-database-managed-instance/business-critical-service-tier.png)

Ces réplicas sont configurés avec la même taille de calcul que le réplica en lecture-écriture utilisé par les connexions normales de base de données. La fonctionnalité **d’échelle horizontale en lecture** vous permet d’équilibrer les charges de travail en lecture seule SQL Database à l’aide de la capacité de l’un des réplicas en lecture seule au lieu de partager le réplica en lecture-écriture. De cette façon, la charge de travail en lecture seule sera isolée à partir de la charge de travail principale en lecture-écriture et n’affectera pas ses performances. La fonctionnalité est conçue pour les applications incluant des charges de travail en lecture seule séparées logiquement, comme des analyses, et peut par conséquent obtenir des avantages en termes de performance en utilisant cette capacité sans frais supplémentaires.

Pour utiliser la fonctionnalité de lecture du Scale-Out avec une base de données, vous devez explicitement l’activer lors de la création de la base de données ou par la suite en modifiant sa configuration à l’aide de PowerShell en appelant le [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) ou le [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) applets de commande ou via le REST API Azure Resource Manager à l’aide de la [bases de données - créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) (méthode).

Une fois la lecture du Scale Out activée pour une base de données, les applications se connectant à cette base de données seront dirigées vers le réplica en lecture-écriture ou un réplica en lecture seule de cette base de données en fonction de la propriété `ApplicationIntent` configurée dans la chaîne de connexion de l’application. Pour plus d’informations sur la propriété `ApplicationIntent`, consultez [Spécification de l’intention de l’application](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Si l’échelle horizontale en lecture est désactivée ou si la propriété d’échelle lecture est définie dans un niveau de service non pris en charge, toutes les connexions sont dirigées vers le réplica en lecture-écriture, indépendamment de la propriété `ApplicationIntent`.

> [!NOTE]
> Store de données de requête et les événements étendus ne sont pas pris en charge sur les réplicas en lecture seule.

## <a name="data-consistency"></a>Cohérence des données

Un des avantages des réplicas est qu’ils sont toujours dans un état cohérent au niveau transactionnel, mais il peut y avoir une petite latence à différents moments entre les différents réplicas. La fonctionnalité de lecture du Scale-out prend en charge la cohérence au niveau de la session. Ainsi, si la session en lecture seule se reconnecte après une erreur de connexion engendrée par l’indisponibilité du réplica, elle peut être redirigée vers un autre réplica qui n’est pas complètement à jour avec le réplica en lecture-écriture. De même, si une application écrit des données à l’aide d’une session en lecture-écriture et les lit immédiatement à l’aide d’une session en lecture seule, il est possible que les dernières mises à jour ne soient pas visibles immédiatement. La raison est que la phase de restauration par progression du journal des transactions vers les réplicas est asynchrone.

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

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Utilisation de l’échelle horizontale en lecture avec des bases de données géorépliquées

Si vous utilisez l’échelle horizontale en lecture pour équilibrer des charges de travail en lecture seule sur une base de données géorépliquée (par exemple, comme membre d’un groupe de basculement), vérifiez que l’échelle horizontale en lecture est activée sur la base de données primaire et les bases de données secondaires géorépliquées. De cette façon, le même effet d’équilibrage de charge est appliqué quand votre application se connecte à la nouvelle base de données primaire après le basculement. Si vous vous connectez à la base de données secondaire géorépliquée et que l’échelle horizontale en lecture est activée, vos sessions avec `ApplicationIntent=ReadOnly` sont routées vers l’un réplicas de la même façon que les connexions sont routées sur la base de données primaire.  Les sessions sans `ApplicationIntent=ReadOnly` sont routées vers le réplica principal de la base de données secondaire géorépliquée, qui est également en lecture seule. Le point de terminaison de la base de données secondaire géorépliquée étant différent de celui de la base de données primaire, il était par le passé inutile de définir `ApplicationIntent=ReadOnly` pour accéder à la base de données secondaire. À des fins de compatibilité descendante, la vue de gestion dynamique `sys.geo_replication_links` affiche `secondary_allow_connections=2` (toute connexion cliente est autorisée).

> [!NOTE]
> Tourniquet (Round-Robin) ou toute autre équilibré de charge le routage entre les réplicas locaux de la base de données secondaire n’est pas pris en charge.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’utilisation de PowerShell pour définir la lecture du scale-out, consultez le [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) ou [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) applets de commande.
- Pour plus d’informations sur l’utilisation de l’API REST pour définir la lecture du Scale-out, consultez [Bases de données - Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).
