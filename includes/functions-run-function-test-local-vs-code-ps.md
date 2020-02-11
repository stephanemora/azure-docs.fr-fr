---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: 916aa2552e5dd004ec767df98ce7c78f7320efd0
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964094"
---
## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Azure Functions Core Tools s’intègre à Visual Studio Code pour vous permettre d’exécuter et de déboguer un projet Azure Functions localement.  

1. Pour déboguer votre fonction, insérez un appel à l’applet de commande [`Wait-Debugger`](/powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6) dans le code de fonction avant d’attacher le débogueur, puis appuyez sur F5 pour démarrer le projet d’application de fonction et attacher le débogueur. La sortie de Core Tools est affichée dans le panneau **Terminal**.

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

1. Pour arrêter le débogage, appuyez sur Ctrl+C pour arrêter Core Tools.

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps de publier le projet sur Azure.

> [!NOTE]
> N’oubliez pas de supprimer tous les appels à `Wait-Debugger` avant de publier vos fonctions sur Azure. 