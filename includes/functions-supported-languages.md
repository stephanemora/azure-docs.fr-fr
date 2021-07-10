---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3edb1db5fd714e8ff9b21a0617ac5053e77a0e20
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111905656"
---
|Langage                                 |1.x         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-dotnet-class-library.md)<sup>1</sup>|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>2</sup>)| Disponibilité générale (.NET Core 3.1)<br/>[GA (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|Disponibilité générale (Nœud 6)|Disponibilité générale (Nœuds 10 et 8)| Disponibilité générale (Nœuds 14, 12 et 10) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>2</sup>)| Disponibilité générale (.NET Core 3.1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|N/A|Disponibilité générale (Java 8)| GA (Java 11 et 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |N/A|GA (PowerShell Core 6)| Disponibilité générale (PowerShell 7 et Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|N/A|Disponibilité générale (Python 3.7 et 3.6)| Disponibilité générale (Python 3.8, 3.7 et 3.6) <br/> Préversion (Python 3.9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |N/A|GA<sup>3</sup>| GA<sup>3</sup> |

<sup>1</sup> Une version d’essai d’Azure Functions est disponible, vous permettant ainsi d’utiliser la préversion de .NET 6.0. Pour plus d’informations, consultez la page [Version bêta d’Azure Functions (version 4)](https://aka.ms/functions-dotnet6earlypreview-wiki).
<sup>2</sup> Les applications de bibliothèque de classes .NET qui ciblent le runtime version 2.x s’exécutent sur .NET Core 3.1 en mode de compatibilité .NET Core 2.x. Pour plus d’informations, consultez [Considérations relatives à Functions v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>3</sup> Prise en charge de la transpilation vers JavaScript.

Pour plus d’informations sur les versions linguistiques prises en charge, consultez l’article du Guide du développeur spécifique à une langue.   
Pour plus d’informations sur les modifications prévues sur la prise en charge des langages, consultez la [Feuille de route Azure](https://azure.microsoft.com/roadmap/?tag=functions).
