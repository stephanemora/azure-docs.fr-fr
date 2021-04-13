---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d400533039ea74a878cb8e543c22de02ee77e4f5
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283244"
---
## <a name="run-the-function-locally"></a>Exécuter la fonction localement

1. Exécutez votre fonction en démarrant l’hôte du runtime d’Azure Functions local à partir du dossier *LocalFunctionProj* :

    ```
    func start
    ```

    Vers la fin de la sortie, les lignes suivantes doivent s’afficher : 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Si HttpExample n’apparaît pas comme indiqué ci-dessus, cela signifie probablement que vous avez démarré l’hôte à partir d’un emplacement autre que le dossier racine du projet. Dans ce cas, utilisez **Ctrl**+**C** pour arrêter l’hôte, accédez au dossier racine du projet, puis réexécutez la commande précédente.

1. Copiez dans un navigateur l’URL de votre fonction `HttpExample` à partir de cette sortie, puis ajoutez la chaîne de requête `?name=<YOUR_NAME>` pour obtenir une URL complète semblable à `http://localhost:7071/api/HttpExample?name=Functions`. Le navigateur doit afficher un message de réponse qui renvoie la valeur de votre chaîne de requête. Le terminal dans lequel vous avez démarré votre projet affiche également une sortie de journal quand vous effectuez des requêtes.

1. Quand vous avez terminé, utilisez **Ctrl**+**C**, puis choisissez `y` pour arrêter l’hôte Functions.
