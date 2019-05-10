---
title: Découvrez comment gérer les comptes de base de données dans Azure Cosmos DB
description: Découvrez comment gérer les comptes de base de données dans Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 57116327168a76f971a22b61144850199cb0cbae
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068801"
---
# <a name="manage-an-azure-cosmos-account"></a>Gérer un compte Azure Cosmos

Cet article décrit comment gérer différentes tâches sur un compte Azure Cosmos à l’aide du portail Azure, d’Azure PowerShell, d’Azure CLI et de modèles Azure Resource Manager.

## <a name="create-an-account"></a>Créer un compte

### <a id="create-database-account-via-portal"></a>Portail Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Interface CLI Azure

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case.

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations WestUS=0 EastUS=1 \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
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

### <a id="create-database-account-via-arm-template"></a>Modèle Azure Resource Manager

Ce modèle Azure Resource Manager va créer un compte Azure Cosmos DB pour n’importe quelle API prise en charge configurée avec deux régions et des options pour sélectionner le niveau de cohérence, le basculement automatique et la fonction multimaître. Pour déployer ce modèle, cliquez sur Déployer dans Azure sur la page readme [Créer un compte Azure Cosmos DB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account)

## <a name="addremove-regions-from-your-database-account"></a>Ajouter/supprimer des régions à partir de votre compte de base de données

### <a id="add-remove-regions-via-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

1. Accédez à votre compte Azure Cosmos, puis ouvrez le menu **Répliquer les données globalement**.

1. Pour ajouter des régions, sélectionnez les hexagones sur la carte avec l’étiquette **+** correspondante. Sinon, pour ajouter une région, sélectionnez l’option **+ Ajouter une région**, puis choisissez une région dans le menu déroulant.

1. Pour supprimer des régions, désactivez une ou plusieurs régions à partir de la carte en sélectionnant les hexagones bleus avec des coches. Vous pouvez aussi sélectionner l’icône de la « Corbeille » (🗑) en regard de la région, sur le côté droit.

1. Pour enregistrer vos modifications, sélectionnez **OK**.

   ![Ajouter ou supprimer le menu des régions](./media/how-to-manage-database-account/add-region.png)

Dans un mode écriture dans une seule région, vous ne pouvez pas supprimer la région d’écriture. Vous devez basculer vers une autre région avant de pouvoir supprimer la région d’écriture actuelle.

Dans un mode d’écriture dans plusieurs régions, vous pouvez ajouter ou supprimer n’importe quelle région si vous en avez au moins une.

