---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: 729275766806bf5fe47d35b04ddc58b11c58217b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908772"
---
## <a name="supported-versions"></a>Versions prises en charge

Les versions du runtime Functions fonctionnent avec des versions spécifiques de .NET. Pour en savoir plus sur les versions de Functions, consultez [Vue d’ensemble des versions du runtime Azure Functions](../articles/azure-functions/functions-versions.md). La prise en charge des versions varie selon que vos fonctions s’exécutent in-process ou out-of-process (isolées). 

Le tableau suivant indique le niveau le plus élevé de .NET Core et .NET Framework pouvant être utilisé avec une version spécifique de Functions. 

| Version du runtime Functions | In-process<br/>([Bibliothèque de classes .NET](../articles/azure-functions/functions-dotnet-class-library.md)) | Out-of-process<br/>([Isolé .NET](../articles/azure-functions/dotnet-isolated-process-guide.md)) |
| ---- | ---- | --- |
| Functions 4.x (préversion) | .NET 6.0 (préversion)| .NET 6.0 (préversion)<sup>2</sup> |
| Functions 3.x | .NET Core 3.1 | .NET 5.0 |
| Functions 2.x | .NET Core 2.1<sup>1</sup> | n/a |
| Functions 1.x | .NET Framework 4.8 | n/a |

<sup>1</sup> Pour plus d’informations, consultez [Considérations relatives à Functions v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).    
<sup>2</sup> Pour le moment, vous pouvez uniquement créer des fonctions de processus isolés en utilisant Azure Functions Core Tools. Pour plus d’informations, consultez [Démarrage rapide : Créer une fonction C# dans Azure à partir de la ligne de commande](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process).  

Pour obtenir les dernières informations sur les versions Azure Functions, notamment sur la suppression des versions mineures les plus anciennes, surveillez les [annonces Azure App Service](https://github.com/Azure/app-service-announcements/issues).
