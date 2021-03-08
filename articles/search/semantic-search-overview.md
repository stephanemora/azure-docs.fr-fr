---
title: Recherche sémantique
titleSuffix: Azure Cognitive Search
description: Découvrez comment la Recherche cognitive utilise les modèles de recherche sémantique de Deep Learning (apprentissage profond) à partir de Bing pour rendre les résultats de recherche plus intuitifs.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: e9cbb7daf61397064bd79f30d851d96fdf63f5a0
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203230"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Recherche sémantique dans la Recherche cognitive Azure

> [!IMPORTANT]
> Les fonctionnalités de la recherche sémantique sont disponibles en préversion publique, uniquement par le biais de l’API REST en préversion. Les fonctionnalités d’évaluation sont proposées telles quelles, dans le cadre de [Conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La recherche sémantique est un ensemble de fonctionnalités liées aux requêtes qui prennent en charge une expérience de requête de meilleure qualité et plus naturelle. Ces fonctionnalités incluent le reclassement sémantique des résultats de recherche, ainsi que la génération de légendes et de réponses avec une mise en surbrillance sémantique. Les 50 premiers résultats retournés par le [moteur de recherche en texte intégral](search-lucene-query-architecture.md) sont reclassés afin de trouver les correspondances les plus pertinentes.

La technologie sous-jacente, tirant parti des investissements réalisés par Bing et Microsoft Research, est intégrée à l’infrastructure de la Recherche cognitive. Pour l’utiliser, vous devez apporter de petites modifications à la requête de recherche, mais aucune configuration ni réindexation supplémentaire n’est nécessaire.

Les fonctionnalités en préversion publique sont les suivantes :

+ Le modèle de classement sémantique qui note les résultats en fonction du contexte ou de la signification sémantique des termes de la requête de recherche
+ Légendes sémantiques mettant en surbrillance les passages pertinents
+ Réponses sémantiques à la requête, également formulées à partir des résultats
+ Vérification orthographique corrigeant les fautes de frappe avant que les termes de la requête n’atteignent le moteur de recherche

## <a name="semantic-search-architecture"></a>Architecture de la recherche sémantique

Les composants de la recherche sémantique sont superposés sur le pipeline d’exécution de la requête existant. La correction orthographique (non illustrée dans le schéma) améliore le rappel en corrigeant les fautes de frappe dans les termes des requêtes individuelles. Une fois l’analyse effectuée, le moteur de recherche récupère les documents qui correspondent à la requête et leur attribue une note à l’aide de l’[algorithme de notation par défaut](index-similarity-and-scoring.md#similarity-ranking-algorithms), BM25 ou classic, en fonction du moment où le service a été créé. Les profils de scoring (ou notation) sont également appliqués durant cette phase. 

Après avoir reçu les 50 premières correspondances, l’[algorithme de classement sémantique](semantic-how-to-query-response.md) réévalue le corpus de documents. Les résultats peuvent inclure plus de 50 correspondances, mais seules les 50 premières correspondances sont reclassées. Pour le classement, l’algorithme utilise le Machine Learning et l’apprentissage de transfert pour réattribuer une note aux documents en fonction de leur pertinence par rapport à l’intention de la requête.

Pour créer les légendes et les réponses, il utilise des modèles de représentation de langage. Dans une légende, l’algorithme utilise un modèle de langage développé par Bing pour extraire les légendes avec des mises en surbrillance permettant de résumer au mieux les résultats de la requête. Si la requête de recherche est une question et que des réponses sont demandées, un modèle de langage similaire identifie les passages de texte qui répondent au mieux à la question telle qu’elle est exprimée par la requête de recherche.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Composants sémantiques dans un pipeline de requête" border="true":::

## <a name="availability-and-pricing"></a>Disponibilité et tarification

Le classement sémantique est disponible par le biais de l’[enregistrement d’inscription](https://aka.ms/SemanticSearchPreviewSignup), auprès des services de recherche créés au niveau Standard (S1, S2, S3) et situés dans l’une des régions suivantes : USA Centre Nord, USA Ouest, USA Ouest 2, USA Est 2, Europe Nord, Europe Ouest. Un service de recherche existant au niveau S1 ou supérieur dans les régions indiquées est éligible pour la préversion (inutile de créer un nouveau service).

La correction orthographique est disponible dans les mêmes régions, mais ne présente aucune restriction de niveau ni d’obligation d’inscription. 

Dans le cadre du lancement de la préversion, du 2 mars au 1er avril, la correction orthographique et le classement sémantique sont proposés gratuitement. Après le 1er avril, les coûts de calcul liés à l’exécution de cette fonctionnalité deviendront un événement facturable. Le coût attendu est d’environ 500 dollars US par mois pour 250 000 requêtes. Vous trouverez des informations détaillées sur les coûts, présentées à la [page des tarifs de la Recherche cognitive](https://azure.microsoft.com/pricing/details/search/) et dans [Estimer et gérer les coûts](search-sku-manage-costs.md).

## <a name="next-steps"></a>Étapes suivantes

Un nouveau type de requête permet le classement de pertinence et les structures de réponse de la recherche sémantique. [Créer une requête sémantique](semantic-how-to-query-request.md) pour commencer. Vous pouvez aussi consulter les articles suivants pour obtenir des informations connexes.

+ [Ajouter une vérification orthographique aux termes de la requête](speller-how-to-add.md)
+ [Classement sémantique et réponses](semantic-how-to-query-response.md)