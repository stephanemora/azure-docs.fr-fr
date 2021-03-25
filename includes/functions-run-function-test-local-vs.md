---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701390"
---
1. Pour exécuter votre fonction, appuyez sur <kbd>F5</kbd> dans Visual Studio. Vous devez peut-être activer une exception de pare-feu afin de permettre aux outils de prendre en charge les requêtes HTTP. Les niveaux d’autorisation ne sont jamais appliqués quand vous exécutez une fonction localement.

2. Copiez l’URL de votre fonction à partir de la sortie runtime Azure Functions.

    ![Azure runtime local](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Collez l’URL de la demande HTTP dans la barre d’adresses de votre navigateur. Ajoutez la chaîne de requête `?name=<YOUR_NAME>` à cette URL et exécutez la demande. L’image suivante montre la réponse dans le navigateur à la requête GET locale retournée par la fonction : 

    ![Réponse de la fonction localhost dans le navigateur](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Pour arrêter le débogage, appuyez sur <kbd>Maj</kbd>+<kbd>F5</kbd> dans Visual Studio.
