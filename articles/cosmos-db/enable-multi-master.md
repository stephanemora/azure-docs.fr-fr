---
title: Activer la fonction multimaître pour les comptes Azure Cosmos DB
description: Cet article explique comment activer la prise en charge de la fonction multimaître pendant la création d’un compte Azure Cosmos DB avec le portail Azure, PowerShell, l’interface de ligne de commande ou un modèle Azure Resource Manager.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: b6fc04fc728f753dc8a3900b26c6c01f03cc7710
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077236"
---
# <a name="enable-multi-master-for-azure-cosmos-db-accounts"></a>Activer la fonction multimaître pour les comptes Azure Cosmos DB

La prise en charge de la fonction multimaître est activée durant la création d’un compte Azure Cosmos DB. Vous pouvez créer un compte Azure Cosmos DB à l’aide du portail Azure, de PowerShell, de l’interface de ligne de commande ou d’un modèle Azure Resource Manager.

> [!IMPORTANT]
> La prise en charge de la fonction multimaître ne peut être activée que pour les nouveaux comptes Azure Cosmos DB. Les comptes Azure Cosmos DB existants ne peuvent pas utiliser cette fonctionnalité. Nous travaillons à la prise en charge pour les comptes existants et vous informerons dès qu’elle sera disponible.

Après avoir créé un compte Azure Cosmos DB avec prise en charge de la fonction multimaître, vous pouvez créer des bases de données et des conteneurs, charger des documents et assigner des stratégies de résolution de conflit. Pour découvrir la résolution de conflit à travers des exemples de code multimaître, consultez l’article [Exemples de code multimaître](multi-master-conflict-resolution.md#code-samples).

## <a name="enable-multi-master-using-azure-portal"></a>Activer la fonction multimaître à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/)

2. Cliquez sur Sélectionner **Créer une ressource > Bases de données > Azure Cosmos DB**.

3. Dans le volet **Nouveau compte**, entrez les paramètres suivants pour un nouveau compte Azure Cosmos DB :

   |**Paramètre**  |**Valeur suggérée** |**Description**|
   |---------|---------|---------|
   |Abonnement   | {Votre abonnement}  |Sélectionnez l’abonnement Azure à utiliser pour ce compte Azure Cosmos DB.  |
   |Groupe de ressources  |   {Nom de votre groupe de ressources}    |  Sélectionnez un groupe de ressources existant ou sélectionnez  **Créer nouveau**, puis entrez le nom du nouveau groupe de ressources pour votre compte. |
   |Nom du compte | {Nom de votre compte}   |  Entrez un nom unique pour identifier votre compte Azure Cosmos DB.        |
   |API  |   Quelconque   |  Sélectionnez un type d’API.   |
   |Lieu  | Sélectionnez une région.   | Sélectionnez l’emplacement géographique où héberger votre compte Azure Cosmos DB. Vous pouvez choisir n’importe quelle région, car ce compte se trouvera dans plusieurs régions.  |
   |Activer la géoredondance   |  Activer  |  Sélectionnez cette option pour que la fonction multimaître puisse être sélectionnée plus bas.   |
   |Activer la fonction multimaître | Activer  | Sélectionnez cette option pour activer la fonction multimaître pour ce compte. |


## <a name="using-multi-master-in-sdks"></a>Utilisation de la fonction multimaître dans les SDK

Avec un compte prenant en charge la fonction multimaître, dans vos applications, vous pouvez tirer parti de cette fonction au moyen de ConnectionPolicy, comme indiqué ci-dessous.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct,
   ConnectionProtocol = Protocol.Tcp,
   UseMultipleWriteLocations = true,
};
policy.PreferredLocations.Add(LocationNames.WestUS);
policy.PreferredLocations.Add(LocationNames.NorthEurope);
policy.PreferredLocations.Add(LocationNames.SoutheastAsia);
```

## <a name="enable-multi-master-using-powershell"></a>Activer la fonction multimaître à l’aide de PowerShell

Vous pouvez également créer un compte Cosmos DB prenant en charge la fonction multimaître en définissant le paramètre `enableMultipleWriteLocations` sur « true ». Pour créer un compte Azure Cosmos DB prenant en charge la fonction multimaître, ouvrez une fenêtre PowerShell et exécutez le script suivant :

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = "<ip-range-filter>"

$consistencyPolicy = @{"defaultConsistencyLevel"="Session";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

## <a name="enable-multi-master-using-cli"></a>Activer la fonction multimaître à l’aide de l’interface de ligne de commande

Vous pouvez activer la fonction multimaître en définissant le paramètre enable-multiple-write-locations sur « true ». Pour créer un compte Azure Cosmos DB prenant en charge la fonction multimaître, ouvrez Azure CLI ou Cloud Shell et exécutez le script suivant :

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --default-consistency-level "Session" \
   --enable-automatic-failover "true" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="enable-multi-master-using-resource-manager-template"></a>Activer la fonction multimaître à l’aide d’un modèle Resource Manager

Le code JSON suivant est un exemple de modèle Resource Manager que vous pouvez utiliser pour déployer un compte Azure Cosmos DB. Pour en savoir plus sur le format et la syntaxe du modèle Resource Manager, consultez la documentation [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Le paramètre principal dans ce modèle est "enableMultipleWriteLocations": true.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à activer la prise en charge de la fonction multimaître pour les comptes Azure Cosmos DB. Vous pouvez maintenant consulter les ressources suivantes :

* [Utilisation du multimaître avec des bases de données NoSQL open source](multi-master-oss-nosql.md)

* [Présentation de la résolution des conflits dans Azure Cosmos DB](multi-master-conflict-resolution.md)
