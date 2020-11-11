---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: e9465e014c1b8770f8968cbee89a5b455ad53470
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424692"
---
## <a name="run-the-function-in-azure"></a>Exécuter la fonction dans Azure

1. Dans la zone **Azure : Fonctions** de la barre latérale, développez la nouvelle application de fonction sous votre abonnement. Développez **Fonctions** , cliquez avec le bouton droit (Windows) ou appuyez sur <kbd>Ctrl -</kbd> clic (macOS) sur **HttpExample** , puis sélectionnez **Copier l’URL de la fonction**.

    ![Copier l'URL de fonction du nouveau déclencheur HTTP](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Collez cette URL pour la requête HTTP dans la barre d’adresse de votre navigateur, ajoutez la chaîne de requête `name` sous la forme `?name=Functions` à la fin de cette URL, puis exécutez la requête. L’URL qui appelle la fonction déclenchée via HTTP doit être au format suivant :

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/httpexample?name=Functions
    ```

    L’exemple suivant montre la réponse dans le navigateur à la requête GET distante retournée par la fonction :

    ![Réponse de la fonction dans le navigateur](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
