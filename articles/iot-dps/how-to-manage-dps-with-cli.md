---
title: Gérer le service Azure IoT Hub Device Provisioning avec Azure CLI et une extension IoT
description: Découvrez comment utiliser Azure CLI et l’extension IoT pour gérer le service IoT Hub Device Provisioning (DPS)
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: devx-track-azurecli
services: iot-dps
ms.openlocfilehash: dd0564fbb23a0695d849852fd464308cd1b5fac9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678941"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Comment utiliser Azure CLI et l’extension IoT afin de gérer le Service IoT Hub Device Provisioning

[Azure CLI](/cli/azure) est un outil en ligne de commande open source et multiplateforme, destiné à la gestion des ressources Azure, telles que IoT Edge. Azure CLI est disponible sous Windows, Linux et macOS. Azure CLI vous permet de gérer les ressources Azure IoT Hub, les instances du service Device Provisioning et les hubs liés prêts à l’utilisation.

L’extension IoT enrichit Azure CLI avec des fonctionnalités telles que la gestion des appareils, et toutes les fonctionnalités de IoT Edge.

Dans ce tutoriel, vous commencez par les étapes de configuration d’Azure CLI et de l’extension IoT. Ensuite, vous voyez comment exécuter des commandes CLI afin d’effectuer des opérations de base dans le service Device Provisioning. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Prérequis

- [Python 2.7x ou Python 3.x](https://www.python.org/downloads/) est nécessaire.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Cet article nécessite la version 2.0.70 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="basic-device-provisioning-service-operations"></a>Opérations de base du service Device Provisioning

L’exemple vous montre comment vous connecter à votre compte Azure, créer un groupe de ressources Azure (un conteneur contenant les ressources associées à une solution Azure), créer un IoT Hub, créer un service Device Provisioning, faire la liste des services Device Provisioning existants et créer un hub IoT lié avec les commandes CLI. 

Effectuez les étapes d’installation décrites précédemment avant de commencer. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?v=17.39a) dès maintenant. 


### <a name="1-log-in-to-the-azure-account"></a>1. Se connecter au compte Azure
  
```azurecli
az login
```

![Capture d'écran d'une fenêtre d'invite de commandes qui exécute la commande az login.](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Créer un groupe de ressources IoTHubBlogDemo dans eastus

```azurecli
az group create -l eastus -n IoTHubBlogDemo
```

![Créer un groupe de ressources](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. Créer deux services Device Provisioning

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps
```

![Créer un service Device Provisioning](./media/how-to-manage-dps-with-cli/create-dps.jpg)

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps2
```

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Faire la liste de tous les services Device Provisioning sous ce groupe de ressources

```azurecli
az iot dps list --resource-group IoTHubBlogDemo
```

![Faire la liste de tous les services Device Provisioning](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Créer un hub IoT blogDemoHub sous le groupe de ressources nouvellement créé

```azurecli
az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo
```

![Créer un hub IoT](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Lier un hub IoT existant à un service Device Provisioning

```azurecli
az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus
```

![Lier un hub](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Inscrire l’appareil
> * Démarrer l’appareil
> * Vérifier que l’appareil est enregistré

Passez au didacticiel suivant pour apprendre à approvisionner plusieurs appareils sur des hubs à charge équilibrée. 

> [!div class="nextstepaction"]
> [Approvisionner des appareils sur des hubs IoT à charge équilibrée](./tutorial-provision-multiple-hubs.md)