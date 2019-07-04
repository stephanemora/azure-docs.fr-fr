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
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176848"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Déployer le projet d’application de fonction sur Azure

Une fois l'application de fonction créée dans Azure, vous pouvez utiliser la commande [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools pour déployer votre code de projet sur Azure. Dans la commande suivante, remplacez `<APP_NAME>` par le nom de votre application obtenu à l'étape précédente.

```bash
func azure functionapp publish <APP_NAME>
```

Une sortie semblable à la suivante, tronquée pour une meilleure lisibilité, s'affiche.

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

Copiez la valeur de l'URL d'appel de votre déclencheur HttpTrigger, que vous pouvez maintenant utiliser pour tester votre fonction dans Azure. L'URL contient une valeur de chaîne de requête `code` qui constitue votre clé de fonction. Cette clé empêche les autres utilisateurs d'appeler le point de terminaison de votre déclencheur HTTP dans Azure.