---
title: 'Azure Cosmos DB : Ressources, kit SDK et API .NET Standard SQL'
description: Tout savoir sur l’API SQL et le Kit de développement logiciel (SDK) .NET, y compris les dates de sortie, les dates de déclassement et les modifications effectuées entre chaque version du Kit de développement logiciel (SDK) .NET Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 0368e99135dd6e377dd2820b3e673c55182319b9
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663807"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>Kit de développement logiciel .NET Standard Azure Cosmos DB pour l’API SQL : Téléchargement et notes de publication
> [!div class="op_single_selector"]
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Flux de modification .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java asynchrone](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Exécuteur en bloc – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Exécuteur en bloc – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**Documentation de l’API**|[Documentation de référence sur l’API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Exemples**|[Exemples de code .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**Prise en main**|[Prise en main du Kit de développement logiciel (SDK) .NET Azure Cosmos DB](sql-api-get-started.md)|
|**Didacticiel d’application web**|[Développement d’applications web avec Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Infrastructure actuellement prise en charge**|[Microsoft .NET Standard 2.0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>Notes de publication
### <a name="a-name310310"></a><a name="3.1.0"/>3.1.0
#### <a name="added"></a>Ajouté
- [#541](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/541) Ajout d’un niveau de cohérence aux options de client et de requête
- [#544](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/544) Ajout d'une prise en charge de jeton de liaison pour LINQ
- [#557](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/557) Ajout d'options de déclencheur aux options de requête d’élément
- [#571](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/571) Ajout d'un sérialiseur JSON.net par défaut avec paramètres facultatifs
- [#572](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/572) Ajout de la validation de la clé de partition sur CreateContainerIfNotExistsAsync
- [#581](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/581) Ajout de LINQ à l'API QueryDefinition
- [#592](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/592) Ajout de CreateIfNotExistsAsync au générateur de conteneurs
- [#597](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/597) Ajout de la propriété de jeton de liaison à ResponseMessage
- [#604](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/604) Ajout de la méthode d'extension ToStreamIterator LINQ

#### <a name="fixed"></a>Résolution
- [#548](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/548) Correction d'un message incorrectement entré dans CosmosException.ToString();
- [#558](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/558) Correction de la contention de verrouillage LocationCache ConcurrentDict
- [#561](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/561) GetItemLinqQueryable fonctionne désormais avec une requête de valeur Null
- [#567](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/567) La requête gère correctement différentes cultures de langue
- [#574](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/574) Correction d'un message d'erreur vide si l’analyse de la requête échoue suite à une exception inattendue
- [#576](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/576) La requête sérialise correctement l’entrée dans un flux

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* Disponibilité générale de la [version 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) du kit de développement logiciel (SDK) .NET
* Cible .NET Standard 2.0, prenant en charge .NET framework 2.0+ et .NET Core 4.6.1+
* Nouveau modèle d’objet, avec CosmosClient de niveau supérieur et les méthodes réparties entre les classes Database et Container appropriées
* Nouvelles API de flux hautement performantes
* Prise en charge intégrée des API du processeur de flux de modification
* API du générateur Fluent pour CosmosClient, Container et le processeur de flux de modification
* API de gestion du débit idiomatique
* RequestOptions et ResponseTypes détaillées pour les requêtes de base de données, de conteneur, d’élément, de requête et de débit
* Possibilité de mettre à l’échelle des conteneurs non partitionnés 
* Sérialiseur extensible et personnalisable
* Pipeline de requêtes extensible avec prise en charge des gestionnaires personnalisés


## <a name="release--retirement-dates"></a>Dates de lancement et de suppression
Microsoft envoie une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible. 

Toute requête envoyée à Azure Cosmos DB à l’aide d’un Kit de développement logiciel (SDK) supprimé est rejetée par le service.

<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [3.1.0](#3.1.0) |29 juillet 2019 |--- |
| [3.0.0](#3.0.0) |15 juillet 2019 |--- |


## <a name="faq"></a>Forum Aux Questions
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

