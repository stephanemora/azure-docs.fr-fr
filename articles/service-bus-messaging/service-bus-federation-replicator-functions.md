---
title: Tâches et applications de réplication de messages - Azure Service Bus | Microsoft Docs
description: Cet article fournit une vue d’ensemble de la création d’applications et de tâches de réplication de messages avec Azure Functions.
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 78598401816adc32eecb34e2bd11fa49e8a44b0f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211807"
---
# <a name="message-replication-tasks-and-applications"></a>Tâches et applications de réplication des messages

Comme expliqué dans l’article sur la [réplication des messages et la fédération interrégionale](service-bus-federation-overview.md), la réplication des séquences de messages entre des paires d’entités Service Bus et entre Service Bus et d’autres sources et cibles de messages repose généralement sur Azure Functions.

[Azure Functions](../azure-functions/functions-overview.md) est un environnement d’exécution fiable et scalable qui permet de configurer et d’exécuter des applications serverless, notamment des tâches de [réplication de messages et de fédération](service-bus-federation-overview.md).

Dans cette vue d’ensemble, vous allez découvrir les fonctionnalités intégrées d’Azure Functions pour ces applications, ainsi que les blocs de code que vous pouvez adapter et modifier pour les tâches de transformation, et vous allez apprendre à configurer une application Azure Functions de sorte qu’elle s’intègre facilement à Service Bus et à d’autres services de messagerie Azure. Cet article va très souvent faire référence à la documentation Azure Functions.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
