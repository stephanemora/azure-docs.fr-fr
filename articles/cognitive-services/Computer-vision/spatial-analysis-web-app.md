---
title: Déployer une application web d’analyse spatiale
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’analyse spatiale dans une application web.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.openlocfilehash: ecccbb4e2741cc7f413e9b2076bd3199b00dd9a8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563328"
---
# <a name="how-to-deploy-a-spatial-analysis-web-application"></a>Guide pratique : Déployer une application web d’analyse spatiale

Utilisez cet article pour apprendre à déployer une application web destinée à collecter des données d’analyse spatiale (insights) à partir d’IoT Hub et à les visualiser. Cela peut avoir des applications utiles dans un large éventail de scénarios et de secteurs d’activité. Par exemple, si une entreprise veut optimiser l’utilisation de ses locaux, elle peut créer rapidement une solution avec différents scénarios. 

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

* Déployer un conteneur d’analyse spatiale
* Configurer l’opération et la caméra
* Configurer la connexion IoT Hub dans l’application Web
* Déployer et tester l’application web

Cette application présentera les scénarios ci-dessous :

* Nombre de personnes entrantes et sortantes dans un espace/magasin
* Nombre de personnes entrantes et sortantes dans une zone de caisses et temps passé dans la files d’attente à la caisse (temps d’arrêt)
* Nombre de personnes portant un masque 
* Nombre de personnes enfreignant les consignes de distanciation sociale

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Compréhension de base des configurations de déploiement d’Azure IoT Edge et d’[Azure IoT Hub](../../iot-hub/index.yml)
* Un [ordinateur hôte](spatial-analysis-container.md) configuré.

## <a name="deploy-the-spatial-analysis-container"></a>Déployer un conteneur d’analyse spatiale

Respectez les étapes de la [configuration de l’ordinateur hôte](./spatial-analysis-container.md) afin de configurer l’ordinateur hôte et connecter un appareil IoT Edge à Azure IoT Hub. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>Déployer un service Azure IoT Hub dans votre abonnement

Tout d’abord, créez une instance d’un service Azure IoT Hub avec le niveau tarifaire Standard (S1) ou Gratuit (S0). Suivez ces instructions afin de créer cette instance à l’aide de l’interface de ligne de commande Azure.

Renseignez les paramètres requis.
* Abonnement : Nom ou ID de votre abonnement Azure.
* Groupe de ressources : Ajoutez un nom à votre groupe de ressources.
* Nom de l’IoT Hub : Ajoutez un nom à votre IoT Hub.
* Nom de l’IoT Hub : Nom de l’instance IoT Hub que vous avez créée. 
* Nom du périphérique : Ajoutez un nom à votre périphérique.

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>Déployer le conteneur sur Azure IoT Edge sur l’ordinateur hôte

L’étape suivante consiste à déployer le conteneur d’**analyse spatiale** sous la forme d’un module IoT sur l’ordinateur hôte à l’aide de l’interface Azure CLI. Le processus de déploiement nécessite un fichier manifeste de déploiement qui décrit les conteneurs, les variables et les configurations nécessaires pour votre déploiement. Vous trouverez un exemple de manifeste de déploiement sur [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/deployment.json) qui comprend des configurations prédéfinies pour tous les scénarios.  

### <a name="set-environment-variables"></a>Définir des variables d’environnement

La plupart des **variables d’environnement** du module IoT Edge sont déjà définies dans les fichiers *DeploymentManifest.json* d’exemple dont les liens se trouvent ci-dessus. Dans le fichier, recherchez les variables d’environnement `ENDPOINT` et `APIKEY`, comme indiqué ci-dessous. Remplacez les valeurs par l’URI de point de terminaison et la clé API que vous avez créées précédemment. Assurez-vous que la valeur CLUF est définie sur « accepter ». 

```json
"EULA": { 
    "value": "accept"
},
"BILLING":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"APIKEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>Configurer les paramètres d’opération

Si vous utilisez l’exemple [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/deployment.json) qui présente déjà toutes les configurations nécessaires (opérations, URL des fichiers vidéo enregistrés et zones, etc.), vous pouvez passer à la section **Exécuter le déploiement**.

Maintenant que la configuration initiale du conteneur d’analyse spatiale est terminée, l’étape suivante consiste à configurer les paramètres des opérations et à les ajouter au déploiement. 

La première étape consiste à mettre à jour l’exemple [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/deployment.json) et à configurer l’opération souhaitée. À titre d’exemple, la configuration de cognitiveservices.vision.spatialanalysis-personcount est indiquée ci-dessous :

```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

Une fois le manifeste de déploiement mis à jour, suivez les instructions du fabricant de la caméra pour installer la caméra, configurer l’URL de la caméra et configurer le nom d’utilisateur et le mot de passe. 

Ensuite, définissez `VIDEO_URL` sur l’URL RTSP de la caméra et les informations d’identification vous permettant de vous connecter à la caméra.

Si le périphérique dispose de plusieurs GPU, sélectionnez le GPU sur lequel exécuter cette opération. Veillez à équilibrer la charge des opérations de manière à ce qu’il n’y ait pas plus de 8 opérations en cours sur un seul GPU.  

Ensuite, configurez la zone dans laquelle vous souhaitez compter les personnes. Pour configurer le polygone de zone, suivez d’abord les instructions du fabricant pour récupérer une image grâce à la caméra. Pour déterminer chaque sommet du polygone, sélectionnez un point sur l’image, prenez les coordonnées en pixels x,y du point par rapport à l’angle supérieur gauche et divisez-les par les dimensions de l’image correspondantes. Définissez les résultats sous forme de coordonnées x,y du sommet. Vous pouvez définir la configuration de polygone de zone dans le champ `SPACEANALYTICS_CONFIG`.

