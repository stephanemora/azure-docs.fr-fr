---
title: Planifier vitre environnement Azure Time Series Insights en préversion | Microsoft Docs
description: Planifier votre environnement Azure Time Series Insights (préversion).
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: b3fab86b2b2f0ad892e02cd089dbd7c45ce601d6
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205769"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planifier votre environnement Azure Time Series Insights (préversion)

Cet article décrit les meilleures pratiques pour planifier et commencer à utiliser la préversion d’Azure Time Series Insights.

> [!NOTE]
> Consultez [planifier votre environnement Azure temps série Insights GA](time-series-insights-environment-planning.md), pour les méthodes recommandées planifier une instance TSI disponibilité générale.

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

* Un [modèle de série chronologique](./time-series-insights-update-tsm.md).
* Un [source d’événements connecté à Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md).
* [Événements transférés à la source d’événement](./time-series-insights-send-events.md) qui sont mappées au modèle et sont au format JSON valide.

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurer les propriétés de l’ID de série de temps et d’horodatage

Pour créer un nouvel environnement Time Series Insights, sélectionnez un **ID de série de temps**. Cette opération fonctionne comme une partition logique pour vos données. Comme indiqué, assurez-vous que votre ID Time Series est prêt.

> [!IMPORTANT]
> Les ID Time Series sont *immuables* et *ne peuvent pas être modifiés ultérieurement*. Vérifiez-les tous avant la sélection finale et leur utilisation.

Vous pouvez sélectionner jusqu’à trois (3) clés pour différencier de manière unique vos ressources. Pour plus d’informations, consultez [Meilleures pratiques pour le choix d’un ID Time Series](./time-series-insights-update-how-to-id.md) et [Stockage et entrée](./time-series-insights-update-storage-ingress.md).

La propriété Timestamp est également importante. Vous pouvez désigner cette propriété lorsque vous ajoutez des sources d’événements. Chaque source d’événement a une propriété Timestamp facultative utilisée pour suivre les sources d’événements. Les valeurs de Timestamp respectent la casse et doivent être mises en forme selon les spécifications propres à chaque source.

> [!TIP]
> Vérifiez les exigences de mise en forme et d’analyse de vos sources d’événements.

Lorsque ce champ est vide, l’heure de mise en file d’attente de l’événement dans la source d’événement est utilisée comme valeur Timestamp de l’événement. Si vous envoyez des données historiques ou des événements par lot, la personnalisation de la propriété Timestamp est plus utile que le temps de file d’attente d’événement par défaut. Pour plus d’informations, consultez l’article [Ajouter des sources d’événements dans IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Comprendre le modèle Time Series

Vous pouvez maintenant configurer le modèle Time Series de votre environnement Time Series Insights. Ce nouveau modèle simplifie la recherche et l’analyse des données IoT. Il permet la collecte, la maintenance et l’enrichissement des données de série chronologique et permet de préparer des jeux de données utilisables par le consommateur. Ce modèle utilise les **ID Time Series**, qui sont mappés sur une instance qui associe la ressource unique à des variables, comme les types, et les hiérarchies. En savoir plus sur le nouveau [modèle Time Series](./time-series-insights-update-tsm.md).

Le modèle étant dynamique, il peut être généré à tout moment. Pour démarrer rapidement, générez-le et chargez-le avant d’envoyer les données à Time Series Insights. Pour créer votre modèle, consultez [Utiliser le modèle Time Series](./time-series-insights-update-how-to-tsm.md).

Pour de nombreux clients, le modèle Time Series correspond à un modèle de ressource existant ou à un système ERP déjà en place. Si vous n’avez pas de modèle, une expérience utilisateur prédéfinie est [fournie](https://github.com/Microsoft/tsiclient) pour vous permettre d’être opérationnel rapidement. Pour voir comment un modèle peut vous aider, consultez l’[exemple d’environnement de démonstration](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Mettre en forme vos événements

Vous pouvez voir la façon dont vous envoyez des événements à Time Series Insights. Dans l’idéal, vos événements sont dénormalisés correctement et efficacement.

Une règle de base :

* stockez les métadonnées dans votre modèle Time Series
* Mode de série de temps, les champs d’instance et les événements incluent uniquement les informations nécessaires, comme un **ID de série de temps** ou **Timestamp**.

Pour plus d’informations, consultez [Événements Shape](./time-series-insights-send-events.md#json).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [entrée et stockage](./time-series-insights-update-storage-ingress.md) dans le temps série Insights Preview.

- En savoir plus sur [modélisation des données](./time-series-insights-update-tsm.md) dans le temps série Insights Preview.