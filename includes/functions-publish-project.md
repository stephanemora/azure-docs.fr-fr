---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e2d63ab38bad341400538c5079fee22737cf0b8e
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562945"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Déployer le projet d’application de fonction sur Azure

Une fois l'application de fonction créée dans Azure, vous pouvez utiliser la commande [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools pour déployer votre code de projet sur Azure. Dans ces exemples, remplacez `<APP_NAME>` par le nom de votre application obtenu à l’étape précédente.

### <a name="c--javascript"></a>C\# / JavaScript

```command
func azure functionapp publish <APP_NAME>
```

### <a name="python"></a>Python

```command
func azure functionapp publish <APP_NAME> --build remote
```

### <a name="typescript"></a>TypeScript

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Une sortie semblable à la suivante, tronquée pour en faciliter la lisibilité, s’affiche :

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Copiez la valeur `Invoke url` de votre déclencheur `HttpTrigger`, que vous pouvez désormais utiliser pour tester votre fonction dans Azure. L'URL contient une valeur de chaîne de requête `code` qui constitue votre clé de fonction. Cette clé empêche les autres utilisateurs d'appeler le point de terminaison de votre déclencheur HTTP dans Azure.