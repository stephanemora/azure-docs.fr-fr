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
ms.openlocfilehash: 26bfeed5315394b9030bff9c471dd5fbedca117f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
[!INCLUDE [resource group intro text](resource-group.md)]

Dans Cloud Shell, créez un groupe de ressources avec la commande [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *Europe de l’Ouest*. Pour afficher tous les emplacements pris en charge pour App Service au niveau **Gratuit**, exécutez la commande [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Vous créez généralement votre groupe de ressources et les ressources dans une région proche de chez vous. 

Une fois la commande terminée, une sortie JSON affiche les propriétés du groupe de ressources.