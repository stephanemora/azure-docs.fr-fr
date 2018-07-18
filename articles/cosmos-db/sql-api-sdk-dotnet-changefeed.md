---
title: 'Azure Cosmos DB : API, SDK et ressources du processeur de flux de modification .NET | Microsoft Docs'
description: Découvrez l’API et le kit SDK du processeur de flux de modification, notamment les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du kit SDK du processeur de flux de modification .NET.
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/21/2018
ms.author: maquaran
ms.openlocfilehash: f69742d111555e776a968454bdc004ba171e6336
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937415"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Kit SDK du processeur de flux de modification .NET : téléchargement et notes de publication
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

|   |   |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentation de l’API**|[Documentation de référence de l’API de la bibliothèque du processeur de flux de modification](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Bien démarrer**|[Prise en main du kit SDK du processeur de flux de modification .NET](change-feed.md)|
|**Infrastructure actuellement prise en charge**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Notes de publication

### <a name="stable-builds"></a>Builds stables

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Journalisation supplémentaire ajoutée.
* Réparation d’une fuite DocumentClient lors de l’appel du travail en attente plusieurs fois.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Correctifs inclus dans l’estimation de travail en attente.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Améliorations de la stabilité.
  * Correctif pour résoudre le problème lié aux tâches annulées susceptibles de provoquer l’arrêt des observateurs dans certaines partitions.
* Prise en charge des points de contrôle manuels.
* Compatible avec les versions 1.21 et version ultérieure du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Ajout de la prise en charge de .NET Standard 2.0. Le package prend désormais en charge les monikers d’infrastructure `netstandard2.0` et `net451`.
* Compatible avec les versions 1.17.0 et supérieures du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).
* Compatible avec les versions 1.5.1 et supérieures du [Kit de développement logiciel (SDK) principal SQL .NET](sql-api-sdk-dotnet-core.md).

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Résout un problème relatif au calcul de l’estimation du travail restant lorsque le flux de modification était vide ou qu’aucun travail n’était en attente.
* Compatible avec les versions 1.13.2 et supérieures du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Ajout d’une méthode pour obtenir une estimation du travail restant à traiter dans le flux de modification.
* Compatible avec les versions 1.13.2 et supérieures du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Kit SDK GA
* Compatible avec les versions 1.14.1 et inférieures du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).

### <a name="pre-release-builds"></a>Builds en préversion

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-prerelease
* Les problèmes suivants ont été résolus :
  * En cas de fractionnement de la partition, un double traitement des documents modifiés avant le fractionnement peut se produire.
  * L’API GetEstimatedRemainingWork a retourné 0 quand aucun bail n’était présent dans la collection de baux.

* Les exceptions suivantes sont rendues publiques. Les extensions qui implémentent IPartitionProcessor peuvent lever ces exceptions.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* Évolutions mineures de l’API :
  * Suppression de ChangeFeedProcessorOptions.IsAutoCheckpointEnabled qui a été marqué comme étant obsolète.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Améliorations sur le plan de la stabilité :
  * Meilleure gestion de l’initialisation du magasin de baux. Quand le magasin de baux est vide, une seule instance de processeur peut l’initialiser. Pendant ce temps, les autres attendent.
  * Renouvellement/libération de bail plus stable/efficace. Le renouvellement et la libération d’un bail au niveau d’une partition sont indépendants du renouvellement des autres. Dans v1, il s’agissait d’une opération séquentielle qui portait sur toutes les partitions.
* Nouvelle API v2 :
  * Modèle de générateur pour la construction flexible du processeur : la classe ChangeFeedProcessorBuilder.
    * Peut prendre n’importe quelle combinaison de paramètres.
    * Peut prendre une instance DocumentClient pour la surveillance et/ou la collection de baux (non disponible dans v1).
  * IChangeFeedObserver.ProcessChangesAsync accepte désormais CancellationToken.
  * IRemainingWorkEstimator : l’estimateur de travail restant peut être utilisé indépendamment du processeur.
  * Nouveaux points d’extensibilité :
    * IParitionLoadBalancingStrategy : pour un équilibrage de charge de partitions personnalisé entre des instances du processeur.
    * ILease, ILeaseManager : pour la gestion de baux personnalisée.
    * IPartitionProcessor : pour les modifications de traitement personnalisées sur une partition.
* Journalisation : utilise la bibliothèque [LibLog](https://github.com/damianh/LibLog).
* 100 % compatible avec l’API v1.
* Nouvelle base de code.
* Compatible avec les versions 1.21.1 et ultérieures du [SDK SQL .NET](sql-api-sdk-dotnet.md).

## <a name="release--retirement-dates"></a>Dates de lancement et de suppression
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible. 

Le service rejette toute requête envoyée à Cosmos DB à l’aide d’un Kit de développement logiciel (SDK) supprimé.

<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [1.3.3](#1.3.3) |8 mai 2018 |--- |
| [1.3.2](#1.3.2) |18 avril 2018 |--- |
| [1.3.1](#1.3.1) |13 mars 2018 |--- |
| [1.2.0](#1.2.0) |31 octobre 2017 |--- |
| [1.1.1](#1.1.1) |29 août 2017 |--- |
| [1.1.0](#1.1.0) |13 août 2017 |--- |
| [1.0.0](#1.0.0) |7 juillet 2017 |--- |


## <a name="faq"></a>Forum Aux Questions
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

