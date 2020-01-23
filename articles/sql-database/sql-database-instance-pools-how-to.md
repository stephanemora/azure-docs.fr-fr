---
title: Guide pratique des pools d’instances (préversion)
description: Cet article explique comment créer et gérer des pools d’instances Azure SQL Database (préversion).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 5a45b9e3ba59a91f580ce0f2dc180adf5d20c87d
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754058"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Guide de procédures des pools d’instances Azure SQL Database (préversion)

Cet article fournit des détails sur la création et la gestion de [pools d’instances](sql-database-instance-pools.md).

## <a name="instance-pool-operations"></a>Opérations des pools d’instances

Le tableau suivant présente les opérations disponibles liées aux pools d’instances et leur disponibilité dans le portail Azure et avec PowerShell.

|Commande|Portail Azure|PowerShell|
|:---|:---|:---|
|Créer un pool d’instances|Non|Oui|
|Mettre à jour le pool d’instances (nombre limité de propriétés)|Non |Oui |
|Vérifier l’utilisation et les propriétés du pool d’instances|Non|Oui |
|Supprimer le pool d’instances|Non|Oui|
|Créer une instance gérée à l’intérieur du pool d’instances|Non|Oui|
|Mettre à jour l'utilisation des ressources de l'instance managée|Oui |Oui|
|Vérifier l’utilisation et les propriétés de l’instance gérée|Oui|Oui|
|Supprimer l’instance gérée du pool|Oui|Oui|
|Créer une base de données dans une instance gérée placée dans le pool|Oui|Oui|
|Supprimer une base de données d’une instance gérée|Oui|Oui|

