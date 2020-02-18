---
title: Fichier Include
description: Fichier Include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 700dbfde3be2f24eb57acbdeb9d2841ef2bdfe44
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77112352"
---
Dans la sortie de commande, la section `identity` montre qu’une identité de type `SystemAssigned` est définie dans la tâche. `principalId` est l'ID du principal de l'identité de la tâche :

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 
Utilisez la commande [az acr task show][az-acr-task-show] pour stocker la valeur principalId dans une variable, afin de l’utiliser dans des commandes ultérieures. Remplacez le nom de votre tâche et de votre registre dans la commande suivante :

```azurecli
principalID=$(az acr task show --name mytask --registry myregistry --query identity.principalId --output tsv)
```

<!-- LINKS - Internal -->
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
