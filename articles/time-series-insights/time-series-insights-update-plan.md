---
title: Planifier votre environnement de préversion – Azure Time Series Insights | Microsoft Docs
description: Bonnes pratiques pour configurer, gérer, planifier et déployer votre environnement de préversion Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b16d78c9670d05fcec8126c5544d1dd97f6a03bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045723"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planifier votre environnement Azure Time Series Insights (préversion)

Cet article décrit les bonnes pratiques pour planifier et commencer à utiliser Azure Time Series Insights (préversion).

> [!NOTE]
> Pour obtenir des bonnes pratiques sur la planification d’une instance Time Series Insights en disponibilité générale, consultez [Planifier votre environnement Azure Time Series Insights en disponibilité générale](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Meilleures pratiques de planification et de préparation

Les bonnes pratiques pour la planification et la préparation de votre environnement sont décrites plus en détail dans les articles suivants :

* Ce que vous obtenez lorsque vous [approvisionnez un environnement Time Series Insights en préversion](#the-preview-environment).
* Ce que sont vos [propriétés ID Time Series et Timestamp](#configure-time-series-ids-and-timestamp-properties).
* Ce qu’est le [nouveau modèle Time Series](#understand-the-time-series-model) et comment créer le vôtre.
* Comment [envoyer des événements de manière efficace dans JSON](#shape-your-events).
* [Options de récupération d’urgence](#business-disaster-recovery) de Time Series Insights.

Azure Time Series Insights utilise un modèle de paiement à l’utilisation. Pour plus d’informations sur les coûts et la capacité, consultez [Tarification de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Environnement de préversion

Lorsque vous approvisionnez un environnement Time Series Insights en préversion, vous créez deux ressources Azure :

* Un environnement Azure Time Series Insights en préversion
* Un compte Stockage Azure à usage général V1

Dans le cadre du processus de provisionnement, vous spécifiez si vous souhaitez activer un stockage chaud. Le stockage chaud vous offre une expérience de requête à plusieurs niveaux. Lorsque cette option est activée, vous devez spécifier une période de conservation comprise entre 7 et 30 jours. Les requêtes exécutées dans la période de conservation du stockage chaud présentent généralement des temps de réponse plus rapides. Quand une requête dépasse la période de conservation du stockage chaud, elle est traitée à partir du stockage froid.

Les requêtes sur le stockage chaud sont gratuites, contrairement aux requêtes sur le stockage froid qui sont facturées. Il est important de bien comprendre vos modèles de requête et de planifier la configuration de votre stockage chaud en conséquence. Nous vous recommandons de placer l’analytique interactive sur les données les plus récentes dans votre stockage chaud, et de placer l’analyse des modèles et les tendances à long terme dans le stockage froid.

> [!NOTE]
> Pour plus d’informations sur l’interrogation de vos données chaudes, consultez les [Informations de référence sur l’API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters).

Pour commencer, vous avez besoin de trois autres éléments :

* Un [modèle Time Series](./time-series-insights-update-tsm.md)
* Une [source d’événements connectée à Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* Les [événements transférés à la source d’événement](./time-series-insights-send-events.md) qui sont mappés sur le modèle et qui respectent un format JSON valide

## <a name="review-preview-limits"></a>Examiner les limites de la préversion

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurer les propriétés ID Time Series et Timestamp

Pour créer un environnement Time Series Insights, sélectionnez un ID Time Series. Cette opération fonctionne comme une partition logique pour vos données. Comme indiqué, assurez-vous que votre ID Time Series est prêt.

> [!IMPORTANT]
> Les ID de série chronologique *ne peuvent pas être changés ultérieurement*. Vérifiez-les tous avant la sélection finale et leur utilisation.

Vous pouvez sélectionner jusqu’à trois clés pour différencier de manière unique vos ressources. Pour plus d’informations, consultez [Meilleures pratiques pour le choix d’un ID Time Series](./time-series-insights-update-how-to-id.md) et [Stockage et entrée](./time-series-insights-update-storage-ingress.md).

La propriété **Timestamp** est également importante. Vous pouvez désigner cette propriété lorsque vous ajoutez des sources d’événements. Chaque source d’événement a une propriété Timestamp facultative utilisée pour suivre les sources d’événements. Les valeurs de Timestamp respectent la casse et doivent être mises en forme selon les spécifications propres à chaque source.

> [!TIP]
> Vérifiez les exigences de mise en forme et d’analyse de vos sources d’événements.

Lorsque ce champ est vide, l’heure de mise en file d’attente de l’événement dans la source d’événement est utilisée comme valeur Timestamp de l’événement. Si vous envoyez des données historiques ou des événements par lot, la personnalisation de la propriété Timestamp est plus utile que le temps de file d’attente d’événement par défaut. Pour plus d’informations, consultez l’article sur l’[ajout de sources d’événements dans Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Comprendre le modèle Time Series

Vous pouvez maintenant configurer le modèle Time Series de votre environnement Time Series Insights. Ce nouveau modèle simplifie la recherche et l’analyse des données IoT. Il permet la collecte, la maintenance et l’enrichissement des données de série chronologique et permet de préparer des jeux de données utilisables par le consommateur. Ce modèle utilise les ID Time Series, qui sont mappés sur une instance qui associe la ressource unique à des variables, comme les types, et les hiérarchies. En savoir plus sur le nouveau [modèle Time Series](./time-series-insights-update-tsm.md).

Le modèle étant dynamique, il peut être généré à tout moment. Pour démarrer rapidement, générez-le et chargez-le avant d’envoyer les données à Time Series Insights. Pour créer votre modèle, consultez [Utiliser le modèle Time Series](./time-series-insights-update-how-to-tsm.md).

Pour de nombreux clients, le modèle Time Series correspond à un modèle de ressource existant ou à un système ERP déjà en place. Si vous n’avez pas de modèle, une expérience utilisateur prédéfinie est [fournie](https://github.com/Microsoft/tsiclient) pour vous permettre d’être opérationnel rapidement. Pour voir comment un modèle peut vous aider, consultez l’[exemple d’environnement de démonstration](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Mettre en forme vos événements

Vous pouvez voir la façon dont vous envoyez des événements à Time Series Insights. Dans l’idéal, vos événements sont dénormalisés correctement et efficacement.

Une règle de base :

* stockez les métadonnées dans votre modèle Time Series.
* Assurez-vous que Time Series Mode, les champs d’instance et les événements ne comprennent que les informations nécessaires, comme un ID de série chronologique ou une propriété Timestamp.

Pour plus d’informations, consultez [Mettre en forme les événements](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Azure Advisor](../advisor/advisor-overview.md) pour planifier vos options de configuration de récupération.
- Renseignez-vous sur [l’entrée et le stockage](./time-series-insights-update-storage-ingress.md) dans Azure Time Series Insights en préversion.
- Renseignez-vous sur la [modélisation des données](./time-series-insights-update-tsm.md) dans Azure Time Series Insights en préversion.
