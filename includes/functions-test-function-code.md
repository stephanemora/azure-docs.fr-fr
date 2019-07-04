---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176845"
---
## <a name="test"></a>Tester la fonction dans Azure

Utiliser cURL pour tester la fonction déployée. À l'aide de l'URL que vous avez copiée à l'étape précédente, ajoutez la chaîne de requête `&name=<yourname>` à l'URL, comme dans l'exemple suivant :

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![Utiliser cURL pour appeler la fonction dans Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Vous pouvez également coller l'URL copiée dans l'adresse de votre navigateur web. De nouveau, ajoutez la chaîne de requête `&name=<yourname>` à l'URL avant d'exécuter la requête.

![Utiliser un navigateur web pour appeler la fonction.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
