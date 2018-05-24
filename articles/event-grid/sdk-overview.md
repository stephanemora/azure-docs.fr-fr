---
title: Kits SDK Azure Event Grid
description: Décrit les Kits de développement logiciel (SDK) d’Azure Event Grid. Ils assurent la gestion, la publication et la consommation.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 82f08341f8c96695a4ceddb19d1b610d70b89f88
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Kits SDK Event Grid de gestion et de publication

Event Grid fournit des Kits de développement logiciel (SDK) qui permettent de gérer ses ressources et de publier (POST) des événements par programme.

## <a name="management-sdks"></a>Kits SDK de gestion

Les Kits SDK de gestion permettent de créer, de mettre à jour et de supprimer des abonnements et des rubriques Event Grid. Voici les Kits SDK actuellement disponibles :

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure.eventgrid-2018-01-01/azure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Kits SDK de plan de données

Les kits SDK de plan de données permettent de publier des événements sur des rubriques tout en gérant l’authentification, la formation de l’événement et la publication asynchrone sur le point de terminaison spécifié. Ils vous permettent également de consommer des événements internes. Voici les Kits SDK actuellement disponibles :

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Event Grid, consultez l’article [What is Event Grid?](overview.md) (Présentation d’Event Grid).
* Pour connaître les commandes Event Grid dans Azure CLI, consultez la section [Azure CLI](/cli/azure/eventgrid).
* Pour connaître les commandes Event Grid dans PowerShell, consultez la section [PowerShell](/powershell/module/azurerm.eventgrid).