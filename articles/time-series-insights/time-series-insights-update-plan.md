---
title: Planifier vitre environnement Azure Time Series Insights en préversion | Microsoft Docs
description: Planifier votre environnement Azure Time Series Insights (préversion).
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 435e5f2163270672ac5f1f5695ca2fe9be22ee6b
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388760"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planifier votre environnement Azure Time Series Insights (préversion)

Cet article décrit les meilleures pratiques pour planifier et démarrer rapidement à l’aide de la version préliminaire d’Azure temps série Insights.

> [!NOTE]
> Pour obtenir des recommandations planifier une instance de Time Series Insights disponibilité générale, consultez [planifier votre environnement de disponibilité générale d’Azure Time Series Insights](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Meilleures pratiques de planification et de préparation

Pour démarrer avec Time Series Insights, il est important de comprendre les points suivants :

* Ce qui s’affiche lorsque vous [approvisionner un environnement de temps série Insights Preview](#the-preview-environment).
* Ce que votre [propriétés ID de série de temps et d’horodatage sont](#configure-time-series-ids-and-timestamp-properties).
* Le [modèle de série chronologique est](#understand-the-time-series-model)et comment créer les vôtres.
* Comment [envoyer efficacement des événements au format JSON](#shape-your-events).
* Time Series Insights [options de récupération d’urgence](#business-disaster-recovery).

Azure Time Series Insights utilise un modèle de paiement à l’utilisation d’entreprise. Pour plus d’informations sur les frais et sur la capacité, consultez [Tarification de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>L’environnement de version préliminaire

Lorsque vous approvisionnez un environnement Time Series Insights en préversion, vous créez deux ressources Azure :

* Un environnement Azure Time Series Insights en préversion
* Un compte Stockage Azure à usage général V1

Pour commencer, vous avez besoin de trois autres éléments :

* Un [modèle Time Series](./time-series-insights-update-tsm.md)
* Une [source d’événements connectée à Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* Les [événements transférés à la source d’événement](./time-series-insights-send-events.md) qui sont mappés sur le modèle et qui respectent un format JSON valide

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurer les propriétés de l’ID de série de temps et d’horodatage

Pour créer un environnement Time Series Insights, sélectionnez un ID Time Series. Cette opération fonctionne comme une partition logique pour vos données. Comme indiqué, assurez-vous que votre ID Time Series est prêt.

> [!IMPORTANT]
> Les ID Time Series sont *immuables* et *ne peuvent pas être modifiés ultérieurement*. Vérifiez-les tous avant la sélection finale et leur utilisation.

Vous pouvez sélectionner jusqu'à trois clés pour différencier de manière unique vos ressources. Pour plus d’informations, consultez [Meilleures pratiques pour le choix d’un ID Time Series](./time-series-insights-update-how-to-id.md) et [Stockage et entrée](./time-series-insights-update-storage-ingress.md).

La propriété Timestamp est également importante. Vous pouvez désigner cette propriété lorsque vous ajoutez des sources d’événements. Chaque source d’événement a une propriété Timestamp facultative utilisée pour suivre les sources d’événements. Les valeurs de Timestamp respectent la casse et doivent être mises en forme selon les spécifications propres à chaque source.

> [!TIP]
> Vérifiez les exigences de mise en forme et d’analyse de vos sources d’événements.

Lorsque ce champ est vide, l’heure de mise en file d’attente de l’événement dans la source d’événement est utilisée comme valeur Timestamp de l’événement. Si vous envoyez des données historiques ou des événements par lot, la personnalisation de la propriété Timestamp est plus utile que le temps de file d’attente d’événement par défaut. Pour plus d’informations, découvrez comment [ajouter des sources d’événements dans Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Comprendre le modèle Time Series

Vous pouvez maintenant configurer le modèle Time Series de votre environnement Time Series Insights. Ce nouveau modèle simplifie la recherche et l’analyse des données IoT. Il permet la collecte, la maintenance et l’enrichissement des données de série chronologique et permet de préparer des jeux de données utilisables par le consommateur. Le modèle utilise des ID de série de temps, qui correspondent à une instance qui associe la ressource unique avec des variables, appelés types et des hiérarchies. En savoir plus sur le nouveau [modèle Time Series](./time-series-insights-update-tsm.md).

Le modèle étant dynamique, il peut être généré à tout moment. Pour démarrer rapidement, générez-le et chargez-le avant d’envoyer les données à Time Series Insights. Pour créer votre modèle, consultez [Utiliser le modèle Time Series](./time-series-insights-update-how-to-tsm.md).

Pour de nombreux clients, le modèle Time Series correspond à un modèle de ressource existant ou à un système ERP déjà en place. Si vous n’avez pas de modèle, une expérience utilisateur prédéfinie est [fournie](https://github.com/Microsoft/tsiclient) pour vous permettre d’être opérationnel rapidement. Pour voir comment un modèle peut vous aider, consultez l’[exemple d’environnement de démonstration](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Mettre en forme vos événements

Vous pouvez voir la façon dont vous envoyez des événements à Time Series Insights. Dans l’idéal, vos événements sont dénormalisés correctement et efficacement.

Une règle de base :

* Store les métadonnées dans votre modèle de série chronologique.
* Mode de série de temps, les champs d’instance et les événements incluent uniquement les informations nécessaires, tel qu’un ID de série de temps ou l’horodatage.

Pour plus d’informations, consultez [Événements Shape](./time-series-insights-send-events.md#json).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [entrée et stockage](./time-series-insights-update-storage-ingress.md) dans le temps série Insights Preview.
- En savoir plus sur [modélisation des données](./time-series-insights-update-tsm.md) dans le temps série Insights Preview.