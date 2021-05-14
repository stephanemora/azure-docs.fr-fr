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
ms.openlocfilehash: 3a5d37c546e57725b15c9ad42c7b422c8c34eed5
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109685132"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gérer IoT Central à partir d’Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Au lieu de créer et de gérer des applications IoT Central dans le site web du [Gestionnaire d’applications Azure IoT Central](https://aka.ms/iotcentral), vous pouvez utiliser [Azure CLI](/cli/azure/) pour gérer vos applications.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Si vous devez exécuter vos commandes CLI dans un autre abonnement Azure, consultez [Changer l’abonnement actif](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

## <a name="create-an-application"></a>Créer une application

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Utilisez la commande [az iot central app create](/cli/azure/iot/central/app#az_iot_central_app_create) pour créer une application IoT Central dans votre abonnement Azure. Par exemple :

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

### <a name="application-templates"></a>Modèles d’application

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

Si vous avez créé votre propre modèle d’application, vous pouvez l’utiliser pour créer une nouvelle application. Quand vous êtes invité à spécifier un modèle d’application, entrez l’ID d’application indiqué dans le lien partageable d’URL de l’application exportée sous la section [Exportation de modèle d’application](howto-use-app-templates.md#create-an-application-template) de votre application.

## <a name="view-your-applications"></a>Afficher vos applications

Utilisez la commande [az iot central app list](/cli/azure/iot/central/app#az_iot_central_app_list) pour lister vos applications IoT Central et afficher les métadonnées.

## <a name="modify-an-application"></a>Modifier une application

Utilisez la commande [az iot central app update](/cli/azure/iot/central/app#az_iot_central_app_update) pour mettre à jour les métadonnées d’une application IoT Central. Par exemple, pour modifier le nom complet de votre application :

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Supprimer une application

Utilisez la commande [az iot central app delete](/cli/azure/iot/central/app#az_iot_central_app_delete) pour supprimer une application IoT Central. Par exemple :

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des applications Azure IoT Central dans Azure CLI, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)
