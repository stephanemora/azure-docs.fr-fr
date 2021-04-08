---
title: Fichier include
description: Fichier include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 520a32ed44951a711dcb1d0975fb452829530c4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244773"
---
[!INCLUDE [resource group intro text](resource-group.md)]

Dans Cloud Shell, créez un groupe de ressources avec la commande [`az group create`](/cli/azure/group). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *Europe Ouest*. Pour afficher tous les emplacements pris en charge pour App Service au niveau **Gratuit**, exécutez la commande [`az appservice list-locations --sku FREE`](/cli/azure/appservice).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Vous créez généralement votre groupe de ressources et les ressources dans une région proche de chez vous. 

Une fois la commande terminée, une sortie JSON affiche les propriétés du groupe de ressources.