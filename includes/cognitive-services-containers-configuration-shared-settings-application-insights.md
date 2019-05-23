---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116669"
---
Le paramètre `ApplicationInsights` vous permet d’ajouter la prise en charge de la télémétrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) à votre conteneur. Application Insights assure une supervision approfondie de votre conteneur. Vous pouvez facilement superviser la disponibilité, les performances et l’utilisation de votre conteneur. De plus, vous pouvez identifier et diagnostiquer rapidement les erreurs dans votre conteneur.

Le tableau suivant décrit les paramètres de configuration pris en charge sous la section `ApplicationInsights`.

|Obligatoire| Nom | Type de données | Description |
|--|------|-----------|-------------|
|Non | `InstrumentationKey` | Chaîne | Clé d’instrumentation de l’instance Application Insights à laquelle les données de télémétrie du conteneur sont envoyées. Pour plus d’informations, consultez [Application Insights pour ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exemple :<br>`InstrumentationKey=123456789`|

