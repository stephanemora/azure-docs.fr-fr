---
title: Créer et gérer Azure Cosmos DB à l’aide de PowerShell
description: Utilisez Azure PowerShell pour gérer vos comptes, bases de données, conteneurs et débits Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/05/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 79302fc0f9addc70461d21c03b02416d15a6fa6c
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814929"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Gérer les ressources de l’API SQL Azure Cosmos DB à l’aide de PowerShell

Le guide suivant décrit comment utiliser PowerShell pour générer des scripts et automatiser la gestion des ressources Azure Cosmos DB, notamment les comptes, les bases de données, les conteneurs et les débits. La gestion d’Azure Cosmos DB est effectuée via l’applet de commande AzResource directement dans le fournisseur de ressources Azure Cosmos DB. Pour connaître toutes les propriétés qui peuvent être gérées à l’aide de PowerShell pour le fournisseur de ressources Azure Cosmos DB, consultez le [schéma de fournisseurs de ressources Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

Pour une gestion multiplateforme d’Azure Cosmos DB, vous pouvez utiliser [Azure CLI](manage-with-cli.md), l’[API REST][rp-rest-api] ou le [portail Azure](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Mise en route

Suivez les instructions indiquées dans [Guide pratique pour installer et configurer Azure PowerShell][powershell-install-configure] pour installer et vous connecter à votre compte Azure dans PowerShell.

* Si vous voulez exécuter les commandes suivantes sans demander la confirmation de l’utilisateur, ajoutez l’indicateur `-Force` à la commande.
* L’ensemble des commandes suivantes sont synchrones.

## <a name="azure-cosmos-accounts"></a>Comptes Azure Cosmos

Les sections suivantes montrent comment gérer le compte Azure Cosmos, et notamment :

* [Créer un compte Azure Cosmos](#create-account)
* [Mettre à jour un compte Azure Cosmos](#update-account)
* [Lister tous les comptes Azure Cosmos d’un abonnement](#list-accounts)
* [Obtenir un compte Azure Cosmos](#get-account)
* [Supprimer un compte Azure Cosmos](#delete-account)
* [Mettre à jour les balises pour un compte Azure Cosmos](#update-tags)
* [Répertorier les clés pour un compte Azure Cosmos](#list-keys)
* [Régénérer les clés pour un compte Azure Cosmos](#regenerate-keys)
* [Répertorier les chaînes de connexion d’un compte Azure Cosmos](#list-connection-strings)
* [Modifier la priorité de basculement pour un compte Azure Cosmos](#modify-failover-priority)

### <a id="create-account"></a> Créer un compte Azure Cosmos

Cette commande crée un compte de base de données Azure Cosmos DB avec [plusieurs régions][distribute-data-globally] et une [stratégie de cohérence](consistency-levels.md) d’obsolescence limitée.

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lowercase and < 31 characters .

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
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
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` Nom du compte Azure Cosmos. Doit être en minuscules, accepte les caractères alphanumériques et « - », et doit être compris entre 3 et 31 caractères.
* `$location` Emplacement de la ressource du compte Azure Cosmos.
* `$locations` Régions de réplica pour le compte de base de données. Il doit y avoir une région d’écriture par compte de base de données avec une valeur de priorité de basculement de 0.
* `$consistencyPolicy` Niveau de cohérence par défaut du compte Azure Cosmos. Pour plus d’informations, consultez [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` Valeurs de propriété transmises au fournisseur Cosmos DB Azure Resource Manager pour configurer le compte.

Les comptes Azure Cosmos peuvent être configurés avec un pare-feu IP et des points de terminaison de service Réseau virtuel. Pour plus d’informations sur la configuration du pare-feu IP pour Azure Cosmos DB, consultez [Configurer le pare-feu IP](how-to-configure-firewall.md).  Pour plus d’informations sur l’activation des points de terminaison de service pour Azure Cosmos DB, consultez [Configurer l’accès à partir de réseaux virtuels](how-to-configure-vnet-service-endpoint.md).

### <a id="list-accounts"></a>Lister tous les comptes Azure Cosmos d’un abonnement

Cette commande vous permet de lister tous les comptes Azure Cosmos d’un abonnement.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a> Obtenir les propriétés d’un compte Azure Cosmos

Cette commande vous permet d’obtenir les propriétés d’un compte Azure Cosmos existant.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Mettre à jour un compte Azure Cosmos

Cette commande vous permet de mettre à jour les propriétés de votre compte de base de données Azure Cosmos DB. Les propriétés pouvant être mises à jour sont les suivantes :

* Ajout ou suppression de régions
* Modification de la stratégie de cohérence par défaut
* Modification de la stratégie de basculement
* Modification du filtre de plage IP
* Modification des configurations de Réseau virtuel
* Activation du multimaître

> [!NOTE]
> Cette commande, qui vous permet d’ajouter ou de supprimer des régions, ne prend aucunement en charge la modification des priorités de basculement. Pour modifier la priorité de basculement, consultez [Modifier la priorité de basculement pour un compte Azure Cosmos](#modify-failover-priority).

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

### <a id="update-tags"></a> Mettre à jour les balises pour un compte Azure Cosmos

L’exemple suivant vous explique comment définir les [balises des ressources Azure][azure-resource-tags] pour un compte Azure Cosmos.

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

### <a id="regenerate-keys"></a> Régénérer des clés de compte

Les clés d’accès à un compte Azure Cosmos doivent être régulièrement régénérées pour garantir la sécurité des connexions. Des clés d’accès primaire et secondaire sont affectées au compte. Les clients peuvent ainsi conserver un accès pendant que l’autre clé est régénérée. Il existe quatre types de clés pour un compte Azure Cosmos (Primary, Secondary, PrimaryReadonly et SecondaryReadonly)

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

Pour les comptes de base de données multirégions, vous pouvez changer l’ordre dans lequel un compte Cosmos promeut les réplicas de lecture secondaires en cas de basculement régional sur le réplica d’écriture principal. Lorsque la région avec `failoverPriority=0` est modifiée, cette commande peut également être utilisée pour lancer un exercice de reprise d’activité après sinistre afin de tester la planification de la reprise d’activité.

Pour l’exemple ci-dessous, supposons que le compte a actuellement une priorité de basculement de westus=0 et eastus=1, et change de région.

> [!CAUTION]
> Le changement de `locationName` pour `failoverPriority=0` déclenche un basculement manuel pour un compte Azure Cosmos. Tout autre changement de priorité ne déclenche pas de basculement.

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

Les sections suivantes montrent comment gérer la base de données Azure Cosmos, et notamment :

* [Créer une base de données Azure Cosmos](#create-db)
* [Créer une base de données Azure Cosmos avec débit partagé](#create-db-ru)
* [Obtenir le débit d’une base de données Azure Cosmos](#get-db-ru)
* [Répertorier toutes les bases de données Azure Cosmos dans un compte](#list-db)
* [Obtenir une base de données Azure Cosmos unique](#get-db)
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

### <a id="create-db-ru"></a>Créer une base de données Azure Cosmos avec débit partagé

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

### <a id="get-db-ru"></a>Obtenir le débit d’une base de données Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="list-db"></a>Obtenir toutes les bases de données Azure Cosmos dans un compte

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Obtenir une base de données Azure Cosmos unique

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

## <a name="azure-cosmos-container"></a>Conteneur Azure Cosmos

Les sections suivantes montrent comment gérer le conteneur Azure Cosmos, et notamment :

* [Créer un conteneur Azure Cosmos](#create-container)
* [Créer un conteneur Azure Cosmos avec une grande clé de partition](#create-container-big-pk)
* [Obtenir le débit d’un conteneur Azure Cosmos](#get-container-ru)
* [Créer un conteneur Azure Cosmos avec débit partagé](#create-container-ru)
* [Créer un conteneur Azure Cosmos avec indexation personnalisée](#create-container-custom-index)
* [Créer un conteneur Azure Cosmos avec indexation désactivée](#create-container-no-index)
* [Créer un conteneur Azure Cosmos avec clé unique et TTL](#create-container-unique-key-ttl)
* [Créer un conteneur Azure Cosmos avec résolution des conflits](#create-container-lww)
* [Répertorier tous les conteneurs Azure Cosmos dans une base de données](#list-containers)
* [Obtenir un conteneur Azure Cosmos unique dans une base de données](#get-container)
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

### <a id="create-container-big-pk"></a>Créer un conteneur Azure Cosmos avec une grande taille de clé de partition

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
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
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>Obtenir le débit d’un conteneur Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>Créer un conteneur Azure Cosmos avec débit partagé

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

### <a id="create-container-custom-index"></a>Créer un conteneur Azure Cosmos avec stratégie d’indexation personnalisée

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
                "indexes"= @()
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

### <a id="create-container-no-index"></a>Créer un conteneur Azure Cosmos avec indexation désactivée

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

### <a id="create-container-unique-key-ttl"></a>Créer un conteneur Azure Cosmos avec stratégie de clé unique et TTL

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
                "indexes"= @()
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

### <a id="create-container-lww"></a>Créer un conteneur Azure Cosmos avec résolution des conflits

Pour créer une stratégie de résolution des conflits dans l’optique d’utiliser une procédure stockée, définissez `"mode"="custom"`, puis définissez le chemin d’accès de résolution comme nom de la procédure stockée, `"conflictResolutionPath"="myResolverStoredProcedure"`. Pour écrire tous les conflits dans ConflictsFeed et les gérer séparément, définissez `"mode"="custom"` et `"conflictResolutionPath"=""`

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

### <a id="list-containers"></a>Répertorier tous les conteneurs Azure Cosmos dans une base de données

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

### <a id="get-container"></a>Obtenir un conteneur Azure Cosmos unique dans une base de données

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

* [Tous les exemples PowerShell](powershell-samples.md)
* [Comment gérer un compte Azure Cosmos](how-to-manage-database-account.md)
* [Créer un conteneur Azure Cosmos](how-to-create-container.md)
* [Configurer la durée de vie dans Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
