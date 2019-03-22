---
title: Liaisons de IoT Hub Azure pour Azure Functions
description: Découvrez comment utiliser des liaisons de IoT Hub dans Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: e71e102a5a6df44e6bdd6a845540de3cbbef98f3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57457494"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Liaisons de IoT Hub Azure pour Azure Functions

Cet article explique comment utiliser des liaisons Azure Functions pour IoT Hub. La prise en charge IoT Hub est basé sur le [Azure Event Hubs liaison](link to event hub doc).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Packages - Functions 1.x

Pour Azure Functions version 1.x, les liaisons de IoT Hub sont fournies dans le [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) package NuGet, version 2.x. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Packages - Functions 2.x

Pour Functions 2.x, utilisez le package [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs), version 3.x. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> Tandis que les exemples de code suivants utilisent les API Event Hub, la syntaxe donnée est applicable aux fonctions de IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
