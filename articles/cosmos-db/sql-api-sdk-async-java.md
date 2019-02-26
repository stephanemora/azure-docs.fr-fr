---
title: 'Azure Cosmos DB : API, SDK et ressource SQL Async Java'
description: Découvrez l’API et le Kit de développement logiciel (SDK) Java Async SQL, notamment les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Java Async SQL d’Azure Cosmos DB.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 2/8/2019
ms.author: moderakh
ms.openlocfilehash: 1a86ceefdc5c3b60dc4bca5a1ac659534653aa66
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416273"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Kit de développement logiciel (SDK) Java Async Azure Cosmos DB pour API SQL : Notes de publication et ressources
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
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

Le Kit de développement logiciel (SDK) Java Async de l’API SQL est différent du Kit de développement logiciel (SDK) Java de l’API SQL en fournissant des opérations asynchrones avec prise en charge de la [bibliothèque Netty](https://netty.io/). Le [Kit de développement logiciel (SDK) Java de l’API SQL](sql-api-sdk-java.md) existant ne prend pas en charge les opérations asynchrones. 

| |  |
|---|---|
| **Téléchargement du Kit de développement logiciel (SDK)** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Documentation de l’API** |[Documentation de référence sur l’API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Contribution au Kit de développement logiciel (SDK)** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Prise en main** | [Prise en main du Kit de développement logiciel (SDK) Java Async](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Code sample** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Conseils sur les performances**| [Fichier Readme de GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Runtime minimal pris en charge**|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>Notes de publication

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* La connectivité de mode direct est désormais généralement disponible. Pour un exemple qui utilise une connectivité de mode direct, consultez le référentiel GitHub [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java).
* Ajout de la prise en charge de QueryMetrics.
* Modification des API acceptant java.util.Collection pour lesquelles l’ordre est important et qui acceptent java.util.List à la place. List est désormais accepté par ConnectionPolicy#getPreferredLocations(), JsonSerialization et PartitionKey(.).

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* Ajout de la prise en charge pour la connectivité de mode direct.
* Modification des API acceptant java.util.Collection pour lesquelles l’ordre est important et qui acceptent java.util.List à la place.
  List est désormais accepté par ConnectionPolicy#getPreferredLocations(), JsonSerialization et PartitionKey(.).
* Correction d’un bogue de session pour la requête de document en mode passerelle.
* Mise à niveau des dépendances (netty 0.4.20 [github #79](https://github.com/Azure/azure-cosmosdb-java/issues/79), RxJava 1.3.8).

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Correctifs de gestion des réponses aux requêtes de très grande taille.
* Correctifs de gestion des jetons de ressource lors de l’instanciation du client ([github #78](https://github.com/Azure/azure-cosmosdb-java/issues/78)).
* Mise à niveau de la dépendance vulnérable jackson-databind ([github #77](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Correction d’un bogue de fuite de ressources.
* Ajout de la prise en charge de multipolygones
* Ajout de la prise en charge des en-têtes personnalisés dans RequestOptions.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Correction d’un bogue d’empaquetage.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Correction d’un bogue NPE dans le chemin de nouvelle tentative d’écriture.
* Correction d’un bogue NPE dans la gestion des points de terminaison.
* Mise à niveau des dépendances vulnérables ([GitHub #68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Ajout de la prise en charge de la journalisation réseau Netty à des fins de dépannage.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Ajout de la prise en charge de l’écriture dans plusieurs régions.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Ajout de la prise en charge de Proxy.
* Ajout de la prise en charge de l’autorisation de jeton de ressource.
* Correction d’un bogue dans la gestion des clés de partition volumineuse ([GitHub n° 63](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Amélioration de la documentation.
* Kit de développement logiciel (SDK) restructuré en modules plus petits.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Correction d’un bogue pour les paramètres régionaux non anglais ([GitHub n° 51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Ajout de méthodes d’assistance dans la ressource liée au conflit.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Remplacement de la dépendance org.json par jackson pour des raisons de performance et de licences ([GitHub n° 29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Suppression de la classe déconseillée OfferV2.
* Ajout d’une méthode d’accesseur à la classe Offre pour le contenu de débit.
* Modification de toutes les méthodes dans Document/Ressource renvoyant des types org.json afin qu’elles renvoient un type d’objet jackson.
* Modification de la méthode des classes getObject(.) pour l’extension de JsonSerializable afin de renvoyer un type ObjectNode jackson.
* Modification de la méthode getCollection(.) pour renvoyer Collection d’ObjectNode.
* Suppression des constructeurse de sous-classes JsonSerializable avec org.json.JSONObject arg.
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) utilise maintenant deux espaces pour l’indentation.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Ajout de la prise en charge de la stratégie d’index unique.
* Ajout de la possibilité de limiter la taille du jeton de continuation de réponse dans les options de flux.
* Ajout de la prise en charge de la division de partition dans une requête portant sur plusieurs partitions.
* Correction d’un bogue lié à la sérialisation de timestamp Json ([GitHub n° 32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Correction d’un bogue lié à la sérialisation d’énumération Json.
* Correction d’un bogue lié à la gestion des documents d’une taille de 2 Mo ([GitHub n° 33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Mise à niveau de la dépendance com.fasterxml.jackson.core:jackson-databind vers 2.9.5 en raison d’un bogue ([jackson-databind: github n° 1599](https://github.com/FasterXML/jackson-databind/issues/1599)).
* Mise à niveau de la dépendance rxjava-extras vers 0.8.0.17 en raison d’un bogue ([rxjava-extras: GitHub n° 30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Mise à jour de la description des métadonnées du fichier pom en fonction du reste de la documentation.
* Amélioration de la syntaxe ([GitHub n° 41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([GitHub n° 40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Ajout de la prise en charge de la contre-pression dans la requête.
* Ajout de la prise en charge de l’ID de plage de clés de partition dans la requête.
* Correction visant à autoriser un jeton de continuation plus grand dans l’en-tête de demande (correctif de bogue GitHub n° 24).
* Mise à niveau de la dépendance Netty vers 4.1.22.Final pour que la machine virtuelle Java s’arrête après la fin du thread principal.
* Correction visant à éviter la transmission d’un jeton de session durant la lecture de ressources principales.
* Ajout d’exemples.
* Ajout de scénarios de test.
* Correction de fichiers d’en-tête Java pour que les documents java soient correctement générés.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Kit de développement logiciel (SDK) GA avec prise en charge de bout en bout des E/S non bloquantes à l’aide de la [bibliothèque Netty](https://netty.io/) en mode passerelle. 

## <a name="release-and-retirement-dates"></a>Dates de lancement et de suppression
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et optimisations sont ajoutées uniquement au SDK actuel. Nous vous recommandons donc de toujours effectuer une mise à niveau vers la dernière version du SDK dès que possible.

Le service rejette toute requête envoyée à Cosmos DB à l’aide d’un Kit de développement logiciel (SDK) supprimé.

<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [2.4.0](#2.4.0) |8 février 2019|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |4 février 2019|--- |
| [2.3.1](#2.3.1) |15 janvier 2019|--- |
| [2.3.0](#2.3.0) |29 novembre 2018|--- |
| [2.2.2](#2.2.2) |8 novembre 2018|--- |
| [2.2.1](#2.2.1) |2 novembre 2018|--- |
| [2.2.0](#2.2.0) |22 septembre 2018|--- |
| [2.1.0](#2.1.0) |5 septembre 2018|--- |
| [2.0.1](#2.0.1) |16 août 2018|--- |
| [2.0.0](#2.0.0) |20 juin 2018|--- |
| [1.0.2](#1.0.2) |18 mai 2018|--- |
| [1.0.1](#1.0.1) |20 avril 2018|--- |
| [1.0.0](#1.0.0) |27 février 2018|--- |

## <a name="faq"></a>Forum Aux Questions
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

