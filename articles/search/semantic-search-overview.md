---
title: Recherche sémantique
titleSuffix: Azure Cognitive Search
description: Découvrez comment la Recherche cognitive utilise les modèles de recherche sémantique de Deep Learning (apprentissage profond) à partir de Bing pour rendre les résultats de recherche plus intuitifs.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.custom: references_regions
ms.openlocfilehash: 1b50fbbdd38d1bb24c1732c465784c3ddb757e3f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114454779"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Recherche sémantique dans la Recherche cognitive Azure

> [!IMPORTANT]
> La recherche sémantique est en préversion publique sous [Conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Elle est disponible via le portail Azure, la préversion de l’API REST et les Kits de développement logiciel (SDK) bêta. Ces fonctionnalités sont facturables. Pour plus d’informations, consultez [Disponibilité et tarification](semantic-search-overview.md#availability-and-pricing).

La recherche sémantique est une collection de fonctionnalités de requête qui ajoutent une pertinence sémantique et une compréhension linguistique aux résultats de recherche. Cet article est une présentation générale de la recherche sémantique dans son ensemble, avec des descriptions de chaque fonctionnalité et de la manière dont elles fonctionnent collectivement. La vidéo intégrée décrit la technologie, et la section à la fin couvre la disponibilité et la tarification.

La recherche sémantique est une fonctionnalité Premium. Nous vous recommandons cet article pour vous familiariser avec le sujet, mais si vous préférez commencer sans plus attendre, procédez comme suit :

> [!div class="checklist"]
> * [Vérifiez les exigences régionales et de niveau de service](#availability-and-pricing).
> * [Inscrivez-vous au programme en préversion](https://aka.ms/SemanticSearchPreviewSignup). Le traitement de la demande peut prendre jusqu’à deux jours ouvrables.
> * Une fois accepté, créez ou modifiez des requêtes pour [renvoyer des légendes et des surlignages sémantiques](semantic-how-to-query-request.md).
> * Ajoutez quelques propriétés de requête supplémentaires pour renvoyer également des [réponses sémantiques](semantic-answers.md).
> * Vous pouvez éventuellement appeler la [vérification orthographique](speller-how-to-add.md) pour optimiser la précision et le rappel.

## <a name="what-is-semantic-search"></a>Qu’est-ce que la recherche sémantique ?

La recherche sémantique est un ensemble de fonctionnalités qui améliorent la qualité des résultats de recherche. Lorsqu’elle est activée sur votre service de recherche, elle étend le pipeline d’exécution de requêtes de deux manières. Tout d’abord, elle ajoute le classement secondaire sur un jeu de résultats initial, en promouvant les résultats les plus sémantiquement pertinents en haut de la liste. Deuxièmement, elle extrait et retourne des légendes et des réponses dans la réponse, que vous pouvez afficher sur une page de recherche pour améliorer l’expérience de recherche de l’utilisateur.

## <a name="how-semantic-ranking-works"></a>Fonctionnement du classement sémantique

Le *classement sémantique* recherche le contexte et la relation entre les termes, en élevant les correspondances plus significatives en fonction de la requête. La compréhension du langage recherche des résumés ou des *légendes* et des *réponses* dans votre contenu et les inclut dans la réponse, qui peut ensuite être affichée sur une page de résultats de recherche pour une expérience de recherche plus productive.

Des modèles préformés de pointe sont utilisés pour la récapitulation et le classement. Pour maintenir les performances rapides que les utilisateurs attendent de la recherche, la synthèse sémantique et le classement sont appliqués uniquement aux 50 premiers résultats, comme indiqué par l’[algorithme de notation de similarité par défaut](index-similarity-and-scoring.md#similarity-ranking-algorithms). À l’aide de ces résultats en tant que corpus de documents, le classement sémantique réévalue ces résultats en fonction de la force sémantique de la correspondance.

La technologie sous-jacente est issue de Bing et Microsoft Research, et est intégrée à l’infrastructure Recherche cognitive comme module complémentaire. Pour plus d’informations sur les investissements en matière de recherche et d’intelligence artificielle portant sur la recherche sémantique, consultez [How AI from Bing is powering Azure Cognitive Search (Blog Microsoft Research)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

La vidéo suivante fournit une vue des fonctionnalités.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="features-in-semantic-search"></a>Fonctionnalités de la recherche sémantique

La recherche sémantique améliore la précision et le rappel grâce à ces nouvelles fonctionnalités :

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

## <a name="semantic-capabilities-and-limitations"></a>Fonctionnalités et limitations sémantiques

La recherche sémantique étant une technologie assez récente, il est important de définir des attentes quant à ce qu’elle peut et ne peut pas faire. Elle améliore la qualité des résultats de recherche de deux manières :

* Tout d’abord, elle promeut des correspondances qui sont sémantiquement plus proches de l’intention de la requête d’origine.

* Deuxièmement, les résultats sont plus faciles à utiliser lorsque les légendes et les réponses éventuelles sont présentes sur la page.

La recherche sémantique n’est pas avantageuse dans tous les scénarios, et avant de poursuivre, assurez-vous que vous disposez d’un contenu qui peut utiliser ses fonctionnalités. Les modèles de langage dans la recherche sémantique fonctionnent mieux sur le contenu pouvant faire l’objet d’une recherche, riche en informations et structuré comme PROSE. Par exemple, lors de l’évaluation de votre contenu pour les réponses, les modèles recherchent et extraient une chaîne verbatim ressemblant à une réponse, mais ne composent pas de nouvelles chaînes en tant que réponses à une requête ou en tant que légendes pour un document correspondant. Pour répondre à la question « Quelle voiture a le meilleur kilométrage », un index doit avoir des expressions telles que « Les voitures hybrides offrent le meilleur kilométrage des voitures sur le marché ».

La recherche sémantique ne peut pas mettre en corrélation ni déduire des informations à partir d’éléments de contenu différents au sein d’un document ou d’un corpus de documents. Par exemple, si une requête cherche des « hôtels situés dans le désert » sans fournir la moindre indication géographique, le moteur ne trouve aucun hôtel situé en Arizona ou au Nevada, même si ces deux États ont des hôtels et des déserts. De même, si la requête comprend la clause « au cours des 5 dernières années », le moteur ne calcule pas un intervalle de temps en fonction de la date du jour. Dans le service Recherche cognitive, les mécanismes qui peuvent être utiles pour les scénarios ci-dessus incluent des [cartes de synonymes](search-synonyms.md) qui vous permettent de créer des associations entre des termes apparemment différents, ou des [filtres de date](search-query-odata-filter.md) spécifiés sous la forme d’une expression OData.

## <a name="availability-and-pricing"></a>Disponibilité et tarification

La recherche sémantique est disponible via [l’enregistrement d’inscription](https://aka.ms/SemanticSearchPreviewSignup). Il existe une inscription pour la recherche sémantique et la vérification orthographique.

| Fonctionnalité | Niveau | Région | Inscription | Tarifs |
|---------|------|--------|---------------------|-------------------|
| Recherche sémantique (légendes, surlignages, réponses) | Niveau Standard (S1, S2, S3) | USA Centre Nord, USA Ouest, USA Ouest 2, USA Est 2, Europe Nord, Europe Ouest | Obligatoire | [Page de tarification de la Recherche cognitive](https://azure.microsoft.com/pricing/details/search/)  |
| Vérification orthographique | Quelconque | USA Centre Nord, USA Ouest, USA Ouest 2, USA Est 2, Europe Nord, Europe Ouest | Obligatoire | Aucun (gratuit) |

Vous pouvez utiliser la vérification orthographique sans la recherche sémantique et ce, gratuitement. Des frais de recherche sémantique sont prélevés lorsque les demandes de requête incluent `queryType=semantic` et que la chaîne de recherche n’est pas vide (par exemple, `search=pet friendly hotels in new york`). La recherche vide (requêtes where `search=*`) n’est pas facturée, même si QueryType a la valeur `semantic`.

Si vous ne souhaitez pas la fonctionnalité de recherche sémantique sur votre service de recherche, vous pouvez [désactiver la recherche sémantique](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchsemanticsearch) pour éviter toute utilisation et facturation accidentelles.

## <a name="next-steps"></a>Étapes suivantes

[Inscrivez-vous](https://aka.ms/SemanticSearchPreviewSignup) à la préversion sur un service de recherche conforme aux exigences de niveau et de région indiquées dans la section précédente.

Le traitement de la demande peut prendre jusqu’à deux jours ouvrables. Une fois votre service prêt, [créez une requête sémantique](semantic-how-to-query-request.md) pour évaluer son niveau de performance sur votre contenu.
