---
title: Vue d’ensemble de l’ingestion – Azure Time Series Insights Gen2 | Microsoft Docs
description: Apprenez-en davantage sur l’ingestion des données dans Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: b688bbf454b3df9f6ae368c66a62657a5522d720
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505448"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Vue d’ensemble de l’ingestion de données dans Azure Time Series Insights Gen2

Votre environnement Azure Time Series Insights Gen2 contient un *moteur d’ingestion* pour collecter, traiter et stocker des données de série chronologique de diffusion en continu. À mesure que les données arrivent dans vos sources d'événements, Azure Time Series Insights Gen2 consomme et stocke vos données en quasi-temps réel.

[![Vue d’ensemble de l’ingestion](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Rubriques relatives à l’ingestion

Les articles suivants décrivent en détail le traitement des données, notamment les bonnes pratiques à suivre :

* En savoir plus sur les [sources d’événements](./concepts-streaming-ingestion-event-sources.md) et des conseils sur la sélection d’un horodateur de source d’événement.

* Vérifier les [types de données](./concepts-supported-data-types.md) pris en charge

* Découvrez comment le moteur d’ingestion appliquera un ensemble de [règles](./concepts-json-flattening-escaping-rules.md) à vos propriétés JSON pour créer vos colonnes de compte de stockage.

* Passez en revue les [limitations du débit](./concepts-streaming-ingress-throughput-limits.md) de votre environnement pour planifier vos besoins en matière de mise à l’échelle.

## <a name="next-steps"></a>Étapes suivantes

* Continuez pour en savoir plus sur les [sources d’événements](./concepts-streaming-ingestion-event-sources.md) pour votre environnement Azure Time Series Insights Gen2.
