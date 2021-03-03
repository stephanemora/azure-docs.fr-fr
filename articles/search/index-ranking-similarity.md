---
title: Configurer l’algorithme de similarité de classement
titleSuffix: Azure Cognitive Search
description: Guide pratique pour définir l’algorithme de similarité afin d’essayer un nouvel algorithme de similarité pour le classement
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 9f806b512ae8e118fca8f32115c8be3b493fd681
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677786"
---
# <a name="configure-ranking-algorithms-in-azure-cognitive-search"></a>Configurer des algorithmes de classement dans la Recherche cognitive Azure

La Recherche cognitive Azure prend en charge deux algorithmes de classement de similarité :

+ Algorithme de *similarité classique*, utilisé par tous les services de recherche jusqu’au 15 juillet 2020.
+ Implémentation de l’algorithme *Okapi BM25*, utilisé dans tous les services de recherche créés après le 15 juillet.

Le classement BM25 est la nouvelle valeur par défaut, car il a tendance à produire des classements de recherche mieux alignés sur les attentes des utilisateurs. Il permet également des options de configuration pour l’ajustement des résultats en fonction de facteurs, tels que la taille des documents. Pour les nouveaux services créés après le 15 juillet 2020, BM25 est utilisé automatiquement et est l’unique algorithme de similarité. Si vous essayez de définir la similarité sur ClassicSimilarity pour un nouveau service, une erreur HTTP 400 est retournée, car cet algorithme n’est pas pris en charge par le service.

Pour les services plus anciens, créés avant le 15 juillet 2020, la similarité classique demeure l’algorithme par défaut. Les services plus anciens peuvent définir des propriétés sur un index de recherche pour appeler BM25, comme expliqué ci-dessous. Si vous passez d’un modèle classique à BM25, attendez-vous à constater quelques différences dans le tri des résultats de la recherche.

> [!NOTE]
> La recherche sémantique est un algorithme de reclassification sémantique supplémentaire qui réduit encore davantage l’écart entre les attentes et les résultats. Contrairement aux autres algorithmes, il s’agit d’une fonctionnalité complémentaire qui procède par itération sur un jeu de résultats existant. Pour utiliser l’algorithme de recherche sémantique en préversion, vous devez créer un nouveau service et spécifier un [type de requête sémantique](semantic-how-to-query-request.md). Pour plus d’informations, consultez [Vue d’ensemble de la recherche sémantique](semantic-search-overview.md).

## <a name="create-a-search-index-for-bm25-scoring"></a>Créer un index de recherche pour le scoring BM25

Si vous exécutez un service de recherche qui a été créé avant le 15 juillet 2020, vous pouvez définir la propriété de similarité sur BM25Similarity ou sur ClassicSimilarity, dans la définition de l’index. Si la propriété similarity est omise ou a la valeur null, l’index utilise l’algorithme classique.

L’algorithme de similarité ne peut être défini qu’au moment de la création de l’index. Toutefois, lorsqu’un index est créé avec BM25, vous pouvez mettre à jour l’index existant pour définir ou modifier les paramètres BM25.

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

## <a name="bm25-similarity-parameters"></a>Paramètres de similarité de BM25

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