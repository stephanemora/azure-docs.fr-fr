---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/16/2021
ms.author: glenga
ms.openlocfilehash: bda70e418b86c44750e9c826144fce8bbc027cbb
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830508"
---
::: zone pivot="programming-language-csharp"  

À l’exception des déclencheurs HTTP et de minuterie, les liaisons sont implémentées sous la forme de packages d’extension. Exécutez la commande [dotnet add package](/dotnet/core/tools/dotnet-add-package) suivante dans la fenêtre de terminal pour ajouter le package d’extension de Stockage à votre projet.

# <a name="in-process"></a>[In-process](#tab/in-process) 
```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage 
```
# <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)
```bash
dotnet add package Microsoft.Azure.Functions.Worker.Extensions.Storage.Queues --prerelease
```
---
Vous pouvez maintenant ajouter la liaison de sortie de stockage à votre projet.  
::: zone-end  
