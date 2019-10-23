---
title: Pare-feu d’applications web Azure - Forum aux questions
description: Cette page fournit des réponses aux questions les plus souvent posées sur Azure Front Door Service.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: f6e0ea7ca5b9b131a8d7d7c2c6546130a7d020b3
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302847"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Forum aux questions sur le pare-feu d’applications web Azure

Cet article répond aux questions courantes sur les fonctionnalités du pare-feu d’applications web Azure (WAF). 

## <a name="what-is-azure-waf"></a>Qu’est-ce qu’Azure WAF ?

Azure WAF est un pare-feu d’applications web qui aide à protéger vos applications web des menaces courantes telles que les injections de code SQL, le script de site à site et autres codes malveillants exploitant des failles de sécurité. Vous pouvez définir une stratégie de pare-feu d’applications web consistant en une combinaison de règles personnalisées et gérées pour contrôler l’accès à vos applications web.

Une stratégie de pare-feu d’applications web Azure peut être appliquée aux applications web hébergées sur les services Application Gateway ou Azure Front Door.

## <a name="what-is-waf-for-azure-front-door-service"></a>Qu’est-ce qu’un pare-feu d’applications web pour le service Azure Front Door Service ? 

Azure Front Door est un réseau de distribution de contenu et d’application hautement évolutif et distribué mondialement. Quand Azure WAF est intégré à Azure Front Door, les attaques par déni de service et sur des applications ciblées sont arrêtées à la périphérie du réseau Azure. Cette protection fonctionne à proximité de la source des attaques avant qu’elles aient la possibilité d’accéder à votre réseau virtuel. Le service WAF pour Azure Front Door Service offre une protection sans sacrifier les performances.

## <a name="does-azure-waf-support-https"></a>Le pare-feu d’applications web prend-il en charge HTTPS ?

Le service Front Door offre le déchargement SSL. Le pare-feu d’applications web est intégré de façon native à Front Door et peut inspecter une demande après qu’elle ait été déchiffrée.

## <a name="does-azure-waf-support-ipv6"></a>Le pare-feu d’applications web prend-il en charge IPv6 ?

Oui. Vous pouvez configurer la restriction IP pour IPv4 et IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Les ensembles de règles gérées sont-ils à jour ?

Nous faisons de notre mieux pour suivre le rythme d’un monde de menaces en constante évolution. Lorsqu’une nouvelle règle est mise à jour, elle est ajoutée à l’ensemble de règles par défaut avec un nouveau numéro de version.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Quelle est la durée de propagation si j’effectue une modification de ma stratégie de pare-feu d’applications web ?

Le déploiement global d’une stratégie de pare-feu d’applications web prend généralement 5 minutes, voire moins.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Les stratégies de pare-feu d’applications web peuvent-elles être différentes d’une région à une autre ?

Une fois intégré au service Front Door, le pare-feu d’applications web constitue une ressource globale. La même configuration s’applique à tous les emplacements Front Door.
 

## <a name="which-azure-waf-options-should-i-choose"></a>Quelles options de pare-feu d’applications web Azure dois-je choisir ?

Il y a deux options lors de l’application de stratégies de pare-feu d’applications web dans Azure. Le pare-feu d’applications web avec Azure Front Door est une solution distribuée mondialement de sécurité en périphérie. Le pare-feu d’applications web avec Application Gateway est une solution dédiée et régionale. Nous vous recommandons de choisir une solution basée sur vos exigences générales en matière de performances et de sécurité. Pour en savoir plus, consultez [Équilibrage de charge avec la suite de livraison d’application Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Prenez-vous en charge les mêmes fonctionnalités de pare-feu d’applications web dans toutes les plateformes intégrées ?

Actuellement, les règles ModSec CRS 2.2.9 et CRS 3.0 sont uniquement prises en charge avec le pare-feu d’applications web sur Application Gateway. Les règles de l’ensemble de règles par défaut Azure, la limitation du débit et le filtrage géographique sont pris en charge uniquement avec le pare-feu d’applications web sur Azure Front Door.

## <a name="is-ddos-protection-integrated-with-front-door"></a>La protection DDoS est-elle intégrée à Front Door ? 

Distribué mondialement aux périphéries réseau d’Azure, Azure Front Door peut absorber et isoler géographiquement d’importants volumes d’attaques. Vous pouvez créer une stratégie de pare-feu d’applications web personnalisée pour bloquer automatiquement et limiter le débit des attaques http(s) aux signatures connues. De plus, vous pouvez activer la protection DDos standard sur le réseau virtuel où vos back-ends sont déployés. Les clients de la protection DDos standard d’Azure profitent d’avantages supplémentaires dont la protection du coût, la garantie SLA et l’accès à des experts de l’équipe spécialisée en attaques DDoS, pour une aide immédiate en cas d’attaque. 

Nous vous recommandons de verrouiller vos back-ends dans un environnement de production, de façon à réduire la surface d’attaque DDoS. Consultez [Comment restreindre l’accès à mon back-end uniquement à Azure Front Door ?](https://docs.microsoft.com/en-us/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [pare-feu d’application web Azure](waf-overview.md).
- En savoir plus sur [Azure Front Door](front-door-overview.md).
