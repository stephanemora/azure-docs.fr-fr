---
title: Pare-feu d’applications web Azure - Forum aux Questions
description: Cette page fournit des réponses aux questions fréquemment posées sur le Service de porte d’entrée Azure
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 05d01851d0a3dc9df6c396e862ce93defd957c70
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788919"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Forum aux questions sur les pare-feu d’applications web Azure

Cet article répond aux questions courantes sur les fonctionnalités de pare-feu (WAF) d’application web Azure. 

## <a name="what-is-azure-waf"></a>Qu’est Azure WAF ?

WAF Azure est un pare-feu d’applications web que vous aide à protéger vos applications web contre les menaces courantes telles que SQL injection, scripts intersites et autres attaques web. Vous pouvez définir une stratégie de pare-feu d’applications Web consistant en une combinaison de règles personnalisées et gérés pour contrôler l’accès à vos applications web.

Une stratégie de pare-feu d’applications Web Azure peut être appliquée aux applications web hébergées sur les services de passerelle d’Application ou de la porte d’entrée Azure.

## <a name="what-is-waf-for-azure-front-door-service"></a>Qu’est WAF pour Service de porte d’entrée Azure ? 

Porte d’entrée Azure est une application hautement évolutive et distribuée dans le monde entier et le réseau de distribution de contenu. WAF Azure, lorsque intégré à la porte d’entrée, arrête un déni de service et ciblé les attaques de l’application sur le périmètre du réseau Azure, sources d’attaque avant leur entrée sur votre réseau virtuel, offre une protection sans sacrifier les performances.

## <a name="how-will-i-be-charged-for-azure-waf-for-front-door"></a>Comment sera être facturé pour le pare-feu d’applications Web Azure pour la porte d’entrée ?
Préversion publique, l’utilisation de pare-feu d’applications Web à la porte d’entrée sont gratuites. Notez que les frais de la porte d’entrée est supplémentaire. Consultez la tarification du Service de la porte d’entrée [ici](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="does-azure-waf-support-https"></a>Pare-feu d’applications Web Azure prend en charge HTTPS ?

Service de la porte d’entrée offre le déchargement SSL. WAF est intégré en mode natif avec la porte d’entrée et peut inspecter une demande une fois qu’il est déchiffré.

## <a name="does-azure-waf-support-ipv6"></a>Pare-feu d’applications Web Azure prend en charge IPv6 ?

Oui. Vous pouvez configurer des restrictions d’adresse IP pour IPv4 et IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>À jour sont les ensembles de règles managé ?

Nous faisons de notre mieux pour suivre la variation du paysage des menaces. Une fois qu’une nouvelle règle est mise à jour, il est ajouté à l’ensemble par défaut règle avec un nouveau numéro de version.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Qu’est la durée de propagation si j’apporte une modification à ma stratégie de pare-feu d’applications Web ?

Déploiement d’une stratégie de pare-feu d’applications Web dans le monde entier généralement prend environ cinq minutes et souvent se termine plus tôt.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Stratégies de pare-feu d’applications Web peuvent être différents pour les différentes régions ?

Lorsque intégrée avec le Service de la porte d’entrée, WAF est une ressource globale. Même configuration s’applique à tous les emplacements de porte d’entrée.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Comment limiter les accès à mon serveur principal soit à partir de la porte d’entrée uniquement ?

Vous pouvez configurer la liste de contrôle d’accès IP sur votre serveur principal pour permettre uniquement porte sortants plages d’adresses IP et refuser tout accès direct à partir d’Internet. Balises de service sont prises en charge que vous pouvez utiliser sur votre réseau virtuel. En outre, vous pouvez vérifier que le champ d’en-tête X-transférés-Host HTTP est valide pour votre application web.




## <a name="which-azure-waf-options-should-i-choose"></a>Les options de pare-feu d’applications Web Azure dois-je choisir ?

Il existe deux options pour appliquer des stratégies de pare-feu d’applications Web dans Azure. WAF avec Azure porte d’entrée est une solution de sécurité distribuée globalement, edge. Pare-feu d’applications Web avec Application Gateway est une solution régionale, dédiée. Nous vous recommandons de que vous choisissez une solution selon vos besoins de performances et la sécurité globales. Pour plus d’informations, consultez [équilibrage de charge avec la suite de livraison d’Azure application](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Prenez-vous en charge les fonctionnalités de WAF mêmes dans toutes les plateformes intégrées ?

Actuellement, ModSec CRS 2.2.9 et les règles CRS 3.0 sont uniquement pris en charge avec WAF au niveau de passerelle d’Application. Limitation du débit, filtrage géographique et Azure managé par défaut un ensemble de règles des règles sont pris en charge uniquement avec le WAF à Azure porte d’entrée.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Protection DDoS est intégrée avec la porte d’entrée ? 

Porte d’entrée Azure globalement distribuées sur les bords de réseau Azure, et peut absorber et géographiquement isoler les attaques de gros volume. Vous pouvez créer stratégie WAF personnalisée à bloc automatiquement et le taux de limiter les attaques HTTP (s) dont les signatures. Davantage de plus, vous pouvez activer DDoS Protection Standard sur le réseau virtuel dans lequel vos serveurs principaux sont déployées. Les clients de Protection DDoS Standard Azure bénéficient des avantages supplémentaires, y compris la protection des coûts, garantie de contrat SLA et l’accès à des experts à partir de l’équipe de réponse DDoS rapide pour obtenir une aide immédiate pendant une attaque. 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [pare-feu d’applications web Azure](waf-overview.md).
- En savoir plus sur [porte d’entrée Azure](front-door-overview.md).
