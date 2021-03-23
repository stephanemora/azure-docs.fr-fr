---
title: Gérer un environnement Gen2 - Azure Time Series | Microsoft Docs
description: Apprenez à gérer un environnement Azure Time Series Insights Gen2.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: cbd28bdf5318bdaf932447f5d1f936d2c614a7f3
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461893"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Gérer Azure Time Series Insights Gen2

Une fois que vous avez créé votre environnement Azure Time Series Insights Gen2 à l’aide de [l’Azure CLI](./how-to-create-environment-using-cli.md) ou [du portail Azure](./how-to-create-environment-using-portal.md), vous pouvez modifier vos stratégies d’accès et d’autres attributs d’environnement en fonction de vos besoins professionnels.

## <a name="manage-the-environment"></a>Gérer l’environnement

Vous pouvez gérer votre environnement Azure Time Series Insights Gen2 à l’aide du [portail Azure](https://portal.azure.com/). Il y a quelques différences essentielles entre les environnements Gen2 et les environnements Gen1 S1 ou S2 en disponibilité générale à ne pas oublier lorsque vous gérez votre environnement par le biais du portail Azure :

* Le volet **Vue d’ensemble** du portail Azure Gen2 présente les changements suivants :

  * La capacité est supprimée car elle ne s’applique pas aux environnements Gen2.
  * La propriété d’**ID de série chronologique** est ajoutée. Elle détermine la façon dont vos données sont partitionnées.
  * Les jeux de données de référence sont supprimés.
  * L’URL affichée vous dirige vers [l’Explorateur Azure Time Series Insights](./concepts-ux-panels.md).
  * Le nom de votre compte de stockage Azure est répertorié.

* Le volet **Configurer** du portail Azure est supprimé, car les unités d’échelle ne s’appliquent pas aux environnements Azure Time Series Insights Gen2. Toutefois, vous pouvez utiliser **Configuration du stockage** pour configurer le magasin chaud récemment introduit.

* Le volet **Données de référence** du portail Azure est supprimé dans Azure Time Series Insights Gen2 parce que le concept de données de référence a été remplacé par le [Modèle de série chronologique (TSM)](./concepts-model-overview.md).

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Environnement Azure Time Series Insights Gen2 dans le portail Azure":::

## <a name="next-steps"></a>Étapes suivantes

* Vérifiez la liste de [meilleures pratiques en matière d’ingestion de diffusion en continu](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)
* Découvrez comment [diagnostiquer et résoudre les problèmes](./how-to-diagnose-troubleshoot.md)
