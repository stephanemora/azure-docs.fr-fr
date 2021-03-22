---
title: Azure Front Door Standard/Premium| Microsoft Docs
description: Cet article fournit une vue d’ensemble d’Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 574340825567dcd512a5da1b311c57fe12954e34
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030543"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>Qu’est-ce qu’Azure Front Door Standard/Premium (préversion) ?

> [!IMPORTANT]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Consultez la [documentation Azure Front Door](../front-door-overview.md).

Azure Front Door Standard/Premium est un réseau de distribution de contenu (CDN) cloud moderne, rapide, fiable et sécurisé qui utilise le réseau de périmètre mondial de Microsoft et s’intègre à une protection intelligente contre les menaces. Il associe les capacités d’Azure Front Door, la norme Microsoft Azure Content Delivery Network (CDN) et Azure Web Application Firewall (WAF) à une seule plateforme CDN cloud sécurisée.

Grâce à Azure Front Door Standard/Premium, vous pouvez transformer vos applications d’entreprise et de consommateurs à l’échelle mondiale en applications modernes personnalisées, sécurisées et performantes, dont le contenu atteint un public mondial à la périphérie du réseau, à proximité de l’utilisateur. Cela permet également à votre application d’effectuer un scale-out sans préchauffage tout en bénéficiant à partir de l’équilibrage de charge HTTP mondial avec basculement instantané.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Architecture d’Azure Front Door Standard/Premium" lightbox="../media/overview/front-door-overview-expanded.png":::

Azure Front Door Standard/Premium fonctionne au niveau de la couche 7 (HTTP/HTTPS) en utilisant le protocole anycast avec fractionnement TCP et le réseau mondial de Microsoft pour améliorer la connectivité à l’échelle mondiale. Selon votre méthode de routage personnalisée utilisant un ensemble de règles, vous pouvez faire en sorte qu’Azure Front Door achemine les demandes de vos clients vers l’origin offrant les plus hautes performances et la meilleure disponibilité. Une origin d’application est un service orienté vers l’Internet, hébergé dans ou hors Azure. Azure Front Door Standard/Premium fournit un large éventail de méthodes de routage du trafic et d’options d’analyse du fonctionnement de l’origin pour répondre aux besoins variés des applications et aux divers scénarios de basculement automatique. À l’instar de Traffic Manager, Front Door est résilient aux défaillances, notamment aux échecs dans une région Azure entière.

Azure Front Door protège également vos applications en périphérie grâce à la protection intégrée de Web Application Firewall, à la protection bot et à la protection intégrée contre les dénis de service distribué (DDoS) de niveau 3 ou 4. Il sécurise également vos serveurs principaux privés grâce à un service de liaison privée. Azure Front Door vous offre les meilleures pratiques de Microsoft en matière de sécurité à l’échelle mondiale.  

>[!NOTE]
> Azure offre une suite de solutions d’équilibrage de charge entièrement managées pour vos scénarios.
>
> * Si vous cherchez à effectuer un routage global basé sur DNS et que vous **ne répondez pas** aux exigences concernant la terminaison de protocole TLS (« déchargement SSL ») ou le traitement de la couche Application ou par requête HTTP/HTTPS, passez en revue [Traffic Manager](../../traffic-manager/traffic-manager-overview.md).
> * Si vous souhaitez équilibrer la charge entre vos serveurs dans une région au niveau de la couche Application, consultez [Application Gateway](../../application-gateway/overview.md).
> * Pour effectuer l’équilibrage de charge de la couche Réseau, consultez [Load Balancer](../../load-balancer/load-balancer-overview.md).
>
> Vos scénarios de bout en bout peuvent tirer parti de la combinaison de ces solutions en fonction de vos besoins.
> Pour obtenir une comparaison des options d’équilibrage de charge Azure, consultez [Vue d’ensemble des options d’équilibrage de charge dans Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

> [!IMPORTANT]
> Azure Front Door Standard/Premium est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-azure-front-door-standardpremium-preview"></a>Pourquoi utiliser Azure Front Door Standard/Premium (préversion) ?

Azure Front Door Standard/Premium fournit une plateforme unifiée unique qui répond à la fois à l’accélération dynamique et statique avec une intégration clé en main de la sécurité et un modèle de tarification simple et prévisible. Front Door vous permet également de définir, gérer et surveiller le routage global de votre application.

Fonctionnalités clés incluses dans Azure Front Door Standard/Premium (préversion) :

- Accélération des performances des applications grâce à l’utilisation du protocole anycast **[basé sur le fractionnement TCP](../front-door-routing-architecture.md#splittcp)** .

- Surveillance intelligente des **[sondes d’intégrité](concept-health-probes.md)** et équilibrage de charge entre les **[origins](concept-origin.md)** .

- Définition de votre propre **[domaine personnalisé](how-to-add-custom-domain.md)** avec une validation de domaine flexible.

- Sécurité des applications grâce au **[pare-feu d’applications web (WAF)](../../web-application-firewall/afds/afds-overview.md)** intégré.

- Déchargement SSL et **[gestion intégrée des certificats](how-to-configure-https-custom-domain.md)** .

- Sécurisation de vos origins à l’aide de **[Private Link](concept-private-link.md)** .  

- Routage du trafic et optimisations personnalisables via un **[ensemble de règles](concept-rule-set.md)** .

- **[Rapports intégrés](how-to-reports.md)** avec un tableau de bord tout-en-un pour Front Door et les modèles de sécurité.

- **[Surveillance en temps réel](how-to-monitor-metrics.md)** et alertes qui s’intègrent à la surveillance Azure.

- **[Journalisation](how-to-logs.md)** pour chaque requête Front Door et sondes d’intégrité en échec.

- Prise en charge native de la connectivité IPv6 de bout en bout et du protocole HTTP/2.

## <a name="pricing"></a>Tarifs

Azure Front Door Standard/Premium propose deux niveaux tarifaires, Standard et Premium. Voir [Comparaison des niveaux](tier-comparison.md). Pour obtenir des informations sur les prix, consultez [Prix de Front Door](https://azure.microsoft.com/pricing/details/frontdoor/). 

## <a name="whats-new"></a>Nouveautés

Abonnez-vous au flux RSS, puis examinez les dernières mises à jour des fonctionnalités Azure Front Door dans la page [Mises à jour Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer une porte d’entrée](create-front-door-portal.md).
