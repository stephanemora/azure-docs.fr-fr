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
ms.openlocfilehash: 9fefba9c79e9c58956243de8db67ca4cf316251c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949981"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Déployer le projet d’application de fonction sur Azure

Une fois l'application de fonction créée dans Azure, vous pouvez utiliser la commande [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools pour déployer votre code de projet sur Azure. Dans ces exemples, remplacez `<APP_NAME>` par le nom de votre application obtenu à l’étape précédente.

### <a name="c--javascript"></a>C\# / JavaScript

```command
func azure functionapp publish <APP_NAME>
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