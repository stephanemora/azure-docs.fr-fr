---
title: 'Azure Front Door : ensemble de règles'
description: Cet article offre une vue d’ensemble de la fonctionnalité d’ensemble de règles d’Azure Front Door Standard/Premium.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 8e6ceebc9e92dabe66baeb9aeff0ae9692e2bdad
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097707"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>Qu’est-ce qu’un ensemble de règles dans Azure Front Door Standard/Premium (préversion) ?

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Un ensemble de règles est un moteur de règles personnalisé qui regroupe une combinaison de règles en un ensemble unique que vous pouvez associer à plusieurs routes. L’ensemble de règles vous permet de personnaliser la façon dont les requêtes sont traitées à la périphérie et la façon dont elles sont traitées par Azure Front Door.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="common-supported-scenarios"></a>Scénarios courants pris en charge

* Implémentation d’en-têtes de sécurité pour prévenir les vulnérabilités des navigateurs comme HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-Frame-Options ainsi que des en-têtes Access-Control-Allow-Origin pour les scénarios de partage des ressources cross-origin (CORS). Les attributs basés sur la sécurité peuvent également être définis avec des cookies.

* Routage des requêtes vers les versions mobiles ou pour appareil de bureau de votre application en fonction du type d’appareil client.

* Utilisation de fonctionnalités de redirection pour retourner les redirections 301, 302, 307 et 308 au client afin de le diriger vers de nouveaux noms d’hôte, de nouveaux chemins, une nouvelle chaîne de requête ou de nouveaux protocoles.

* Modification dynamique de la configuration de la mise en cache de votre route en fonction des requêtes entrantes.

* Réécriture du chemin de l’URL de la requête et transfert de la requête à l’origine appropriée de votre groupe d’origines configurées.

* Ajout, modification ou suppression de l’en-tête de la requête/réponse pour masquer des informations sensibles ou pour capturer des informations importantes dans les en-têtes.

* Prise en charge de variables de serveur pour modifier dynamiquement les en-têtes de requête/réponse ou les chemins de réécriture d’URL/chaînes de requête, par exemple, quand une nouvelle page est chargée ou qu’un formulaire est publié. Actuellement, la variable de serveur est prise en charge uniquement sur les **[actions d’ensemble de règles](concept-rule-set-actions.md)** .

## <a name="architecture"></a>Architecture

L’ensemble de règles traite les requêtes à la périphérie. Quand une requête arrive sur votre point de terminaison Azure Front Door Standard/Premium, WAF est exécuté en premier, suivi des paramètres configurés dans la route. Ces paramètres incluent l’ensemble de règles associé à la route. Les ensembles de règles sont traités en intégralité dans la route. Il en va de même pour les règles d’un ensemble de règles. Pour que toutes les actions de chaque règle soient exécutées, toutes les conditions de correspondance au sein d’une règle doivent être satisfaites. Si une requête ne correspond à aucune des conditions de la configuration de votre ensemble de règles, seules les configurations de la route sont exécutées.

Si l’option **Arrêter l’évaluation des règles restantes** est cochée, aucun des autres ensembles de règles associés à la route n’est exécuté.  

### <a name="example"></a>Exemple

Dans le diagramme suivant, les stratégies WAF sont exécutées en premier. Un ensemble de règles est configuré pour ajouter un en-tête de réponse. L’en-tête modifie alors la durée maximale du contrôle de cache si la condition de correspondance est remplie.

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="Diagramme montrant l’architecture d’un ensemble de règles." lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>Terminologie

Avec l’ensemble de règles Azure Front Door, vous pouvez créer une combinaison de configurations de règles, chacune composée d’un ensemble de règles. Cette section présente quelques termes clés que vous rencontrerez quand vous configurerez votre ensemble de règles.

Pour en savoir plus sur la limite de quota, consultez [Abonnement Azure et limites, quotas et contraintes du service](../../azure-resource-manager/management/azure-subscription-service-limits.md).

* *Ensemble de règles* : Ensemble de règles associé à une ou plusieurs [routes](concept-route.md). Chaque configuration est limitée à 25 règles. Vous pouvez créer jusqu’à 10 configurations.

* *Règle d’un ensemble de règles* : Règle pouvant avoir jusqu’à 10 conditions de correspondance et 5 actions. Les règles n’existent que localement dans un ensemble de règles. Elles ne peuvent pas être exportées pour être utilisées dans différents ensembles de règles. Les utilisateurs peuvent créer la même règle dans plusieurs ensembles de règles.

* *Condition de correspondance* : Il existe de nombreuses conditions de correspondance qui peuvent être utilisées pour analyser vos requêtes entrantes. Une règle peut contenir jusqu’à 10 conditions de correspondance. Les conditions de correspondance sont évaluées avec un opérateur **AND**. *L’expression régulière est prise en charge dans les conditions*. Pour obtenir une liste complète des conditions de correspondance, consultez cet [article sur les conditions d’ensemble de règles](concept-rule-set-match-conditions.md).

* *Action* : Les actions dictent la manière dont AFD traite les requêtes entrantes en fonction des conditions de correspondance. Vous pouvez modifier les comportements de mise en cache et les en-têtes de requête/de réponse et effectuer une réécriture d’URL et une redirection d’URL. *Les variables serveur sont prises en charge dans les actions*. Une règle peut contenir jusqu’à 10 conditions de correspondance. Pour obtenir une liste complète des actions, consultez cet [article sur les actions d’ensemble de règles](concept-rule-set-actions.md).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer une instance Front Door Standard/Premium](create-front-door-portal.md).
* Découvrez comment configurer votre premier [ensemble de règles](how-to-configure-rule-set.md).
 
