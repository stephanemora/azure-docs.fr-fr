---
title: Créer votre première fonction dans Azure à l’aide de Visual Studio Code
description: Créer et publier une fonction HTTP déclenchée simple dans Azure à l’aide de l’extension Azure Functions dans Visual Studio Code.
ms.topic: quickstart
ms.date: 06/25/2019
ms.custom: mvc, devcenter
ms.openlocfilehash: b89e6d75cd82a65dfbce29b949c4b7d463582bb9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230793"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Créer votre première fonction à l’aide de Visual Studio Code

Azure Functions vous permet d’exécuter votre code dans un environnement [sans serveur](https://azure.microsoft.com/solutions/serverless/) et sans avoir à créer une machine virtuelle ou à publier une application web au préalable.

Dans cet article, vous allez apprendre à utiliser l’extension [Extension Azure Functions pour Visual Studio Code] pour créer et tester une fonction « hello world » sur votre ordinateur local à l’aide de Microsoft Visual Studio Code. Vous allez ensuite publier le code de la fonction dans Azure à partir de Visual Studio Code.

![Code Azure Functions dans un projet Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

L’extension prend actuellement en charge les fonctions C#, JavaScript, Java et Python. Les étapes décrites dans cet article et l’article qui suit sont valables uniquement pour les fonctions JavaScript et C#. Pour savoir comment utiliser Visual Studio Code afin de créer et de publier des fonctions Python, voir [Créer et déployer des fonctions Azure Functions serverless en Python avec Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01). Pour savoir comment utiliser Visual Studio Code pour créer et publier des fonctions PowerShell, consultez [Créer votre première fonction PowerShell dans Azure](functions-create-first-function-powershell.md). 

L’extension est actuellement en préversion. Pour plus d’informations, consultez la page d’extension [Extension Azure Functions pour Visual Studio Code].

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide :

* Installer [Visual Studio Code](https://code.visualstudio.com/) sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Installez la version 2.x d’[Azure Functions Core Tools](functions-run-local.md#v2).

* Installer les exigences spécifiques pour le langage choisi :

    | Langage | Prérequis |
    | -------- | --------- |
    | **C#** | [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Active LTS et Maintenance LTS (versions 8.11.1 et 10.14.1 recommandées).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps de publier le projet sur Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Exécuter la fonction dans Azure

1. Copiez l’URL du déclencheur HTTP à partir du panneau **Sortie**. Cette URL inclut la clé de la fonction, qui est passée au paramètre de requête `code`. Comme auparavant, veillez à ajouter la chaîne de requête `?name=<yourname>` à la fin de cette URL et exécutez la requête.

    L’URL qui appelle la fonction déclenchée via HTTP doit être au format suivant :

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. Collez cette nouvelle URL de requête HTTP dans la barre d’adresse de votre navigateur. La capture d’écran suivante du navigateur montre la requête renvoyée par la fonction suite à la demande distante GET : 

    ![Réponse de la fonction dans le navigateur](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Étapes suivantes

Vous avez utilisé Visual Studio Code pour créer une application de fonction à l’aide d’une fonction HTTP simple déclenchée via HTTP. Dans le prochain article, vous développez cette fonction en ajoutant une liaison de sortie. Cette liaison écrit la chaîne de la requête HTTP dans un message dans une file d’attente de Stockage File d’attente Azure. L’article suivant vous montre également comment nettoyer ces nouvelles ressources Azure en supprimant le groupe de ressources que vous avez créé.

> [!div class="nextstepaction"]
> [Ajouter une liaison de file d’attente Stockage Azure à votre fonction](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Extension Azure Functions pour Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
