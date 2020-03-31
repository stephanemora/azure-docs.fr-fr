---
title: Commandes d’extension MongoDB pour gérer les données dans les API d’Azure Cosmos DB pour MongoDB
description: Cet article décrit comment utiliser les commandes d’extension MongoDB pour gérer les données stockées dans les API d’Azure Cosmos DB pour MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f57b274715eb1c8a4d517f5655c09c366574d412
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445220"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Utiliser les commandes d’extension MongoDB pour gérer les données stockées dans les API d’Azure Cosmos DB pour MongoDB 

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous pouvez communiquer avec l’API Azure Cosmos DB pour MongoDB par le biais de n’importe quel [pilote du client open source MongoDB](https://docs.mongodb.org/ecosystem/drivers). L’API d’Azure Cosmos DB pour MongoDB permet d’utiliser les pilotes clients existants en adhérant au [protocole Wire MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

En utilisant l’API d’Azure Cosmos DB pour MongoDB, vous pouvez profiter des nombreux avantages de Cosmos DB, comme la distribution mondiale, le partitionnement automatique, la haute disponibilité, les garanties de latence, le chiffrement automatique au repos, les sauvegardes, etc. tout en préservant vos investissements dans votre application MongoDB.

## <a name="mongodb-protocol-support"></a>Prise en charge des protocoles MongoDB

Par défaut, l’API d’Azure Cosmos DB pour MongoDB est compatible avec la version 3.2 du serveur MongoDB. Pour plus d’informations, consultez les [fonctionnalités et la syntaxe prises en charge](mongodb-feature-support.md). Les opérateurs de requête ou les fonctionnalités ajoutés dans MongoDB version 3.4 sont actuellement disponibles en préversion dans l’API d’Azure Cosmos DB pour MongoDB. Les commandes d’extension suivantes prennent en charge les fonctionnalités spécifiques d’Azure Cosmos DB lors de l’exécution d’opérations CRUD sur les données stockées dans l’API d’Azure Cosmos DB pour MongoDB :

* [Créer une base de données](#create-database)
* [Mettre à jour la base de données](#update-database)
* [Obtenir une base de données](#get-database)
* [Créer une collection](#create-collection)
* [Mettre à jour la collection](#update-collection)
* [Obtenir une collection](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> Créer une base de données

La commande d’extension de création de base de données crée une base de données MongoDB. Le nom de la base de données est tiré du contexte de bases de données par rapport auquel la commande est exécutée. Le format de la commande CreateDatabase est le suivant :

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

Le tableau suivant décrit les paramètres inclus dans la commande :

|**Champ**|**Type** |**Description** |
|---------|---------|---------|
| customAction   |  string  |   Nom de la commande personnalisée, qui doit être "CreateDatabase".      |
| offerThroughput | int  | Débit approvisionné que vous définissez sur la base de données. Ce paramètre est facultatif. |

### <a name="output"></a>Output

Retourne une réponse de commande personnalisée par défaut. Consultez la [sortie par défaut](#default-output) de la commande personnalisée pour connaître les paramètres de la sortie.

### <a name="examples"></a>Exemples

**Créer une base de données**

Pour créer une base de données nommée « test », utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Créer une base de données avec débit**

Pour créer une base de données nommée « test » avec un débit approvisionné de 1 000 unités de requête, utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a> Mettre à jour la base de données

La commande d’extension de mise à jour de base de données met à jour les propriétés associées à la base de données spécifiée. Actuellement, vous pouvez mettre à jour uniquement la propriété « offerThroughput ».

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

Le tableau suivant décrit les paramètres inclus dans la commande :

|**Champ**|**Type** |**Description** |
|---------|---------|---------|
| customAction    |    string     |   Nom de la commande personnalisée. Il doit être "UpdateDatabase".      |
|  offerThroughput   |  int       |     Nouveau débit approvisionné que vous souhaitez définir sur la base de données.    |

### <a name="output"></a>Output

Retourne une réponse de commande personnalisée par défaut. Consultez la [sortie par défaut](#default-output) de la commande personnalisée pour connaître les paramètres de la sortie.

### <a name="examples"></a>Exemples

**Mettre à jour le débit approvisionné associé à une base de données**

Pour mettre à jour le débit approvisionné d’une base de données nommée « test » avec 1 200 unités de requête, utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a> Obtenir une base de données

La commande d’extension d’obtention de base de données retourne l’objet de base de données. Le nom de la base de données est tiré du contexte de base de données par rapport auquel la commande est exécutée.

```
{
  customAction: "GetDatabase"
}
```

Le tableau suivant décrit les paramètres inclus dans la commande :


|**Champ**|**Type** |**Description** |
|---------|---------|---------|
|  customAction   |   string      |   Nom de la commande personnalisée. Il doit être "GetDatabase".|
        
### <a name="output"></a>Output

Si la commande réussit, la réponse contient un document avec les champs suivants :

|**Champ**|**Type** |**Description** |
|---------|---------|---------|
|  `ok`   |   `int`     |   État de la réponse. 1 == réussite. 0 == échec.      |
| `database`    |    `string`        |   Nom de la base de données.      |
|   `provisionedThroughput`  |    `int`      |    Débit approvisionné défini sur la base de données. Ce paramètre de réponse est facultatif.     |

Si la commande échoue, une réponse de commande personnalisée par défaut est retournée. Consultez la [sortie par défaut](#default-output) de la commande personnalisée pour connaître les paramètres de la sortie.

### <a name="examples"></a>Exemples

**Obtenir la base de données**

Pour obtenir l’objet de base de données pour une base de données nommée « test », utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a> Créer une collection

La commande d’extension de création de collection crée une collection MongoDB. Le nom de la base de données est tiré du contexte de bases de données par rapport auquel la commande est exécutée. Le format de la commande CreateCollection est le suivant :

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

Le tableau suivant décrit les paramètres inclus dans la commande :

|**Champ**|**Type** |**Description** |
|---------|---------|---------|
| customAction    | string | Nom de la commande personnalisée. Il doit être "CreateCollection".     |
| collection      | string | Nom de la collection                                   |
| offerThroughput | int    | Débit approvisionné à définir sur la base de données. Ce paramètre est facultatif. |
| shardKey        | string | Chemin d’accès des clés de partition pour créer une collection partitionnée. Ce paramètre est facultatif. |

### <a name="output"></a>Output

Retourne une réponse de commande personnalisée par défaut. Consultez la [sortie par défaut](#default-output) de la commande personnalisée pour connaître les paramètres de la sortie.

### <a name="examples"></a>Exemples

**Créer une collection non partitionnée**

Pour créer une collection non partitionnée nommée « testCollection » avec un débit approvisionné de 1 000 unités de requête, utilisez la commande suivante : 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Créer une collection partitionnée**

Pour créer une collection partitionnée nommée « testCollection » avec un débit approvisionné de 1 000 unités de requête, utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a> Mettre à jour la collection

La commande d’extension de mise à jour de collection met à jour les propriétés associées à la collection spécifiée.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

Le tableau suivant décrit les paramètres inclus dans la commande :

|**Champ**|**Type** |**Description** |
|---------|---------|---------|
|  customAction   |   string      |   Nom de la commande personnalisée. Il doit être "UpdateCollection".      |
|  collection   |   string      |   Nom de la collection.       |
| offerThroughput   |int|   Débit approvisionné à définir sur la collection.|

## <a name="output"></a>Output

Retourne une réponse de commande personnalisée par défaut. Consultez la [sortie par défaut](#default-output) de la commande personnalisée pour connaître les paramètres de la sortie.

### <a name="examples"></a>Exemples

**Mettre à jour le débit approvisionné associé à une collection**

Pour mettre à jour le débit approvisionné d’une collection nommée « testCollection » avec 1 200 unités de requête, utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> Obtenir une collection

La commande personnalisée d’obtention de collection retourne l’objet de collection.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

Le tableau suivant décrit les paramètres inclus dans la commande :


|**Champ**|**Type** |**Description** |
|---------|---------|---------|
| customAction    |   string      |   Nom de la commande personnalisée. Il doit être "GetCollection".      |
| collection    |    string     |    Nom de la collection.     |

### <a name="output"></a>Output

Si la commande réussit, la réponse contient un document avec les champs suivants.


|**Champ**|**Type** |**Description** |
|---------|---------|---------|
|  `ok`   |    `int`     |   État de la réponse. 1 == réussite. 0 == échec.      |
| `database`    |    `string`     |   Nom de la base de données.      |
| `collection`    |    `string`     |    Nom de la collection.     |
|  `shardKeyDefinition`   |   `document`      |  Document de spécification d’index utilisé comme clé de partition. Ce paramètre de réponse est facultatif.       |
|  `provisionedThroughput`   |   `int`      |    Débit approvisionné à définir sur la collection. Ce paramètre de réponse est facultatif.     |

Si la commande échoue, une réponse de commande personnalisée par défaut est retournée. Consultez la [sortie par défaut](#default-output) de la commande personnalisée pour connaître les paramètres de la sortie.

### <a name="examples"></a>Exemples

**Obtenir la collection**

Pour obtenir l’objet de collection pour une collection nommée « testCollection », utilisez la commande suivante :

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> Sortie par défaut d’une commande personnalisée

Si elle n’est pas spécifiée, la réponse personnalisée contient un document avec les champs suivants :

|**Champ**|**Type** |**Description** |
|---------|---------|---------|
|  `ok`   |    `int`     |   État de la réponse. 1 == réussite. 0 == échec.      |
| `code`    |   `int`      |   Retourné uniquement lorsque la commande a échoué (ok == 0). Contient le code d’erreur MongoDB. Ce paramètre de réponse est facultatif.      |
|  `errMsg`   |  `string`      |    Retourné uniquement lorsque la commande a échoué (ok == 0). Contient un message d’erreur convivial. Ce paramètre de réponse est facultatif.      |

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ensuite commencer à apprendre les concepts relatifs à Azure Cosmos DB suivants : 

* [Indexation dans Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Faire expirer automatiquement des données avec la durée de vie dans Azure Cosmos DB](../cosmos-db/time-to-live.md)
