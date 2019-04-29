---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599432"
---
Le paramètre `ApplicationInsights` vous permet d’ajouter la prise en charge de la télémétrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) à votre conteneur. Application Insights assure une supervision approfondie de votre conteneur. Vous pouvez facilement superviser la disponibilité, les performances et l’utilisation de votre conteneur. De plus, vous pouvez identifier et diagnostiquer rapidement les erreurs dans votre conteneur.

Le tableau suivant décrit les paramètres de configuration pris en charge sous la section `ApplicationInsights`.

|Obligatoire| Nom | Type de données | Description |
|--|------|-----------|-------------|
|Non | `InstrumentationKey` | String | Clé d’instrumentation de l’instance Application Insights à laquelle les données de télémétrie du conteneur sont envoyées. Pour plus d’informations, consultez [Application Insights pour ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exemple :<br>`InstrumentationKey=123456789`|

