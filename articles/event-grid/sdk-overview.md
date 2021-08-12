---
title: Kits SDK Azure Event Grid
description: Décrit les Kits de développement logiciel (SDK) d’Azure Event Grid. Ils assurent la gestion, la publication et la consommation.
ms.topic: reference
ms.date: 05/17/2021
ms.openlocfilehash: 330f0399a16e6765a754d342580a221adc6fb7d9
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062773"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Kits SDK Event Grid de gestion et de publication

Event Grid fournit des Kits de développement logiciel (SDK) qui permettent de gérer ses ressources et de publier (POST) des événements par programme.

## <a name="management-sdks"></a>Kits SDK de gestion

Les Kits SDK de gestion permettent de créer, de mettre à jour et de supprimer des abonnements et des rubriques Event Grid. Voici les Kits SDK actuellement disponibles :

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Nœud](https://www.npmjs.com/package/@azure/arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Kits SDK de plan de données

Les kits SDK de plan de données permettent de publier des événements sur des rubriques tout en gérant l’authentification, la formation de l’événement et la publication asynchrone sur le point de terminaison spécifié. Ils vous permettent également de consommer des événements internes. Voici les Kits SDK actuellement disponibles :

| Langage de programmation | Kit SDK | 
| -------------------- | ---------- | 
| .NET | Dernier kit de développement logiciel (SDK) stable : [Azure.Messaging.EventGrid](https://www.nuget.org/packages/Azure.Messaging.EventGrid/)<p>Kit de développement logiciel (SDK) hérité : [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) |
| Java | Dernier kit de développement logiciel (SDK) stable : [azure-messaging-eventgrid](https://search.maven.org/artifact/com.azure/azure-messaging-eventgrid/)<p>SDK hérité : [azure-eventgrid](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)</p> |  
| Python | [azure-eventgrid](https://pypi.org/project/azure-eventgrid/) |
| JavaScript | [@azure/eventgrid](https://www.npmjs.com/package/@azure/eventgrid/) | 
| Go | [Kit de développement logiciel (SDK) Azure pour Go](https://github.com/Azure/azure-sdk-for-go) |
| Ruby | [azure_event_grid](https://rubygems.org/gems/azure_event_grid) |


## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir des exemples d’application, consultez la page [Exemples de code Event Grid](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Pour une présentation d’Event Grid, consultez l’article [What is Event Grid?](overview.md) (Présentation d’Event Grid).
* Pour connaître les commandes Event Grid dans Azure CLI, consultez la section [Azure CLI](/cli/azure/eventgrid).
* Pour connaître les commandes Event Grid dans PowerShell, consultez la section [PowerShell](/powershell/module/az.eventgrid).