### <a id="add-remove-regions-via-cli"></a>Interface CLI Azure

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations westus=0

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( 
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East Us"; "failoverPriority"=1 } 
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="configure-multiple-write-regions"></a>Configurer plusieurs régions d’écriture

### <a id="configure-multiple-write-regions-portal"></a>Portail Azure

Ouvrez l’onglet **Répliquer les données globalement** et sélectionnez **Activer** pour activer l’écriture dans plusieurs régions. Une fois que vous avez activé l’écriture dans plusieurs régions, toutes les régions de lecture indiquées sur votre compte deviennent des régions de lecture et d’écriture. 

> [!NOTE]
> Une fois l’écriture dans plusieurs régions activée, vous ne pouvez plus la désactiver. 

![Capture d’écran de la configuration de la fonction multimaître dans un compte Azure Cosmos](./media/how-to-manage-database-account/single-to-multi-master.png)

Si vous avez des questions sur cette fonctionnalité, utilisez l’adresse askcosmosdb@microsoft.com. 

### <a id="configure-multiple-write-regions-cli"></a>Interface CLI Azure

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Update an Azure Cosmos Account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>modèle Azure Resource Manager

Un compte peut passer de maître unique à multimaître en déployant le modèle Resource Manager utilisé pour créer le compte et définir `enableMultipleWriteLocations: true`. Le modèle Azure Resource Manager suivant est un modèle minimal qui va déployer un compte Azure Cosmos DB pour l’API SQL avec une seule région et la fonction multimaître activée.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations": [
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Activer le basculement automatique pour votre compte Azure Cosmos DB

L’option de basculement automatique permet à Azure Cosmos DB de basculer vers la région ayant la priorité de basculement la plus élevée sans action de l’utilisateur au cas où une région devienne indisponible. Lorsque le basculement automatique est activé, la priorité des régions peut être modifiée. Le compte doit avoir au moins deux régions pour activer le basculement automatique.

### <a id="enable-automatic-failover-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos DB et ouvrez le volet **Répliquer les données globalement**.

2. En haut du volet, sélectionnez **Basculement automatique**.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le volet **Basculement automatique**, vérifiez que l’option **Activer le basculement automatique** est définie sur **ACTIVÉ**. 

4. Sélectionnez **Enregistrer**.

   ![Menu du portail pour le basculement automatique](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Interface CLI Azure

```azurecli-interactive
# Enable automatic failover on an existing account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-automatic-failover true
```

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Définir les priorités de basculement pour votre compte Azure Cosmos

Une fois qu’un compte Cosmos est configuré pour le basculement automatique, la priorité de basculement des régions peut être modifiée.

> [!IMPORTANT]
> Vous ne pouvez pas modifier la région d’écriture (priorité de basculement de zéro) lorsque le compte est configuré pour le basculement automatique. Pour changer la région d’écriture, vous devez désactiver le basculement automatique et effectuer un basculement manuel.

### <a id="set-failover-priorities-via-portal"></a>Portail Azure

1. Dans votre compte Azure Cosmos, ouvrez le volet **Répliquer les données globalement**.

2. En haut du volet, sélectionnez **Basculement automatique**.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le volet **Basculement automatique**, vérifiez que l’option **Activer le basculement automatique** est définie sur **ACTIVÉ**.

4. Pour modifier la priorité de basculement, faites glisser les régions de lecture via les trois points à gauche de la ligne qui s’affichent quand vous placez le curseur dessus.

5. Sélectionnez **Enregistrer**.

   ![Menu du portail pour le basculement automatique](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Interface CLI Azure

```azurecli-interactive
# Assume region order is initially eastus=0 westus=1 southeastasia=2 on account creation
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb failover-priority-change --name $accountName --resource-group $resourceGroupName --failover-policies eastus=0 southeastasia=1 westus=2
```

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1, Southeast Asia = 2
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$failoverPolicies = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="Southeast Asia"; "failoverPriority"=1 },
    @{ "locationName"="East US"; "failoverPriority"=2 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a id="manual-failover"></a>Effectuer un basculement manuel sur un compte Azure Cosmos

> [!IMPORTANT]
> Le compte Azure Cosmos doit être configuré pour le basculement manuel pour que l’opération fonctionne.

Le processus de basculement manuel implique de changer la région d’écriture du compte (priorité de basculement = 0) pour une autre région configurée pour le compte.

> [!NOTE]
> Les comptes multimaître ne peuvent pas être basculés manuellement. Pour les applications qui utilisent le SDK Azure Cosmos DB, celui-ci détecte lorsqu’une région devient indisponible, puis redirige automatiquement vers la région la plus proche si vous utilisez une API d’hébergement multiple dans le SDK.

### <a id="enable-manual-failover-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos, puis ouvrez le menu **Répliquer les données globalement**.

2. En haut du menu, sélectionnez **Basculement manuel**.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le menu **Basculement manuel**, sélectionnez votre nouvelle région d’écriture. Sélectionnez la case à cocher pour indiquer que vous comprenez que cette option modifie votre région d’écriture.

4. Pour déclencher le basculement, sélectionnez **OK**.

   ![Menu du portail pour le basculement manuel](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Interface CLI Azure

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1
```

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

$account.Properties.locations=$locations;
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations et d’exemples de gestion du compte Azure Cosmos ainsi que des bases de données et conteneurs, consultez les articles suivants :

* [Gérer Azure Cosmos DB à l’aide d’Azure PowerShell](manage-with-powershell.md)
* [Gérer Azure Cosmos DB à l’aide d’Azure CLI](manage-with-cli.md)
