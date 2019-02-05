---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 914c006daf49e22ebec870a549bfdbc63f882647
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55148004"
---
## <a name="test"></a>Tester la fonction

Utilisez cURL pour tester la fonction déployée sur un ordinateur Mac ou Linux ou Powershell sur Windows. Exécutez la commande cURL suivante, en remplaçant l’espace réservé `<app_name>` par le nom de votre Function App. Ajoutez la chaîne de requête `&name=<yourname>` à l’URL.

```powershell
Invoke-WebRequest -Uri "https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>"
```

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Réponse de la fonction affichée dans un navigateur.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Si `cURL` ou `Invoke-WebRequest` n’est pas disponible dans votre ligne de commande, entrez la même URL dans la barre d’adresse de votre navigateur web. Remplacez à nouveau l’`<app_name>` espace réservé par le nom de votre application de fonction, et ajoutez la chaîne de requête `&name=<yourname>` à l’URL avant d’exécuter la requête.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Réponse de la fonction affichée dans un navigateur.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
