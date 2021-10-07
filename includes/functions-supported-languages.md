---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6f0978a5177a499ccc48eda83c10b327d92b8b94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599935"
---
|Langage                                 |1.x         |2.x| 3.x | 4.x (préversion) |
|-----------------------------------------|------------|---| --- | --- |
|[C#](../articles/azure-functions/functions-dotnet-class-library.md)|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>1</sup>)| Disponibilité générale (.NET Core 3.1)<br/>[GA (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) | Préversion (.NET 6.0) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|Disponibilité générale (Nœud 6)|Disponibilité générale (Nœuds 10 et 8)| Disponibilité générale (Nœuds 14, 12 et 10) | Préversion (Nœud 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>1</sup>)| Disponibilité générale (.NET Core 3.1) | Préversion (.NET 6.0) |
|[Java](../articles/azure-functions/functions-reference-java.md)|N/A|Disponibilité générale (Java 8)| GA (Java 11 et 8)| Préversion (Java 11 et 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |N/A|GA (PowerShell Core 6)| Disponibilité générale (PowerShell 7.0 et Core 6)| Préversion (PowerShell 7.0)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|N/A|Disponibilité générale (Python 3.7 et 3.6)| Disponibilité générale (Python 3.9, 3.8, 3.7 et 3.6)| Préversion (Python 3.9, 3.8)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript)<sup>2</sup> |N/A|GA| GA | PRÉVERSION |

<sup>1</sup> Les applications de bibliothèque de classes .NET qui ciblent le runtime version 2.x s’exécutent sur .NET Core 3.1 en mode de compatibilité .NET Core 2.x. Pour plus d’informations, consultez [Considérations relatives à Functions v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> Prise en charge via la transpilation vers JavaScript.

Pour plus d’informations sur les versions linguistiques prises en charge, consultez l’article du Guide du développeur spécifique à une langue.   
Pour plus d’informations sur les modifications prévues sur la prise en charge des langages, consultez la [Feuille de route Azure](https://azure.microsoft.com/roadmap/?tag=functions).
