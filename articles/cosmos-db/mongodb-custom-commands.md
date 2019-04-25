---
title: Commandes d’extension MongoDB pour gérer les données stockées dans les API d’Azure Cosmos DB pour MongoDB
description: Cet article décrit comment utiliser les commandes d’extension MongoDB pour gérer les données stockées dans les API d’Azure Cosmos DB pour MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: aef77f121f20d867c8ec5e764d8c9639c961713d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60446582"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Utiliser des commandes d’extension MongoDB pour gérer les données stockées dans les API d’Azure Cosmos DB pour MongoDB 

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous pouvez communiquer avec API d’Azure Cosmos DB pour MongoDB à l’aide de l’open source [pilotes du client MongoDB](https://docs.mongodb.org/ecosystem/drivers). L’API de Azure Cosmos DB pour MongoDB permet d’utiliser les pilotes clients existants en adhérant à la [protocole filaire MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

En utilisant les API de l’Azure Cosmos DB pour MongoDB, vous pouvez profiter des avantages Cosmos DB, telles que la distribution mondiale, un partitionnement automatique, haute disponibilité, des garanties de latence, automatique, le chiffrement au repos, sauvegardes, et nombreuses tandis que de plus, en conservant vos investissements dans votre application MongoDB.

## <a name="mongodb-protocol-support"></a>Prise en charge du protocole MongoDB

Par défaut, des API de l’Azure Cosmos DB pour MongoDB est compatible avec MongoDB server version 3.2, pour plus d’informations, consultez [pris en charge de la syntaxe et les fonctionnalités](mongodb-feature-support.md). Les fonctionnalités ou les opérateurs de requête ajoutés dans MongoDB version 3.4 sont actuellement disponibles en version préliminaire dans l’API de Azure Cosmos DB pour MongoDB. Les commandes d’extension suivantes prennent en charge les fonctionnalités spécifiques d’Azure Cosmos DB lors de l’exécution des opérations CRUD sur les données stockées dans les API d’Azure Cosmos DB pour MongoDB :

* [Créer la base de données](#create-database)
* [Mettre à jour de la base de données](#update-database)
* [Obtenir la base de données](#get-database)
* [Créer la collection](#create-collection)
* [Mettre à jour de la collection](#update-collection)
* [Obtention de la collection](#get-collection)

## <a id="create-database"></a> Créer la base de données

La commande d’extension de base de données create crée une nouvelle base de données MongoDB. Le nom de la base de données est utilisé à partir du contexte de bases de données par rapport à laquelle la commande est exécutée. Le format de la commande CreateDatabase est comme suit :

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

Le tableau suivant décrit les paramètres au sein de la commande :

|**Champ**|**Type** |**Description** |
|---------|---------|---------|
| customAction   |  string  |   Nom de la commande personnalisée, il doit être « CreateDatabase ».      |
| offerThroughput | int  | Débit approvisionné que vous définissez sur la base de données. Ce paramètre est facultatif. |

### <a name="output"></a>Sortie

Retourne une réponse de commande personnalisée par défaut. Consultez le [sortie par défaut](#default-output) de commande personnalisée pour les paramètres dans la sortie.

### <a name="examples"></a>Exemples

**Créer une base de données**

Pour créer une base de données nommée « test », utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Créer une base de données avec un débit**

Pour créer une base de données nommée « test » et le débit approvisionné de 1000 unités de requête, utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> Mettre à jour de la base de données

La commande d’extension de base de données mise à jour met à jour les propriétés associées à la base de données spécifié. Actuellement, vous pouvez mettre à jour uniquement la propriété « offerThroughput ».

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

Le tableau suivant décrit les paramètres au sein de la commande :

|**Champ**|**Type** |**Description** |
|---------|---------|---------|
| customAction    |    string     |   Nom de la commande personnalisée. Doit être « UpdateDatabase ».      |
|  offerThroughput   |  int       |     Débit approvisionné nouveau que vous souhaitez définir la base de données.    |

### <a name="output"></a>Sortie

Retourne une réponse de commande personnalisée par défaut. Consultez le [sortie par défaut](#default-output) de commande personnalisée pour les paramètres dans la sortie.

### <a name="examples"></a>Exemples

**Mettre à jour le débit approvisionné associé à une base de données**

Pour mettre à jour le débit approvisionné une base de données avec le nom « test » à 1200 unités de requête, utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> Obtenir la base de données

La commande d’extension de base de données get retourne l’objet de base de données. Le nom de la base de données est utilisé à partir du contexte de base de données par rapport à laquelle la commande est exécutée.

```
{
  customAction: "GetDatabase"
}
```

Le tableau suivant décrit les paramètres au sein de la commande :


|**Champ**|**Type** |**Description** |
|---------|---------|---------|
|  customAction   |   string      |   Nom de la commande personnalisée. Doit être « GetDatabase »|
        
### <a name="output"></a>Sortie

Si la commande aboutit, la réponse contient un document avec les champs suivants :

|**Champ**|**Type** |**Description** |
|---------|---------|---------|
|  `ok`   |   `int`     |   État de réponse. 1 == réussite. 0 == Échec.      |
| `database`    |    `string`        |   Nom de la base de données.      |
|   `provisionedThroughput`  |    `int`      |    Débit approvisionné qui est défini sur la base de données. Il s’agit d’un paramètre de réponse facultative.     |

Si la commande échoue, une réponse de commande personnalisée par défaut est retournée. Consultez le [sortie par défaut](#default-output) de commande personnalisée pour les paramètres dans la sortie.

### <a name="examples"></a>Exemples

**Obtenir la base de données**

Pour obtenir l’objet de base de données pour une base de données nommée « test », utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> Créer la collection

La commande d’extension de collection create crée une nouvelle collection MongoDB. Le nom de la base de données est utilisé à partir du contexte de bases de données par rapport à laquelle la commande est exécutée. Le format de la commande CreateCollection est comme suit :

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

Le tableau suivant décrit les paramètres au sein de la commande :

|**Champ**|**Type** |**Description** |
|---------|---------|---------|
| customAction    | string | Nom de la commande personnalisée. Doit être « CreateDatabase »     |
| collection      | string | Nom de la collection                                   |
| offerThroughput | int    | Débit approvisionné pour définir la base de données. Il est un paramètre facultatif |
| shardKey        | string | Chemin d’accès des clé de partition pour créer une collection partitionnée. Il est un paramètre facultatif |

### <a name="output"></a>Sortie

Retourne une réponse de commande personnalisée par défaut. Consultez le [sortie par défaut](#default-output) de commande personnalisée pour les paramètres dans la sortie.

### <a name="examples"></a>Exemples

**Créer une collection unsharded**

Pour créer une collection d’unsharded avec le nom « testCollection » et le débit approvisionné de 1000 unités de requête, utilisez la commande suivante : 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Créer une collection partitionnée**

Pour créer une collection partitionnée avec le nom « testCollection » et le débit approvisionné de 1000 unités de requête, utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> Mettre à jour de la collection

La commande d’extension de collection update met à jour les propriétés associées à la collection spécifiée.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

Le tableau suivant décrit les paramètres au sein de la commande :

|**Champ**|**Type** |**Description** |
|---------|---------|---------|
|  customAction   |   string      |   Nom de la commande personnalisée. Doit être « UpdateCollection ».      |
|  collection   |   string      |   Nom de la collection.       |
| offerThroughput   |int|   Débit approvisionné pour définir sur la collection.|

## <a name="output"></a>Sortie

Retourne une réponse de commande personnalisée par défaut. Consultez le [sortie par défaut](#default-output) de commande personnalisée pour les paramètres dans la sortie.

### <a name="examples"></a>Exemples

**Mettre à jour le débit approvisionné associé à une collection**

Pour mettre à jour le débit approvisionné d’une collection avec le nom « testCollection » pour les unités de requête 1200, utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> Obtention de la collection

La commande personnalisée de collection get retourne l’objet de collection.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

Le tableau suivant décrit les paramètres au sein de la commande :


|**Champ**|**Type** |**Description** |
|---------|---------|---------|
| customAction    |   string      |   Nom de la commande personnalisée. Doit être « GetCollection ».      |
| collection    |    string     |    Nom de la collection.     |

### <a name="output"></a>Sortie

Si la commande aboutit, la réponse contient un document avec les champs suivants


|**Champ**|**Type** |**Description** |
|---------|---------|---------|
|  `ok`   |    `int`     |   État de réponse. 1 == réussite. 0 == Échec.      |
| `database`    |    `string`     |   Nom de la base de données.      |
| `collection`    |    `string`     |    Nom de la collection.     |
|  `shardKeyDefinition`   |   `document`      |  Document de spécification d’index utilisé comme une clé de partition. Il s’agit d’un paramètre de réponse facultative.       |
|  `provisionedThroughput`   |   `int`      |    Débit approvisionné pour définir sur la collection. Il s’agit d’un paramètre de réponse facultative.     |

Si la commande échoue, une réponse de commande personnalisée par défaut est retournée. Consultez le [sortie par défaut](#default-output) de commande personnalisée pour les paramètres dans la sortie.

### <a name="examples"></a>Exemples

**Obtient la collection**

Pour obtenir l’objet de collection pour une collection nommée « testCollection », utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> Sortie par défaut d’une commande personnalisée

Si non spécifié, une réponse personnalisée contient un document avec les champs suivants :

|**Champ**|**Type** |**Description** |
|---------|---------|---------|
|  `ok`   |    `int`     |   État de réponse. 1 == réussite. 0 == Échec.      |
| `code`    |   `int`      |   Uniquement renvoyé lorsque la commande a échoué (autrement dit, OK == 0). Contient le code d’erreur MongoDB. Il s’agit d’un paramètre de réponse facultative.      |
|  `errMsg`   |  `string`      |    Uniquement renvoyé lorsque la commande a échoué (autrement dit, OK == 0). Contient un message d’erreur convivial. Il s’agit d’un paramètre de réponse facultative.      |

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ensuite passer pour découvrir les concepts Azure Cosmos DB suivants : 

* [Indexation dans Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Faire expirer automatiquement des données avec la durée de vie dans Azure Cosmos DB](../cosmos-db/time-to-live.md)