---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169904"
---
| | |
|--|--|
|**`<DESTINATION>`**| Destination à laquelle les journaux sont envoyés. Les valeurs valides sont `AppInsights` et `Blob`.<br/>Quand vous utilisez `AppInsights`, vérifiez qu’[Application Insights est activé dans votre application de fonction](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration).<br/>Quand vous affectez `Blob` comme destination, les journaux sont créés dans un conteneur d’objets blob nommé `azure-functions-scale-controller` dans le compte de stockage par défaut défini dans le paramètre d’application `AzureWebJobsStorage`. |
|**`<VERBOSITY>`** | Spécifie le niveau de journalisation. Les valeurs prises en charge sont `None`, `Warning` et `Verbose`.<br/>Quand il est défini sur `Verbose`, le contrôleur de mise à l’échelle enregistre une raison pour chaque modification du nombre de Workers, ainsi que des informations sur les déclencheurs pris en compte dans ces décisions. Les journaux détaillés incluent les avertissements de déclencheur et les hachages utilisés par les déclencheurs avant et après l’exécution du contrôleur de mise à l’échelle. |

> [!CAUTION]
> Ne laissez pas la journalisation du contrôleur de mise à l’échelle activée. Activez la journalisation jusqu’à ce que vous ayez collecté suffisamment de données pour comprendre le comportement du contrôleur de mise à l’échelle, puis désactivez-la.