---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 20dfc2413ed437fe6b08797c9c91acb5498dca23
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177543"
---
Créer une [application web](../articles/app-service/containers/app-service-linux-intro.md) dans le plan App Service `myAppServicePlan`. 

Dans Cloud Shell, vous pouvez utiliser la commande [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Dans l’exemple suivant, remplacez `<app-name>` par un nom d’application unique (les caractères autorisés sont `a-z`, `0-9` et `-`). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-local-git
```

Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> L’URL du Git distant est indiquée dans la propriété `deploymentLocalGitUrl`, avec le format `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Enregistrez cette URL, car vous en aurez besoin ultérieurement.
>
