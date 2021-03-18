---
title: Recherche sémantique
titleSuffix: Azure Cognitive Search
description: Découvrez comment la Recherche cognitive utilise les modèles de recherche sémantique de Deep Learning (apprentissage profond) à partir de Bing pour rendre les résultats de recherche plus intuitifs.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.custom: references_regions
ms.openlocfilehash: 19b7f9bc19bec989e524dce7172037025e2fe4fd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432977"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Recherche sémantique dans la Recherche cognitive Azure

> [!IMPORTANT]
> Les fonctionnalités de la recherche sémantique sont disponibles en préversion publique, uniquement par le biais de l’API REST en préversion. Les fonctionnalités d’évaluation sont proposées telles quelles, dans le cadre de [Conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La recherche sémantique est un ensemble de fonctionnalités liées aux requêtes qui prennent en charge une expérience de requête de meilleure qualité et plus naturelle. Ces fonctionnalités incluent le reclassement sémantique des résultats de recherche, ainsi que la génération de légendes et de réponses avec une mise en surbrillance sémantique. Les 50 premiers résultats retournés par le [moteur de recherche en texte intégral](search-lucene-query-architecture.md) sont reclassés afin de trouver les correspondances les plus pertinentes.

La technologie sous-jacente est issue de Bing et Microsoft Research, et est intégrée à l’infrastructure Recherche cognitive. Pour plus d’informations sur les investissements en matière de recherche et d’intelligence artificielle portant sur la recherche sémantique, consultez [How AI from Bing is powering Azure Cognitive Search (Blog Microsoft Research)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

Pour utiliser la recherche sémantique dans des requêtes, vous devez apporter de petites modifications à la requête de recherche, mais aucune configuration ni réindexation supplémentaire n’est nécessaire.

Les fonctionnalités en préversion publique sont les suivantes :

+ Modèle de classement sémantique qui utilise le contexte ou la signification sémantique pour calculer un score de pertinence
+ Légendes sémantiques qui résument les passages clés d’un résultat en vue de simplifier l’analyse
+ Réponses sémantiques à la requête, si celle-ci est une question
+ Mises en surbrillance sémantiques qui mettent l’accent sur les expressions et termes clés
+ Vérification orthographique corrigeant les fautes de frappe avant que les termes de la requête n’atteignent le moteur de recherche

## <a name="availability-and-pricing"></a>Disponibilité et tarification

Le classement sémantique est disponible par le biais de l’[enregistrement d’inscription](https://aka.ms/SemanticSearchPreviewSignup), auprès des services de recherche créés au niveau Standard (S1, S2, S3) et situés dans l’une des régions suivantes : USA Centre Nord, USA Ouest, USA Ouest 2, USA Est 2, Europe Nord, Europe Ouest. La correction orthographique est disponible dans les mêmes régions, mais ne présente aucune restriction de niveau. Si vous avez un service existant qui répond aux critères de niveau et de région, seule l’inscription est requise.

Dans le cadre du lancement de la préversion, du 2 mars au 1er avril, la correction orthographique et le classement sémantique sont proposés gratuitement. Après le 1er avril, les coûts de calcul liés à l’exécution de cette fonctionnalité deviendront un événement facturable. Le coût attendu est d’environ 500 dollars US par mois pour 250 000 requêtes. Vous trouverez des informations détaillées sur les coûts, présentées à la [page des tarifs de la Recherche cognitive](https://azure.microsoft.com/pricing/details/search/) et dans [Estimer et gérer les coûts](search-sku-manage-costs.md).

## <a name="semantic-search-architecture"></a>Architecture de la recherche sémantique

Les composants de la recherche sémantique sont superposés sur le pipeline d’exécution de la requête existant. La correction orthographique (non illustrée dans le schéma) améliore le rappel en corrigeant les fautes de frappe dans les termes des requêtes individuelles. Une fois l’analyse effectuée, le moteur de recherche récupère les documents qui correspondent à la requête et leur attribue une note à l’aide de l’[algorithme de notation par défaut](index-similarity-and-scoring.md#similarity-ranking-algorithms), BM25 ou classic, en fonction du moment où le service a été créé. Les profils de scoring (ou notation) sont également appliqués durant cette phase.

Après avoir reçu les 50 premières correspondances, le [modèle de classement sémantique](semantic-how-to-query-response.md) réévalue le corpus de documents. Les résultats peuvent inclure plus de 50 correspondances, mais seules les 50 premières correspondances sont reclassées. Pour le classement, le modèle utilise le Machine Learning et l’apprentissage de transfert pour réattribuer une note aux documents en fonction de leur pertinence par rapport à l’intention de la requête.

Pour créer des légendes et des réponses, la recherche sémantique utilise la représentation linguistique afin d’extraire et de mettre en évidence les passages clés qui résument le mieux un résultat. Si la requête de recherche est une question et que des réponses sont demandées, la réponse comprend un passage de texte qui répond au mieux à la question, telle qu’elle est exprimée par la requête de recherche.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Composants sémantiques dans un pipeline de requête" border="true":::

## <a name="next-steps"></a>Étapes suivantes

Un nouveau type de requête permet le classement de pertinence et les structures de réponse de la recherche sémantique.

[Créer une requête sémantique](semantic-how-to-query-request.md) pour commencer. Vous pouvez aussi consulter les articles suivants pour obtenir des informations connexes.

+ [Ajouter une vérification orthographique aux termes de la requête](speller-how-to-add.md)
+ [Classement sémantique et réponses (réponses et légendes)](semantic-how-to-query-response.md)