---
title: Gérer IoT Central à partir d’Azure CLI | Microsoft Docs
description: Cet article explique comment créer et gérer une application IoT Central avec l’interface de ligne de commande. Vous pouvez afficher, modifier et supprimer l’application à l’aide de CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: bd87f15ff63edf1da447faf986cad2f9591610dd
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502966"
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

## <a name="install-the-extension"></a>Installer l’extension

Les commandes de cet article font partie de l’extension CLI **azure-iot**. Exécutez la commande suivante pour installer l’extension :

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>Créer une application

Utilisez la commande [az iot central app create](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-create) pour créer une application IoT Central dans votre abonnement Azure. Par exemple :

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Ces commandes créent d’abord un groupe de ressources dans la région USA Est pour l’application. Le tableau suivant décrit les paramètres utilisés avec la commande **az iot central app create** :

| Paramètre         | Description |
| ----------------- | ----------- |
| resource-group    | Groupe de ressources qui contient l’application. Ce groupe de ressources doit déjà exister dans votre abonnement. |
| location          | Par défaut, cette commande utilise l’emplacement du groupe de ressources. Actuellement, vous pouvez créer une application IoT Central dans les zones géographiques **Australie**, **Asie-Pacifique**, **Europe**, **États-Unis**, **Royaume-Uni** ou **Japon**. |
| name              | Nom de l’application dans le portail Azure. |
| subdomain         | Sous-domaine dans l’URL de l’application. Dans l’exemple, l’URL de l’application est `https://mysubdomain.azureiotcentral.com`. |
| sku               | Vous pouvez utiliser **ST1** ou **ST2**. Consultez [Tarifs Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | Modèle d’application à utiliser. Pour plus d’informations, consultez le tableau suivant. |
| display-name      | Nom de l’application tel qu’il est affiché dans l’interface utilisateur. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Afficher vos applications

Utilisez la commande [az iot central app list](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-list) pour lister vos applications IoT Central et afficher les métadonnées.

## <a name="modify-an-application"></a>Modifier une application

Utilisez la commande [az iot central app update](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-update) pour mettre à jour les métadonnées d’une application IoT Central. Par exemple, pour modifier le nom complet de votre application :

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Supprimer une application

Utilisez la commande [az iot central app delete](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-delete) pour supprimer une application IoT Central. Par exemple :

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des applications Azure IoT Central dans Azure CLI, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)
