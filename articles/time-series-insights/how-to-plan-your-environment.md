---
title: Planifier votre environnementGen2 - Azure Time Series Insights | Microsoft Docs
description: Bonnes pratiques pour configurer, gérer, planifier et déployer votre environnement Gen2 Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: a0a17f28f2a87ae42b552bfa07732b8b8232ef5e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527933"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>Planifier votre environnement Azure Time Series Insights Gen2

Cet article décrit les bonnes pratiques pour planifier et commencer à utiliser Azure Time Series Insights Gen2.

## <a name="best-practices-for-planning-and-preparation"></a>Meilleures pratiques de planification et de préparation

Les bonnes pratiques pour la planification et la préparation de votre environnement sont décrites plus en détail dans les articles suivants :

* Ce que vous obtenez lorsque vous [approvisionnez un environnement Time Series Insights Gen2](#the-gen2-environment).
* Ce que sont vos [propriétés ID Time Series et Timestamp](#configure-time-series-ids-and-timestamp-properties).
* Ce qu’est le [nouveau modèle Time Series](#understand-the-time-series-model) et comment créer le vôtre.
* Comment [envoyer des événements de manière efficace dans JSON](#shape-your-events).
* [Options de récupération d’urgence](#business-disaster-recovery) d’Azure Time Series Insights.

Azure Time Series Insights utilise un modèle de paiement à l’utilisation. Pour plus d’informations sur les coûts et la capacité, consultez [Tarification d’Azure Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-gen2-environment"></a>Environnement Gen2

Lorsque vous approvisionnez un environnement Azure Time Series Insights Gen2, vous créez deux ressources Azure :


* Un environnement Azure Time Series Insights Gen2
* Un compte Azure Storage

Dans le cadre du processus de provisionnement, vous spécifiez si vous souhaitez activer un stockage chaud. Le stockage chaud vous offre une expérience de requête à plusieurs niveaux. Lorsque cette option est activée, vous devez spécifier une période de conservation comprise entre 7 et 30 jours. Les requêtes exécutées dans la période de conservation du stockage chaud présentent généralement des temps de réponse plus rapides. Quand une requête dépasse la période de conservation du stockage chaud, elle est traitée à partir du stockage froid.

Les requêtes sur le stockage chaud sont gratuites, contrairement aux requêtes sur le stockage froid qui sont facturées. Il est important de bien comprendre vos modèles de requête et de planifier la configuration de votre stockage chaud en conséquence. Nous vous recommandons de placer l’analytique interactive sur les données les plus récentes dans votre stockage chaud, et de placer l’analyse des modèles et les tendances à long terme dans le stockage froid.

> [!NOTE]
> Pour plus d’informations sur l’interrogation de vos données chaudes, consultez les [Informations de référence sur l’API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters).

Pour commencer, vous avez besoin de trois autres éléments :

* Un [modèle Time Series](./concepts-model-overview.md)
* Une [source d’événements connectée à Time Series Insights](./concepts-streaming-ingestion-event-sources.md)
* Les [événements transférés à la source d’événement](./time-series-insights-send-events.md) qui sont mappés sur le modèle et qui respectent un format JSON valide

## <a name="review-azure-time-series-insights-gen2-limits"></a>Analyse des limites d'Azure Time Series Insights Gen2

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurer les propriétés ID Time Series et Timestamp

Pour créer un environnement Azure Time Series Insights, sélectionnez un ID Time Series. Cette opération fonctionne comme une partition logique pour vos données. Comme indiqué, assurez-vous que votre ID Time Series est prêt.

> [!IMPORTANT]
> Les ID de série chronologique *ne peuvent pas être changés ultérieurement*. Vérifiez-les tous avant la sélection finale et leur utilisation.

Vous pouvez sélectionner jusqu’à trois clés pour différencier de manière unique vos ressources. Pour plus d’informations, consultez [Bonnes pratiques pour le choix d’un ID Time Series](./time-series-insights-update-how-to-id.md) et [Règles d’ingestion](concepts-json-flattening-escaping-rules.md).

La propriété **Timestamp** est également importante. Vous pouvez désigner cette propriété lorsque vous ajoutez des sources d’événements. Chaque source d’événement a une propriété Timestamp facultative utilisée pour suivre les sources d’événements. Les valeurs de Timestamp respectent la casse et doivent être mises en forme selon les spécifications propres à chaque source.

> [!TIP]
> Vérifiez les exigences de mise en forme et d’analyse de vos sources d’événements.

Lorsque ce champ est vide, l’heure de mise en file d’attente de l’événement dans la source d’événement est utilisée comme valeur Timestamp de l’événement. Si vous envoyez des données historiques ou des événements par lot, la personnalisation de la propriété Timestamp est plus utile que le temps de file d’attente d’événement par défaut. Pour plus d’informations, consultez l’article sur l’[ajout de sources d’événements dans Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Comprendre le modèle Time Series

Vous pouvez maintenant configurer le modèle Azure Time Series de votre environnement Azure Time Series Insights. Ce nouveau modèle simplifie la recherche et l’analyse des données IoT. Il permet la collecte, la maintenance et l’enrichissement des données de série chronologique et permet de préparer des jeux de données utilisables par le consommateur. Ce modèle utilise les ID Time Series, qui sont mappés sur une instance qui associe la ressource unique à des variables, comme les types, et les hiérarchies. Pour en savoir plus, consultez la présentation [Modèle TIme Series](./concepts-model-overview.md).

Le modèle étant dynamique, il peut être généré à tout moment. Pour démarrer rapidement, générez-le et chargez-le avant d’envoyer les données à Azure Time Series Insights. Pour créer votre modèle, consultez [Utiliser le modèle Time Series](./time-series-insights-update-how-to-tsm.md).

Pour de nombreux clients, le modèle Time Series correspond à un modèle de ressource existant ou à un système ERP déjà en place. Si vous n’avez pas de modèle, une expérience utilisateur prédéfinie est [fournie](https://github.com/Microsoft/tsiclient) pour vous permettre d’être opérationnel rapidement. Pour voir comment un modèle peut vous aider, consultez l’[exemple d’environnement de démonstration](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Mettre en forme vos événements

Vous pouvez voir la façon dont vous envoyez des événements à Azure Time Series Insights. Dans l’idéal, vos événements sont dénormalisés correctement et efficacement.

Une règle de base :

* stockez les métadonnées dans votre modèle Time Series.
* Assurez-vous que Time Series Mode, les champs d’instance et les événements ne comprennent que les informations nécessaires, comme un ID de série chronologique ou une propriété Timestamp.

Pour plus d’informations, consultez [Mettre en forme les événements](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Azure Advisor](../advisor/advisor-overview.md) pour planifier vos options de configuration de récupération.
- Consultez [Azure Advisor](../advisor/advisor-overview.md) pour planifier vos options de configuration de récupération.
- En savoir plus sur [l’ingestion des données](./concepts-ingestion-overview.md) dans Azure Time Series Insights Gen2.
- Découvrez cet article sur [le stockage des données](./concepts-storage.md) dans Azure Time Series Insights Gen2.
- Renseignez-vous sur la [modélisation des données](./concepts-model-overview.md) dans Azure Time Series Insights Gen2.
