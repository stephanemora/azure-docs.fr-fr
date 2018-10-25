---
title: Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB | Microsoft Docs
description: Découvrez comment le partitionnement fonctionne dans Azure Cosmos DB, comment configurer le partitionnement et les clés de partition, et comment choisir la clé de partition appropriée pour votre application.
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 968651e2bd06d54c8b735bf2418e0d84b94f315d
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078560"
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partitionner et mettre à l’échelle dans Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) est un service de base de données multimodèle distribué à l’échelle mondiale, qui a été conçu pour vous permettre d’accéder à des performances élevées et prévisibles. Il s’adapte en toute transparence avec votre application. Cet article offre une vue d’ensemble du fonctionnement du partitionnement pour toutes les modèles de données dans Azure Cosmos DB. Il vous explique aussi comment configurer des conteneurs Azure Cosmos DB pour mettre efficacement vos applications à l’échelle.

Azure Cosmos DB prend en charge les types de conteneurs suivants sur toutes les API :

- **Conteneur fixe** : ces conteneurs peuvent stocker une base de données de graphiques dont la taille peut atteindre 10 Go et lui allouer jusqu’à 10 000 unités de requête par seconde. Pour créer un conteneur fixe, il est inutile de spécifier une propriété de clé de partition dans les données.

- **Conteneur illimité** : ces conteneurs sont dimensionnés automatiquement pour stocker un graphique au-delà de la limite de 10 Go grâce au partitionnement horizontal. Chaque partition stocke 10 Go, et les données sont automatiquement réparties en fonction de la **clé de partition spécifiée**, qui est un paramètre obligatoire lors de l’utilisation d’un conteneur illimité. Ce type de conteneur peut stocker un volume de données quasi illimité et traiter 100 000 unités de requête par seconde ou plus [en contactant le support](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

## <a name="partitioning-in-azure-cosmos-db"></a>Partitionnement dans Azure Cosmos DB
Azure Cosmos DB propose des conteneurs pour le stockage des données, appelés collections (pour les documents), graphes ou tables. Les conteneurs sont des ressources logiques. Ils peuvent s’étendre sur plusieurs partitions physiques ou serveurs. Le nombre de partitions est déterminé par Azure Cosmos DB en fonction de la taille de stockage et du débit provisionné pour un conteneur ou un ensemble de conteneurs. 

### <a name="physical-partition"></a>Partition physique

Une partition *physique* est une quantité fixe de stockage réservé s’appuyant sur des disques SSD, combinée à une quantité variable de ressources de calcul (processeur et mémoire). Chaque partition physique est répliquée à des fins de haute disponibilité. Chaque ensemble de conteneurs peut partager une ou plusieurs partitions physiques. La gestion des partitions physiques est entièrement assurée par Azure Cosmos DB, et vous n’avez pas à écrire de code complexe ni à gérer vos partitions. Les conteneurs Azure Cosmos DB sont illimités en termes de débit et de stockage. Les partitions physiques représentent un concept interne d’Azure Cosmos DB et sont temporaires. Azure Cosmos DB ajustera automatiquement le nombre de partitions physiques en fonction de votre charge de travail. Vous ne devez pas corréler votre conception de base de données au nombre de partitions physiques, mais plutôt veiller à choisir la bonne clé de partition qui détermine les partitions logiques. 

### <a name="logical-partition"></a>Partition logique

Une partition *logique* est une partition dans une partition physique qui stocke toutes les données associées à une valeur de clé de partition unique. Plusieurs partitions logiques peuvent finalement se retrouver dans la même partition physique. Dans le diagramme suivant, un conteneur unique comporte trois partitions logiques. Chaque partition logique stocke les données pour une clé de partition, LAX, AMS et MEL respectivement. Aucune des partitions logiques LAX, AMS et MEL ne peut croître au-delà de la limite maximale de partition logique de 10 Go. 

![Partitionnement des ressources](./media/introduction/azure-cosmos-db-partitioning.png) 

Quand un conteneur satisfait aux [prérequis pour le partitionnement](#prerequisites), le partitionnement est complètement transparent pour votre application. Le service gère la distribution des données entre les partitions physiques et logiques et le routage des demandes de requête vers la partition appropriée. 

## <a name="how-does-partitioning-work"></a>Fonctionnement du partitionnement

Chaque doc doit avoir une *clé de partition* et une *clé de ligne*, qui l’identifient de façon unique. Votre clé de partition agit comme une partition logique pour vos données et fournit à Azure Cosmos DB une frontière naturelle pour la distribution de données entre plusieurs partitions physiques. **Les données pour une partition logique unique doivent résider dans une seule partition physique et la gestion des partitions physiques est gérée par Azure Cosmos DB**. 

En bref, voici comment le partitionnement fonctionne dans Azure Cosmos DB :

* Vous provisionnez un ensemble de conteneurs Azure Cosmos DB avec un débit de **T** RU/s (requêtes par seconde).  
* En arrière-plan, Azure Cosmos DB provisionne les partitions physiques nécessaires pour satisfaire **T** requêtes par seconde. Si la valeur de **T** est supérieure au débit maximal par partition physique **t**, Azure Cosmos DB provisionne **N = T/t** partitions physiques. La valeur de débit maximal par partition(t) est configurée par Azure Cosmos DB ; cette valeur est attribuée en fonction du débit approvisionné total et de la configuration matérielle utilisée.  
* Azure Cosmos DB alloue l’espace des hachages de clé de partition uniformément entre les **N** partitions physiques. Par conséquent, le nombre de partitions logiques hébergées par chaque partition physique est **1/N** * nombre de valeurs de clé de partition.  
* Quand une partition physique **p** atteint sa limite de stockage, Azure Cosmos DB fractionne **p** en deux nouvelles partitions physiques : **p1** et **p2**. Des valeurs correspondant à environ la moitié des clés sont distribuées à chacune des nouvelles partitions physiques. Cette opération de division est complètement invisible pour votre application. Si une partition physique atteint sa limite de stockage et que toutes les données dans la partition physique appartiennent à la même clé de partition logique, l’opération de fractionnement n’a pas lieu. En effet, toutes les données d’une clé de partition logique unique doivent résider dans la même partition physique. Dans ce cas, une stratégie de clé de partition différente doit être employée.  
* Quand vous provisionnez un débit supérieur à **t*N**, Azure Cosmos DB fractionne une ou plusieurs de vos partitions physiques pour prendre en charge le débit plus élevé.

La sémantique pour les clés de partition est légèrement différente pour correspondre à celle de chaque API, comme indiqué dans le tableau suivant :

| API | Clé de partition | Clé de ligne |
| --- | --- | --- |
| SQL | Chemin de clé de partition personnalisée | `id` fixe | 
| MongoDB | Clé de partitionnement personnalisée  | `_id` fixe | 
| Gremlin | Propriété de clé de partition personnalisée | `id` fixe | 
| Table | `PartitionKey` fixe | `RowKey` fixe | 

Azure Cosmos DB utilise le partitionnement basé sur le hachage. Quand vous écrivez un élément, Azure Cosmos DB hache la valeur de clé de partition et utilise le résultat haché pour déterminer dans quelle partition stocker l’élément. 

> [!NOTE]
> Azure Cosmos DB stocke tous les éléments avec la même clé de partition dans la même partition physique. 

## <a name="best-practices-when-choosing-a-partition-key"></a>Meilleures pratiques lors du choix d’une clé de partition

Le choix de la clé de partition est une décision importante que vous devrez prendre au moment de la conception. Choisissez un nom de propriété qui a une large plage de valeurs et des modèles d’accès uniformes. Il est recommandé de disposer d’une clé de partition avec un grand nombre de valeurs distinctes (par ex., des centaines ou des milliers). Ceci vous permet de répartir uniformément votre charge de travail entre ces valeurs. Une clé de partition idéale apparaît fréquemment sous forme de filtre dans vos requêtes efficaces et possède une cardinalité suffisante pour garantir l’évolutivité de votre solution.

Si une partition physique atteint sa limite de stockage et que les données de la partition ont la même clé de partition, Azure Cosmos DB retourne le message *« La clé de partition a atteint la taille maximale de 10 Go »* et la partition n’est pas fractionnée. Le choix d’une clé de partition en bon état est une décision très importante. 

Choisissez une clé de partition de sorte que :

* La distribution du stockage est égale entre toutes les clés.  
* Choisissez une clé de partition qui répartira les données de manière uniforme entre les partitions.

  Il est judicieux de vérifier la façon dont vos données sont réparties sur les partitions. Pour vérifier cette répartition des données dans le portail, connectez-vous à votre compte Azure Cosmos DB et cliquez sur **Métriques** dans la section **Surveillance**. Ensuite, cliquez sur l’onglet **Stockage** pour déterminer le mode de répartition des données dans les différentes partitions physiques.

  ![Partitionnement des ressources](./media/partition-data/partitionkey-example.png)

  Ci-dessus, l’image de gauche montre le résultat d’une clé de partition incorrecte et l’image de droite le résultat lorsqu’une clé de partition correcte a été choisie. Dans l’image de gauche, vous pouvez voir que les données ne sont pas réparties uniformément sur les partitions. Vous devez vous efforcer de choisir une clé de partition qui distribue vos données de manière qu’elles ressemblent à l’image de droite.

* Optimisez les requêtes pour obtenir des données dans les limites d’une partition, lorsque cela est possible. Une stratégie de partitionnement optimale doit être en phase avec les modèles de requête. Les requêtes qui obtiennent des données à partir d’une seule partition offrent les meilleures performances. Les demandes appelées avec une concurrence élevée peuvent être acheminées efficacement en incluant la clé de partition dans le prédicat de filtre.  

* Il est généralement préférable de choisir une clé de partition avec une cardinalité plus élevée car cela améliore souvent la distribution et l’évolutivité. Par exemple, une clé synthétique peut être formée en concaténant les valeurs à partir de plusieurs propriétés pour augmenter la cardinalité.  

Lorsque vous choisissez une clé de partition en tenant compte des considérations ci-dessus, vous n’avez pas à vous soucier du nombre de partitions ou du débit alloué par partition physique car Azure Cosmos DB adapte le nombre de partitions physiques ainsi que les partitions individuelles en cas de besoin.

## <a name="prerequisites"></a>Prérequis pour le partitionnement

Les conteneurs Azure Cosmos DB peuvent être créés fixes ou illimités. Les conteneurs de taille fixe ont une limite maximale de 10 Go et de 10 000 RU/s de débit. Pour créer un conteneur dit illimité, vous devez spécifier une clé de partition et un débit minimum de 1 000 RU/s. Vous pouvez également créer des conteneurs Azure Cosmos DB de sorte qu’ils se partagent le débit. Dans de tels cas, chaque conteneur doit spécifier une clé de partition et peut se développer à l’infini. 

Les conditions préalables à prendre en compte pour le partitionnement et la mise à l’échelle sont les suivantes :

* Quand vous créez un conteneur (par ex., une collection, un graphique ou une table) dans le portail Azure, sélectionnez l’option de capacité de stockage **Illimité** pour tirer parti de la mise à l’échelle automatique. Pour que les partitions physiques se fractionnent automatiquement en **p1** et **p2** comme décrit dans l’article [Fonctionnement du partitionnement](#how-does-partitioning-work), vous devez créer le conteneur avec un débit minimal de 1 000 RU/s (ou partager le débit entre plusieurs conteneurs) et fournir une clé de partition. 

* Si vous créez un conteneur avec un débit initial supérieur ou égal à 1 000 RU/s et si vous fournissez une clé de partition, vous pouvez alors bénéficier d’une mise à l’échelle illimitée sans apporter aucune modification à ce conteneur. Ainsi, même si vous créez un conteneur **fixe**, celui-ci agit comme un conteneur illimité si le conteneur d’origine est créé avec un débit minimal de 1 000 RU/s et si une clé de partition est spécifiée.

* Tous les conteneurs configurés pour partager le débit dans le cadre d’un ensemble de conteneurs sont traités comme des conteneurs **Illimités**.

Si vous avez créé un conteneur de **taille fixe** sans clé de partition ou avec un débit inférieur à 1 000 RU/s, le conteneur ne peut pas se mettre à l’échelle automatiquement. Pour migrer les données d’un conteneur fixe vers un conteneur illimité, vous devez utiliser l’[outil de migration de données](import-data.md) ou la [bibliothèque de flux de modification](change-feed.md). 

## <a name="designing-for-partitioning"></a> Créer une clé de partition 
Vous pouvez utiliser le portail Azure ou l’interface CLI Azure pour créer des conteneurs et les faire évoluer à tout moment. Cette section montre comment créer des conteneurs et spécifier le débit approvisionné et la clé de partition avec chacune des API.


### <a name="sql-api"></a>API SQL
L’exemple suivant montre comment créer un conteneur (une collection) à l’aide de l’API SQL. 

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Vous pouvez lire un élément (document) à l’aide de la méthode `GET` dans l’API REST ou à l’aide de `ReadDocumentAsync` dans un des kits de développement logiciel.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

Pour plus d’informations, voir [Partitionnement dans Azure Cosmos DB avec l’API SQL](sql-api-partition-data.md).

### <a name="mongodb-api"></a>API MongoDB
Avec l’API MongoDB, vous pouvez créer une collection partitionnée MongoDB via l’outil, le pilote ou le SDK de votre choix. Dans cet exemple, nous utilisons l’interpréteur de commandes Mongo (Mongo Shell) pour créer une collection.

Dans l’interpréteur de commandes Mongo :

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Résultats :

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>API de table

Pour créer une table à l’aide de l’API Table, utilisez la méthode `CreateIfNotExists`. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

Le débit approvisionné est défini en tant qu’argument de `CreateIfNotExists`. La clé de partition est implicitement créée en tant que valeur `PartitionKey`. 

Vous pouvez récupérer une entité unique à l’aide du code suivant :

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Pour plus d’informations, consultez [Développer avec l’API Table](tutorial-develop-table-dotnet.md).

### <a name="gremlin-api"></a>API Gremlin

Avec l’API Gremlin, vous pouvez utiliser le portail Azure ou Azure CLI pour créer un conteneur qui représente un graphe. Azure Cosmos DB étant multimodèle, vous pouvez aussi utiliser l’une des autres API pour créer et mettre à l’échelle votre conteneur de graphiques.

> [!NOTE]
> `/id` et `/label` ne sont pas pris en charge en tant que clés de partition d’un conteneur dans l’API Gremlin.

Vous pouvez lire un sommet ou une arête à l’aide de la clé de partition et l’ID dans Gremlin. Par exemple, pour un graphe ayant la région (« USA ») comme clé de partition et « Seattle » comme clé de ligne, vous pouvez trouver un vertex en utilisant la syntaxe suivante :

```
g.V(['USA', 'Seattle'])
```

Vous pouvez faire référence à une arête en utilisant la clé de partition et la clé de ligne.

```
g.E(['USA', 'I5'])
```

Pour plus d’informations, voir [Utilisation d’un graphique partitionné dans Azure Cosmos DB](graph-partitioning.md).

## <a name="form-partition-key-by-concatenating-multiple-fields"></a>Former une clé de partition en concaténant plusieurs champs

Vous pouvez également former une clé de partition en concaténant et en remplissant plusieurs valeurs de propriété dans une propriété unique artificielle « partitionKey » de l’élément. Ces clés sont appelées clés synthétiques.

Par exemple, vous avez un document qui ressemble à :

```json
{
"deviceId": "abc-123",
"date": 2018
}
```

Une option consiste à définir partitionKey sur /deviceId ou /date. Si vous souhaitez former une clé de partition sur l’ID de l’appareil et date. Concaténez ces deux valeurs à une propriété artificielle « partitionKey », et définissez la clé de partition sur /partitionKey.

```json
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Dans les scénarios en temps réel, vous pouvez avoir des milliers de documents. Vous devez donc définir une logique côté client pour concaténer des valeurs dans une clé synthétique, insérer la clé synthétique dans les documents et l’utiliser pour spécifier la clé de partition.

## <a name="designing-for-scale"></a> Mettre à l’échelle
Pour mettre à l’échelle efficacement avec Azure Cosmos DB, vous devez choisir une bonne clé de partition lorsque vous créez votre conteneur. Il y a principalement deux éléments à prendre en considération quand il s’agit de choisir une bonne clé de partition :

* **Limite des requêtes et transactions**. Votre choix de clé de partition doit concilier la nécessité d’utiliser des transactions et l’exigence de répartition des entités sur plusieurs clés de partitions pour garantir une solution évolutive. D’un côté, vous pouvez définir la même clé de partition pour tous vos éléments, mais cette solution peut limiter la scalabilité de votre solution. D’un autre côté, vous pouvez affecter une clé de partition unique à chaque élément. Si ce choix s’avère hautement scalable, il vous empêche en revanche d’utiliser des transactions entre documents via des procédures stockées et des déclencheurs. Une clé de partition idéale vous permet d’utiliser des requêtes efficaces et présente une cardinalité suffisante pour garantir la scalabilité de votre solution. 
* **Absence de goulots d’étranglement au niveau des performances et du stockage**. Il est important de choisir une propriété qui permet la distribution des écritures entre plusieurs valeurs distinctes. Les requêtes pour la même clé de partition ne peuvent pas dépasser le débit approvisionné alloué à une partition et leur vitesse sera limitée. Il est donc important de choisir une clé de partition qui ne se traduit pas par des « zones réactives » au sein de votre application. Comme toutes les données relatives à une clé de partition unique doivent être stockées dans une partition, vous devez éviter les clés de partition qui ont des volumes de données importants pour une même valeur. 

Penchons-nous sur quelques scénarios concrets et sur les clés de partition adaptées à chaque cas :
* Si vous implémentez un service principal de profil utilisateur, l’*ID utilisateur* est un bon choix pour une clé de partition.
* Si vous stockez des données IoT, comme l’état d’un appareil, l’*ID de l’appareil* est un bon choix pour une clé de partition.
* Si vous utilisez Azure Cosmos DB pour la journalisation de données de séries chronologiques, il est intéressant de choisir le *nom d’hôte* ou l’*ID de processus* pour une clé de partition.
* Si vous disposez d’une architecture multilocataire, l’*ID de locataire* est un bon choix pour une clé de partition.

Dans certains cas d’utilisation, comme l’IoT et les profils utilisateur, la clé de partition peut être identique à votre *ID* (clé de document). Dans d’autres cas, comme les données de séries chronologiques, la clé de partition peut être différente de l’*ID*.

### <a name="partitioning-and-loggingtime-series-data"></a>Partitionnement et journalisation de données de série chronologique
Azure Cosmos DB est très souvent utilisé à des fins de journalisation et de télémétrie. Il est important de choisir une bonne clé de partition dans ce scénario, car vous pouvez être amené à lire et/ou écrire d’énormes volumes de données. Le choix d’une clé de partition dépend de vos taux de lectures et d’écritures et des types de requêtes que vous prévoyez d’exécuter. Voici quelques conseils sur la façon de choisir une clé de partition appropriée :

* Si votre cas d’utilisation implique un faible taux d’écritures qui s’accumulent sur une longue période et que vous devez interroger par plages d’horodatages avec d’autres filtres, utilisez un cumul de l’horodatage. Par exemple, une bonne approche est d’utiliser la date comme clé de partition. Cette approche vous permet d’interroger toutes les données correspondant à une date donnée à partir d’une même partition. 
* Si votre charge de travail nécessite beaucoup d’écritures, ce qui est fréquent dans ce scénario, utilisez une clé de partition qui n’est pas basée sur l’horodatage. En tant que telle, Azure Cosmos DB peut distribuer et mettre à l’échelle des écritures uniformément entre différentes partitions. Dans ce cas, un *nom d’hôte*, un *ID de processus*, un *ID d’activité* ou une autre propriété présentant une cardinalité élevée sont un bon choix. 
* Une troisième solution consiste à adopter une approche hybride, où vous avez plusieurs conteneurs, un pour chaque jour/mois, et où la clé de partition est une propriété plus granulaire comme le *nom d’hôte*. L’avantage de cette approche est que vous pouvez définir un débit différent pour chaque conteneur ou un ensemble de conteneurs en fonction de la fenêtre de temps et des besoins de mise à l’échelle et de performances. Par exemple, un conteneur pour le mois en cours peut être approvisionné avec un débit plus élevé, car il prend en charge les lectures et les écritures. Les mois précédents peuvent être approvisionnés avec un débit inférieur, car ils ne prennent en charge que les lectures.

### <a name="partitioning-and-multitenancy"></a>Partitionnement et multilocation
Si vous implémentez une application multilocataire avec Azure Cosmos DB, deux conceptions populaires doivent être prises en compte : *une clé de partition par locataire* et *un conteneur par locataire*. Voici les avantages et inconvénients de chaque modèle :

* **Une clé de partition par locataire**. Dans ce modèle, les locataires sont colocalisés au sein d’un même conteneur. Les requêtes et les insertions pour un locataire unique peuvent être exécutées sur une partition unique. Vous pouvez également implémenter une logique transactionnelle sur tous les éléments appartenant à un locataire. Comme plusieurs locataires partagent un conteneur, vous pouvez mieux utiliser le stockage et le débit approvisionné en regroupant des ressources pour tous les locataires dans un même conteneur au lieu de procéder à un approvisionnement pour chaque locataire. L’inconvénient est que vous ne bénéficiez pas de l’isolation des performances par locataire. L’augmentation du débit en vue de garantir les performances profitera à l’ensemble du conteneur avec tous ses locataires, contrairement à ce qui est le cas lors d’augmentations ciblées pour un locataire individuel.
* **Un conteneur par locataire**. Dans ce modèle, chaque locataire a son propre conteneur, et vous pouvez réserver un débit avec des performances garanties pour chaque locataire. Ce modèle est plus rentable pour les applications multilocataires avec peu de clients.

Vous pouvez également utiliser une approche hybride, qui regroupe les petits locataires et isole les locataires plus volumineux dans leurs propres conteneurs.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, nous avons fourni une vue d’ensemble des concepts et des meilleures pratiques de mise à l’échelle et de partitionnement dans Azure Cosmos DB. 

* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md).
* Renseignez-vous sur la [distribution globale dans Azure Cosmos DB](distribute-data-globally.md).



