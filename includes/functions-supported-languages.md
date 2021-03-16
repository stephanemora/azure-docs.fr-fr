---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d09d69db33cadee1b5a214e9cb2f09a508f37242
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431842"
---
|Langage                                 |1.x         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|Disponibilité générale (.NET Framework 4.7)|GA (.NET Core 2.2<sup>1</sup>)| Disponibilité générale (.NET Core 3.1)<br/>[Préversion (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|Disponibilité générale (Nœud 6)|Disponibilité générale (Nœuds 10 et 8)| GA (Nœuds 12 et 10)<br />Préversion (Nœud 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|Disponibilité générale (.NET Framework 4.7)|GA (.NET Core 2.2<sup>1</sup>)| Disponibilité générale (.NET Core 3.1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|N/A|Disponibilité générale (Java 8)| GA (Java 11 et 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |N/A|GA (PowerShell Core 6)| Disponibilité générale (PowerShell 7 et Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|N/A|Disponibilité générale (Python 3.7 et 3.6)| Disponibilité générale (Python 3.8, 3.7 et 3.6) <br/> Préversion (Python 3.9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |N/A|Disponibilité générale<sup>2</sup>| Disponibilité générale<sup>2</sup> |


<sup>1</sup> Les applications de bibliothèque de classes .NET qui ciblent le runtime version 2.x peuvent désormais être exécutées sur .NET Core 3.1 en mode de compatibilité .NET Core 2.x. Pour plus d’informations, consultez [Considérations relatives à Functions v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> Prise en charge via la transpilation vers JavaScript.

Pour plus d’informations sur les versions linguistiques prises en charge, consultez l’article du Guide du développeur spécifique à une langue.   
Pour plus d’informations sur les modifications prévues sur la prise en charge des langages, consultez la [Feuille de route Azure](https://azure.microsoft.com/roadmap/?tag=functions).
