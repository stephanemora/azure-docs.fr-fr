---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f7e023bcfeaa07a4ee9a80ccf4ec17120605c1ba
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134070"
---
## <a name="test"></a>Tester la fonction

Utilisez cURL pour tester la fonction déployée sur un ordinateur Mac ou Linux ou un interpréteur de commandes (Bash) sur Windows. Exécutez la commande cURL suivante, en remplaçant l’espace réservé `<app_name>` par le nom de votre Function App. Ajoutez la chaîne de requête `&name=<yourname>` à l’URL.

```bash
curl https://<app_name>.azurewebsites.net/api/HttpTrigger?name=<yourname>
```  

![Réponse de la fonction affichée dans un navigateur.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Si vous n’avez pas de cURL disponible dans votre ligne de commande, entrez la même URL dans la barre d’adresse de votre navigateur web. Remplacez à nouveau l’`<app_name>` espace réservé par le nom de votre application de fonction, et ajoutez la chaîne de requête `&name=<yourname>` à l’URL avant d’exécuter la requête.

    https://<app_name>.azurewebsites.net/api/HttpTrigger?name=<yourname>
   
![Réponse de la fonction affichée dans un navigateur.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
