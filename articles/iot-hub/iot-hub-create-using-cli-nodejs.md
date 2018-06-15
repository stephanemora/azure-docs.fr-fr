---
title: Création d’un IoT Hub à l’aide de l’interface de ligne de commande Azure (azure.js) | Microsoft Docs
description: Création d’un Azure IoT Hub à l’aide de l’interface de ligne de commande Azure multiplateforme (azure.js).
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 5b8a7ded940f59bba63556e844c45bd7cfb8eb63
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632089"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Création d’un IoT Hub à l’aide de l’interface de ligne de commande Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser l’interface de ligne de commande Azure (azure.js) pour créer et gérer des Azure IoT Hubs de façon programmée. Cet article explique comment utiliser l’interface de ligne de commande Azure (azure.js) pour créer un IoT Hub.

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

* Azure CLI (azure.js) : l’interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager décrits dans cet article.
* [Azure CLI 2.0 (az.py)](iot-hub-create-using-cli.md) : interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez en créer un [gratuitement][lnk-free-trial] en quelques minutes.
* [Azure CLI 0.10.4][lnk-CLI-install] ou version ultérieure. Si vous avez déjà installé l’interface de ligne de commande (CLI) Azure, vous pouvez valider la version actuelle à l’invite de commandes avec la commande suivante :

```azurecli
azure --version
```

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md). L’interface de ligne de commande (CLI) Azure doit être en mode Azure Resource Manager :
>
> ```azurecli
> azure config mode arm
> ```

## <a name="set-your-azure-account-and-subscription"></a>Configurer votre compte et votre abonnement Microsoft Azure

1. À l’invite de commandes, connectez-vous en tapant la commande suivante :

   ```azurecli
    azure login
   ```

   Utilisez le navigateur web suggéré et le code d’authentification.
1. Si vous possédez plusieurs abonnements Azure, la connexion à Azure donne accès à tous les abonnements Azure associés à vos informations d’identification. Vous pouvez afficher les abonnements et identifier l’abonnement Azure par défaut à l’aide de la commande suivante :

   ```azurecli
    azure account list
   ```

   Pour définir le contexte de l’abonnement sous lequel vous souhaitez exécuter le reste des commandes, utilisez la commande suivante :

   ```azurecli
    azure account set <subscription name>
   ```

1. Si vous n’avez pas de groupe de ressources, vous pouvez en créer un nommé **exampleResourceGroup** :

   ```azurecli
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Azure pour gérer des ressources Azure, voir l’article [Utiliser l’interface de ligne de commande Azure pour gérer les ressources et les groupes de ressources Azure][lnk-CLI-arm].

## <a name="create-an-iot-hub"></a>Création d’un IoT Hub

Paramètres obligatoires :

```azurecli
azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>
```

* **resource-group**. Nom du groupe de ressources. Le format ne tient pas compte de la casse et accepte les caractères suivants : trait de soulignement, valeurs alphanumériques et trait d’union. La longueur doit être incluse entre 1 et 64 caractères.
* **name**. Nom de l’instance IoT Hub à créer. Le format ne respecte pas la casse et accepte les caractères alphanumériques et les traits d’union. La longueur doit être comprise entre 3 et 50 caractères.
* **location**. Emplacement (région/centre de données Azure) associé à la configuration de l’instance IoT Hub.
* **sku-name**. Nom de la référence, à choisir parmi les éléments suivants : [F1, S1, S2, S3]. Pour plus d’informations sur chaque référence SKU, consultez [tarification d’Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/). Actuellement, les niveaux de base sont uniquement disponibles via le portail. 
* **units**. Nombre d’unités configurées. Pour plus d’informations sur les limites des unités, consultez [tarification d’Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Pour afficher tous les paramètres disponibles pour la création, vous pouvez utiliser la commande d’aide dans une invite de commandes :

```azurecli
azure iothub create -h
```

Voici un exemple rapide. Pour créer une instance IoT Hub appelée **exampleIoTHubName** dans le groupe de ressources **exampleResourceGroup**, exécutez la commande suivante :

```azurecli
azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Cette commande CLI Azure crée un IoT Hub Standard S1 pour lequel vous êtes facturé. Vous pouvez supprimer l’IoT Hub **exampleIoTHubName** à l’aide de la commande suivante :
>
> ```azurecli
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le développement pour IoT Hub, consultez l’article suivant :

* [Kits de développement logiciel (SDK) IoT][lnk-sdks]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Utilisation du portail Azure pour gérer IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
