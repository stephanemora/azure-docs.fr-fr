---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: e65a628f846892294fdd9be693b89a535ac694a0
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111901441"
---
## <a name="supported-versions"></a>Versions prises en charge

Les versions du runtime Functions fonctionnent avec des versions spécifiques de .NET. Pour en savoir plus sur les versions de Functions, consultez [Vue d’ensemble des versions du runtime Azure Functions](../articles/azure-functions/functions-versions.md). La prise en charge des versions varie selon que vos fonctions s’exécutent in-process ou out-of-process (isolées). 

Le tableau suivant indique le niveau le plus élevé de .NET Core et .NET Framework pouvant être utilisé avec une version spécifique de Functions. 

| Version du runtime Functions | In-process<br/>([Bibliothèque de classes .NET](../articles/azure-functions/functions-dotnet-class-library.md)) | Out-of-process<br/>([Isolé .NET](../articles/azure-functions/dotnet-isolated-process-guide.md)) |
| ---- | ---- | --- |
| Functions 4.x<sup>1</sup> | .NET 6.0 (préversion)| .NET 6.0 (préversion) |
| Functions 3.x | .NET Core 3.1 | .NET 5.0 |
| Functions 2.x | .NET Core 2.1<sup>2</sup> | n/a |
| Functions 1.x | .NET Framework 4.8 | n/a |

<sup>1</sup> Azure Functions fournit une prise en charge expérimentale pour vous permettre de tester vos fonctions en cours d’exécution sur la version préliminaire de .NET 6.0. Cette version préliminaire n’est pas officiellement prise en charge. Pour plus d’informations, consultez la page [Version bêta d’Azure Functions (version 4)](https://aka.ms/functions-dotnet6earlypreview-wiki).  
<sup>2</sup> Pour plus d’informations, consultez [Considérations relatives à Functions v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).   

Pour obtenir les dernières informations sur les versions Azure Functions, notamment sur la suppression des versions mineures les plus anciennes, surveillez les [annonces Azure App Service](https://github.com/Azure/app-service-announcements/issues).
