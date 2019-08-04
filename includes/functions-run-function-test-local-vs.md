---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 0493de7374cffcc40f0434d84facf50b6a708c7b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592867"
---
1. Pour exécuter votre fonction, appuyez sur **F5**. Il se peut que vous deviez activer une exception de pare-feu afin que les outils puissent traiter des requêtes HTTP. Les niveaux d’autorisation ne sont jamais appliqués lors d’une exécution en local.

2. Copiez l’URL de votre fonction à partir de la sortie runtime Azure Functions.

    ![Azure runtime local](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Collez l’URL de la demande HTTP dans la barre d’adresses de votre navigateur. Ajoutez la chaîne de requête `?name=<YOUR_NAME>` à cette URL et exécutez la demande. La capture d’écran suivante du navigateur montre la requête renvoyée par la fonction suite à la demande locale GET : 

    ![Réponse de la fonction localhost dans le navigateur](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Pour arrêter le débogage, appuyez sur **MAJ + F5**.