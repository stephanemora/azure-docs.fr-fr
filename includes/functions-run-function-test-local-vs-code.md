---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: eae828d03431dd339c5399d8db8c6e46141ab11b
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075324"
---
## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Visual Studio Code s’intègre à [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) pour vous permettre d’exécuter ce projet sur votre ordinateur de développement local avant toute publication sur Azure.

1. Pour appeler votre fonction, appuyez sur <kbd>F5</kbd> pour démarrer le projet d’application de fonction. La sortie de Core Tools est affichée dans le panneau **Terminal**. Votre application démarre dans le panneau **Terminal**. Vous pouvez voir le point de terminaison de l’URL de votre fonction déclenchée par HTTP en cours d’exécution localement.

    ![Sortie VS Code de la fonction locale](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Si vous rencontrez des problèmes d’exécution sur Windows, assurez-vous que le terminal par défaut pour Visual Studio Code n’est pas défini sur **WSL Bash**.

1. Une fois Core Tools en cours d’exécution, accédez à la zone **Azure : Functions**. Sous **Fonctions**, développez **Projet local** > **Fonctions**. Cliquez avec le bouton droit (Windows) ou effectuez <kbd>Ctrl-clic</kbd> (macOS) sur la fonction `HttpExample`, puis choisissez **Exécuter la fonction maintenant**.

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Exécuter la fonction maintenant dans Visual Studio Code":::
    
1. Dans **Entrer le corps de la requête**, vous pouvez voir la valeur du corps du message de requête pour `{ "name": "Azure" }`. Appuyez sur Entrée pour envoyer ce message de demande à votre fonction. 

   Vous pourriez à la place envoyer une requête HTTP GET à l’adresse `http://localhost:7071/api/HttpExample` dans un navigateur web.

1. Quand la fonction s’exécute localement et retourne une réponse, une notification est générée dans Visual Studio Code. Les informations relatives à l’exécution de la fonction sont affichées dans le panneau **Terminal**.

1. Appuyez sur <kbd>Ctrl+C</kbd> pour arrêter Core Tools et déconnecter le débogueur.
