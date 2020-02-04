---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4075b8d05c179e8115ff46c9f82751817372491
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842150"
---
## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Visual Studio Code s’intègre à [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) pour vous permettre d’exécuter ce projet sur votre ordinateur de développement local avant toute publication sur Azure.

1. Pour appeler votre fonction, appuyez sur F5 pour démarrer le projet d’application de fonction. La sortie de Core Tools est affichée dans le panneau **Terminal**.

1. Si vous n’avez pas encore installé Azure Functions Core Tools, sélectionnez **Installer** à l’invite. Quand Core Tools est installé, votre application démarre dans le panneau **Terminal**.

1. Dans le panneau **Terminal**, copiez le point de terminaison de l’URL de votre fonction déclenchée via HTTP. 

    ![Sortie Azure locale](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Collez l’URL de la demande HTTP dans la barre d’adresses de votre navigateur. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande GET. 

1. Une réponse est retournée, semblable à celle-ci dans un navigateur :

    ![Réponse de la fonction localhost dans le navigateur](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Appuyez sur Maj+F5 pour arrêter Core Tools et déconnecter le débogueur.
