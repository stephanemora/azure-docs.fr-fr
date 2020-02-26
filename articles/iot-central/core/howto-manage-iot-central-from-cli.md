---
title: Gérer IoT Central à partir d’Azure CLI | Microsoft Docs
description: Cet article explique comment créer et gérer une application IoT Central avec l’interface de ligne de commande. Vous pouvez afficher, modifier et supprimer l’application à l’aide de CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c44b7cd045547d01d1a31f949a42087e78e88b21
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198835"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gérer IoT Central à partir d’Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Au lieu de créer et de gérer des applications IoT Central dans le site web du [Gestionnaire d’applications Azure IoT Central](https://aka.ms/iotcentral), vous pouvez utiliser [Azure CLI](/cli/azure/) pour gérer vos applications.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous préférez exécuter Azure CLI sur votre ordinateur local, consultez [Installer le module Azure CLI](/cli/azure/install-azure-cli). Lorsque vous exécutez Azure CLI en local, utilisez la commande **az login** pour vous connecter à Azure avant d’essayer les commandes dans cet article.

> [!TIP]
> Si vous devez exécuter vos commandes CLI dans un autre abonnement Azure, consultez [Changer l’abonnement actif](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription).

## <a name="create-an-application"></a>Créer une application

Utilisez la commande [az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create) pour créer une application IoT Central dans votre abonnement Azure. Par exemple :

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview@1.0.0" \
  --display-name "My Custom Display Name"
```

Ces commandes créent d’abord un groupe de ressources dans la région USA Est pour l’application. Le tableau suivant décrit les paramètres utilisés avec la commande **az iotcentral app create** :

| Paramètre         | Description |
| ----------------- | ----------- |
| resource-group    | Groupe de ressources qui contient l’application. Ce groupe de ressources doit déjà exister dans votre abonnement. |
| location          | Par défaut, cette commande utilise l’emplacement du groupe de ressources. Actuellement, vous pouvez créer une application IoT Central dans les zones géographiques **Australie**, **Asie-Pacifique**, **Europe** ou **États-Unis**. |
| name              | Nom de l’application dans le portail Azure. |
| subdomain         | Sous-domaine dans l’URL de l’application. Dans l’exemple, l’URL de l’application est https://mysubdomain.azureiotcentral.com. |
| sku               | Vous pouvez utiliser **ST1** ou **ST2**. Consultez [Tarifs Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | Modèle d’application à utiliser. Pour plus d’informations, consultez le tableau suivant. |
| display-name      | Nom de l’application tel qu’il est affiché dans l’interface utilisateur. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Afficher vos applications

Utilisez la commande [az iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list) pour lister vos applications IoT Central et afficher les métadonnées.

## <a name="modify-an-application"></a>Modifier une application

Utilisez la commande [az iotcentral app update](/cli/azure/iotcentral/app#az-iotcentral-app-update) pour mettre à jour les métadonnées d’une application IoT Central. Par exemple, pour modifier le nom complet de votre application :

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Supprimer une application

Utilisez la commande [az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) pour supprimer une application IoT Central. Par exemple :

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des applications Azure IoT Central dans Azure CLI, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)
