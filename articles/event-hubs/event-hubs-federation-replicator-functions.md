---
title: Tâches et applications de réplication d’événements - Azure Event Hubs | Microsoft Docs
description: Cet article fournit une vue d’ensemble de la création d’applications et de tâches de réplication d’événements avec Azure Functions.
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97663441"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Tâches et applications de réplication d’événements avec Azure Functions

> [!TIP]
> Pour toutes les tâches de réplication avec état où vous devez prendre en compte les charges utiles de vos événements et les transformer, les agréger, les enrichir ou les réduire, utilisez [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) au lieu d’Azure Functions.

Comme expliqué dans l’article sur [la réplication d’événements et la fédération entre les régions](event-hubs-federation-overview.md), la réplication sans état des flux d’événements entre les paires d’Event Hubs et entre les Event Hubs et les autres sources et cibles de flux d’événements s’appuie sur Azure Functions.

[Azure Functions](../azure-functions/functions-overview.md) est un environnement d’exécution fiable et évolutif qui permet de configurer et d’exécuter des applications serverless, y compris les tâches de réplication d’événements et de fédération.

Dans cette vue d’ensemble, vous découvrirez les fonctionnalités intégrées d’Azure Functions pour ces applications, ainsi que les blocs de code que vous pouvez adapter et modifier pour les tâches de transformation, et vous apprendrez à configurer une application Azure Functions de sorte qu’elle s’intègre facilement à Event Hubs et à d’autres services de messagerie Azure. Cet article va très souvent faire référence à la documentation Azure Functions.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









