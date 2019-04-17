---
title: Découvrez comment gérer les comptes de base de données dans Azure Cosmos DB
description: Découvrez comment gérer les comptes de base de données dans Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: b2b5e58ca480aa3abaa0766319977b8d1160ebeb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282999"
---
# <a name="manage-an-azure-cosmos-account"></a>Gérer un compte Azure Cosmos

Cet article décrit comment gérer votre compte Azure Cosmos. Vous allez apprendre à définir le multihébergement, à ajouter ou supprimer une région, à configurer plusieurs régions d’écriture et à définir les priorités de basculement. 

## <a name="create-a-database-account"></a>Création d’un compte de base de données

### <a id="create-database-account-via-portal"></a>Portail Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Interface CLI Azure

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Configurer les clients pour le multihébergement

### <a id="configure-clients-multi-homing-dotnet"></a>Kit SDK .NET v2

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-dotnet-v3"></a>Kit SDK .NET v3 (préversion)

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

### <a id="configure-clients-multi-homing-java-async"></a>Kit SDK Java Async

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-javascript"></a>Kit SDK Node.js/JavaScript/TypeScript

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

### <a id="configure-clients-multi-homing-python"></a>Kit SDK Python

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="addremove-regions-from-your-database-account"></a>Ajouter/supprimer des régions à partir de votre compte de base de données

### <a id="add-remove-regions-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos, puis ouvrez le menu **Répliquer les données globalement**.

2. Pour ajouter des régions, sélectionnez les hexagones sur la carte avec l’étiquette **+** qui correspond aux régions souhaitées. Sinon, pour ajouter une région, sélectionnez l’option **+ Ajouter une région**, puis choisissez une région dans le menu déroulant.

3. Pour supprimer des régions, désactivez une ou plusieurs régions à partir de la carte en sélectionnant les hexagones bleus avec des coches. Vous pouvez aussi sélectionner l’icône de la « Corbeille » (🗑) en regard de la région, sur le côté droit.

4. Pour enregistrer vos modifications, sélectionnez **OK**.

   ![Ajouter ou supprimer le menu des régions](./media/how-to-manage-database-account/add-region.png)

Dans un mode écriture dans une seule région, vous ne pouvez pas supprimer la région d’écriture. Vous devez basculer vers une autre région avant de pouvoir supprimer la région d’écriture actuelle.

Dans un mode d’écriture dans plusieurs régions, vous pouvez ajouter ou supprimer n’importe quelle région si vous en avez au moins une.

### <a id="add-remove-regions-via-cli"></a>Interface CLI Azure

```bash
# Create an account with 1 region
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0

# Add a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0 westus=1

# Remove a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0
```

## <a name="configure-multiple-write-regions"></a>Configurer les régions d’écriture multiples

### <a id="configure-multiple-write-regions-portal"></a>Portail Azure

Lorsque vous créez un compte de base de données, assurez-vous que le paramètre **Multi-region Writes** (Écritures multirégions) est activé.

![Capture d’écran de création de compte Azure Cosmos](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Interface CLI Azure

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>modèle Azure Resource Manager

Le code JSON suivant est un exemple de modèle [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Vous pouvez l’utiliser pour déployer un compte Azure Cosmos avec le [niveau de cohérence des obsolescences limitées](consistency-levels.md). L’intervalle d’obsolescence maximale est défini sur 5 secondes. Le nombre maximal de requêtes d’obsolescence toléré est défini sur 100. Pour en savoir plus sur le format et la syntaxe du modèle Resource Manager, consultez [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

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


## <a id="manual-failover"></a>Activer le basculement manuel pour votre compte Azure Cosmos

### <a id="enable-manual-failover-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos, puis ouvrez le menu **Répliquer les données globalement**.

2. En haut du menu, sélectionnez **Basculement manuel**.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le menu **Basculement manuel**, sélectionnez votre nouvelle région d’écriture. Sélectionnez la case à cocher pour indiquer que vous comprenez que cette option modifie votre région d’écriture.

4. Pour déclencher le basculement, sélectionnez **OK**.

   ![Menu du portail pour le basculement manuel](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Interface CLI Azure

```bash
# Given your account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0 eastus=1
```

## <a id="automatic-failover"></a>Activer le basculement automatique pour votre compte Azure Cosmos DB

### <a id="enable-automatic-failover-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos DB et ouvrez le volet **Répliquer les données globalement**. 

2. En haut du volet, sélectionnez **Basculement automatique**.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le volet **Basculement automatique**, vérifiez que l’option **Activer le basculement automatique** est définie sur **ACTIVÉ**. 

4. Sélectionnez **Enregistrer**.

   ![Menu du portail pour le basculement automatique](./media/how-to-manage-database-account/automatic-failover.png)

Vous pouvez également définir les priorités de basculement dans ce menu.

### <a id="enable-automatic-failover-via-cli"></a>Interface CLI Azure

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Définir les priorités de basculement pour votre compte Azure Cosmos

### <a id="set-failover-priorities-via-portal"></a>Portail Azure

1. Dans votre compte Azure Cosmos, ouvrez le volet **Répliquer les données globalement**. 

2. En haut du volet, sélectionnez **Basculement automatique**.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le volet **Basculement automatique**, vérifiez que l’option **Activer le basculement automatique** est définie sur **ACTIVÉ**. 

4. Pour modifier la priorité de basculement, faites glisser les régions de lecture via les trois points à gauche de la ligne qui s’affichent quand vous placez le curseur dessus. 

5. Sélectionnez **Enregistrer**.

   ![Menu du portail pour le basculement automatique](./media/how-to-manage-database-account/automatic-failover.png)

Vous ne pouvez pas modifier la région d’écriture dans ce menu. Vous devez effectuer un basculement manuel pour modifier la région d’écriture manuellement.

### <a id="set-failover-priorities-via-cli"></a>Interface CLI Azure

```bash
# Assume region order is initially eastus=0 westus=1 automatic failover on account creation
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies westus=0 eastus=1
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants :

* [Gérer la cohérence](how-to-manage-consistency.md)
* [Gérer les conflits entre les régions](how-to-manage-conflicts.md)
* [Diffusion mondiale - sous le capot](global-dist-under-the-hood.md)
* [Guide pratique pour configurer la fonction multimaître dans vos applications](how-to-multi-master.md)
* [Configurer les clients pour la multirésidence](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Ajouter ou supprimer des régions de votre compte Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Créer une stratégie de résolution de conflits personnalisée](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

