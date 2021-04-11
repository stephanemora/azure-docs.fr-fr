---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 4cbf179658ddc13aca9bf30934dc28ab0cf5fd9d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081063"
---
| Propriété | Description |
|--|--|
|**`<DESTINATION>`**| Destination à laquelle les journaux sont envoyés. Les valeurs valides sont `AppInsights` et `Blob`.<br/>Quand vous utilisez `AppInsights`, vérifiez qu’[Application Insights est activé dans votre application de fonction](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration).<br/>Quand vous affectez `Blob` comme destination, les journaux sont créés dans un conteneur d’objets blob nommé `azure-functions-scale-controller` dans le compte de stockage par défaut défini dans le paramètre d’application `AzureWebJobsStorage`. |
|**`<VERBOSITY>`** | Spécifie le niveau de journalisation. Les valeurs prises en charge sont `None`, `Warning` et `Verbose`.<br/>Quand il est défini sur `Verbose`, le contrôleur de mise à l’échelle enregistre une raison pour chaque modification du nombre de Workers, ainsi que des informations sur les déclencheurs pris en compte dans ces décisions. Les journaux détaillés incluent les avertissements de déclencheur et les hachages utilisés par les déclencheurs avant et après l’exécution du contrôleur de mise à l’échelle. |

> [!TIP]
> Gardez à l’esprit que, lorsque vous laissez l’option de journalisation du contrôleur de mise à l’échelle activée, cela a un impact sur les [coûts potentiels de surveillance de votre application de fonction](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits). Envisagez d’activer la journalisation jusqu’à ce que vous ayez collecté suffisamment de données pour comprendre le comportement du contrôleur de mise à l’échelle, puis de la désactiver.
