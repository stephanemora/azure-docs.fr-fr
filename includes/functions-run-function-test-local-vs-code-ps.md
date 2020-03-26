---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: edf5fc33ec14d41630462cca1a4ace0663473196
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78191021"
---
## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Azure Functions Core Tools s’intègre à Visual Studio Code pour vous permettre d’exécuter et de déboguer un projet Azure Functions localement. Pour plus d’informations sur le débogage dans Visual Studio Code, consultez [Déboguer localement des fonctions PowerShell Azure](../articles/azure-functions/functions-debug-powershell-local.md). 

1. Appuyez sur F5 pour démarrer le projet d’application de fonction. La sortie de Core Tools est affichée dans le panneau **Terminal**.

1. Dans le panneau **Terminal**, copiez le point de terminaison de l’URL de votre fonction déclenchée via HTTP.

    ![Sortie Azure locale](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL, puis utilisez `Invoke-RestMethod` dans une deuxième invite de commandes PowerShell pour exécuter la requête, comme suit :

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Vous pouvez également exécuter la requête GET depuis un navigateur avec l’URL suivante :

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    Quand vous appelez le point de terminaison HttpTrigger sans passer de paramètre `name` comme paramètre de requête ou dans le corps, la fonction retourne une erreur `BadRequest`. Quand vous examinez le code dans run.ps1, vous voyez que cette erreur est voulue.

1. Les informations relatives à la requête s’affichent dans le panneau **Terminal**.

    ![Exécution de fonctions dans le panneau Terminal](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Quand vous avez terminé, appuyez sur **Ctrl+C** pour arrêter Core Tools.

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps de publier le projet sur Azure.