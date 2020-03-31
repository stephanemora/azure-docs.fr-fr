---
title: Création d’un IoT Hub à l’aide de l’interface de ligne de commande Azure | Microsoft Docs
description: Découvrez comment utiliser les commandes Azure CLI pour créer un groupe de ressources, puis un hub IoT dans le groupe de ressources. Découvrez également comment supprimer le hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79237389"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Création d’un IoT Hub à l’aide de l’interface de ligne de commande Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Cet article explique comment créer un hub IoT à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce guide pratique, vous devez disposer d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Se connecter à votre compte Azure et le définir

Si vous exécutez Azure CLI localement et non à l’aide de Cloud Shell, vous devez vous connecter à votre compte Azure.

Dans l’invite de commande, exécutez la [commande login](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) :

   ```azurecli
   az login
   ```

Suivez les instructions pour vous authentifier à l’aide du code et vous connecter à votre compte Azure via un navigateur web.

## <a name="create-an-iot-hub"></a>Création d’un IoT Hub

Utilisez l’interface de ligne de commande Azure pour créer un groupe de ressources, puis ajoutez un IoT Hub.

1. Lorsque vous créez un IoT Hub, vous devez le créer dans un groupe de ressources. Utilisez un groupe de ressources existant, ou exécutez la [commande suivante pour en créer un groupe de ressources](https://docs.microsoft.com/cli/azure/resource) :
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > L’exemple précédent crée le groupe de ressources dans l’emplacement USA Ouest. Vous pouvez afficher une liste des emplacements disponibles en exécutant cette commande : 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Exécutez la [commande suivante pour créer un hub IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) dans votre groupe de ressources en utilisant un nom globalement unique pour votre hub IoT :
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


La commande précédente crée un IoT Hub dans le niveau de tarification S1 pour lequel vous êtes facturé. Pour plus d’informations, voir la [tarification d’Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Supprimer un hub IoT

Vous pouvez utiliser Azure CLI pour [supprimer une ressource](https://docs.microsoft.com/cli/azure/resource), par exemple un hub IoT, ou un groupe de ressources et toutes ses ressources, y compris les hubs IoT.

Pour [supprimer un hub IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete), exécutez la commande suivante :

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Pour [supprimer un groupe de ressources](https://docs.microsoft.com/cli/azure/group#az-group-delete) et toutes ses ressources, exécutez la commande suivante :

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation d’un hub IoT, consultez les articles suivants :

* [Guide du développeur d’IoT Hub](iot-hub-devguide.md)
* [Utilisation du portail Azure pour gérer IoT Hub](iot-hub-create-through-portal.md)
