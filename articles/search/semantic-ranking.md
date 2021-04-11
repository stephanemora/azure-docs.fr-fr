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
ms.openlocfilehash: c3a0a8bd5805757b92e3f5b046335c8883b4ba72
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888921"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Classement sémantique dans Recherche cognitive Azure

> [!IMPORTANT]
> Les fonctionnalités de recherche sémantique sont disponibles en préversion publique, uniquement via l’API REST en préversion et le portail. Les fonctionnalités en préversion sont proposées telles quelles, sous des [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), et ne sont pas garanties d’avoir la même implémentation lors de la mise à la disposition générale. Ces fonctionnalités sont facturables. Pour plus d’informations, consultez [Disponibilité et tarifs](semantic-search-overview.md#availability-and-pricing).

Le classement sémantique est une extension du pipeline d’exécution de requête qui améliore la précision en reclassant les meilleures correspondances d’un jeu de résultats initial. Le classement sémantique s’appuie sur de grands réseaux basés sur un transformateur, formés pour capturer la sémantique de termes de requête, par opposition à la correspondance linguistique de mots clés. Contrairement à l’[algorithme de classement de similarité par défaut](index-ranking-similarity.md), le classeur sémantique utilise le contexte et le sens des mots pour déterminer la pertinence.

Le classement sémantique est gourmand en ressources et en temps. Pour terminer le traitement dans le respect de latence attendue d’une opération de requête, les entrées du classeur sémantique sont consolidées et réduites afin que les étapes de résumé et de reclassement sous-jacentes puissent être effectuées le plus rapidement possible.

## <a name="preparation-for-semantic-ranking"></a>Préparation pour le classement sémantique

Avant d’évaluer la pertinence, le contenu doit être réduit à un certain nombre d’entrées que le classeur sémantique peut gérer efficacement . La réduction du contenu comprend la séquence d’étapes suivante.

1. La réduction du contenu commence par l’utilisation du jeu de résultats initial que renvoie l’[algorithme de classement](index-ranking-similarity.md) par défaut qu’utilise la recherche par mot clé. Les résultats de la recherche peuvent inclure jusqu’à 1 000 concordances, mais le classement sémantique ne traite que les 50 premières. 

   Selon la requête, le nombre de résultats initiaux peut être sensiblement inférieur à 50 à concordances trouvées. Quel que soit le nombre de documents, le jeu de résultats initial constitue le corpus de documents pour le classement sémantique.

1. Dans le corpus de documents, le contenu de chaque champ dans « searchFields » est extrait et combiné dans une chaîne longue.

1. Toutes les chaînes excessivement longues sont découpées pour garantir que la longueur totale est conforme aux exigences d’entrée de l’étape de résumé. Cet exercice de découpage est la raison pour laquelle il est important de placer les champs courts en premier dans « searchFields », afin de s’assurer de leur inclusion dans la chaîne. Si vous avez des documents très volumineux avec des champs contenant beaucoup de texte, tout ce qui se trouve après la limite maximale est ignoré.

Chaque document est désormais représenté par une seule chaîne longue.

> [!NOTE]
> Les entrées de paramètre pour les modèles sont des jetons, ou unités lexicales, pas des caractères ou des mots. La segmentation du texte en unités lexicales est déterminée en partie par l’affectation de l’analyseur à des champs pouvant faire l’objet d’une recherche. Pour obtenir des informations sur la façon dont les chaînes sont segmentées en unités lexicales, vous pouvez examiner la sortie de jeton d’un analyseur à l’aide de l’[API REST Analyseur de test](/rest/api/searchservice/test-analyzer).

## <a name="summarization"></a>Résumé

Après une réduction de chaîne, il est possible de transmettre les entrées réduites via des modèles de compréhension et de représentation linguistique de lecture automatique qui déterminent les expressions et les phrases résumant le mieux le document par rapport à la requête.

Les entrées pour le résumé sont les chaînes longues obtenues pour chaque document durant la phase de préparation. À partir d’une entrée donnée, le modèle de résumé trouve le passage représentant le mieux le document correspondant. Ce passage constitue également une [légende sémantique](semantic-how-to-query-request.md) pour le document. Chaque légende est disponible en texte brut, avec des mises en surbrillance, et comprend moins de 200 mots par document.

Une [réponse sémantique](semantic-answers.md) est également retournée si vous avez spécifié le paramètre « answers », si la requête a été posée en tant que question, et si un passage peut être trouvé dans la chaîne longue susceptible de fournir une réponse à la question.

## <a name="scoring-and-ranking"></a>Scoring et classement

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