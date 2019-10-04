---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949940"
---
## <a name="create-the-local-function-app-project"></a>Créer le projet d’application de fonction local

Exécutez la commande suivante à partir de la ligne de commande pour créer un projet d’application de fonction dans le dossier `MyFunctionProj` du répertoire local actif. Un référentiel GitHub est également créé dans `MyFunctionProj`.

```command
func init MyFunctionProj
```

Lorsque vous y êtes invités, sélectionnez un worker runtime parmi les choix de langues suivants :

+ `dotnet` : crée un projet de bibliothèque de classes .NET (.csproj).
+ `node` : crée un projet Node.js. Choisissez `javascript` ou `typescript`. 
+ `python` : pour un projet Python, effectuez plutôt la procédure [Créer une fonction déclenchée via HTTP dans Azure](../articles/azure-functions/functions-create-first-function-python.md).
+ `powershell` : pour un projet PowerShell, effectuez plutôt la procédure [Créer votre première fonction PowerShell dans Azure](../articles/azure-functions/functions-create-first-function-powershell.md). 


Une fois le projet créé, utilisez la commande suivante pour accéder au nouveau dossier du projet `MyFunctionProj`.

```command
cd MyFunctionProj
```
