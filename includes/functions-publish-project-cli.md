---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424741"
---
## <a name="deploy-the-function-project-to-azure"></a>Déployer le projet de fonction sur Azure

Après avoir créé votre application de fonction dans Azure, vous êtes prêt à déployer votre projet Functions local à l’aide de la commande [func azure functionapp publish](../articles/azure-functions/functions-run-local.md#project-file-deployment).  

Dans l’exemple suivant, remplacez `<APP_NAME>` par le nom de votre application.

```console
func azure functionapp publish <APP_NAME>
```

La commande de publication affiche des résultats similaires à la sortie suivante (tronquée par souci de simplicité) :

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
