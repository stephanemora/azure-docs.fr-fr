---
title: Approvisionner le débit avec mise à l’échelle automatique dans l’API SQL Azure Cosmos DB
description: Apprenez à approvisionner le débit avec mise à l’échelle automatique au niveau du conteneur et de la base de données dans l’API SQL Azure Cosmos DB à l’aide du Portail Azure, CLI, PowerShell et d’autres kits de développement logiciel (SDK).
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/18/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 95f201a7809184b511c3187cf3fcf5fadd233168
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531575"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db---sql-api"></a>Approvisionner le débit avec mise à l’échelle automatique sur une base de données ou un conteneur dans Azure Cosmos DB - API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Cet article explique comment approvisionner le débit avec mise à l’échelle automatique sur une base de données ou un conteneur (collection, graphique ou table) dans l’API SQL Azure Cosmos DB. Vous pouvez activer la mise à l’échelle automatique sur un seul conteneur ou approvisionner le débit avec mise à l’échelle automatique sur une base de données et le partager entre tous les conteneurs de cette base de données.

Si vous utilisez une autre API, consultez les articles [API pour MongoDB](mongodb/how-to-provision-throughput-mongodb.md), [API Cassandra](cassandra/how-to-provision-throughput-cassandra.md), [API Gremlin](how-to-provision-throughput-gremlin.md) pour approvisionner le débit.

## <a name="azure-portal"></a>Portail Azure

### <a name="create-new-database-or-container-with-autoscale"></a>Créer une base de données ou un conteneur avec mise à l’échelle automatique

