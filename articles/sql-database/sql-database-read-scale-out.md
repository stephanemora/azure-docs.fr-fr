---
title: Base de données SQL Azure - lire des requêtes sur les réplicas | Microsoft Docs
description: La base de données SQL Azure offre la possibilité d’équilibrer les charges de travail en lecture seule à l’aide de la capacité des réplicas en lecture seule, appelée Lecture du Scale-out.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.openlocfilehash: 26204d5bd61d193a3d08e26f98faf77ecc367a94
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2018
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Utiliser des réplicas en lecture seule pour équilibrer des charges de travail de requêtes en lecture seule (version préliminaire)

La **lecture du Scale-out** offre la possibilité d’équilibrer les charges de travail en lecture seule d’une base de données SQL Azure à l’aide de la capacité des réplicas en lecture seule. 

## <a name="overview-of-read-scale-out"></a>Vue d’ensemble de la lecture du Scale-out

Chaque base de données du niveau Premium ([modèle d’achat basé sur des unités DTU](sql-database-service-tiers.md#dtu-based-purchasing-model)) ou du niveau Critique pour l’entreprise ([modèle d’achat basé sur vCore](sql-database-service-tiers.md#vcore-based-purchasing-model-preview)) est automatiquement configurée avec plusieurs réplicas AlwaysON pour prendre en charge le contrat SLA de disponibilité. Ces réplicas sont configurés avec le même niveau de performances que le réplica en lecture-écriture utilisé par les connexions normales de base de données. La fonctionnalité **Lecture du Scale-out** vous permet d’équilibrer les charges de travail en lecture seule de la base de données SQL à l’aide de la capacité des réplicas en lecture seule au lieu de partager le réplica en lecture-écriture. De cette manière, la charge de travail en lecture seule sera isolée à partir de la charge de travail principale en lecture-écriture et n’affectera pas ses performances. La fonctionnalité est conçue pour les applications incluant des charges de travail en lecture seule logiquement séparées, telles que des analyses, et pourrait par conséquent obtenir des gains de performance à l’aide de cette capacité supplémentaire sans aucun coût supplémentaire.

Pour utiliser la fonctionnalité de lecture du Scale-out avec une base de données particulière, vous devez l’activer explicitement lors de la création de la base de données, ou ultérieurement en modifiant sa configuration à l’aide de PowerShell en appelant les applets de commande [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [ New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) ou via l’API REST de Azure Resource Manager à l’aide de la méthode [Bases de données - Créer ou mettre à jour](/rest/api/sql/databases/createorupdate). 

Une fois la lecture du Scale Out activée pour une base de données, les applications se connectant à cette base de données seront dirigées vers le réplica en lecture-écriture ou un réplica en lecture seule de cette base de données en fonction de la propriété `ApplicationIntent` configurée dans la chaîne de connexion de l’application. Pour plus d’informations sur la propriété `ApplicationIntent`, consultez [Spécification de l’intention de l’application](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

> [!NOTE]
> Durant la préversion, le magasin de données des requêtes et les événements étendus ne sont pas pris en charge sur les réplicas en lecture seule.

## <a name="data-consistency"></a>Cohérence des données

Un des avantages de la technologie AlwaysON est que les réplicas sont toujours dans un état cohérent au niveau transactionnel, mais il peut y avoir une latence faible à différents moments entre différents réplicas. La fonctionnalité de lecture du Scale-out prend en charge la cohérence au niveau de la session. Ainsi, si la session en lecture seule se reconnecte après une erreur de connexion engendrée par l’indisponibilité du réplica, elle peut être redirigée vers un autre réplica qui n’est pas complètement à jour avec le réplica en lecture-écriture. De même, si une application écrit des données à l’aide d’une session en lecture-écriture et les lit immédiatement à l’aide d’une session en lecture seule, il est possible que les dernières mises à jour ne soient pas visibles immédiatement. La raison est que la phase de restauration par progression du journal des transactions vers les réplicas est asynchrone.

> [!NOTE]
> Les latences de réplication dans la région sont réduites, et cette situation est rare.


## <a name="connecting-to-a-read-only-replica"></a>Connexion à un réplica en lecture seule

Lorsque vous activez la lecture du Scale-out pour une base de données, l’option `ApplicationIntent` dans la chaîne de connexion fournie par le client détermine si la connexion est routée vers le réplica en écriture ou un réplica en lecture seule. Plus précisément, si la valeur `ApplicationIntent` est `ReadWrite` (valeur par défaut), la connexion sera dirigée vers le réplica en lecture-écriture de la base de données. Le comportement existant est identique. Si la valeur `ApplicationIntent` est `ReadOnly`, la connexion est acheminée vers un réplica en lecture.

Par exemple, la chaîne de connexion suivante connecte le client à un réplica en lecture seule (en remplaçant les éléments entre crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

L’une des chaînes de connexion suivantes connecte le client à un réplica en lecture-écriture (en remplaçant les éléments entre crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="enable-and-disable-read-scale-out-using-azure-powershell"></a>Activer et désactiver la lecture du Scale-out à l’aide d’Azure PowerShell

La gestion de la lecture du Scale-out dans Azure PowerShell nécessite la version d’Azure PowerShell de décembre 2016 ou plus récente. Pour obtenir la version de PowerShell la plus récente, consultez [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Activez ou désactivez la lecture du Scale-out dans Azure PowerShell en appelant l’applet de commande [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) et en passant la valeur souhaitée, `Enabled` ou `Disabled` pour le paramètre `-ReadScale`. Vous pouvez également utiliser l’applet de commande [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) pour créer une nouvelle base de données avec la lecture du Scale-out activée.

Par exemple, pour activer la lecture du Scale-out pour une base de données existante (en remplaçant les éléments entre crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Pour désactiver la lecture du Scale-out pour une base de données existante (en remplaçant les éléments entre crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Pour créer une nouvelle base de données avec la lecture du Scale-out activée (en remplaçant les éléments entre crochets pointus par les valeurs correctes pour votre environnement et en supprimant ces crochets) :

```powershell
New-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

## <a name="enabling-and-disabling-read-scale-out-using-the-azure-sql-database-rest-api"></a>Activation et désactivation de la lecture du Scale-out à l’aide de l’API REST de Azure SQL Database

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

Pour plus d’informations, consultez [Bases de données - Créer ou mettre à jour](/rest/api/sql/databases/createorupdate).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’utilisation de PowerShell pour configurer la lecture du Scale-out, consultez l’applet de commande [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).
- Pour plus d’informations sur l’utilisation de l’API REST pour définir la lecture du Scale-out, consultez [Bases de données - Créer ou mettre à jour](/rest/api/sql/databases/createorupdate).
