---
title: Algorithme de similarité pour le classement
titleSuffix: Azure Cognitive Search
description: Guide pratique pour définir l’algorithme de similarité afin d’essayer un nouvel algorithme de similarité pour le classement
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: c327440649300533c94c2a1956e3c45f433c9780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409959"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Algorithme de classement dans la Recherche cognitive Azure

> [!IMPORTANT]
> À compter du 15 juillet 2020, les services de recherche nouvellement créés utiliseront la fonction de classement BM25, qui s’est avérée efficace, dans la plupart des cas, pour fournir des classements de recherche répondant mieux aux attentes des utilisateurs que le classement par défaut actuel.  Au-delà du classement plus élevé, BM25 active également des options de configuration pour les résultats de paramétrage en fonction de facteurs tels que la taille des documents.  
>
> Avec ce changement, vous verrez probablement de légères différences dans l’ordre de vos résultats de recherche.   Pour ceux qui souhaitent tester l’impact de ce changement, nous avons rendu disponible dans l’API 2019-05-06-Preview la possibilité d’activer le scoring de BM25 sur les nouveaux index.  

Cet article explique comment vous pouvez mettre à jour un service créé avant le 15 juillet 2020 pour utiliser le nouvel algorithme de classement BM25.

La Recherche cognitive Azure utilisera l’implémentation Lucene officielle de l’algorithme Okapi BM25, *BM25Similarity*, qui remplacera l’implémentation de *ClassicSimilarity* précédemment utilisée. Comme l’ancien algorithme ClassicSimilarity, BM25Similarity est une fonction de récupération semblable à TF-IDF qui utilise la fréquence du terme (TF) et la fréquence de document inverse (IDF) comme variables pour calculer les notes de pertinence pour chaque paire document-requête, qui sont ensuite utilisées pour le classement. Bien que semblable d’un point de vue conceptuel à l’ancien algorithme de similarité classique, BM25 prend racine dans la récupération d’informations probabilistes pour les améliorer. BM25 propose également des options de personnalisation avancées, comme permettre à l’utilisateur de décider comment la note de pertinence est mise à l’échelle avec la fréquence du terme des termes correspondants.

## <a name="how-to-test-bm25-today"></a>Comment tester BM25 aujourd’hui

Quand vous créez un index, vous pouvez définir une propriété de « similarité ». Vous devez utiliser la version *2019-05-06-Preview*, comme indiqué ci-dessous.

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
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

Pour les services créés avant le 15 juillet 2020 : Si la similarité est omise ou définie sur la valeur Null, l’index utilise l’ancien algorithme de similarité classique.

Pour les services créés après le 15 juillet 2020 : Si la similarité est omise ou définie sur la valeur Null, l’index utilise le nouvel algorithme de similarité BM25.

Vous pouvez également définir explicitement la valeur de similarité sur l’une des deux valeurs suivantes : *"#Microsoft.Azure.Search.ClassicSimilarity"* ou *"#Microsoft.Azure.Search.BM25Similarity"* .


## <a name="bm25-similarity-parameters"></a>Paramètres de similarité de BM25

La similarité de BM25 ajoute deux paramètres personnalisables par l’utilisateur pour contrôler la note de pertinence calculée :

### <a name="k1"></a>k1

Le paramètre *k1* contrôle la fonction de mise à l’échelle entre la fréquence du terme de chaque terme correspondant et la note de pertinence finale d’une paire document-requête.

La valeur zéro représente un « modèle binaire », où la contribution d’un seul terme correspondant est identique pour tous les documents correspondants, quel que soit le nombre de fois que ce terme apparaît dans le texte, tandis qu’une valeur k1 plus grande permet à la note de continuer à augmenter à mesure que des instances supplémentaires du même terme sont trouvées dans le document. Par défaut, la Recherche cognitive Azure utilise la valeur 1.2 pour le paramètre k1. L’utilisation d’une valeur k1 supérieure peut être importante dans les cas où nous pensons que plusieurs termes peuvent faire partie d’une requête de recherche. Dans ce cas, nous pouvons souhaiter privilégier les documents qui correspondent à un grand nombre des différents termes de requête faisant l’objet d’une recherche plutôt que les documents qui n’ont une correspondance qu’avec un seul terme, plusieurs fois. Par exemple, lors de l’interrogation de l’index pour rechercher les documents contenant les termes « Apollo Spaceflight », nous pouvons souhaiter diminuer la note d’un article sur la mythologie grecque qui contient le terme « Apollo » plusieurs dizaines de fois mais qui ne mentionne pas « Spaceflight », par rapport à un autre article qui mentionne explicitement à la fois « Apollo » et « Spaceflight » quelques fois seulement. 
 
### <a name="b"></a>b

Le paramètre *b* contrôle la manière dont la longueur d’un document affecte la note de pertinence.

La valeur 0.0 signifie que la longueur du document n’aura pas d’influence sur la note, tandis que la valeur 1.0 signifie que l’impact de la fréquence du terme sur la note de pertinence sera normalisé par la longueur du document. La valeur par défaut utilisée dans la Recherche cognitive Azure pour le paramètre b est 0.75. La normalisation de la fréquence du terme par la longueur du document est utile dans les cas où nous voulons pénaliser les documents plus longs. Dans certains cas, des documents plus longs (par exemple, un roman entier) sont davantage susceptibles de contenir de nombreux termes non pertinents, par rapport à des documents beaucoup plus courts.

### <a name="setting-k1-and-b-parameters"></a>Définition des paramètres k1 et b

Pour personnaliser les valeurs b ou k1, ajoutez-les simplement sous forme de propriétés à l’objet de similarité lors de l’utilisation de BM25 :

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

L’algorithme de similarité ne peut être défini qu’au moment de la création de l’index. Cela signifie que l’algorithme de similarité utilisé ne peut pas être modifié pour les index existants. Les paramètres  *"b"* et *"k1"* peuvent être modifiés lors de la mise à jour de la définition d’un index existant qui utilise BM25. Le changement de ces valeurs sur un index existant placera votre index hors connexion pendant au moins quelques secondes, ce qui entraînera l’échec de vos demandes d’indexation et de requête. C’est pourquoi vous devrez définir le paramètre « allowIndexDowntime=true » dans la chaîne de requête de votre demande de mise à jour :

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>Voir aussi  

 [REST de Recherche cognitive Azure](https://docs.microsoft.com/rest/api/searchservice/)   
 [Ajouter des profils de scoring à votre index](index-add-scoring-profiles.md)    
 [Créer un index &#40;API REST de Recherche cognitive Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Kit de développement logiciel (SDK) de Recherche cognitive Azure .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
