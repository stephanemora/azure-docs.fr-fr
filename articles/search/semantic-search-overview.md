---
title: Recherche sémantique
titleSuffix: Azure Cognitive Search
description: Découvrez comment la Recherche cognitive utilise les modèles de recherche sémantique de Deep Learning (apprentissage profond) à partir de Bing pour rendre les résultats de recherche plus intuitifs.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2021
ms.custom: references_regions
ms.openlocfilehash: 4e6b30e6b6df02bac366e2907f33fd2bda13716f
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109789800"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Recherche sémantique dans la Recherche cognitive Azure

> [!IMPORTANT]
> La recherche sémantique est en préversion publique, disponible via l’API REST en préversion et le portail. Les fonctionnalités en préversion sont proposées telles quelles, sous des [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), et ne sont pas garanties d’avoir la même implémentation lors de la mise à la disposition générale. Ces fonctionnalités sont facturables. Pour plus d’informations, consultez [Disponibilité et tarifs](semantic-search-overview.md#availability-and-pricing).

La recherche sémantique est une collection de fonctionnalités de requête qui ajoutent une pertinence sémantique et une compréhension linguistique aux résultats de recherche. Cet article est une présentation générale de la recherche sémantique dans son ensemble, avec des descriptions de chaque fonctionnalité et de la manière dont elles fonctionnent collectivement. La vidéo intégrée décrit la technologie, et la section à la fin couvre la disponibilité et la tarification.

La recherche sémantique est une fonctionnalité Premium. Nous vous recommandons cet article pour vous familiariser avec le sujet, mais si vous préférez commencer sans plus attendre, procédez comme suit :

1. [Vérifiez les exigences régionales et de niveau de service](#availability-and-pricing).
1. [Inscrivez-vous au programme en préversion](https://aka.ms/SemanticSearchPreviewSignup).
1. Une fois accepté, créez ou modifiez des requêtes pour renvoyer [des légendes et des surlignages sémantiques](semantic-how-to-query-request.md).
1. Ajoutez quelques propriétés de requête supplémentaires pour renvoyer également des [réponses sémantiques](semantic-answers.md).
1. Vous pouvez également inclure une propriété de [vérification orthographique](speller-how-to-add.md) pour optimiser la précision et le rappel.

## <a name="what-is-semantic-search"></a>Qu’est-ce que la recherche sémantique ?

La recherche sémantique est une couche facultative d’IA liée aux requêtes. Lorsqu’elle est activée sur votre service de recherche, elle étend le pipeline d’exécution de requête classique de deux manières. Tout d’abord, elle ajoute un modèle facultatif de classement sémantique. Puis, elle renvoie des légendes et des réponses dans la réponse.

Le *classement sémantique* recherche le contexte et la relation entre les termes, en élevant les correspondances plus significatives en fonction de la requête. La compréhension du langage recherche des résumés ou des *légendes* et des *réponses* dans votre contenu et les inclut dans la réponse, qui peut ensuite être affichée sur une page de résultats de recherche pour une expérience de recherche plus productive.

Des modèles préformés de pointe sont utilisés pour la récapitulation et le classement. Pour maintenir les performances rapides que les utilisateurs attendent de la recherche, la synthèse sémantique et le classement sont appliqués uniquement aux 50 premiers résultats, comme indiqué par l’[algorithme de notation de similarité par défaut](index-similarity-and-scoring.md#similarity-ranking-algorithms). À l’aide de ces résultats en tant que corpus de documents, le classement sémantique réévalue ces résultats en fonction de la force sémantique de la correspondance.

La technologie sous-jacente est issue de Bing et Microsoft Research, et est intégrée à l’infrastructure Recherche cognitive comme module complémentaire. Pour plus d’informations sur les investissements en matière de recherche et d’intelligence artificielle portant sur la recherche sémantique, consultez [How AI from Bing is powering Azure Cognitive Search (Blog Microsoft Research)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

La vidéo suivante fournit une vue des fonctionnalités.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="feature-description-and-workflow"></a>Description de la fonctionnalité et workflow

La recherche sémantique améliore la précision et le rappel avec l’ajout des fonctionnalités suivantes :

| Fonctionnalité | Description |
|---------|-------------|
| [Vérification orthographique](speller-how-to-add.md) | Corrige les fautes de frappe avant que les termes de la requête n’atteignent le moteur de recherche. |
| [Classement sémantique](semantic-ranking.md) | Utilise le contexte ou la signification sémantique pour calculer un nouveau score de pertinence. |
| [Légendes et mises en surbrillance sémantiques](semantic-how-to-query-request.md) | Phrases ou morceaux de phrases d’un document qui résument le mieux le contenu, en mettant en évidence les passages clés pour faciliter l’analyse. Les légendes qui synthétisent un résultat sont utiles lorsque les champs de contenu individuels sont trop denses pour la page résultats. Le texte mis en surbrillance élève les termes et expressions les plus pertinents afin que les utilisateurs puissent déterminer rapidement la raison pour laquelle une correspondance a été considérée comme pertinente. |
| [Réponses sémantiques](semantic-answers.md) | Sous-structure facultative et supplémentaire renvoyée par une requête sémantique. Elle fournit une réponse directe à une requête qui ressemble à une question. |

### <a name="order-of-operations"></a>Ordre des opérations

Les composants de la recherche sémantique étendent le pipeline d’exécution des requêtes existant dans les deux sens. Si vous activez la correction orthographique, le [vérificateur orthographique](speller-how-to-add.md) corrige les fautes de frappe au début, avant que les termes de la requête n’atteignent le moteur de recherche.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Composants sémantiques dans une exécution de requête" border="true":::

L’exécution de la requête se poursuit normalement, avec l’analyse des termes, l’analyse et les analyses sur les index inversés. Le moteur récupère les documents à l’aide de la correspondance de jetons et évalue les résultats à l’aide de l’[algorithme de notation de similarité par défaut](index-similarity-and-scoring.md#similarity-ranking-algorithms). Les scores sont calculés en fonction du degré de similarité linguistique entre les termes de la requête et les termes correspondants dans l’index. Si vous les définissez, les profils de scoring (ou notation) sont également appliqués durant cette phase. Les résultats sont ensuite transmis au sous-système de recherche sémantique.

Dans l’étape de préparation, le corpus de documents renvoyé par le jeu de résultats initial est analysé au niveau de la phrase et du paragraphe pour rechercher les passages qui résument chaque document. Contrairement à la recherche par mot clé, cette étape utilise la lecture et la compréhension des machines pour évaluer le contenu. Au cours de cette étape du traitement du contenu, une requête sémantique retourne des [légendes](semantic-how-to-query-request.md) et des [réponses](semantic-answers.md). Pour les formuler, la recherche sémantique utilise la représentation linguistique afin d’extraire et de mettre en évidence les passages clés qui résument le mieux un résultat. Si la requête de recherche est une question et que des réponses sont demandées, la réponse comprend un passage de texte qui répond au mieux à la question, telle qu’elle est exprimée par la requête de recherche. 

Pour les légendes et les réponses, du texte existant est utilisé dans la formulation. Les modèles sémantiques ne composent pas de nouvelles phrases ou expressions à partir du contenu disponible, pas plus qu’il n’applique la logique nécessaire pour arriver à de nouvelles conclusions. En bref, le système ne renvoie jamais de contenu qui n’existe pas déjà.

Les résultats sont ensuite réévalués en fonction de la [similitude conceptuelle](semantic-ranking.md) des termes de requête.

Pour utiliser les fonctionnalités sémantiques dans des requêtes, vous devez apporter de petites modifications à la [requête de recherche](semantic-how-to-query-request.md), mais aucune configuration ni réindexation supplémentaire n’est nécessaire.

## <a name="availability-and-pricing"></a>Disponibilité et tarification

La recherche sémantique est disponible via [l’enregistrement d’inscription](https://aka.ms/SemanticSearchPreviewSignup). Dans le cadre du lancement de la préversion, du 2 mars à début juin, des fonctionnalités sémantiques sont proposées gratuitement.

| Fonctionnalité | Niveau | Région | Inscription | Tarification projetée |
|---------|------|--------|---------------------|-------------------|
| Recherche sémantique (légendes, surlignages, réponses) | Niveau Standard (S1, S2, S3) | USA Centre Nord, USA Ouest, USA Ouest 2, USA Est 2, Europe Nord, Europe Ouest | Obligatoire | À compter du 1er juin, la tarification prévue est de 500 USD par mois pour les 250 000 premières requêtes et de 2 USD par tranche de 1 000 requêtes supplémentaires.  |
| Vérification orthographique | Quelconque | USA Centre Nord, USA Ouest, USA Ouest 2, USA Est 2, Europe Nord, Europe Ouest | Obligatoire | Aucun (gratuit) |

Il existe un [enregistrement d’inscription](https://aka.ms/SemanticSearchPreviewSignup) pour les fonctionnalités sémantiques et la vérification orthographique. 

Vous pouvez utiliser la vérification orthographique sans la recherche sémantique et ce, gratuitement. Des frais de recherche sémantique sont prélevés lorsque les demandes de requête incluent `queryType=semantic` et que la chaîne de recherche n’est pas vide (par exemple, `search=pet friendly hotels in new york`). La recherche vide (requêtes où `search=*`) n’est pas facturée.

Vous trouverez des informations de tarification détaillées sur la [page de tarification de la Recherche cognitive](https://azure.microsoft.com/pricing/details/search/) et dans [Estimer et gérer les coûts](search-sku-manage-costs.md).

## <a name="next-steps"></a>Étapes suivantes

[Inscrivez-vous](https://aka.ms/SemanticSearchPreviewSignup) à la préversion sur un service de recherche conforme aux exigences de niveau et de région indiquées dans la section précédente.

Lorsque votre service est prêt, [créez une requête sémantique](semantic-how-to-query-request.md) pour voir le classement sémantique en action.