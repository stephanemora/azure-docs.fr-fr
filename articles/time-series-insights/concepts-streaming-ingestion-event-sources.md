---
title: 'Sources d’événements d’ingestion de diffusion en continu : Azure Time Series Insights Gen2 | Microsoft Docs'
description: En savoir plus sur la diffusion en continu de données dans Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4e22d93d3037c190193f53b7cfdbc87cff2da6ed
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504394"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Sources d’événements Azure Time Series Insights Gen2

Votre environnement Azure Time Series Insights Gen2 peut avoir jusqu’à deux sources d’événements de diffusion en continu. Deux types de ressources Azure sont pris en charge en tant qu’entrées :

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Les événements doivent être envoyés en tant que JSON encodé en UTF-8.

## <a name="create-or-edit-event-sources"></a>Créer ou modifier des sources d’événements

La source de l’événement est le lien entre votre hub et votre environnement Azure Time Series Insights Gen2, et une ressource distincte de type `Time Series Insights event source` est créée dans votre groupe de ressources. Les ressources IoT Hub ou Event Hubs peuvent résider dans le même abonnement Azure que votre environnement Azure Time Series Insights Gen2 ou dans un autre abonnement. Toutefois, il est recommandé d’héberger votre environnement Azure Time Series Insights et le hub IoT ou l’Event Hub dans la même région Azure.

Vous pouvez utiliser le [portail Azure](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment), [Azure CLI](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source), [des modèles Azure Resource Manager](time-series-insights-manage-resources-using-azure-resource-manager-template.md) et l’[API REST](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) pour créer, modifier ou supprimer les sources d’événements de votre environnement.

## <a name="start-options"></a>Options de démarrage

Lorsque vous créez une source d’événement, vous avez la possibilité de spécifier les données préexistantes à collecter. Ce paramètre est facultatif. Les options suivantes sont disponibles :

| Nom   |  Description  |  Exemple de modèle Azure Resource Manager |
|----------|-------------|------|
| EarliestAvailable | Ingérer toutes les données préexistantes stockées dans le hub IoT ou l’Event Hub. | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Commencer à ingérer les données qui arrivent après la création de la source de l’événement. Toutes les données préexistantes qui ont été diffusées avant la création de la source de l’événement seront ignorées. Il s’agit du paramètre par défaut dans le portail Azure.   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | Votre environnement ingérera les données à partir de l’heure (UTC) personnalisée de mise en file d’attente. Tous les événements qui ont été empilés dans votre hub IoT ou votre Event Hub à l’heure personnalisée de mise en file d’attente ou après celle-ci seront ingérés et stockés. Tous les événements qui sont arrivés avant l’heure personnalisée de mise en file d’attente seront ignorés. Notez que « heure de mise en file d’attente » fait référence à l’heure (UTC) à laquelle l’événement est arrivé dans votre hub IoT ou votre Event Hub. Cela diffère d’une [propriété de timestamp](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) personnalisée qui se trouve dans le corps de votre événement. |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Si vous sélectionnez EarliestAvailable et que vous avez un grand nombre de données préexistantes, vous risquez de connaître une latence initiale élevée, car votre environnement Azure Time Series Insights Gen2 traite toutes vos données.
> - Cette latence élevée devrait finir par s’estomper à mesure que les données sont indexées. Si vous rencontrez une latence élevée, soumettez un ticket de support via le portail Azure.

* EarliestAvailable

![Diagramme EarliestAvailable](media/concepts-streaming-event-sources/event-source-earliest-available.png)

* EventSourceCreationTime

![Diagramme EventSourceCreationTime](media/concepts-streaming-event-sources/event-source-creation-time.png)

* CustomEnqueuedTime

![Diagramme CustomEnqueuedTime](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)


## <a name="streaming-ingestion-best-practices"></a>Meilleures pratiques en matière d’ingestion de diffusion en continu

- Créez toujours un groupe de consommateurs unique pour votre environnement Azure Time Series Insights Gen2 afin de consommer des données de votre source d’événements. La réutilisation des groupes de consommateurs peut provoquer des déconnexions aléatoires et peut entraîner une perte de données.

- Configurez votre environnement Azure Time Series Insights Gen2 et votre hub IoT et/ou Event Hubs dans la même région Azure. Bien qu’il soit possible de configurer une source d’événement dans une région distincte, ce scénario n’est pas pris en charge et nous ne pouvons pas garantir une haute disponibilité.

- N’allez pas au-delà de la [limite du débit](./concepts-streaming-ingress-throughput-limits.md) de votre environnement ou de la limite de partition.

