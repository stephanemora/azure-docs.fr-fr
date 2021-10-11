---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/18/2021
ms.author: glenga
ms.openlocfilehash: ed981abd0ff849fb2a88164b2814794a48603ce7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559878"
---
Cet article prend en charge la création de deux types de fonctions C# compilées : 

| Modèle d’exécution | Description |
| --- | --- |
| **[In-process](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=in-process)**| Le code de votre fonction s’exécute dans le même processus que le processus hôte Functions. Pour en découvrir plus, consultez [Développer des fonctions de bibliothèque de classes C# à l’aide d’Azure Functions](../articles/azure-functions/functions-dotnet-class-library.md). |
| **[Processus isolé](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)**| Le code de votre fonction s’exécute dans un processus Worker .NET distinct. Pour plus d’informations, consultez [Guide d’exécution de fonctions sur .NET 5.0 dans Azure](../articles/azure-functions/dotnet-isolated-process-guide.md). |