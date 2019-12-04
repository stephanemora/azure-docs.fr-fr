---
title: Gérer le service Azure IoT Hub Device Provisioning avec Azure CLI et une extension IoT
description: Découvrez comment utiliser Azure CLI et l’extension IoT afin de gérer le Service IoT Hub Device Provisioning.
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0e276ca32d7cd02f9668b33b3729757404b13b00
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229741"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Comment utiliser Azure CLI et l’extension IoT afin de gérer le Service IoT Hub Device Provisioning

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) est un outil en ligne de commande open source et multiplateforme, destiné à la gestion des ressources Azure, telles que IoT Edge. Azure CLI est disponible sur Windows, Linux et Mac OS. Azure CLI vous permet de gérer les ressources Azure IoT Hub, les instances du service Device Provisioning et les hubs liés prêts à l’utilisation.

L’extension IoT enrichit Azure CLI avec des fonctionnalités telles que la gestion des appareils, et toutes les fonctionnalités de IoT Edge.

Dans ce tutoriel, vous commencez par les étapes de configuration d’Azure CLI et de l’extension IoT. Ensuite, vous voyez comment exécuter des commandes CLI afin d’effectuer des opérations de base dans le service Device Provisioning. 

## <a name="installation"></a>Installation 

### <a name="step-1---install-python"></a>Étape 1 - Installez Python

[Python 2.7x ou Python 3.x](https://www.python.org/downloads/) est nécessaire.

### <a name="step-2---install-the-azure-cli"></a>Étape 2 : Installer Azure CLI

Suivez les [instructions d’installation](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour configurer Azure CLI dans votre environnement. Vous devez utiliser Azure CLI version 2.0.24 ou ultérieure. Utilisez `az –version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack. Une méthode d’installation simple sur Windows consiste à télécharger et installer le [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Étape 3 - Installez l’extension IoT

[Le fichier Lisez-moi de l’extension IoT](https://github.com/Azure/azure-iot-cli-extension) décrit plusieurs manières d’installer l’extension. La façon la plus simple consiste à exécuter `az extension add --name azure-cli-iot-ext`. Après l’installation, vous pouvez utiliser `az extension list` pour valider les extensions actuellement installées ou `az extension show --name azure-cli-iot-ext` pour afficher les détails concernant l’extension IoT. Pour supprimer l’extension, vous pouvez utiliser `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Opérations de base du service Device Provisioning
L’exemple vous montre comment vous connecter à votre compte Azure, créer un groupe de ressources Azure (un conteneur contenant les ressources associées à une solution Azure), créer un IoT Hub, créer un service Device Provisioning, faire la liste des services Device Provisioning existants et créer un hub IoT lié avec les commandes CLI. 

Effectuez les étapes d’installation décrites précédemment avant de commencer. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?v=17.39a) dès maintenant. 


### <a name="1-log-in-to-the-azure-account"></a>1. Se connecter au compte Azure
  
    az login

![se connecter][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Créer un groupe de ressources IoTHubBlogDemo dans eastus

    az group create -l eastus -n IoTHubBlogDemo

![Créer un groupe de ressources][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Créer deux services Device Provisioning

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Créer un service Device Provisioning][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Faire la liste de tous les services Device Provisioning sous ce groupe de ressources

    az iot dps list --resource-group IoTHubBlogDemo

![Faire la liste de tous les services Device Provisioning][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Créer un hub IoT blogDemoHub sous le groupe de ressources nouvellement créé

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Créer un hub IoT][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Lier un hub IoT existant à un service Device Provisioning

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Lier un hub][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Inscrire l’appareil
> * Démarrer l’appareil
> * Vérifier que l’appareil est enregistré

Passez au didacticiel suivant pour apprendre à approvisionner plusieurs appareils sur des hubs à charge équilibrée. 

> [!div class="nextstepaction"]
> [Approvisionner des appareils sur des hubs IoT à charge équilibrée](./tutorial-provision-multiple-hubs.md)