1. Connectez-vous au [Portail Azure](https://portal.azure.com) ou à l’ [explorateur Azure Cosmos DB.](https://cosmos.azure.com/)

1. Accédez à votre compte Azure Cosmos DB et ouvrez l’onglet **Explorateur de données**.

1. Sélectionnez **Nouveau conteneur**. Entrez un nom pour votre base de données, conteneur, ainsi qu'une clé de partition. Sous Débit de la base de données ou d’un conteneur, sélectionnez l’option **Mise à l’échelle automatique** et définissez le [débit maximal (RU/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) auquel vous souhaitez que la base de données ou le conteneur soit mis à l’échelle.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="Création d’un conteneur et configuration du débit provisionné en mode de mise à l’échelle automatique":::

1. Sélectionnez **OK**.

Pour configurer la mise à l’échelle automatique sur une base de données à débit partagé, sélectionnez l’option **Approvisionner le débit d’une base de données** lors de la création d’une base de données. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>Activer la mise à l’échelle automatique sur une base de données ou un conteneur existant

1. Connectez-vous au [Portail Azure](https://portal.azure.com) ou à l’ [explorateur Azure Cosmos DB.](https://cosmos.azure.com/)

1. Accédez à votre compte Azure Cosmos DB et ouvrez l’onglet **Explorateur de données**.

1. Sélectionnez **Mise à l’échelle et paramètres** pour votre conteneur ou **Mise à l’échelle** pour votre base de données.

1. Sous **Mise à l’échelle**, sélectionnez l’option **Mise à l’échelle automatique**, puis **Enregistrer**.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="Activation de la mise à l’échelle automatique sur un conteneur existant":::

> [!NOTE]
> Lorsque vous activez la mise à l’échelle automatique sur une base de données ou un conteneur existant, la valeur de départ pour le nombre maximum de RU/s est déterminée par le système, en fonction de vos paramètres de débit approvisionné manuellement et de votre stockage. Une fois l’opération terminée, vous pouvez modifier le nombre maximal de RU/s si nécessaire. [En savoir plus.](autoscale-faq.yml#how-does-the-migration-between-autoscale-and-standard--manual--provisioned-throughput-work-) 

## <a name="azure-cosmos-db-net-v3-sdk"></a>Kit de développement logiciel (SDK) .NET Azure Cosmos DB V3

Utilisez la [version 3.9 ou ultérieure](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) du Kit de développement logiciel (SDK) .NET Azure Cosmos DB pour l’API SQL pour gérer les ressources avec mise à l’échelle automatique. 

> [!IMPORTANT]
> Vous pouvez utiliser le Kit de développement logiciel (SDK) .NET pour créer des ressources avec mise à l’échelle automatique. Le Kit de développement logiciel (SDK) ne prend pas en charge la migration entre le débit en mode de mise à l’échelle automatique et le débit standard (manuel). Actuellement, le scénario de migration est pris en charge uniquement dans le [portail Azure](#enable-autoscale-on-existing-database-or-container), l’interface [CLI](#azure-cli) et [PowerShell](#azure-powershell).

### <a name="create-database-with-shared-throughput"></a>Créer une base de données avec débit partagé

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>Créer un conteneur avec débit dédié

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>Lire le débit actuel (RU/s)

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>Modifier le débit maximal avec mise à l’échelle automatique (RU/s)

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk"></a>Kit de développement logiciel (SDK) Java Azure Cosmos DB V4

Vous pouvez utiliser la [version 4.0 ou ultérieure](https://mvnrepository.com/artifact/com.azure/azure-cosmos) du Kit de développement logiciel (SDK) Java Azure Cosmos DB pour l’API SQL pour gérer les ressources avec mise à l’échelle automatique.

> [!IMPORTANT]
> Vous pouvez utiliser le Kit de développement logiciel (SDK) Java pour créer des ressources avec mise à l’échelle automatique. Le Kit de développement logiciel (SDK) ne prend pas en charge la migration entre le débit en mode de mise à l’échelle automatique et le débit standard (manuel). Actuellement, le scénario de migration est pris en charge uniquement dans le [portail Azure](#enable-autoscale-on-existing-database-or-container), l’interface [CLI](#azure-cli) et [PowerShell](#azure-powershell).
### <a name="create-database-with-shared-throughput"></a>Créer une base de données avec débit partagé

# <a name="async"></a>[Asynchrone](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

# <a name="sync"></a>[Synchronisation](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>Créer un conteneur avec débit dédié

# <a name="async"></a>[Asynchrone](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

# <a name="sync"></a>[Synchronisation](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>Lire le débit actuel (RU/s)

# <a name="async"></a>[Asynchrone](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

# <a name="sync"></a>[Synchronisation](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>Modifier le débit maximal avec mise à l’échelle automatique (RU/s)

# <a name="async"></a>[Asynchrone](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

# <a name="sync"></a>[Synchronisation](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Les modèles Azure Resource Manager peuvent être utilisés pour provisionner le débit avec mise à l’échelle automatique sur une nouvelle ressource de base de données ou au niveau du conteneur pour toutes les API Azure Cosmos DB. Consultez [Modèles Azure Resource Manager pour Azure Cosmos DB](./templates-samples-sql.md) afin de voir des exemples. Par défaut, les modèles Azure Resource Manager ne peuvent pas être utilisés pour effectuer une migration entre le débit provisionné et le débit avec mise à l’échelle automatique sur une ressource existante. 

## <a name="azure-cli"></a>Azure CLI

L’interface Azure CLI peut être utilisée pour provisionner le débit avec mise à l’échelle automatique sur une nouvelle ressource de base de données ou au niveau du conteneur pour toutes les API Azure Cosmos DB ou activer la mise à l’échelle automatique sur une ressource existante. Pour voir des exemples, consultez [Exemples Azure CLI pour Azure Cosmos DB](cli-samples.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell peut être utilisé pour provisionner le débit avec mise à l’échelle automatique sur une nouvelle ressource de base de données ou au niveau du conteneur pour toutes les API Azure Cosmos DB ou activer la mise à l’échelle automatique sur une ressource existante. Pour obtenir des exemples, consultez [Exemples Azure PowerShell pour Azure Cosmos DB](powershell-samples.md).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [avantages du débit approvisionné avec la mise à l’échelle automatique](provision-throughput-autoscale.md#benefits-of-autoscale).
* Découvrez comment [choisir entre le débit manuel et le débit avec mise à l’échelle automatique](how-to-choose-offer.md).
* Examinez le [FAQ sur la mise à l’échelle automatique](autoscale-faq.yml).
