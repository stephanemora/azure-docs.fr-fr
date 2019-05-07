---
title: Créer et gérer Azure Cosmos DB à l’aide de PowerShell
description: Utilisez Azure Powershell pour gérer vos comptes Azure Cosmos DB, les bases de données, conteneurs et débit.
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/06/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 347c3a1c6cabca9532e5ada1237f2933841d5094
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077589"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Gérer les ressources de l’API SQL Azure Cosmos DB à l’aide de PowerShell

Le guide suivant décrit comment utiliser PowerShell pour le script et automatiser la gestion d’Azure Cosmos DB, y compris le compte, une base de données, un conteneur et un débit. Gestion d’Azure Cosmos DB est non par le biais d’applets de commande spécifiques à Azure Cosmos DB, mais avec le fournisseur de ressources directement par le biais de l’applet de commande AzResource. Pour afficher toutes les propriétés qui peuvent être gérées à l’aide de PowerShell pour le fournisseur de ressources Azure Cosmos DB, consultez [schéma de fournisseur de ressources Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

Pour une gestion multiplateforme d’Azure Cosmos DB, vous pouvez utiliser [Azure CLI](manage-with-cli.md), le [API REST][rp-rest-api], ou le [Azure portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Mise en route

Suivez les instructions de [comment installer et configurer Azure PowerShell] [ powershell-install-configure] pour installer et se connecter à votre compte Azure dans Powershell.

* Si vous voulez exécuter les commandes suivantes sans demander la confirmation de l’utilisateur, ajoutez l’indicateur `-Force` à la commande.
* L’ensemble des commandes suivantes sont synchrones.

## <a name="azure-cosmos-accounts"></a>Comptes Cosmos Azure

Les sections suivantes montrent comment gérer le compte Azure Cosmos, y compris :

* [Créer un compte Azure Cosmos](#create-account)
* [Mettre à jour un compte Azure Cosmos](#update-account)
* [Obtenir un compte Azure Cosmos](#get-account)
* [Supprimer un compte Azure Cosmos](#delete-account)
* [Mettre à jour des balises pour un compte Azure Cosmos](#update-tags)
* [Liste des clés pour un compte Azure Cosmos](#list-keys)
* [Régénère les clés pour un compte Azure Cosmos](#regenerate-keys)
* [Liste des chaînes de connexion pour un compte Azure Cosmos](#list-connection-strings)
* [Modifier la priorité de basculement pour un compte Azure Cosmos](#modify-failover-priority)

### <a id="create-account"></a> Créer un compte Azure Cosmos

Cette commande vous permet de créer un compte de base de données Azure Cosmos DB. Configurez votre nouveau compte de base de données pour une seule ou [plusieurs régions][distribute-data-globally] avec une [stratégie de cohérence](consistency-levels.md) déterminée.

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` Le nom du compte Azure Cosmos. Doit être en minuscules, accepte d’alphanumériques et le '-' caractère et entre 3 et 50 caractères.
* `$location` L’emplacement du compte Azure Cosmos.
* `$locations` Les régions de réplica pour le compte de base de données. Il doit y avoir une région d’écriture par compte de base de données avec une valeur de priorité de basculement de 0.
* `$consistencyPolicy` Le niveau de cohérence par défaut du compte Azure Cosmos. Pour plus d’informations, consultez [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` Les valeurs de propriété passées au fournisseur Cosmos DB Azure Resource Manager pour configurer le compte.

Points de terminaison de service Azure Cosmos comptes peuvent être configurés avec un pare-feu IP ainsi que de réseau virtuel. Pour plus d’informations sur la façon de configurer le pare-feu IP pour Azure Cosmos DB, consultez [configurer le pare-feu IP](how-to-configure-firewall.md).  Pour plus d’informations sur l’activation des points de terminaison de service pour Azure Cosmos DB, consultez [configurer l’accès à partir de réseaux virtuels](how-to-configure-vnet-service-endpoint.md) .

### <a id="get-account"></a> Obtenir les propriétés d’un compte Azure Cosmos

Cette commande permet d’obtenir les propriétés d’un compte Azure Cosmos existant.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Mettre à jour un compte Azure Cosmos

Cette commande vous permet de mettre à jour les propriétés de votre compte de base de données Azure Cosmos DB. Les propriétés pouvant être mis à jour sont les suivantes :

* Ajout ou suppression des zones
* Modification de la stratégie de cohérence par défaut
* Modification de la stratégie de basculement
* Modification de filtre de plage IP
* Modification des configurations de réseau virtuel
* L’activation de multiples maîtres

> [!NOTE]
> Cette commande, qui vous permet d’ajouter ou de supprimer des régions, ne prend aucunement en charge la modification des priorités de basculement. Pour modifier les priorités de basculement, consultez [modifier la priorité de basculement pour un compte Azure Cosmos](#modify-failover-priority).

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Supprimer un compte Azure Cosmos

Cette commande vous permet de supprimer un compte Azure Cosmos existant.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Balises de mise à jour d’un compte Azure Cosmos

L’exemple suivant explique comment définir [balises de ressources Azure] [ azure-resource-tags] pour un compte Azure Cosmos.

> [!NOTE]
> Cette commande peut être combinée aux commandes de création ou de mise à jour, via l’ajout de l’indicateur `-Tags` avec le paramètre correspondant.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a> Liste des clés de comptes

Lorsque vous créez un compte Azure Cosmos DB, le service génère deux clés d’accès maître qui peuvent être utilisées pour l’authentification lors de l’accès au compte Azure Cosmos DB. En fournissant deux clés d’accès, Azure Cosmos DB vous permet de régénérer les clés sans interruption de votre compte Azure Cosmos DB. Des clés en lecture seule pour l’authentification des opérations en lecture seule sont également disponibles. Il existe deux clés en lecture et écriture (primaire et secondaire) et deux clés en lecture seule (primaire et secondaire).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> Répertorier les chaînes de connexion

Pour les comptes MongoDB, vous pouvez récupérer la chaîne de connexion pour connecter votre application MongoDB au compte de base de données à l’aide de la commande suivante.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a> Régénérer les clés de compte

Clés d’accès à un compte Azure Cosmos doivent être régénérés régulièrement pour garantir la sécurité des connexions. Clés d’accès primaire et secondaire sont affectés au compte. Ainsi, les clients maintenir l’accès pendant que l’autre est régénérée. Il existe quatre types de clés pour un compte Azure Cosmos (principal, secondaire, PrimaryReadonly et SecondaryReadonly)

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="modify-failover-priority"></a> Modifier la priorité de basculement

Pour les comptes de base de données dans plusieurs régions, vous pouvez modifier la priorité de basculement des différentes régions dans le compte de base de données Azure Cosmos DB existant dans. Pour plus d’informations sur le basculement dans votre compte de base de données Azure Cosmos DB, consultez la section [Azure Cosmos DB, un service de base de données mondialement distribué sur Azure][distribute-data-globally].

Pour l’exemple ci-dessous, supposons que le compte a une priorité de basculement en cours de westus = 0 et eastus = 1. L’exemple ci-dessous retournera les régions.

> [!CAUTION]
> Cette opération déclenche un basculement manuel de votre compte pour la nouvelle région avec un paramètre failoverPriority égale à zéro.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Base de données Azure Cosmos

Les sections suivantes montrent comment gérer la base de données Azure Cosmos, y compris :

* [Créer une base de données Azure Cosmos](#create-db)
* [Créer une base de données Azure Cosmos avec un débit partagé](#create-db-ru)
* [Répertorier toutes les bases de données Azure Cosmos dans un compte](#get-all-db)
* [Obtenir une base de données Azure Cosmos](#get-db)
* [Supprimer une base de données Azure Cosmos](#delete-db)

### <a id="create-db"></a>Créer une base de données Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>Créer une base de données Azure Cosmos avec un débit partagé

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-all-db"></a>Obtenir toutes les bases de données Azure Cosmos dans un compte

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Obtenir une base de données Azure Cosmos

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Supprimer une base de données Azure Cosmos

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Conteneur d’Azure Cosmos

Les sections suivantes montrent comment gérer le conteneur Azure Cosmos, y compris :

* [Créer un conteneur Azure Cosmos](#create-container)
* [Créer un conteneur Azure Cosmos avec un débit partagé](#create-container-ru)
* [Créer un conteneur Azure Cosmos avec l’indexation personnalisée](#create-container-custom-index)
* [Créer un conteneur Azure Cosmos avec l’indexation mis hors tension](#create-container-no-index)
* [Créer un conteneur Azure Cosmos avec la clé unique et durée de vie](#create-container-unique-key-ttl)
* [Créer un conteneur Azure Cosmos avec la résolution des conflits](#create-container-lww)
* [Liste de tous les conteneurs Azure Cosmos dans une base de données](#list-all-container)
* [Obtenir un seul conteneur Azure Cosmos dans une base de données](#get-container)
* [Supprimer un conteneur Azure Cosmos](#delete-container)

### <a id="create-container"></a>Créer un conteneur Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }
    }; 
    "options"=@{ "Throughput"="400" }
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-ru"></a>Créer un conteneur Azure Cosmos avec un débit partagé

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }
    }; 
    "options"=@{}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>Créer un conteneur Azure Cosmos avec la stratégie d’index personnalisé

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent"; 
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>Créer un conteneur Azure Cosmos avec l’indexation mis hors tension

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>Créer un conteneur Azure Cosmos avec la stratégie de clé unique et durée de vie

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent"; 
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    }; 
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Créer un conteneur Azure Cosmos avec la résolution des conflits

Pour créer une stratégie de résolution de conflit pour utiliser une procédure stockée, définissez `"mode"="custom"` et définissez le chemin d’accès de résolution en tant que le nom de la procédure stockée, `"conflictResolutionPath"="myResolverStoredProcedure"`. Pour écrire tous les conflits dans le ConflictsFeed et gérer séparément, définissez `"mode"="custom"` et `"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins"; 
            "conflictResolutionPath"="/myResolutionPath"
        }
    }; 
    "options"=@{ "Throughput"="400" }
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-all-container"></a>Liste de tous les conteneurs Azure Cosmos dans une base de données

```azurepowershell-interactive
# List all Azure Cosmos containers in a database 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>Obtenir un seul conteneur Azure Cosmos dans une base de données

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Supprimer un conteneur Azure Cosmos

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>Étapes suivantes

* [Tous les exemples de PowerShell](powershell-samples.md)
* [Comment gérer le compte Azure Cosmos](how-to-manage-database-account.md)
* [Créer un conteneur Azure Cosmos](how-to-create-container.md)
* [Configurer le time-to-live dans Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/