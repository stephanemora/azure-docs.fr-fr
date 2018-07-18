---
title: 'Azure Cosmos DB : API, Kit de développement logiciel (SDK) et ressources Java Async SQL | Microsoft Docs'
description: Découvrez l’API et le Kit de développement logiciel (SDK) Java Async SQL, notamment les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Java Async SQL d’Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 06/20/2018
ms.author: sngun
ms.openlocfilehash: e4a3b3a482f56065c54525a4d9cd7971f50f5b2a
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300677"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Kit de développement logiciel (SDK) Java Async Azure Cosmos DB pour API SQL : notes de publication et ressources
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
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

Le Kit de développement logiciel (SDK) Java Async de l’API SQL est différent du Kit de développement logiciel (SDK) Java de l’API SQL en fournissant des opérations asynchrones avec prise en charge de la [bibliothèque Netty](http://netty.io/). Le [Kit de développement logiciel (SDK) Java de l’API SQL](sql-api-sdk-java.md) existant ne prend pas en charge les opérations asynchrones. 

<table>

<tr><td>**Téléchargement du Kit de développement logiciel (SDK)**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Documentation de l’API**</td><td>[Documentation de référence sur l’API Java](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**Contribution au Kit de développement logiciel (SDK)**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Prise en main**</td><td>[Prise en main du Kit de développement logiciel (SDK) Java Async](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Code sample**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Conseils sur les performances**</td><td>[Fichier Readme de GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Runtime minimal pris en charge**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Notes de publication

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Remplacement de la dépendance org.json par jackson pour des raisons de performance et de licences ([github #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
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
* Correction d’un bogue lié à la sérialisation de timestamp Json ([github n°32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Correction d’un bogue lié à la sérialisation d’énumération Json.
* Correction d’un bogue lié à la gestion des documents d’une taille de 2 Mo ([github n°33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Mise à niveau de la dépendance com.fasterxml.jackson.core:jackson-databind vers 2.9.5 en raison d’un bogue ([jackson-databind: github n°1 599](https://github.com/FasterXML/jackson-databind/issues/1599)).
* Mise à niveau de la dépendance rxjava-extras vers 0.8.0.17 en raison d’un bogue ([rxjava-extras: github n°30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Mise à jour de la description des métadonnées du fichier pom en fonction du reste de la documentation.
* Amélioration de la syntaxe ([github n°41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([github n°40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Ajout de la prise en charge de la contre-pression dans la requête.
* Ajout de la prise en charge de l’ID de plage de clés de partition dans la requête.
* Correction visant à autoriser un jeton de continuation plus grand dans l’en-tête de demande (correctif de bogue github n°24).
* Mise à niveau de la dépendance Netty vers 4.1.22.Final pour que la machine virtuelle Java s’arrête après la fin du thread principal.
* Correction visant à éviter la transmission d’un jeton de session durant la lecture de ressources principales.
* Ajout d’exemples.
* Ajout de scénarios de test.
* Correction de fichiers d’en-tête Java pour que les documents java soient correctement générés.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Kit de développement logiciel (SDK) GA avec prise en charge de bout en bout des E/S non bloquantes à l’aide de la [bibliothèque Netty](http://netty.io/) en mode passerelle. 

## <a name="release-and-retirement-dates"></a>Dates de lancement et de suppression
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et optimisations sont ajoutées uniquement au SDK actuel. Nous vous recommandons donc de toujours effectuer une mise à niveau vers la dernière version du SDK dès que possible.

Le service rejette toute requête envoyée à Cosmos DB à l’aide d’un Kit de développement logiciel (SDK) supprimé.

<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [2.0.0](#2.0.0) |20 juin 2018|--- |
| [1.0.2](#1.0.2) |18 mai 2018|--- |
| [1.0.1](#1.0.1) |20 avril 2018|--- |
| [1.0.0](#1.0.0) |27 février 2018|--- |

## <a name="faq"></a>Forum Aux Questions
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

