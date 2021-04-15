---
title: Classement sémantique
titleSuffix: Azure Cognitive Search
description: Découvrez comment fonctionne l’algorithme de classement sémantique dans Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: bf311eb2b2d0ff7a9c17380d2e384bc05c6f05f3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562033"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Classement sémantique dans Recherche cognitive Azure

> [!IMPORTANT]
> Les fonctionnalités de recherche sémantique sont disponibles en préversion publique, uniquement via l’API REST en préversion et le portail. Les fonctionnalités en préversion sont proposées telles quelles, sous des [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), et ne sont pas garanties d’avoir la même implémentation lors de la mise à la disposition générale. Ces fonctionnalités sont facturables. Pour plus d’informations, consultez [Disponibilité et tarifs](semantic-search-overview.md#availability-and-pricing).

Le classement sémantique est une extension du pipeline d’exécution de requête qui améliore la précision en reclassant les meilleures correspondances d’un jeu de résultats initial. Le classement sémantique s’appuie sur de grands réseaux basés sur un transformateur, formés pour capturer la sémantique de termes de requête, par opposition à la correspondance linguistique de mots clés. Contrairement à l’[algorithme de classement de similarité par défaut](index-ranking-similarity.md), le classeur sémantique utilise le contexte et le sens des mots pour déterminer la pertinence.

Le classement sémantique est gourmand en ressources et en temps. Pour terminer le traitement dans le respect de latence attendue d’une opération de requête, les entrées du classeur sémantique sont consolidées et réduites afin que les étapes de résumé et de reclassement sous-jacentes puissent être effectuées le plus rapidement possible.

## <a name="pre-processing"></a>Prétraitement

Avant d’évaluer la pertinence, le contenu doit être réduit à un nombre gérable d’entrées que le ranker sémantique peut gérer efficacement.

1. Tout d’abord, la réduction du contenu commence par le jeu de résultats initial que renvoie l’[algorithme de classement](index-ranking-similarity.md) par défaut utilisé par la recherche par mot clé. Pour une requête donnée, les résultats peuvent être une poignée de documents, jusqu’à la limite maximale de 1 000 documents. Comme le traitement d’un grand nombre de correspondances prendrait trop de temps, seuls les 50 premiers documents sont classés dans le classement sémantique.

   Quel que soit le nombre de documents, qu’il s’agisse de 1 ou de 50, le jeu de résultats initial établit la première itération du corpus de documents pour le classement sémantique.

1. Ensuite, dans tout le corpus, le contenu de chaque champ dans « searchFields » est extrait et combiné dans une chaîne longue.

1. Après la consolidation des chaînes, toute chaîne excessivement longue est découpée pour garantir que la longueur totale est conforme aux exigences d’entrée mentionnées l’étape de résumé.

   Cet exercice de découpage est la raison pour laquelle il est important de placer les champs courts en premier dans « searchFields », afin de s’assurer de leur inclusion dans la chaîne. Si vous avez des documents très volumineux avec des champs contenant beaucoup de texte, tout ce qui se trouve après la limite maximale est ignoré.

Chaque document est désormais représenté par une seule chaîne longue.

> [!NOTE]
> La chaîne est composée de jetons, et non de caractères ou de mots. La segmentation du texte en unités lexicales est déterminée en partie par l’affectation de l’analyseur à des champs pouvant faire l’objet d’une recherche. Si vous utilisez un analyseur spécialisé, tel que nGram ou EdgeNGram, vous pouvez exclure ce champ de searchFields. Pour obtenir des informations sur la façon dont les chaînes sont segmentées en unités lexicales, vous pouvez examiner la sortie de jeton d’un analyseur à l’aide de l’[API REST Analyseur de test](/rest/api/searchservice/test-analyzer).

## <a name="extraction"></a>Extraction

Après une réduction de chaîne, il est possible de transmettre les entrées réduites via des modèles de compréhension et de représentation linguistique de lecture automatique qui déterminent les expressions et les phrases résumant le mieux le document par rapport à la requête. Cette phase extrait de la chaîne le contenu qui sera transmis au ranker sémantique.

Les entrées pour le résumé sont les chaînes longues obtenues pour chaque document durant la phase de préparation. À partir de chaque chaîne, le modèle de résumé trouve un passage qui est le plus représentatif. Ce passage constitue également une [légende sémantique](semantic-how-to-query-request.md) pour le document. Chaque légende est disponible dans une version en texte brut et une version en surbrillance et compte souvent moins de 200 mots par document.

Une [réponse sémantique](semantic-answers.md) est également retournée si vous avez spécifié le paramètre « answers », si la requête a été posée en tant que question, et si un passage peut être trouvé dans la chaîne longue susceptible de fournir une réponse à la question.

## <a name="semantic-ranking"></a>Classement sémantique

1. Les légendes sont évaluées sur le plan de leur pertinence conceptuelle et sémantique, par rapport à la requête fournie.

   Le diagramme suivant illustre la signification de « pertinence sémantique ». Considérez le terme « capital », qui peut être utilisé dans le contexte de la finance, du droit, de la géographie ou de la grammaire. Si une requête comprend des termes du même espace vectoriel (par exemple, « capital » et « investissement »), un document contenant des unités lexicales d’inscrivant dans le même cluster obtiendra un scoring plus élevé qu’un autre n’en contenant pas.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Représentation vectorielle du contexte" border="true":::

1. Le @search.rerankerScore est attribué à chaque document en fonction de la pertinence sémantique de la légende.

1. Une fois tous les documents notés, ils sont répertoriés dans l’ordre décroissant par score, et inclus dans la charge utile de la réponse à la requête. La charge utile comprend des réponses, du texte brut et des légendes en surbrillance, ainsi que tous les champs que vous avez marqués comme pouvant être extraits ou spécifiés dans une clause select.

## <a name="next-steps"></a>Étapes suivantes

Le classement sémantique est proposé sur des niveaux standard, dans des régions spécifiques. Pour plus d’informations sur la disponibilité et l’inscription, consultez [Disponibilité et tarification](semantic-search-overview.md#availability-and-pricing). Un nouveau type de requête active le classement et les structures de réponse de recherche sémantique. Pour commencer, [créez une requête sémantique](semantic-how-to-query-request.md).

Vous pouvez également consulter les articles suivants sur le classement par défaut. Le classement sémantique dépend du classeur de similarité pour retourner les résultats initiaux. La connaissance de l’exécution et du classement des requêtes vous apportera une bonne compréhension du fonctionnement de l’ensemble du processus.

+ [Recherche en texte intégral dans Recherche cognitive Azure](search-lucene-query-architecture.md)
+ [Similarité et scoring dans Recherche cognitive Azure](index-similarity-and-scoring.md)
+ [Analyseurs pour le traitement de texte dans Recherche cognitive Azure](search-analyzers.md)