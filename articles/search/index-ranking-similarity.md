---
title: Configurer l’algorithme de similarité
titleSuffix: Azure Cognitive Search
description: Découvrez comment activer BM25 sur des services de recherche plus anciens et comment les paramètres BM25 peuvent être modifiés pour mieux prendre en charge le contenu de vos index.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 52b3523d3c092f1b9375f53038cc3b20d0ddedcc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232832"
---
# <a name="configure-the-similarity-ranking-algorithm-in-azure-cognitive-search"></a>Configurer l’algorithme de classement des similitudes dans Recherche cognitive Azure

La Recherche cognitive Azure prend en charge deux algorithmes de classement de similarité :

+ Algorithme de *similarité classique*, utilisé par tous les services de recherche jusqu’au 15 juillet 2020.
+ Implémentation de l’algorithme *Okapi BM25*, utilisé dans tous les services de recherche créés après le 15 juillet.

Le classement BM25 est la nouvelle valeur par défaut, car il a tendance à produire des classements de recherche mieux alignés sur les attentes des utilisateurs. Il est doté de [paramètres](#set-bm25-parameters) permettant d'ajuster les résultats en fonction de facteurs tels que la taille du document. 

Pour les nouveaux services créés après le 15 juillet 2020, BM25 est utilisé automatiquement et est l’unique algorithme de similarité. Si vous essayez de définir la similarité sur ClassicSimilarity pour un nouveau service, une erreur HTTP 400 est retournée, car cet algorithme n’est pas pris en charge par le service.

Pour les services plus anciens, créés avant le 15 juillet 2020, la similarité classique demeure l’algorithme par défaut. Les services plus anciens peuvent être mis à niveau vers BM25 en fonction de l’index, comme expliqué ci-dessous. Si vous passez d’un modèle classique à BM25, attendez-vous à constater quelques différences dans le tri des résultats de la recherche.

> [!NOTE]
> Le classement sémantique, actuellement en préversion pour les services standard dans les régions sélectionnées, est une étape supplémentaire dans la production de résultats plus pertinents. Contrairement aux autres algorithmes, il s’agit d’une fonctionnalité complémentaire qui procède par itération sur un jeu de résultats existant. Pour plus d’informations, consultez [Vue d’ensemble de la recherche sémantique](semantic-search-overview.md) et [Classement sémantique](semantic-ranking.md).

## <a name="enable-bm25-scoring-on-older-services"></a>Activer le scoring BM25 sur les services plus anciens

Si vous exécutez un service de recherche qui a été créé avant le 15 juillet 2020, vous pouvez activer BM25 en définissant une propriété de similarité sur les nouveaux index. La propriété est exposée uniquement sur les nouveaux index. Par conséquent, si vous souhaitez BM25 sur un index existant, vous devez supprimer et [reconstruire l’index](search-howto-reindex.md) avec une nouvelle propriété de similarité définie sur « Microsoft.Azure.Search.BM25Similarity ».

Une fois qu’il existe un index avec une propriété de similarité, vous pouvez basculer entre BM25Similarity ou ClassicSimilarity. 

Les liens suivants décrivent la propriété Similarity dans les kits de développement logiciel (SDK) Azure. 

| Bibliothèque cliente | Propriété de similarité |
|----------------|---------------------|
| .NET  | [SearchIndex.Similarity](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex.setSimilarity](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex.Similarity](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [propriété de similarité sur SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>Exemple REST

Vous pouvez également utiliser l’[API REST](/rest/api/searchservice/create-index), comme l’illustre l’exemple suivant :

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

## <a name="set-bm25-parameters"></a>Définir les paramètres BM25

La similarité de BM25 ajoute deux paramètres personnalisables par l’utilisateur pour contrôler la note de pertinence calculée. Vous pouvez définir des paramètres BM25 lors de la création de l’index, ou sous la forme d’une mise à jour d’index si l’algorithme BM25 a été spécifié au cours de la création de l’index.

| Propriété | Type | Description |
|----------|------|-------------|
| k1 | nombre | Contrôle la fonction de mise à l’échelle entre la fréquence de chaque terme correspondant et le score de pertinence final d’une paire document-requête. Les valeurs se situent généralement entre 0,0 et 3,0, la valeur par défaut étant 1,2. </br></br>La valeur 0.0 représente un « modèle binaire », dans lequel la contribution d’un seul terme correspondant est identique pour tous les documents correspondants, quel que soit le nombre de fois que ce terme apparaît dans le texte, alors qu’une valeur k1 plus grande permet au score de continuer à progresser à mesure que des instances supplémentaires du même terme sont trouvées dans le document. </br></br>L’utilisation d’une valeur k1 supérieure peut être importante dans les cas où nous pensons que plusieurs termes peuvent faire partie d’une requête de recherche. Dans ce cas, nous pouvons souhaiter privilégier les documents qui correspondent à un grand nombre des différents termes de requête faisant l’objet d’une recherche plutôt que les documents qui n’ont une correspondance qu’avec un seul terme, plusieurs fois. Par exemple, lors de l’interrogation de l’index pour rechercher les documents contenant les termes « Apollo Spaceflight », nous pouvons souhaiter diminuer le score d’un article sur la mythologie grecque qui contient le terme « Apollo » plusieurs dizaines de fois mais ne mentionne pas « Spaceflight », par rapport à un autre article qui mentionne explicitement à la fois « Apollo » et « Spaceflight » quelques fois seulement. |
| b | nombre | Contrôle la manière dont la longueur d’un document affecte le score de pertinence. Les valeurs sont comprises entre 0 et 1, la valeur par défaut étant 0,75. </br></br>La valeur 0.0 signifie que la longueur du document n’aura pas d’influence sur la note, tandis que la valeur 1.0 signifie que l’impact de la fréquence du terme sur la note de pertinence sera normalisé par la longueur du document. </br></br>La normalisation de la fréquence du terme par la longueur du document est utile dans les cas où nous voulons pénaliser les documents plus longs. Dans certains cas, des documents plus longs (par exemple, un roman entier) sont davantage susceptibles de contenir de nombreux termes non pertinents, par rapport à des documents beaucoup plus courts. |

### <a name="setting-k1-and-b-parameters"></a>Définition des paramètres k1 et b

Pour définir ou modifier des valeurs b ou k1, ajoutez-les à l’objet de similarité BM25. La définition ou la modification de ces valeurs dans un index existant placera votre index hors connexion pendant au moins quelques secondes, ce qui entraînera l’échec de l’indexation active et des demandes de requête. Par conséquent, vous devez définir le paramètre « allowIndexDowntime=true » de la demande de mise à jour :

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>Voir aussi  

+ [Référence d’API REST](/rest/api/searchservice/)
+ [Ajouter des profils de scoring à votre index](index-add-scoring-profiles.md)
+ [Créer une API d’index](/rest/api/searchservice/create-index)
+ [Kit de développement logiciel (SDK) de Recherche cognitive Azure .NET](/dotnet/api/overview/azure/search)