- Configurez une [alerte](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) de latence pour être averti si votre environnement rencontre des problèmes de traitement des données. Pour connaître les conditions d’alerte suggérées, consultez [Charges de travail de production](./concepts-streaming-ingestion-event-sources.md#production-workloads) ci-dessous.

- Utilisez l’ingestion de streaming uniquement pour des données récentes et en quasi-temps réel. Les données historiques de streaming ne sont pas prises en charge.

- Vous devez comprendre la manière dont les propriétés sont placées dans une séquence d’échappement et comment les données JSON [aplaties et stockées.](./concepts-json-flattening-escaping-rules.md)

- Suivez le principe des privilèges minimum lorsque vous fournissez des chaînes de connexion à la source d’événements. Pour Event Hubs, configurez une stratégie d’accès partagé avec l’*envoi* de revendication uniquement, et pour IoT Hub utilisez l’autorisation *service Connect* uniquement.

> [!CAUTION]
> Si vous supprimez votre hub IoT ou votre Event Hub et que vous recréez une nouvelle ressource portant le même nom, vous devez créer une nouvelle source d’événement et attacher le nouveau hub IoT ou Event Hub. Les données ne seront pas ingérées tant que vous n’aurez pas effectué cette étape.

## <a name="production-workloads"></a>Charges de travail de production

En plus des meilleures pratiques ci-dessus, nous vous recommandons d’implémenter les éléments suivants en ce qui concerne les charges de travail critiques pour l’entreprise.

- Augmentez la durée de conservation de vos données IoT Hub ou Event Hubs au maximum (sept jours).

- Créer des alertes d’environnement sur le Portail Azure. Les alertes basées sur des [métriques](./how-to-monitor-tsi-reference.md#metrics) de plateforme vous permettent de valider le comportement du pipeline de bout en bout. Pour connaître les instructions de création et de gestion des alertes, [cliquez ici](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts). Voici les conditions d’alerte suggérées :

  - IngressReceivedMessagesTimeLag est supérieur à 5 minutes.
  - IngressReceivedBytes est égal à 0.
- Maintenez l’équilibrage de votre charge d’ingestion entre vos partitions IoT Hub et Event Hub.

### <a name="historical-data-ingestion"></a>Ingestion de données historiques

L’utilisation du pipeline de streaming pour importer des données historiques n’est actuellement pas prise en charge dans Azure Time Series Insights Gen2. Si vous devez importer des données passées dans votre environnement, suivez les instructions ci-dessous :

- Ne diffusez pas parallèlement des données historiques et dynamiques. L’ingestion de données dans le désordre entraînera une dégradation des performances de requête.
- Ingérez les données historiques de façon chronologique pour obtenir des performances optimales.
- Restez dans les limites du débit d’ingestion ci-dessous.
- Désactivez le magasin Warm si les données sont antérieures à la période de rétention du magasin Warm.

## <a name="event-source-timestamp"></a>Horodateur de la source de l’événement

Quand vous configurez une source d’événement, vous êtes invité à fournir une propriété d’ID de timestamp. La propriété de timestamp est utilisée pour effectuer le suivi des événements dans le temps. Il s’agit de l’heure qui sera utilisée comme timestamp `$ts` dans les [API de requête](/rest/api/time-series-insights/dataaccessgen2/query/execute) et pour tracer des séries dans Explorateur Azure Time Series Insights. Si aucune propriété n’est fournie au moment de la création, ou si la propriété d’horodatage est absente d’un événement, IoT Hub ou Event Hubs sont utilisés par défaut. Les valeurs de propriété d’horodatage sont stockées au format UTC.

En général, les utilisateurs choisissent de personnaliser la propriété d’horodatage et utilisent l’heure à laquelle le capteur ou la balise a généré la lecture au lieu d’utiliser le hub par défaut mis en file d’attente. Cela est particulièrement nécessaire lorsque les appareils présentent une perte de connectivité intermittente et qu’un lot de messages retardés est transféré à Azure Time Series Insights Gen2.

Si votre horodateur personnalisé se trouve dans un objet JSON imbriqué ou un tableau, vous devrez fournir le nom de propriété correct en suivant nos conventions d’affectation de noms [de mise à plat et d’échappement](concepts-json-flattening-escaping-rules.md). Par exemple, l’horodateur de la source de l’événement pour la charge utile JSON indiquée [ici](concepts-json-flattening-escaping-rules.md#example-a) doit être saisi ainsi : `"values.time"`.

### <a name="time-zone-offsets"></a>Décalage de fuseau horaire

Les horodateurs doivent être envoyés au format ISO 8601 et seront stockés au format UTC. Si un décalage de fuseau horaire est fourni, l’offset est appliqué, puis l’heure est stockée et retournée au format UTC. Si le format du décalage est incorrect, il sera ignoré. Dans les situations où votre solution n’a peut-être pas le contexte du décalage d’origine, vous pouvez envoyer les données de décalage dans une propriété d’événement distincte supplémentaire pour vous assurer qu’elles sont conservées et que votre application peut faire référence à une réponse de requête.

Le décalage de fuseau horaire doit être mis en forme de l’une des manières suivantes :

±HHMMZ</br>
±HH:MM</br>
±HH:MMZ</br>

## <a name="next-steps"></a>Étapes suivantes

- Lisez les [Règles de mise à plat et d’échappement JSON](./concepts-json-flattening-escaping-rules.md) pour comprendre comment les événements seront stockés.

- Comprendre les [limites de débit](./concepts-streaming-ingress-throughput-limits.md) de votre environnement
