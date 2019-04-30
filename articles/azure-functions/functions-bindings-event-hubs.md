---
title: Liaisons Azure Event Hubs pour Azure Functions
description: Découvrez comment utiliser des liaisons Azure Event Hubs dans Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 11/08/2017
ms.date: 03/20/2019
ms.author: v-junlch
ms.openlocfilehash: 7ca3ab8e3becf884fdba460f1981430e0c66ef99
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104446"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Liaisons Azure Event Hubs pour Azure Functions

Cet article explique comment utiliser des liaisons [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) pour Azure Functions. Azure Functions prend en charge des liaisons de déclencheur et de sortie pour des Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Packages - Functions 1.x

Pour la version Azure Functions 1.x, les liaisons Event Hubs sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus), version 2.x.
Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs).


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Packages - Functions 2.x

Pour Functions 2.x, utilisez le package [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs), version 3.x.
Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)

<!-- Update_Description: wording update -->