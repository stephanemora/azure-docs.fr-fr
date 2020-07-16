---
title: 'Vue d’ensemble de l’ingestion : Azure Time Series Insights | Microsoft Docs'
description: En savoir plus sur l’ingestion des données dans Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: ecee6817b58639d1b60264c818ab9517e5d133e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049366"
---
# <a name="azure-time-series-insights-data-ingestion-overview"></a>Présentation de l’ingestion de données Azure Time Series Insights

Votre environnement Azure Time Series Insights contient un *moteur d’ingestion* pour collecter, traiter et stocker des données de série chronologique de diffusion en continu. À mesure que les données arrivent dans vos sources d’événements, Azure Time Series Insights consomme et stocke vos données en temps quasi réel.

[![Vue d’ensemble de l’ingestion](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Rubriques relatives à l’ingestion

Les articles suivants décrivent en détail le traitement des données, notamment les bonnes pratiques à suivre :

* En savoir plus sur les [sources d’événements](concepts-streaming-ingestion-event-sources.md) et des conseils sur la sélection d’un horodateur de source d’événement.

* Vérifier les [types de données](concepts-supported-data-types.md) pris en charge

* Découvrez comment le moteur d’ingestion appliquera un ensemble de [règles](./concepts-json-flattening-escaping-rules.md) à vos propriétés JSON pour créer vos colonnes de compte de stockage.

* Passez en revue les [limitations du débit](concepts-streaming-throughput-limitations.md) de votre environnement pour planifier vos besoins en matière de mise à l’échelle.

## <a name="next-steps"></a>Étapes suivantes

* Continuez pour en savoir plus sur les [sources d’événements](concepts-streaming-ingestion-event-sources.md) pour votre environnement Azure Time Series Insights. 
