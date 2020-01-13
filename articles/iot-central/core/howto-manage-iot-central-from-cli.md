---
title: Gérer IoT Central à partir d’Azure CLI | Microsoft Docs
description: Cet article explique comment créer et gérer une application IoT Central avec l’interface de ligne de commande. Il est possible d’afficher, de modifier et de supprimer l’application avec l’interface CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1051ea91378cc2e2facec7e34f6d303297b91ce8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454066"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gérer IoT Central à partir d’Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Au lieu de créer et de gérer des applications IoT Central dans le site web du [Gestionnaire d’applications Azure IoT Central](https://aka.ms/iotcentral), vous pouvez utiliser [Azure CLI](/cli/azure/) pour gérer vos applications.

## <a name="prerequisites"></a>Conditions préalables requises

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous préférez exécuter Azure CLI sur votre ordinateur local, consultez [Installer le module Azure CLI](/cli/azure/install-azure-cli). Lorsque vous exécutez Azure CLI en local, utilisez la commande **az login** pour vous connecter à Azure avant d’essayer les commandes dans cet article.

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
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Ces commandes créent d’abord un groupe de ressources dans l’emplacement USA st pour l’application. Le tableau suivant décrit les paramètres utilisés avec la commande **az iotcentral app create** :

| Paramètre         | Description |
| ----------------- | ----------- |
| resource-group    | Groupe de ressources qui contient l’application. Ce groupe de ressources doit déjà exister dans votre abonnement. |
| location          | Par défaut, cette commande utilise l’emplacement du groupe de ressources. Actuellement, vous pouvez créer une application IoT Central dans les emplacements **États-Unis**, **Australie**, **Asie-Pacifique** ou **Europe**. |
| name              | Nom de l’application dans le portail Azure. |
| subdomain         | Sous-domaine dans l’URL de l’application. Dans l’exemple, l’URL de l’application est https://mysubdomain.azureiotcentral.com. |
| sku               | Actuellement, la seule valeur est **S1** (niveau Standard). Consultez [Tarifs Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | Modèle d’application à utiliser. Pour plus d’informations, voir le tableau suivant : |
| display-name      | Nom de l’application tel qu’il est affiché dans l’interface utilisateur. |

**Modèle d’application avec les fonctionnalités généralement disponibles**

| Nom du modèle            | Description |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Crée une application vide, que vous pouvez remplir avec vos propres modèles d’appareil et vos propres appareils.


**Modèles d’application avec des fonctionnalités en préversion publique**

| Nom du modèle            | Description |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | Crée une application Plug-and-Play en préversion vide, que vous pouvez remplir avec vos propres modèles d’appareil et vos propres appareils. |
| iotc-condition@1.0.0     | Crée une application avec un modèle Analytique dans le magasin - Supervision des conditions. Utilisez ce modèle pour connecter et superviser l’environnement de magasin. |
| iotc-consumption@1.0.0   | Créez une application avec le modèle de supervision de la consommation d’eau. Utilisez ce modèle pour superviser et contrôler le débit d’eau. |
| iotc-distribution@1.0.0  | Crée une application avec un modèle de distribution numérique. Utilisez ce modèle pour améliorer l’efficacité des sorties d’entrepôt en numérisant les ressources et les actions clés. |
| iotc-inventory@1.0.0     | Crée une application avec un modèle de gestion intelligente des stocks. Utilisez ce modèle pour automatiser la réception, le déplacement de produits, l’inventaire tournant et le suivi des capteurs. |
| iotc-logistics@1.0.0     | Crée une application avec un modèle Logistique connectée. Utilisez ce modèle pour suivre votre expédition en temps réel par voie aérienne, maritime et terrestre grâce à la supervision de la localisation et des conditions. |
| iotc-meter@1.0.0         | Crée une application avec le modèle de supervision des compteurs intelligents. Utilisez ce modèle pour superviser la consommation énergétique et l’état du réseau, ainsi que pour identifier les tendances afin d’améliorer le service client et la gestion des compteurs intelligents.  |
| iotc-patient@1.0.0       | Crée une application avec un modèle de supervision continue des patients. Utilisez ce modèle pour organiser les soins des patients, réduire le taux de réadmission et gérer les maladies. |
| iotc-power@1.0.0         | Crée une application avec un modèle de supervision des panneaux solaires. Utilisez ce modèle pour superviser l’état des panneaux solaires et les tendances de la génération d’énergie. |
| iotc-quality@1.0.0       | Créez une application avec le modèle de supervision de la qualité de l’eau. Utilisez ce modèle pour superviser numériquement la qualité de l’eau.|
| iotc-store@1.0.0         | Crée une application avec un modèle Analytique dans le magasin - Paiement. Utilisez ce modèle pour superviser et gérer le passage en caisse dans votre magasin. |
| iotc-waste@1.0.0         | Crée une application avec un modèle Gestion des déchets connectée. Utilisez ce modèle pour superviser les bennes à ordures et répartir les agents. |

> [!NOTE]
> Les modèles d’application en préversion sont actuellement disponibles uniquement dans les emplacements en **Europe** et **États-Unis**.

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
