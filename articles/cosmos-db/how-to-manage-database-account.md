---
title: Découvrez comment gérer les comptes de base de données dans Azure Cosmos DB
description: Découvrez comment gérer les comptes de base de données dans Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 67cd78d4900b8ce53cf0c50116c02a9c1b967687
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958761"
---
# <a name="manage-database-accounts-in-azure-cosmos-db"></a>Gérer les comptes de base de données dans Azure Cosmos DB

Cet article décrit comment gérer votre compte Cosmos DB pour définir le multihébergement, ajouter/supprimer une région, configurer plusieurs régions d’écriture et définir les priorités de basculement. 

## <a name="create-a-database-account"></a>Création d’un compte de base de données

### <a id="create-database-account-via-portal"></a>Portail Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Interface CLI Azure

```bash
# Create an account
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Configurer les clients pour le multihébergement

### <a id="configure-clients-multi-homing-dotnet"></a>Kit SDK .NET

```csharp
// Create a new Connection Policy
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to Preferred locations
// The name of the location will match what you see in the portal/etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the Connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>Kit SDK Java Async

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>Kit SDK Java Sync

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>Kit SDK Node.js/JavaScript/TypeScript

```javascript
// Set up the connection policy with your preferred regions
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>Kit SDK Python

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>Ajouter/supprimer des régions à partir de votre compte de base de données

### <a id="add-remove-regions-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos DB et ouvrez le menu **Replicate data globally** (Répliquer les données globalement).

2. Pour ajouter des régions, sélectionnez-en une ou plusieurs sur la carte en cliquant sur les hexagones vides avec l’étiquette **"+"** correspondant à la région souhaitée. Vous pouvez également ajouter une région en sélectionnant l’option **+ Ajouter une région** et en choisissant une région dans le menu déroulant.

3. Pour supprimer des régions, désélectionnez-en une ou plusieurs sur la carte en cliquant sur les hexagones bleus avec une coche, ou sélectionnez l’icône (🗑) de corbeille sur le côté droit.

4. Cliquez sur « Enregistrer » pour enregistrer vos modifications.

   ![Menu pour ajouter/supprimer des régions](./media/how-to-manage-database-account/add-region.png)

Dans un mode d’écriture à une seule région, vous ne pouvez pas supprimer la région d’écriture. Vous devez basculer vers une autre région avant de supprimer la région d’écriture actuelle.

Dans un mode d’écriture à plusieurs régions, vous pouvez ajouter ou supprimer n’importe quelle région, du moment qu’il vous en reste au moins une.

### <a id="add-remove-regions-via-cli"></a>Interface CLI Azure

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'westus=0'
```

## <a name="configure-multiple-write-regions"></a>Configurer les régions d’écriture multiples

### <a id="configure-multiple-write-regions-portal"></a>Portail Azure

Lorsque vous créez un compte de base de données, assurez-vous que le paramètre **Multi-region Writes** (Écritures multirégions) est activé.

![Capture d’écran de création de compte Cosmos DB](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Interface CLI Azure

```bash
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>modèle Azure Resource Manager

Le code JSON suivant est un exemple de modèle Resource Manager. Vous pouvez l’utiliser pour déployer un compte Azure Cosmos DB avec une stratégie de cohérence basée sur une obsolescence limitée : intervalle d’obsolescence maximal de 5 secondes, et nombre maximal de requêtes d’obsolescence tolérées de 100. Pour en savoir plus sur le format et la syntaxe du modèle Resource Manager, consultez la documentation [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
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


## <a name="enable-manual-failover-for-your-cosmos-account"></a>Activer le basculement manuel pour votre compte Cosmos

### <a id="enable-manual-failover-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos DB et ouvrez le menu **Replicate data globally** (Répliquer les données globalement).

2. Cliquez sur le bouton **Basculement manuel** en haut du menu.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le menu **Basculement manuel**, sélectionnez votre nouvelle région d’écriture, puis cochez la case pour indiquer que vous avez compris que cette option modifiera votre région d’écriture.

4. Cliquez sur OK pour déclencher le basculement.

   ![Menu du portail pour le basculement manuel](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Interface CLI Azure

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a name="enable-automatic-failover-for-your-cosmos-account"></a>Activer le basculement automatique pour votre compte Cosmos

### <a id="enable-automatic-failover-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos DB et ouvrez le volet **Replicate data globally** (Répliquer les données globalement). 

2. Cliquez sur le bouton **Basculement automatique** en haut du volet.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le volet **Basculement automatique**, assurez-vous que l’option **Activer le basculement automatique** est définie sur **ON** (Activé). 

4. Cliquez sur « Enregistrer » en bas du menu.

   ![Menu du portail pour le basculement automatique](./media/how-to-manage-database-account/automatic-failover.png)

Vous pouvez également définir les priorités de basculement dans ce menu.

### <a id="enable-automatic-failover-via-cli"></a>Interface CLI Azure

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-cosmos-account"></a>Définir les priorités de basculement pour votre compte Cosmos

### <a id="set-failover-priorities-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos DB et ouvrez le volet **Replicate data globally** (Répliquer les données globalement). 

2. Cliquez sur le bouton **Basculement automatique** en haut du volet.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le volet **Basculement automatique**, assurez-vous que l’option **Activer le basculement automatique** est définie sur **ON** (Activé). 

4. Vous pouvez modifier la priorité de basculement en cliquant sur les régions de lecture et en les faisant glisser. Pour ce faire, survolez la ligne avec le curseur de la souris, et cliquez sur les trois points qui s’affichent sur la gauche. 

5. Cliquez sur « Enregistrer » en bas du menu.

   ![Menu du portail pour le basculement automatique](./media/how-to-manage-database-account/automatic-failover.png)

Vous ne pouvez pas modifier la région d’écriture dans ce menu. Vous devez effectuer un basculement manuel pour modifier la région d’écriture manuellement.

### <a id="set-failover-priorities-via-cli"></a>Interface CLI Azure

```bash
az cosmosdb failover-priority-change --name <Cosmos DB Account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la gestion des conflits de données et des niveaux de cohérence dans Cosmos DB, consultez la documentation suivante :

* [Comment gérer la cohérence](how-to-manage-consistency.md)
* [Comment gérer les conflits entre les régions](how-to-manage-conflicts.md)

