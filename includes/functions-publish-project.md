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
ms.openlocfilehash: fbb537c9584c948af37694b3bfc77a7c345e084d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639119"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Déployer le projet d’application de fonction sur Azure

Une fois l'application de fonction créée dans Azure, vous pouvez utiliser la commande [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools pour déployer votre code de projet sur Azure. Dans la commande suivante, remplacez `<APP_NAME>` par le nom de votre application obtenu à l'étape précédente.

```bash
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