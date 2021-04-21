---
title: Fichier include
description: Fichier include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/06/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c059746262cdaf2901c765d3ce3848887b2e629c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781117"
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
principalID=$(az acr task show \
  --name <task_name> --registry <registry_name> \
  --query identity.principalId --output tsv)
```

<!-- LINKS - Internal -->
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
