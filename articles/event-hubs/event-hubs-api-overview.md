---
title: Vue d’ensemble de l’API Azure Event Hubs | Microsoft Docs
description: Cet article fournit une vue d’ensemble des API disponibles (exécution et gestion) pour utiliser le service Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 08e10996f633d35ffbf946b61937a0e9a76f7227
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162223"
---
# <a name="available-event-hubs-apis"></a>API Event Hubs disponibles

Cet article décrit l’ensemble des clients API disponibles, que vous pouvez utiliser pour la gestion des ressources Event Hubs.

## <a name="runtime-apis"></a>API de runtime

La section suivante décrit tous les clients de runtime Azure Event Hubs actuellement disponibles. Bien que certaines de ces bibliothèques incluent aussi des fonctionnalités de gestion limitées, il existe [des bibliothèques](#management-apis) dédiées aux opérations de gestion. Ces bibliothèques sont principalement destinées à envoyer et recevoir des messages d’un hub d’événements.

Pour plus d’informations sur l’état actuel de chaque bibliothèque runtime, consultez la section [Informations supplémentaires](#additional-information).

| Langage/Plateforme | Package client | Package EventProcessorHost | Référentiel |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/A |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Nœud | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/A | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | N/A | N/A | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Informations supplémentaires

#### <a name="net"></a>.NET

L’écosystème .NET ayant plusieurs runtimes, il existe plusieurs bibliothèques .NET pour Event Hubs. La bibliothèque .NET Standard peut être exécutée à l’aide de .NET Core ou de .NET Framework, tandis que la bibliothèque .NET Framework peut uniquement être exécutée dans un environnement .NET Framework. Pour plus d’informations sur les versions .NET Framework, consultez [Versions d’infrastructure](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Nœud

La [bibliothèque JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) est actuellement en préversion et est tenue à jour en parallèle par les employés de Microsoft et des contributeurs externes. Toutes les contributions, y compris le code source sont les bienvenues et seront examinées.

## <a name="management-apis"></a>API de gestion

Le tableau suivant répertorie toutes les bibliothèques dédiées à la gestion actuellement disponibles. Aucune de ces bibliothèques ne contient d’opérations de runtime. Elles sont uniquement destinées à la gestion des entités Event Hubs.

| Langage/Plateforme | Gestion des packages | Référentiel |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)
* [Créer un concentrateur d’événements](event-hubs-create.md)
* [FAQ sur les hubs d’événements](event-hubs-faq.md)
