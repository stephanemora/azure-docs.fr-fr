---
title: Déployer un modèle Azure Machine Learning sur un appareil Azure IoT Edge | Microsoft Docs
description: Ce document décrit comment les modèles Azure Machine Learning peuvent être déployés sur des appareils Azure IoT Edge.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 792ac3f26bdea6c6ccb084d893925d60e6333edb
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852450"
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Déployer un modèle Azure Machine Learning sur un appareil Azure IoT Edge

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Les modèles Azure Machine Learning peuvent être placés dans des conteneurs en tant que services web Docker. Azure IoT Edge vous permet de déployer des conteneurs à distance sur des appareils. Utilisez ces services ensemble pour exécuter vos modèles à la périphérie afin d’accélérer le temps de réponse et de réduire le transfert de données. 

Vous trouverez des scripts et des instructions supplémentaires dans le [Kit AI pour Azure IoT Edge](https://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Opérationnaliser le modèle

Les modules Azure IoT Edge sont basés sur des images conteneur. Pour déployer votre modèle Machine Learning sur un appareil IoT Edge, vous devez créer une image Docker.

Opérationnalisez votre modèle en suivant les instructions fournies dans [Déploiement d’un modèle d’apprentissage automatique comme service web](model-management-service-deploy.md) afin de créer une image Docker avec votre modèle.

## <a name="deploy-to-azure-iot-edge"></a>Déployer sur Azure IoT Edge

Une fois que vous avez l’image de votre modèle, vous pouvez la déployer sur n’importe quel appareil Azure IoT Edge. Tous les modèles Machine Learning peuvent s’exécuter sur des appareils IoT Edge. 

### <a name="set-up-an-iot-edge-device"></a>Configurer un appareil IoT Edge

Utilisez la documentation Azure IoT Edge pour préparer un appareil. 

1. [Inscrire un appareil auprès d’Azure IoT Hub](../../iot-edge/how-to-register-device-portal.md). La sortie de ce processus est une chaîne de connexion que vous pouvez utiliser pour configurer votre appareil physique. 
2. Installez le runtime IoT Edge sur votre appareil physique et configurez-le avec une chaîne de connexion. Vous pouvez installer le runtime sur des appareils [Windows](../../iot-edge/how-to-install-iot-edge-windows-with-windows.md) ou [Linux](../../iot-edge/how-to-install-iot-edge-linux.md).  


### <a name="find-the-machine-learning-container-image-location"></a>Rechercher l’emplacement d’image de conteneur Machine Learning
Vous avez besoin de l’emplacement de votre image de conteneur Machine Learning. Pour rechercher l’emplacement de l’image de conteneur :

1. Connectez-vous au [portail Azure](http://portal.azure.com/).
2. Dans **Azure Container Registry**, sélectionnez le registre que vous souhaitez inspecter.
3. Dans le registre, cliquez sur **Dépôts** pour afficher la liste de tous les dépôts et leurs images.

Pendant que vous examinez votre registre de conteneurs dans le portail Azure, récupérez les informations d’identification du registre de conteneurs. Ces informations d’identification doivent être fournies à l’appareil IoT Edge pour que celui-ci puisse extraire l’image de votre registre privé. 

1. Dans le registre de conteneurs, cliquez sur **Clés d’accès**. 
2. **Activez** l’utilisateur administrateur, si ce n’est déjà fait. 
3. Enregistrez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. 

### <a name="deploy-the-container-image-to-your-device"></a>Déployez l’image conteneur sur votre appareil.

Avec l’image conteneur et les informations d’identification du registre de conteneurs, vous êtes prêt à déployer le modèle Machine Learning sur votre appareil IoT Edge. 

Suivez les instructions dans [Déployer des modules IoT Edge à partir du portail Azure](../../iot-edge/how-to-deploy-modules-portal.md) pour lancer votre modèle sur votre appareil IoT Edge. 











