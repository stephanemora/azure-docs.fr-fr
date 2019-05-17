---
title: Mettre à niveau vers la version la plus récente de l’API REST du service Recherche Azure – Recherche Azure
description: Passez en revue les différences entre les versions des API et découvrez ce que vous devez faire pour migrer le code existant vers la dernière version de l’API REST du service Recherche Azure.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 85a8ddf4ce87d7ac8ce460c0aff56311a2ea4578
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540689"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Mettre à niveau vers la version la plus récente de l’API REST du service Recherche Azure
Si vous utilisez une version précédente de la [API REST de Service Azure Search](https://docs.microsoft.com/rest/api/searchservice/), cet article vous aidera à mettre à niveau votre application pour utiliser la dernière version d’API à la disposition générale, 2019-05-06.

Version 2019-05-06 de l’API REST contient des modifications à partir de versions antérieures. Ces modifications sont, pour la plupart, à compatibilité descendante. La modification de votre code est donc facilitée, selon la version que vous utilisiez précédemment. [Étapes pour mettre à niveau](#UpgradeSteps) décrit les modifications de code nécessaires pour utiliser les nouvelles fonctionnalités.

> [!NOTE]
> Une instance de service de recherche Azure prend en charge les versions d’une plage de l’API REST, y compris aux plus anciennes. Vous pouvez continuer à utiliser ces versions d’API, mais nous vous recommandons de migrer votre code vers la dernière version afin que vous pouvez accéder aux nouvelles fonctionnalités.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Quelles sont les nouveautés dans la version 2019-05-06
Version 2019-05-06 est la dernière version de la disposition générale de l’API REST de Service Azure Search. Les fonctionnalités qui ont migré vers un état à la disposition générale dans cette version d’API :

* [La saisie semi-automatique](index-add-suggesters.md) est une fonctionnalité prédictives qui se termine une entrée de terme partiellement spécifié.

* [Les types complexes](search-howto-complex-data-types.md) fournit la prise en charge native pour les données d’objet structuré dans un index Azure Search.

* [Modes d’analyse de JsonLines](search-howto-index-json-blobs.md), composant d’objet Blob Azure à l’indexation, crée un document de recherche par entité JSON séparé par un saut de ligne.

* [Recherche COGNITIVE](cognitive-search-concept-intro.md) assure l’indexation qui s’appuie sur les moteurs d’enrichissement d’intelligence artificielle de Cognitive Services.

Plusieurs fonctionnalités de préversions coïncident avec cette mise à jour à la disposition générale. Pour passer en revue la liste des nouvelles fonctionnalités en version préliminaire, consultez [version d’api REST de recherche 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Changements cassants

Le code existant qui contient les fonctionnalités suivantes s’arrête sur l’api-version = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexeur pour Azure Cosmos DB - source de données est maintenant « type » : « cosmosdb »

Si vous utilisez un [indexeur Cosmos DB](search-howto-index-cosmosdb.md ), vous devez modifier `"type": "documentdb"` à `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Erreurs de résultat d’exécution indexeur n’ont plus état

La structure d’erreur pour l’exécution de l’indexeur avait précédemment un `status` élément. Cet élément a été supprimé, car il a été pas fournit des informations utiles.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Source de données d’indexeur API ne renvoie plus les chaînes de connexion

À partir de l’API versions 2019-05-06 / 2019-05-06-Preview et versions ultérieures, la source de données API ne renvoie plus les chaînes de connexion dans la réponse de toute opération REST. Dans les précédentes versions d’API, des sources de données créés à l’aide de POST, Azure Search retourné **201** suivie de la réponse OData, qui contenue la chaîne de connexion en texte brut.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Nommé reconnaissance d’entité compétence cognitive est plus disponible

Si vous appelez [reconnaissance d’entité nom](cognitive-search-skill-named-entity-recognition.md) compétence dans votre code, l’appel échoue. Fonctionnalité de remplacement est [reconnaissance d’entité](cognitive-search-skill-entity-recognition.md). Vous pourrez remplacer la référence de qualification par aucune autre modification. La signature de l’API est la même pour les deux versions. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Procédure de mise à niveau
Si vous mettez à niveau à partir d’une version précédente de la disponibilité générale, 2017-11-11 ou 2016-09-01, vous n’avez probablement faire de modifications à votre code, autre que pour modifier le numéro de version. Les seules situations dans lesquelles vous pouvez avoir à modifier votre code sont les suivantes :

* Lorsque votre code échoue, car des propriétés non reconnues sont renvoyées dans une réponse de l’API. Par défaut, votre application doit ignorer les propriétés qu’elle ne comprend pas.

* Votre code conserve des demandes d’API et tente de les renvoyer à la nouvelle version de l’API. Par exemple, cela peut se produire si votre application conserve les jetons de continuation renvoyés par l’API Recherche (pour plus d’informations, recherchez `@search.nextPageParameters` dans les [références sur l’API Recherche](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Si vous êtes concerné par l’une de ces situations, vous aurez peut-être à modifier votre code en conséquence. Sinon, aucune modification n’est nécessaire, sauf si vous souhaitez recommencer à utiliser le [nouvelles fonctionnalités](#WhatsNew) de version 2019-05-06.

Si vous mettez à niveau à partir d’une version d’API en version préliminaire, la méthode ci-dessus s’applique également, mais vous devez également être conscient que certaines fonctionnalités ne sont pas disponibles dans la version 2019-05-06 :

* [Requêtes « More like this »](search-more-like-this.md)
* [Indexation des objets blob CSV](search-howto-index-csv-blobs.md)
* [Prise en charge de l’API MongoDB pour les indexeurs de Cosmos DB](search-howto-index-cosmosdb.md)

Si votre code utilise ces fonctionnalités, vous ne serez pas en mesure de mettre à niveau vers la version 2019-05-06 API sans le supprimer de votre utilisation de ces.

> [!IMPORTANT]
> Les API en préversion sont destinées à être utilisées à des fins de test et d’évaluation, et non dans les environnements de production.
> 

### <a name="upgrading-complex-types"></a>La mise à niveau des types complexes

Si votre code utilise des types complexes avec les anciennes versions d’API en version préliminaire 2017-11-11-Preview ou 2016-09-01-Preview, il existe certaines limites nouvelles et modifiées dans la version 2019-05-06 dont vous devez être conscient :

+ Les limites relatives à la profondeur de sous-champs et le nombre de collections complexes par index ont été réduits. Si vous avez créé des index qui dépassent ces limites à l’aide de l’api de préversions, toute tentative de mise à jour ou de les recréer à l’aide des API version 2019-05-06 échouera. Si cela s’applique à vous, vous devrez reconcevez votre schéma s’ajuste les nouvelles limites et puis reconstruire votre index.

+ Il existe une nouvelle limite dans api-version 2019-05-06 sur le nombre d’éléments des collections complexes par document. Si vous avez créé des index avec des documents qui dépassent ces limites à l’aide de l’api de préversions, toute tentative de réindexation des données en utilisant des api-version 2019-05-06 échouera. Si cela s’applique à vous, vous devez réduire le nombre d’éléments de collection complexe par document avant la réindexation de vos données.

Pour plus d’informations, consultez [limites de Service pour Azure Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Comment mettre à niveau une ancienne structure de type complexe

Si votre code utilise des types complexes avec l’un des anciennes versions d’API en version préliminaire, vous utilisez peut-être un format de définition d’index qui ressemble à ceci :

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Un format plus récent de la forme d’arbre pour la définition des champs d’index a été introduit dans l’API version 2017-11-11-Preview. Dans le nouveau format, chaque champ complexe a une collection de champs où ses sous-champs sont définies. Dans l’API version 2019-05-06, ce nouveau format est utilisé exclusivement et créer ou mettre à jour d’un index à l’aide de l’ancien format échoue. Si vous avez créés à l’aide de l’ancien format des index, vous devez utiliser des API version 2017-11-11-Preview pour les mettre à jour vers le nouveau format afin qu’ils puissent être gérés à l’aide des API version 2019-05-06.

Vous pouvez mettre à jour « plats » index vers le nouveau format avec les étapes suivantes à l’aide des API version 2017-11-11-Preview :

1. Effectuer une demande GET pour récupérer votre index. S’il est déjà dans le nouveau format, vous avez terminé.

2. Traduire l’index du format « plate » vers le nouveau format. Vous devrez écrire du code pour cela, car aucun exemple de code n’est disponible au moment de la rédaction.

3. Effectuer une demande PUT pour mettre à jour de l’index vers le nouveau format. Évitez de modifier tous les autres détails de l’index, telles que la possibilités de recherche/FILTRABILITÉ de champs, dans la mesure où cela n’est pas autorisé par l’API d’Index de mise à jour.

> [!NOTE]
> Il n’est pas possible de gérer les index créés avec l’ancien format « plat » à partir du portail Azure. Mettez à niveau votre index à partir de la représentation sous forme de « plate » vers la représentation sous forme de « arborescence » aussitôt.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue la documentation de référence API REST de Service de recherche Azure. Si vous rencontrez des problèmes, nous demander de l’aide sur [StackOverflow](https://stackoverflow.com/) ou [contactez le support technique](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Référence de l’API REST du service de recherche](https://docs.microsoft.com/rest/api/searchservice/)

