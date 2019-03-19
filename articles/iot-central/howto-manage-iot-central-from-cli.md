---
title: Gérer des IoT Central à partir de la CLI Azure | Microsoft Docs
description: Gérer IoT Central à partir de la CLI Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/07/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 9e5d842cece316bc9c53e1e8583f40a0f222b91d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2019
ms.locfileid: "56809261"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gérer des IoT Central à partir d’Azure CLI

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Au lieu de la création et la gestion des applications IoT Central à partir de l’IoT Central [Application Manager](https://aka.ms/iotcentral) page, vous pouvez utiliser [Azure CLI](/cli/azure/) pour gérer vos applications.

## <a name="prerequisites"></a>Conditions préalables

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous préférez exécuter Azure CLI sur votre ordinateur local, consultez [installer l’interface CLI](/cli/azure/install-azure-cli). Lorsque vous exécutez Azure CLI localement, utilisez le **connexion de az** commande pour vous connecter à Azure avant d’essayer les commandes dans cet article.

## <a name="create-an-application"></a>Création d'une application

Utilisez le [créer az iotcentral application](/cli/azure/iotcentral/app#az-iotcentral-app-create) commande pour créer une application IoT Central dans votre abonnement Azure. Par exemple : 

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
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Ces commandes créent tout d’abord un groupe de ressources dans l’est des États-Unis pour l’application. Le tableau suivant décrit les paramètres utilisés avec la **créer az iotcentral application** commande :

| Paramètre         | Description |
| ----------------- | ----------- |
| resource-group    | Groupe de ressources qui contient l’application. Ce groupe de ressources doit déjà exister dans votre abonnement. |
| location          | Par défaut, cette commande utilise l’emplacement du groupe de ressources. Actuellement, vous pouvez créer une application IoT Central dans les régions **USA Est**, **USA Ouest**, **Europe Nord** et **Europe Ouest**. |
| Nom              | Nom de l’application dans le portail Azure. |
| sous-domaine         | Sous-domaine dans l’URL de l’application. Dans l’exemple, l’URL de l’application est https://mysubdomain.azureiotcentral.com. |
| sku               | Actuellement, la seule valeur est **S1** (niveau Standard). Consultez [Tarifs Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | Modèle d’application à utiliser. Pour plus d’informations, voir le tableau suivant : |
| display-name      | Nom de l’application tel qu’il est affiché dans l’interface utilisateur. |

**Modèles d’application**

| Nom du modèle            | Description |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Crée une application vide, que vous pouvez remplir avec vos propres modèles d’appareil et vos propres appareils. |
| iotc-demo@1.0.0          | Crée une application qui comprend un modèle d’appareil déjà créé pour une machine de distribution réfrigérée. Utilisez ce modèle pour commencer à explorer Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Crée une application avec des modèles d’appareil tout prêts qui vous permettent de connecter un appareil MXChip ou Raspberry Pi. Utilisez ce modèle si vous êtes développeur d’appareils et que vous testez l’un de ces appareils. |

## <a name="view-your-applications"></a>Afficher vos applications

Utilisez le [liste d’applications az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-list) commande pour répertorier vos applications IoT Central et afficher des métadonnées.

## <a name="modify-an-application"></a>Modifier une application

Utilisez le [mise à jour de az iotcentral application](/cli/azure/iotcentral/app#az-iotcentral-app-update) commande pour mettre à jour les métadonnées d’une application IoT Central. Par exemple, pour modifier le nom complet de votre application :

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Supprimer une application

Utilisez le [suppression d’application az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-delete) commande pour supprimer une application IoT Central. Par exemple : 

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment gérer des applications Azure IoT Central à partir d’Azure CLI, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)
