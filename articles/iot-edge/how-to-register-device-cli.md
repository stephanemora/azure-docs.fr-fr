---
title: Inscrire un nouvel appareil Azure IoT Edge (CLI) | Microsoft Docs
description: Utiliser l’extension IoT pour Azure CLI 2.0 afin d’inscrire un nouvel appareil IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/30/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6935dfe5b32364e402017cd3005ab17969ce1ce
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034813"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli-20"></a>Inscrire un nouvel appareil Azure IoT Edge avec Azure CLI 2.0

Avant d’utiliser vos appareils IoT avec Azure IoT Edge, vous devez les inscrire auprès de votre hub IoT. Une fois que vous avez inscrit un appareil, vous recevez une chaîne de connexion qui vous permet de le configurer pour les charges de travail Edge. 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) est un outil à ligne de commande open source et multiplateforme, destiné à la gestion des ressources Azure telles que IoT Edge. Il vous permet de gérer les ressources Azure IoT Hub, les instances de service Device Provisioning et les hubs liés dès l’installation. La nouvelle extension IoT enrichit Azure CLI 2.0 avec des fonctionnalités telles que la gestion des périphériques et la fonctionnalité complète de IoT Edge.

Cet article explique comment inscrire un nouvel appareil IoT Edge à l’aide d’Azure CLI 2.0.

## <a name="prerequisites"></a>Prérequis

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) dans votre abonnement Azure. 
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) dans votre environnement. La version d’Azure CLI 2.0 doit être au minimum 2.0.24. Utilisez `az –-version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack. 
* [Extension IoT pour Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Créer un appareil

Utilisez la commande suivante pour créer une identité d’appareil dans votre hub IoT : 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Cette commande inclut trois paramètres :
* **device-id** : fournissez un nom descriptif unique à votre hub IoT.
* **hub-name** : indiquez le nom de votre hub IoT.
* **edge-enabled** : ce paramètre déclare que l’appareil est destiné à être utilisé avec IoT Edge.

   ![Créer un appareil IoT Edge](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Voir tous les appareils

Utilisez la commande suivante pour voir tous les appareils dans votre hub IoT :

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Tout appareil inscrit en tant qu’appareil IoT Edge a la propriété **capabilities.iotEdge** définie sur **true**. 

## <a name="retrieve-the-connection-string"></a>Récupérer la chaîne de connexion

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT. Utilisez la commande suivante pour retourner la chaîne de connexion d’un appareil spécifique :

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

Le paramètre device-id respecte la casse. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer des modules sur un appareil avec Azure CLI 2.0](how-to-deploy-modules-cli.md).