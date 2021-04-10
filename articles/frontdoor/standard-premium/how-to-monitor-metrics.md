---
title: Métriques de supervision pour Azure Front Door Standard/Premium
description: Cet article décrit les métriques de supervision d’Azure Front Door Standard/Premium.
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 72388eb8006ff1b9628db5066dc63e6a0811f3d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557324"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Supervision en temps réel dans Azure Front Door Standard/Premium

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Azure Front Door Standard/Premium est intégré à Azure Monitor et a 11 métriques pour vous aider à superviser Azure Front Door Standard/Premium en temps réel afin de suivre, dépanner et déboguer les problèmes.  

Azure Front Door Standard/Premium mesure et envoie ses métriques par intervalles de 60 secondes. Les métriques peuvent prendre jusqu’à 3 minutes pour s’afficher dans le portail. Les métriques peuvent être affichées dans des graphiques ou des grilles de votre choix, et sont accessibles via le portail, PowerShell, l’interface CLI et l’API. Pour plus d’informations, consultez  [Métriques Azure Monitor](../../azure-monitor/essentials/data-platform-metrics.md).  

Les métriques par défaut sont gratuites. Vous pouvez activer d’autres métriques moyennant des coûts supplémentaires. 

Vous pouvez configurer des alertes pour chaque métrique, comme un seuil pour 4XXErrorRate ou 5XXErrorRate. Quand le taux d’erreur dépasse le seuil, une alerte est déclenchée selon ce qui est configuré. Pour plus d'informations, consultez [Créer, afficher et gérer des alertes de métrique à l'aide d'Azure Monitor](../../azure-monitor/alerts/alerts-metric.md). 

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Métriques prises en charge dans Azure Front Door Standard/Premium

| Mesures  | Description | Dimensions |
| ------------- | ------------- | ------------- |
| Taux de réussite en octets | Pourcentage de sortie du cache AFD, calculé par rapport à la sortie totale. </br> **Taux de réussite en octets** = (sortie de la périphérie - sortie de l’origine)/sortie de la périphérie. </br> **Scénarios exclus du calcul du taux de réussite en octets** :</br> 1. Vous ne configurez explicitement aucun cache par le biais du comportement de mise en cache des chaînes de requête ou du moteur de règles. </br> 2. Vous configurez explicitement une directive de contrôle du cache avec un cache privé ou sans magasin. </br>3. Le taux de réussite en octets peut être faible si la majeure partie du trafic est transférée à l’origine au lieu d’être desservie depuis ce qui est mis en cache, en fonction de vos configurations ou de vos scénarios. | Point de terminaison |
| RequestCount | Nombre de requêtes de clients prises en charge par CDN. | Point de terminaison, pays du client, région du client, état HTTP, groupe d’états HTTP |
| ResponseSize | Nombre de requêtes clientes traitées par AFD. |Point de terminaison, pays du client, région du client, état HTTP, groupe d’états HTTP |
| TotalLatency | Durée totale de la requête du client reçue par CDN **jusqu’au dernier octet de réponse envoyé de CDN au client**. |Point de terminaison, pays du client, région du client, état HTTP, groupe d’états HTTP |
| RequestSize | Nombre d’octets envoyés en tant que requêtes des clients vers AFD. | Point de terminaison, pays du client, région du client, état HTTP, groupe d’états HTTP |
| Pourcentage d’erreurs 4XX | Pourcentage de toutes les requêtes clientes pour lesquelles le code d’état de la réponse est 4XX. | Point de terminaison, pays du client, région du client |
| Pourcentage d’erreurs 5XX | Pourcentage de toutes les demandes client pour lesquelles le code d’état de la réponse est 5XX. | Point de terminaison, pays du client, région du client |
| OriginRequestCount  | Nombre de requêtes envoyées depuis AFD à l’origine. | Point de terminaison, origine, état HTTP, groupe d’états HTTP |
| OriginLatency | Temps calculé à partir du moment où la requête est envoyée par la périphérie AFD au back-end jusqu’à ce qu’AFD reçoive le dernier octet de la réponse du back-end. | Point de terminaison, origine |
| OriginHealth% | Pourcentage de sondes d’intégrité réussies d’AFD vers l’origine.| Origine, groupe d’origines |
| Nombre de requêtes WAF | Requête WAF avec correspondance. | Action, nom de la règle, nom de la stratégie |

## <a name="access-metrics-in-azure-portal"></a>Accéder aux métriques dans le portail Azure

1. Dans le menu du portail Azure, sélectionnez **Toutes les ressources** >>  **\<your-AFD Standard/Premium (Preview) -profile>** .

2. Sous **Supervision**, sélectionnez **Métriques** :

3. Dans **Métriques**, sélectionnez la métrique à ajouter :

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="Capture d’écran de la page des métriques." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. Sélectionnez **Ajouter un filtre** pour ajouter un filtre :

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="Capture d’écran de l’ajout de filtres aux métriques." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. Sélectionnez **Appliquer une division** pour diviser les données selon différentes dimensions :

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="Capture d’écran de l’ajout de dimensions aux métriques." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. Sélectionnez **Nouveau graphique** pour ajouter un nouveau graphique :

## <a name="configure-alerts-in-azure-portal"></a>Configurer des alertes dans le portail Azure

1. Configurez des alertes sur Azure Front Door Standard/Premium (préversion) en sélectionnant **Supervision** >> **Alertes**.

1. Sélectionnez **Nouvelle règle d’alerte** pour les métriques listées dans la section Métriques.

L’alerte sera facturée conformément à Azure Monitor. Pour plus d’informations sur les alertes, consultez [Alertes Azure Monitor](../../azure-monitor/alerts/alerts-overview.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [rapports Azure Front Door Standard/Premium](how-to-reports.md).
- Découvrez les [journaux Azure Front Door Standard/Premium](how-to-logs.md).