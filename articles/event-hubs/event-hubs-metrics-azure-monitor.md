---
title: Métriques dans Azure Monitor - Azure Event Hubs | Microsoft Docs
description: Cet article fournit des informations sur l’utilisation d’Azure Monitor pour une surveillance d’Azure Event Hubs.
ms.topic: article
ms.date: 02/25/2021
ms.openlocfilehash: 52ab66fe264b85be117eb8e2e21cb89f38d0fcae
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715579"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Métriques Azure Event Hubs dans Azure Monitor

Les métriques Event Hubs vous donnent l’état de vos ressources Event Hubs dans votre abonnement Azure. Avec un ensemble riche de données de métriques, vous pouvez évaluer l’intégrité globale de vos concentrateurs d’événements au niveau de l’espace de noms, mais également au niveau de l’entité. Ces statistiques peuvent être importantes car elles vous aident à surveiller l’état de vos concentrateurs d’événements. Les métriques peuvent également vous aider à résoudre les problèmes constituant des causes premières sans avoir à contacter le support technique Azure.

Azure Monitor fournit des interfaces utilisateur unifiées pour la surveillance entre divers services Azure. Pour plus d’informations, consultez [Analyse dans Microsoft Azure](../azure-monitor/overview.md) et [Récupérer des métriques Azure Monitor avec .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) sur GitHub.

## <a name="access-metrics"></a>Accéder aux mesures

Azure Monitor propose plusieurs méthodes d’accès aux mesures. Vous pouvez accéder aux métriques via le [portail Azure](https://portal.azure.com), ou bien utiliser les API Azure Monitor (REST et .NET) et des solutions d’analyse comme Log Analytics et Event Hubs. Pour plus d’informations, consultez [Supervision des données collectées par Azure Monitor](../azure-monitor/data-platform.md).

Les métriques sont activées par défaut, et vous pouvez accéder aux 30 derniers jours de données. Si vous souhaitez conserver des données plus longtemps, vous pouvez archiver les données métriques sur un compte Stockage Azure. Vous pouvez configurer ce paramètre dans les [paramètres de diagnostic](../azure-monitor/essentials/diagnostic-settings.md) dans Azure Monitor.


## <a name="access-metrics-in-the-portal"></a>Accéder aux métriques dans le portail

Vous pouvez surveiller les mesures au fil du temps dans le [portail Azure](https://portal.azure.com). L’exemple suivant montre comment afficher les demandes réussies et les demandes entrantes au niveau du compte :

![Affichage des métriques de réussite][1]

Vous pouvez également accéder aux métriques directement via l’espace de noms. Pour ce faire, sélectionnez votre espace de noms, puis **Métriques**. Pour afficher les métriques filtrées à l’étendue de l’Event Hub, sélectionnez le concentrateur d’événements, puis **Métriques**.

Pour les métriques prenant en charge des dimensions, vous devez filtrer avec la valeur de dimension souhaitée, comme dans l’exemple suivant :

![Filtrage avec une valeur de dimension][2]

## <a name="billing"></a>Facturation

L’utilisation de mesures dans Azure Monitor est actuellement gratuite. Toutefois, si vous utilisez d’autres solutions qui ingèrent des données de métriques, il se peut que ces solutions vous soient facturées. Par exemple, vous êtes facturé par Stockage Azure si vous archivez des données de mesures pour un compte de stockage Azure. Vous êtes également facturé par Azure si vous diffusez des données de métriques vers des journaux Azure Monitor pour une analyse avancée.

Les métriques suivantes vous donnent une vue d’ensemble de l’intégrité de votre service. 

> [!NOTE]
> Nous déprécions plusieurs métriques lors de leur déplacement sous un autre nom. Vous devrez peut-être mettre à jour vos références. Les métriques marquées avec le mot-clé « dépréciée » ne seront pas gérées à l’avenir.

Toutes les valeurs de métriques sont envoyées à Azure Monitor toutes les minutes. Le fragment de temps définit l’intervalle de temps pour lequel des valeurs de métriques sont présentées. L’intervalle de temps pris en charge pour toutes les métriques de Event Hubs est de 1 minute.

## <a name="azure-event-hubs-metrics"></a>Métriques Azure Event Hubs
Pour obtenir la liste des métriques prises en charge par le service, consultez [Azure Event Hubs](../azure-monitor/essentials/metrics-supported.md#microsofteventhubnamespaces).

> [!NOTE]
> Quand une erreur utilisateur se produit, Azure Event Hubs met à jour la métrique **Erreurs d’utilisateur**, mais ne journalise aucune autre information de diagnostic. Par conséquent, vous devez capturer les détails des erreurs d’utilisateur dans vos applications. Vous pouvez également convertir la télémétrie générée lors de l’envoi ou de la réception de messages dans Application Insights. Pour obtenir un exemple, consultez [Suivi avec Application Insights](../service-bus-messaging/service-bus-end-to-end-tracing.md#tracking-with-azure-application-insights).

## <a name="azure-monitor-integration-with-siem-tools"></a>Intégration d'Azure Monitor aux outils SIEM
Le routage de vos données de surveillance (journaux d’activité, journaux de diagnostic, etc.) vers un Event Hub avec Azure Monitor vous permet d’opérer facilement une intégration avec des outils SIEM (Security Information and Event Management). Pour plus d’informations, consultez les articles/billets de blog suivants :

- [Diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)
- [Présentation d'Azure Log Integration](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Utilisez Azure Monitor pour intégrer les outils SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

Si un outil SIEM consomme des données de journal à partir d’un Event Hub et que vous ne voyez aucun message entrant ou voyez des messages entrants mais pas de messages sortants dans le graphe des métriques, procédez comme suit :

- Une **absence de messages entrants** indique que le service Azure Monitor ne déplace pas les journaux d’audit/de diagnostic dans le Event Hub. Si tel est le cas, ouvrez un ticket de support auprès de l’équipe Azure Monitor. 
- La présence de messages entrants mais **pas de messages sortants** indique que l’application SIEM ne lit pas les messages. Contactez le fournisseur SIEM afin de déterminer si la configuration du Event Hub de ces applications est correcte.


## <a name="next-steps"></a>Étapes suivantes

* Consultez la [Vue d’ensemble de surveillance Azure](../azure-monitor/overview.md).
* Exemple [Récupérer les métriques Azure Monitor avec .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) sur GitHub. 

Pour plus d’informations sur les concentrateurs d’événements, accédez aux liens suivants :

- Tutoriel Prise en main d'Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [FAQ sur les hubs d’événements](event-hubs-faq.md)
* [Exemples d’application complets qui utilisent des concentrateurs d’événements](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