Voici un exemple d’image vidéo qui montre comment les coordonnées des sommets sont calculées pour une image de taille 1 920 x 1 080.
![Exemple d’image vidéo](./media/spatial-analysis/sample-video-frame.jpg)

Vous pouvez également sélectionner un seuil de confiance pour le comptage des personnes détectées et la génération d’événements. Définissez le seuil sur 0 si vous souhaitez que tous les événements apparaissent dans la sortie.

### <a name="execute-the-deployment"></a>Exécuter le déploiement

Maintenant que le manifeste de déploiement est terminé, utilisez cette commande dans l’interface de ligne de commande Azure pour déployer le conteneur sur l’ordinateur hôte en tant que module IoT Edge.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

Renseignez les paramètres requis.

* Nom de l’IoT Hub : Nom de votre Azure IoT Hub.
* DeploymentManifest.json : Nom de votre fichier de déploiement.
* Nom de l’appareil IoT Edge : Nom de l’appareil IoT Edge de votre ordinateur hôte
* Abonnement : ID ou nom de votre abonnement.

Cette commande démarre le déploiement, et vous permet d’afficher l’état du déploiement dans votre instance Azure IoT Hub depuis le portail Azure. L’état peut se présenter sous la forme *417 : la configuration de déploiement de l’appareil n’est pas définie* jusqu’à ce que l’appareil termine de télécharger les images du conteneur et démarre l’exécution.

### <a name="validate-that-the-deployment-was-successful"></a>Vérifiez que déploiement a réussi.

Localisez la section *État du runtime* dans les paramètres du module IoT Edge pour le module d’analyse spatiale de votre instance IoT Hub sur le portail Azure. Les champs **Valeur souhaitée** et **Valeur signalée** de la section *État du runtime* doivent indiquer `Running`. Pour plus d’informations sur le portail Azure, voir ci-dessous.

![Exemple de vérification du déploiement](./media/spatial-analysis/deployment-verification.png)

À ce stade, le conteneur d’analyse spatiale exécute l’opération. Il émet des insights IA pour les opérations et route ces insights en tant que télémétrie vers votre instance Azure IoT Hub. Pour configurer d’autres caméras, vous pouvez mettre à jour le fichier de manifeste de déploiement et exécuter à nouveau le déploiement.

## <a name="spatial-analysis-web-application"></a>Application web d’analyse spatiale

L’application web d’analyse spatiale permet aux développeurs de configurer rapidement un exemple d’application web, de l’héberger dans leur environnement Azure et d’utiliser l’application pour valider des événements E2E.

## <a name="build-docker-image"></a>Générer une image Docker

Suivez le [guide](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/README.md#docker-image) pour générer et pousser (push) l’image à une instance Azure Container Registry de votre abonnement.

## <a name="setup-steps"></a>Étapes de configuration

Pour installer le conteneur, créez un nouveau Azure App Service et renseignez les paramètres requis. Accédez ensuite à l’onglet **Docker** et sélectionnez **Conteneur unique**, puis **Azure Container Registry**. Utilisez l’instance Azure Container Registry à laquelle vous avez envoyé l’image ci-dessus.

![Entrer les détails de l’image](./media/spatial-analysis/solution-app-create-screen.png)

Après avoir entré les paramètres ci-dessus, cliquez sur **Vérifier + créer** et créez l’application.

### <a name="configure-the-app"></a>Configurer l’application 

Attendez que l’installation se termine, puis accédez à votre ressource depuis le portail Azure. Accédez à la section **Configuration** et ajoutez les deux **paramètres d’application** suivants.

* `EventHubConsumerGroup` : nom de chaîne du groupe de consommateurs de votre compte Azure IoT Hub. Vous pouvez créer un groupe de consommateurs dans votre compte IoT Hub ou utiliser le groupe par défaut. 
* `IotHubConnectionString` : chaîne de connexion à votre compte Azure IoT Hub, qui peut être récupérée à partir de la section Clés de votre ressource Azure IoT Hub. ![Configure Parameters](./media/spatial-analysis/solution-app-config-page.png)

Une fois ces 2 paramètres ajoutés, cliquez sur **Enregistrer**. Cliquez ensuite sur **Authentification/autorisation** dans le menu de navigation de gauche et mettez le niveau d’authentification souhaité à jour. Nous vous recommandons l’option Express d’Azure Active Directory (Azure AD). 

### <a name="test-the-app"></a>Tester l’application

Accédez au service Azure et vérifiez que le déploiement a réussi et que l’application web est en cours d’exécution. Accédez à l’URL configurée : `<yourapp>.azurewebsites.net` pour afficher l’application en cours d’exécution.

![test du déploiement](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>Obtenir le code source PersonCount
Si vous voulez afficher ou modifier le code source de cette application, vous pouvez le trouver [sur GitHub](https://github.com/Azure-Samples/cognitive-services-spatial-analysis).

## <a name="next-steps"></a>Étapes suivantes

* [Configurer les opérations d’analyse spatiale](./spatial-analysis-operations.md)
* [Journalisation et résolution des problèmes](spatial-analysis-logging.md)
* [Guide de positionnement de la caméra](spatial-analysis-camera-placement.md)
* [Guide de positionnement de zone et de ligne](spatial-analysis-zone-line-placement.md)
