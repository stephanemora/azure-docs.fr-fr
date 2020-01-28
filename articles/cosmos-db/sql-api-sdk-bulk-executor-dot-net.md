---
title: 'Azure Cosmos DB : API .NET Bulk Executor, SDK et ressources'
description: Tout savoir sur l’API .NET et le SDK de l’exécuteur en bloc, notamment les dates de publication, les dates de déclassement et les modifications apportées entre chaque version du SDK .NET de l’exécuteur en bloc Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169399"
---
# <a name="net-bulk-executor-library-download-information"></a>Bibliothèque d’exécuteurs en bloc .NET : Télécharger des informations 

> [!div class="op_single_selector"]
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
| **Description**| La bibliothèque de l’exécuteur en bloc .Net permet aux applications clientes d’effectuer des opérations en bloc sur des comptes Azure Cosmos DB. Cette bibliothèque fournit les espaces de noms BulkImport, BulkUpdate et BulkDelete. Le module BulkImport peut ingérer en bloc des documents de manière optimisée afin que le débit provisionné pour une collection soit consommé dans ses limites maximales. Le module BulkUpdate peut mettre à jour en bloc des données existantes dans des conteneurs Azure Cosmos sous forme de correctifs. Le module BulkDelete peut supprimer en bloc des documents de manière optimisée afin que le débit provisionné pour une collection soit consommé dans ses limites maximales.|
|**Téléchargement du Kit de développement logiciel (SDK)**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Bibliothèque d’exécuteurs en bloc dans GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Documentation de l’API**|[Documentation de référence sur l’API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Prise en main**|[Bien démarrer avec le kit SDK .NET de la bibliothèque de l’exécuteur en bloc](bulk-executor-dot-net.md)|
| **Infrastructure actuellement prise en charge**| Microsoft .NET Framework 4.5.2, 4.6.1 et .NET Standard 2.0 |

> [!NOTE]
> Si vous utilisez l’Exécuteur en bloc, consultez la version 3.x la plus récente du [Kit de développement logiciel (SDK) .NET](tutorial-sql-api-dotnet-bulk-import.md), qui a intégré l’Exécuteur en bloc dans le Kit de développement logiciel (SDK). 

## <a name="release-notes"></a>Notes de publication

### <a name="a-name241-preview241-preview"></a><a name="2.4.1-preview"/>2.4.1 (préversion)

* Correction de TotalElapsedTime dans la réponse de BulkDelete pour mesurer correctement la durée totale, incluant les nouvelles tentatives.

### <a name="a-name240-preview240-preview"></a><a name="2.4.0-preview"/>2.4.0-preview

* Dépendance du kit SDK remplacée par >= 2.5.1

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Ajout de la prise en charge de l’exécuteur graphique en masse pour accepter ttl sur les sommets et les bords

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* Correction d’un problème qui causait des exceptions lors de la mise à l’échelle élastique d’Azure Cosmos DB lors de l’exécution en mode Passerelle. Ce correctif le rend fonctionnellement équivalent à la version 1.4.1.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* Ajout de la prise en charge de BulkDelete pour les comptes de l’API SQL afin d’accepter les clés de partition et les tuples d’ID de document à supprimer. Cette modification le rend fonctionnellement équivalent à la version 1.4.0.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Ajout de MongoBulkExecutor pour prendre en charge .NET Standard 2.0. Cette fonctionnalité le rend fonctionnellement équivalent à la version 1.3.0, avec l’ajout de la prise en charge de .NET Standard 2.0 comme framework cible.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* Ajout de .NET Standard 2.0 comme l’un des frameworks cibles pris en charge pour que la bibliothèque d’exécuteurs en bloc fonctionne avec les applications .NET Core.

### <a name="a-name188188"></a><a name="1.8.8"/>1.8.8

* Correction d’un problème sur MongoBulkExecutor qui augmentait de façon inattendue la taille du document en ajoutant un remplissage et, dans certains cas, en dépassant la limite de taille de document maximale.

### <a name="a-name187187"></a><a name="1.8.7"/>1.8.7

* Correction d’un problème avec BulkDeleteAsync lorsque la collection a des chemins de clés de partition imbriqués.

### <a name="a-name186186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor implémente désormais IDisposable et doit être supprimé après avoir été utilisé.

### <a name="a-name185185"></a><a name="1.8.5"/>1.8.5

* Suppression du verrou sur la version du SDK. Le package est désormais dépendant du kit de développement logiciel de version 2.5.1 ou ultérieure.

### <a name="a-name184184"></a><a name="1.8.4"/>1.8.4

* Correction du traitement des identificateurs lors de l’appel de BulkImport avec une liste d’objets POCO contenant des valeurs numériques.

### <a name="a-name183183"></a><a name="1.8.3"/>1.8.3

* Correction de TotalElapsedTime dans la réponse de BulkDelete pour mesurer correctement la durée totale, incluant les nouvelles tentatives.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Correction d'une consommation élevée du processeur dans certains scénarios.
* Le suivi utilise désormais TraceSource. Les utilisateurs peuvent définir des écouteurs pour la source `BulkExecutorTrace`.
* Correction d’un scénario rare susceptible d'entraîner un verrouillage lors de l’envoi de documents d'environ 2 Mo.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Mise à jour d’exécuteur en bloc pour utiliser la dernière version du Kit de développement logiciel (SDK) Azure Cosmos DB.NET (2.4.0)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* Ajout de la prise en charge de l’exécuteur graphique en masse pour accepter ttl sur les sommets et les bords

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* Correction d’un problème qui causait des exceptions lors de la mise à l’échelle élastique d’Azure Cosmos DB lors de l’exécution en mode Passerelle.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* Ajout de la prise en charge de BulkDelete pour les comptes de l’API SQL afin d’accepter les clés de partition et les tuples d’ID de document à supprimer.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Résolution d’un problème qui provoquait un problème de mise en forme dans l’agent utilisateur utilisé par l’exécuteur en bloc.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Améliorations des API d’importation et de mise à jour de l’exécuteur en bloc afin de s’adapter en toute transparence à la mise à l’échelle élastique de conteneur Cosmos quand le stockage dépasse la capacité actuelle sans lever d’exception.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Amélioration de la dépendance du SDK .NET DocumentDB à la version 2.1.3.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Résolution d’un problème qui faisait en sorte que l’exécuteur en bloc lève une erreur JSRT lors de l’importation vers des collections fixes.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Ajout de la prise en charge de l’opération BulkDelete pour les comptes d’API SQL Azure Cosmos DB.
* Ajout de la prise en charge de l’opération BulkImport pour les comptes disposant de l’API Azure Cosmos DB pour MongoDB.
* Amélioration de la dépendance du Kit de développement logiciel (SDK) .NET DocumentDB à la version 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Ajout de la prise en charge de l’opération BulkImport pour les comptes d’API Gremlin Azure Cosmos DB.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Correction d’un bogue mineur dans l’opération BulkImport pour les comptes d’API SQL Azure Cosmos DB.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Ajout de la prise en charge des opérations BulkImport et BulkUpdate pour les comptes d’API SQL Azure Cosmos DB.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la bibliothèque de l’exécuteur en bloc Java, consultez l’article suivant :

[Informations sur la version et le SDK de bibliothèque de l’exécuteur en bloc Java](sql-api-sdk-bulk-executor-java.md)
