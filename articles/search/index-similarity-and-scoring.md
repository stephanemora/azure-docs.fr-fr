---
title: Vue d’ensemble de la similarité et du scoring
titleSuffix: Azure Cognitive Search
description: Explique les concepts de similarité et de scoring, et ce qu’un développeur peut faire pour personnaliser le résultat du scoring.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 9f9cc4c29b117c83595a36c4e28b1edb428c3cde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254062"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Similarité et scoring dans Recherche cognitive Azure

Le scoring consiste à calculer un score de recherche pour chaque élément retourné dans des résultats de recherche pour des requêtes de texte intégral. Le score est un indicateur de la pertinence d'un élément dans le contexte de l'opération de recherche en cours. Plus le score est élevé, plus l'élément est pertinent. Dans des résultats de recherche, les éléments sont classés par ordre décroissant de pertinence, sur la base des résultats de recherche calculés pour chacun d'eux. 

Par défaut, les 50 premiers sont retournés dans la réponse, mais vous pouvez utiliser le paramètre **$top** pour retourner un nombre inférieur ou supérieur d’éléments (jusqu’à 1000 par réponse) et le paramètre **$skip** pour obtenir le jeu de résultats suivant.

Le score de recherche est calculé sur la base de propriétés statistiques des données et de la requête. Recherche cognitive Azure trouve les documents qui contiennent des correspondances de recherche (en totalité ou en partie, selon [searchMode](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional)), en favorisant les documents qui contiennent de nombreuses occurrences du terme recherché. Le score de recherche augmente davantage si le terme est rare dans l’index de données, mais courant au sein du document. La base de cette approche de la pertinence du calcul est appelée *TF-IDF* ou Term Frequency-Inverse Document Frequency (fréquence de terme-fréquence inverse de document).

Des valeurs de score de recherche peuvent être répétées dans un jeu de résultats. Quand plusieurs correspondances ont le même score de recherche, le classement des éléments ayant le même score n’est ni défini ni stable. Réexécutez la requête, et vous constaterez peut-être que des éléments changent de position, en particulier si vous utilisez le service gratuit ou un service facturable avec plusieurs réplicas. Si deux éléments ont un score identique, il est impossible de prédire celui qui apparaîtra en première position.

Si vous souhaitez départager des scores identiques, vous pouvez ajouter une clause **$orderby** afin de trier d’abord par score, puis par un autre champ pouvant être trié (par exemple `$orderby=search.score() desc,Rating desc`). Pour plus d’informations, consultez [$orderby](https://docs.microsoft.com/azure/search/search-query-odata-orderby).

> [!NOTE]
> Un `@search.score = 1.00` indique un jeu de résultats sans score ou non classé. Le score est uniforme parmi tous les résultats. Des résultats sans score se produisent quand le formulaire de requête est une recherche approximative, des requêtes Regex ou de caractères génériques, ou une expression **$filter**. 

## <a name="scoring-profiles"></a>Profils de scoring

Vous pouvez personnaliser la façon dont les différents champs sont classés en définissant un *profil de scoring* personnalisé. Les profils de scoring vous permettent de mieux contrôler le classement d'éléments dans des résultats de recherche. Par exemple, vous pouvez privilégier des éléments en fonction de leur revenu potentiel, promouvoir des éléments plus récents, voire en favoriser d'autres restés trop longtemps en stock. 

Un profil de scoring fait partie de la définition d'index, composée de champs, fonctions et paramètres pondérés. Pour plus d’informations sur la définition d’un profil de scoring, consultez [Profils de scoring](index-add-scoring-profiles.md).

## <a name="scoring-statistics"></a>Statistiques de scoring

À des fins de scalabilité, Recherche cognitive Azure distribue chaque index horizontalement par le biais d’un processus de partitionnement, ce qui signifie que les portions d’un index sont physiquement séparées.

Par défaut, le score d’un document est calculé en fonction de propriétés statistiques des données *au sein d’une partition*. Cette approche n’est généralement pas un problème pour un corpus important de données, et elle offre de meilleures performances que le calcul du score à partir des informations de l’ensemble des partitions. Cela dit, avec cette optimisation des performances, deux documents très similaires (ou même des documents identiques) risquent de se retrouver avec des scores de pertinence différents s’ils figurent dans des partitions différentes.

Si vous préférez calculer le score à partir des propriétés statistiques sur l’ensemble des partitions, vous pouvez le faire en ajoutant *scoringStatistics=global* en tant que [paramètre de requête](https://docs.microsoft.com/rest/api/searchservice/search-documents) (ou en ajoutant *"scoringStatistics": "global"* en tant que paramètre de corps de la [demande de requête](https://docs.microsoft.com/rest/api/searchservice/search-documents)).

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global
  Content-Type: application/json
  api-key: [admin key]  
```

> [!NOTE]
> Une clé API d’administration est requise pour le paramètre `scoringStatistics`.

## <a name="similarity-ranking-algorithms"></a>Algorithmes de classement de similarité

Recherche cognitive Azure prend en charge deux algorithmes de classement de similarité différents : un algorithme de *similarité classique* et l’implémentation officielle de l’algorithme *Okapi BM25* (actuellement en préversion). L’algorithme de similarité classique est l’algorithme par défaut, mais à compter du 15 juillet tous les nouveaux services créés utiliseront le nouvel algorithme BM25. Il s’agira du seul algorithme disponible sur les nouveaux services.

Pour le moment, vous pouvez spécifier l’algorithme de classement de similarité que vous souhaitez utiliser. Pour plus d’informations, consultez [Algorithme de classement](index-ranking-similarity.md).

## <a name="watch-this-video"></a>Regardez cette vidéo

Dans cette vidéo de 16 minutes, l’ingénieur logiciel Raouf Merouche explique le processus d’indexation, d’interrogation et de création de profils de scoring. Vous aurez une bonne idée de ce qui se passe sous le capot quand vos documents sont indexés et récupérés.

>[!VIDEO https://channel9.msdn.com/Shows/AI-Show/Similarity-and-Scoring-in-Azure-Cognitive-Search/player]

+ 2 - 3 minutes : indexation (traitement du texte et analyse lexicale).
+ 3 - 4 minutes : indexation (index inversés).
+ 4 - 6 minutes : interrogation (récupération et classement).
+ 7 - 16 minutes : profils de scoring.

## <a name="see-also"></a>Voir aussi

 [Profils de scoring](index-add-scoring-profiles.md) [Référence de l’API REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [API de recherche dans des documents](https://docs.microsoft.com/rest/api/searchservice/search-documents)   
 [Kit de développement logiciel (SDK) de Recherche cognitive Azure .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
