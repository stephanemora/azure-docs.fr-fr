---
title: 'Azure Front Door : protection DDoS'
description: Cette page fournit des informations sur la façon dont Azure Front Door Standard/Premium vous aide à vous protéger contre les attaques DDoS.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097816"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>Protection DDoS sur Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Azure Front Door offre plusieurs fonctionnalités et caractéristiques qui peuvent contribuer à empêcher les attaques par déni de service distribué (DDoS). Ces fonctionnalités peuvent empêcher les attaquants d’atteindre votre application et de nuire à la disponibilité et aux performances de celle-ci.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="integration-with-azure-ddos-protection-basic"></a>Intégration avec Azure DDoS Protection Basic

Front Door est protégé par Azure DDoS Protection Basic. La fonctionnalité est intégrée par défaut à la plateforme Front Door sans frais additionnels. De par son échelle et sa capacité, le réseau déployé à l’échelle mondiale de Front Door assure une défense contre les attaques courantes de la couche réseau. Cette défense est par ailleurs renforcée par le monitoring continu du trafic et l’atténuation en temps réel. La protection DDoS de base protège également contre les avalanches de requêtes DNS de couche 7 et les attaques volumétriques de couches 3 et 4 les plus fréquentes qui ciblent les points de terminaison publics. Ce service a également fait ses preuves dans la protection des services professionnels et grand public de Microsoft contre les attaques de grande envergure. Pour plus d’informations, consultez [Azure DDoS Protection](../../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Blocage de protocole

Front Door accepte uniquement le trafic sur les protocoles HTTP et HTTPS, et traite uniquement les demandes valides avec un en-tête `Host` connu. Ce comportement permet de limiter certains types d’attaques DDoS courantes, y compris les attaques volumétriques qui sont réparties entre toute une série de protocoles et de ports, les attaques d’amplification DNS et les attaques par empoisonnement TCP.

## <a name="capacity-absorption"></a>Absorption de la capacité

Front Door est un service distribué à l’échelle mondiale et à très grande échelle. Nous avons de nombreux clients, y compris des produits cloud de grande ampleur de Microsoft, qui reçoivent des centaines de milliers de demandes chaque seconde. Front Door est situé à la périphérie du réseau d’Azure, pour absorber et isoler géographiquement les attaques en gros volume. Cela peut empêcher le trafic malveillant d’aller plus loin que la périphérie du réseau Azure.

## <a name="caching"></a>Mise en cache

[Les fonctionnalités de mise en cache de Front Door](concept-caching.md) peuvent être utilisées pour protéger les back-ends des volumes de trafic importants générés par une attaque. Les ressources mises en cache sont retournées à partir des nœuds de périphérie Front Door, afin qu’elles ne soient pas transférées vers votre back-end. Même les temps d’expiration de cache courts (secondes ou minutes) sur des réponses dynamiques peuvent réduire de façon considérable la charge sur les services back-ends. Pour plus d’informations sur les concepts et les modèles de mise en cache, consultez [Considérations relatives à la mise en cache](/azure/architecture/best-practices/caching) et [modèle de réserve de caches](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>Pare-feu d’applications web (WAF)

[Le pare-feu d’application web de Front Door](../../web-application-firewall/afds/afds-overview.md) peut être utilisé pour limiter de nombreux types d’attaques :

* L’utilisation de l’ensemble de règles gérées offre une protection contre de nombreuses attaques courantes.
* Le trafic provenant de l’extérieur d’une région géographique définie ou à l’intérieur d’une région bien précise peut être bloqué ou redirigé vers une page web statique. Pour plus d’informations, consultez [Géo-filtrage](../../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* Les adresses et les plages d’adresses IP que vous identifiez comme malveillantes peuvent être bloquées.
* La limitation du débit peut être appliquée pour empêcher les adresses IP d’appeler votre service trop fréquemment.
* Vous pouvez créer des [règles WAF personnalisées](../../web-application-firewall/afds/waf-front-door-custom-rules.md) pour bloquer automatiquement et limiter le débit des attaques HTTP ou HTTPS aux signatures connues.

## <a name="for-further-protection"></a>Pour une protection renforcée

Si vous avez besoin d’une protection renforcée, vous pouvez activer [la protection DDoS Azure standard](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) sur le réseau virtuel où vos back-ends sont déployés. Les clients de l’offre Standard de la protection DDoS bénéficient d’avantages supplémentaires, notamment :

* Protection contre les coûts
* Garantie SLA
* Accès à des experts de l’équipe de réponse rapide aux attaques DDoS, pour une aide immédiate pendant une attaque.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer une instance de Front Door Standard/Premium](create-front-door-portal.md).
