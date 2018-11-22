---
title: Exemples Node.js pour Azure Cosmos DB | Microsoft Docs
description: Recherchez des exemples Node.js sur GitHub pour les tâches courantes dans Azure Cosmos DB, y compris les opérations CRUD.
services: cosmos-db
author: deborahc
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: sample
ms.date: 09/24/2018
ms.author: deborahc
ms.openlocfilehash: 8dd2d244480878f6c01628a50e373702ef4bc527
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976687"
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Exemples Node.js pour Azure Cosmos DB
> [!div class="op_single_selector"]
> * [Exemples .NET](sql-api-dotnet-samples.md)
> * [Exemples Java](sql-api-java-samples.md)
> * [Exemples Java Async](sql-api-async-java-samples.md)
> * [Exemples Node.js](sql-api-nodejs-samples.md)
> * [Exemples Python](sql-api-python-samples.md)
> * [Galerie d’exemples de code Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Le référentiel GitHub [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) contient des exemples de solutions qui exécutent des opérations CRUD ainsi que d’autres opérations courantes sur les ressources Azure Cosmos DB. Cet article fournit :

* Des liens vers les tâches dans chacun des exemples de fichiers de projet Node.js.
* Des liens vers le contenu de référence d’API connexe.

**Composants requis**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Vous pouvez [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): ce dernier vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Vous avez également besoin du [Kit de développement logiciel (SDK) JavaScript](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Chaque exemple est autonome, se définit lui-même et se nettoie automatiquement. Les exemples transmettent donc plusieurs appels à [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#create). À chaque appel, votre abonnement sera facturé pour 1 heure d’utilisation selon le niveau de performances du conteneur en cours de création.
   > 
   > 

## <a name="database-examples"></a>Exemples de base de données
Le fichier [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement/app.js) du projet [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/DatabaseManagement) explique comment effectuer les tâches suivantes.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créez une base de données si elle n’existe pas](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L35-L37) |[Databases.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest#createifnotexists) |
| [Répertorier les bases de données pour un compte](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Databases.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest#readall) |
| [Lire une base de données par identifiant](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Database.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest#read) |
| [Supprimer une base de données](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L57-L60) |[Database.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest#delete) |

## <a name="container-examples"></a>Exemples de conteneur
Le fichier [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement/app.js) du projet [Container.Management](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) explique comment effectuer les tâches suivantes.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créez un conteneur s’il n’existe pas](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#createifnotexists) |
| [Liste les conteneurs d’un compte](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#readall) |
| [Lire une collection par identifiant](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L47-L51) |[Container.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest#read) |
| [Supprimer un conteneur](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L54-L55) |[Container.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest#delete) |

## <a name="item-examples"></a>Exemples d’éléments
Le fichier [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement/app.js) du projet [ItemManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) explique comment effectuer les tâches suivantes.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer des éléments](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L49-L56) |[Items.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#create) |
| [Lire tous les éléments figurant dans un conteneur](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Items.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#readall) |
| [Lire un élément par identifiant](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#read) |
| [Lire un élément uniquement si l’élément a été modifié](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L73-L94) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#read)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Rechercher des documents](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L97-L118) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#query) |
| [Remplacer un élément](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L131-L136) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#replace) |
| [Remplacer l’élément par la vérification conditionnelle ETag](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L139-L160) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#replace)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Supprimer un élément](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L162-L164) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#delete) |

## <a name="indexing-examples"></a>Exemples d'indexation
Le fichier [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement/app.js) du projet [IndexManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/IndexManagement) explique comment effectuer les tâches suivantes.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Indexer manuellement un document spécifique](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L135-L177) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Exclure manuellement un document spécifique de l’index](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L90-L131) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Utiliser l’indexation en différé pour importer en bloc ou lire des conteneurs lourds](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L183-L214) |[IndexingMode.Lazy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingmode?view=azure-node-latest) |
| [Exclure un chemin de l’index](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L352-L429) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [Autoriser une analyse sur un chemin d’accès de la chaîne pendant une opération de plage](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L219-L275) |[FeedOptions.EnableScanInQuery](https://docs.microsoft.com/javascript/api/%40azure/cosmos/feedoptions?view=azure-node-latest#enablescaninquery) |
| [Créer un index de plage sur un chemin d’accès de la chaîne](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L281-L346) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#query) |
| [Créer un conteneur avec la valeur par défaut indexPolicy, puis la mettre à jour en ligne](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L435-L507) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#create)


Pour plus d’informations sur l’indexation, consultez [Stratégies d’indexation d’Azure Cosmos DB](index-policy.md).

## <a name="server-side-programming-examples"></a>Exemples de programmation côté serveur
Le fichier [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/app.js) du projet [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) explique comment effectuer les tâches suivantes.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer une procédure stockée](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/JS/upsert.js#L12-L72) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [Exécuter une procédure stockée](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/app.js#L44) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest#execute) |

Pour plus d’informations sur la programmation côté serveur, consultez [Programmation Azure Cosmos DB côté serveur : procédures stockées, déclencheurs de base de données et fonctions définies par l’utilisateur](programming.md).