[Commandes PowerShell](https://docs.microsoft.com/powershell/module/az.sql/) disponibles

|Applet de commande |Description |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Crée un pool d’instances Azure SQL Database. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Retourne des informations sur le pool d’instances Azure SQL. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Définit les propriétés d’un pool d’instances Azure SQL Database. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Supprime un pool d’instances Azure SQL Database. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Retourne des informations sur l’utilisation du pool d’instances Azure SQL. |


Pour utiliser PowerShell, [installez la dernière version de PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core) et suivez les instructions pour [installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Pour les opérations liées aux instances à la fois dans les pools et les instances uniques, utilisez les [commandes de l’instance gérée](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances) standard, mais la propriété de *nom du pool d’instances* doit être remplie lors de l’utilisation de ces commandes pour une instance d’un pool.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Comment déployer des instances gérées dans des pools

Le processus de déploiement d’une instance dans un pool se compose des deux étapes suivantes :

1. Déploiement d’un pool d’instances unique. Il s’agit d’une opération de longue durée, où la durée est identique au déploiement [d’une instance unique créée dans un sous-réseau vide](sql-database-managed-instance.md#managed-instance-management-operations).

2. Déploiement d’instances répété dans un pool d’instances. Le paramètre du pool d’instances doit être spécifié explicitement dans le cadre de cette opération. Il s’agit d’une opération relativement rapide qui prend généralement jusqu’à 5 minutes.

Dans la préversion publique, les deux étapes sont uniquement prises en charge par PowerShell et mes modèles Resource Manager. L’expérience du portail Azure n’est pas disponible actuellement.

Une fois qu’une instance gérée est déployée sur un pool, vous *pouvez* utiliser le portail Azure pour modifier ses propriétés sur la page de niveau tarifaire.


## <a name="create-an-instance-pool"></a>Créer un pool d’instances

Pour créer un pool d’instances :

1. [Créez un réseau virtuel avec un sous-réseau](#create-a-virtual-network-with-a-subnet).
2. [Créez un pool d’instances](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Créer un réseau virtuel avec un sous-réseau 

Pour placer plusieurs pools d’instances dans le même réseau virtuel, consultez les articles suivants :

- [Déterminer la taille du sous-réseau d’un réseau virtuel pour Azure SQL Database Managed Instance](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Créez un réseau virtuel et un sous-réseau à l’aide du [modèle du portail Azure](sql-database-managed-instance-create-vnet-subnet.md) ou suivez les instructions relatives à la [préparation d’un réseau virtuel existant](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Créer un pool d’instances 

Une fois les étapes précédentes terminées, vous êtes prêt à créer un pool d’instances.

Les restrictions suivantes s’appliquent aux pools d’instances :

- Seules les options Usage général et Gen5 sont disponibles en préversion publique.
- Le nom du pool ne peut contenir que des minuscules, des chiffres et des traits d’union, et ne peut pas commencer par un trait d’union.
- Si vous souhaitez utiliser AHB (Azure Hybrid Benefit), il s’applique au niveau du pool d’instances. Vous pouvez définir le type de licence lors de la création du pool ou le mettre à jour à tout moment après sa création.

> [!IMPORTANT]
> Le déploiement d’un pool d’instances est une opération longue qui prend environ 4,5 heures.

Pour obtenir les paramètres du réseau :

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Pour créer un pool d’instances :

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Étant donné que le déploiement d’un pool d’instances est une opération de longue durée, vous devez attendre qu’elle se termine avant de passer aux étapes suivantes de cet article.

## <a name="create-a-managed-instance-inside-the-pool"></a>Créer une instance gérée à l’intérieur du pool 

Après le déploiement réussi du pool d’instances, il est temps de créer une instance à l’intérieur de celui-ci.

Pour créer une instance gérée, exécutez la commande suivante :

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Le déploiement d’une instance à l’intérieur d’un pool prend quelques minutes. Une fois la première instance créée, des instances supplémentaires peuvent être créées :

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Créer une base de données à l’intérieur d’une instance 

Pour créer et gérer des bases de données dans une instance gérée qui se trouve à l’intérieur d’un pool, utilisez les commandes d’instance unique.

Pour créer une base de données à l’intérieur d’une instance gérée :

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Obtenir l’utilisation du pool d’instances 
 
Pour obtenir une liste d’instances à l’intérieur d’un pool :

```powershell
$instancePool | Get-AzSqlInstance
```


Pour obtenir l’utilisation des ressources du pool :

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Pour obtenir une vue d’ensemble détaillée de l’utilisation du pool et des instances à l’intérieur de celui-ci :

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Pour répertorier les bases de données dans une instance :

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Il existe une limite de 100 bases de données par pool (pas par instance).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Mettre à l’échelle une instance gérée à l’intérieur d’un pool 


Après avoir rempli une instance gérée avec des bases de données, vous pourriez atteindre des limites de l’instance pour le stockage ou les performances. Dans ce cas, si l’utilisation du pool n’a pas été dépassée, vous pouvez mettre à l’échelle votre instance.
La mise à l’échelle d’une instance gérée à l’intérieur d’un pool est une opération qui prend quelques minutes. La configuration requise pour la mise à l’échelle est le nombre de vCores et la quantité de stockage disponibles au niveau du pool d’instances.

Pour mettre à jour le nombre de vCores et la taille de stockage :

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Pour mettre à jour la taille de stockage uniquement :

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Se connecter à une instance gérée à l’intérieur d’un pool

Pour vous connecter à une instance gérée dans un pool, les deux étapes suivantes sont requises :

1. [Activez le point de terminaison public pour l’instance](#enable-the-public-endpoint-for-the-instance).
2. [Ajoutez une règle de trafic entrant au groupe de sécurité réseau](#add-an-inbound-rule-to-the-network-security-group).

Une fois ces deux étapes terminées, vous pouvez vous connecter à l’instance à l’aide d’une adresse de point de terminaison public, d’un port et des informations d’identification fournies lors de la création de l’instance. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Activer le point de terminaison public pour l’instance

L’activation du point de terminaison public pour une instance peut être effectuée via le portail Azure ou à l’aide de la commande PowerShell suivante :


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Ce paramètre peut également être défini lors de la création de l’instance.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Ajouter une règle de trafic entrant au groupe de sécurité réseau 

Cette étape peut être effectuée à l’aide du portail Azure ou à l’aide de commandes PowerShell, à tout moment après la préparation du sous-réseau pour l’instance gérée.

Pour plus d’informations, consultez [Autoriser le trafic du point de terminaison public sur le groupe de sécurité réseau](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Déplacer une instance unique existante à l’intérieur d’un pool d’instances 
 
Le déplacement d’instances dans et hors d’un pool est une des limitations de la préversion publique. Une solution de contournement utilisable s’appuie sur la restauration dans le temps des bases de données d’une instance située en dehors d’un pool vers une instance qui se trouve déjà dans un pool. 

Les deux instances doivent se trouver dans le même abonnement et la même région. La restauration inter-régions et inter-abonnements n’est actuellement pas prise en charge.

Ce processus présente un temps d’arrêt.

Pour déplacer des bases de données existantes :

1. Suspendez les charges de travail sur l’instance gérée à partir de laquelle vous effectuez la migration.
2. Générez des scripts pour créer des bases de données système et les exécuter sur l’instance qui se trouve à l’intérieur du pool d’instances.
3. Effectuez une restauration dans le temps de chaque base de données de l’instance unique vers l’instance dans le pool.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Pointez votre application vers la nouvelle instance et reprenez ses charges de travail.

S’il existe plusieurs bases de données, répétez le processus pour chaque base de données.


## <a name="next-steps"></a>Étapes suivantes

- Pour consulter la liste des fonctionnalités et les comparer, consultez [Fonctionnalités SQL communes](sql-database-features.md).
- Pour plus d’informations sur la configuration du réseau virtuel, consultez [Configuration de réseau virtuel d’une instance managée](sql-database-managed-instance-connectivity-architecture.md).
- Pour obtenir un guide de démarrage rapide qui crée une instance managée et restaure une base de données à partir d’un fichier de sauvegarde, consultez [Créer une instance managée](sql-database-managed-instance-get-started.md).
- Pour un tutoriel utilisant le service Azure Database Migration Service (DMS) pour la migration, consultez [Migration d’une instance managée à l’aide de DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Pour une supervision avancée des performances de base de données Managed Instance avec informations de dépannage intégrées, consultez [Surveiller Azure SQL Database avec Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Pour des informations sur les prix, consultez [Tarifs des instances managées SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
