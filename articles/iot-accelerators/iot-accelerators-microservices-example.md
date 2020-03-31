---
title: Changer et redéployer un microservice - Azure | Microsoft Docs
description: Ce tutoriel vous montre comment modifier et redéployer un microservice dans la surveillance à distance
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447622"
---
# <a name="customize-and-redeploy-a-microservice"></a>Personnaliser et redéployer un microservice

Ce tutoriel vous montre comment modifier un des [microservices](https://azure.com/microservices) de la solution de surpervision à distance, générer une image de votre microservice, déployer l’image sur votre hub Docker, puis l’utiliser dans une solution de supervision à distance. Pour présenter ce concept, le tutoriel utilise un scénario de base où vous appelez une API de microservice et remplacez le message d’état « Alive and Well » par « New Edits Made Here! »

La solution de supervision à distance utilise des microservices qui sont générés à l’aide d’images Docker extraites d’un hub Docker. 

Dans ce tutoriel, vous allez apprendre à :

>[!div class="checklist"]
> * Modifier et générer un microservice dans la solution de supervision à distance
> * Générer une image Docker
> * Envoyer une image Docker à votre hub Docker
> * Extraire la nouvelle image Docker
> * Visualiser les modifications 

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

>[!div class="checklist"]
> * [Déployer l’accélérateur de solution de supervision à distance localement](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Un compte Docker](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) : nécessaire pour afficher la réponse de l’API

## <a name="call-the-api-and-view-response-status"></a>Appeler l’API et afficher l’état de la réponse

Dans cette partie, vous appelez l’API de microservice Gestionnaire IoT Hub par défaut. L’API retourne un message d’état que vous modifiez ultérieurement en personnalisant le microservice.

1. Vérifiez que la solution de supervision à distance est en cours d’exécution localement sur votre ordinateur.
2. Recherchez l’endroit où vous avez téléchargé Postman et ouvrez-le.
3. Dans Postman, entrez ce qui suit dans la zone GET : `http://localhost:8080/iothubmanager/v1/status`.
4. Affichez la valeur de retour et vous devez voir "Status": "OK:Alive and Well".

    ![Message Postman Alive and Well](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Modifier l’état et générer l’image

Remplacez maintenant le message d’état du microservice Gestionnaire IoT Hub par « New Edits Made Here! » et regénérez l’image Docker avec ce nouvel état. Si vous rencontrez des problèmes ici, reportez-vous à notre section [Résolution des problèmes](#Troubleshoot).

1. Vérifiez que votre terminal est ouvert et accédez au répertoire où vous avez cloné la solution de supervision à distance. 
1. Accédez au répertoire « azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services ».
1. Ouvrez StatusService.cs dans l’éditeur de texte ou l’IDE de votre choix. 
1. Recherchez le code suivant :

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    remplacez-le par le code ci-dessous et enregistrez-le.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Revenez à votre terminal, mais passez maintenant au répertoire suivant : « azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker ».
6. Pour générer votre image Docker, tapez

    ```sh
    sh build
    ```
    
    ou sur Windows :
    
    ```cmd
    ./build.cmd
    ```

7. Pour vérifier que votre nouvelle image a été correctement créée, tapez

    ```cmd/sh
    docker images 
    ```

Le dépôt doit correspondre à « azureiotpcs/iothub-manager-dotnet ».

![Image Docker correctement créée](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Marquer et envoyer l’image
Avant de pouvoir envoyer votre nouvelle image Docker à un hub Docker, Docker attend que vos images soient marquées. Si vous rencontrez des problèmes ici, reportez-vous à notre section [Résolution des problèmes](#Troubleshoot).

1. Recherchez l’ID de l’image Docker créée en tapant :

    ```cmd/sh
    docker images
    ```

2. Pour marquer votre image avec le type « testing »

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Pour envoyer votre image nouvellement marquée à votre hub Docker, tapez

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Ouvrez votre navigateur Internet, accédez à votre [hub Docker](https://hub.docker.com/) et connectez-vous.
5. Vous devez maintenant voir votre image Docker qui vient d’être envoyée sur votre hub Docker.
![Image Docker dans le hub Docker](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Mettre à jour votre solution de supervision à distance
Vous devez maintenant mettre à jour le fichier docker-compose.yml local pour extraire votre nouvelle image Docker de votre hub Docker. Si vous rencontrez des problèmes ici, reportez-vous à notre section [Résolution des problèmes](#Troubleshoot).

1. Revenez au terminal et passez au répertoire suivant : « azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local ».
2. Ouvrez docker-compose.yml dans n’importe quel éditeur de texte ou IDE voulu.
3. Recherchez le code suivant :

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    modifiez-le pour ressembler à l’image ci-dessous et enregistrez-le.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Afficher le nouvel état de la réponse
Terminez en redéployant une instance locale de la solution de supervision à distance et en affichant le nouvel état de la réponse dans Postman.

1. Revenez à votre terminal et passez au répertoire suivant : « azure-iot-pcs-remote-monitoring-dotnet/scripts/local ».
2. Démarrez votre instance locale de la solution de supervision à distance en tapant la commande suivante dans le terminal :

    ```cmd/sh
    docker-compose up
    ```

3. Recherchez l’endroit où vous avez téléchargé Postman et ouvrez-le.
4. Dans Postman, entrez la requête suivante dans la zone GET : `http://localhost:8080/iothubmanager/v1/status`. Vous devez maintenant voir "Status": "OK: New Edits Made Here!".

![Message Postman New Edits Made Here](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes, essayez de supprimer les images Docker et les conteneurs sur l’ordinateur local.

1. Pour supprimer tous les conteneurs, vous devez tout d’abord arrêter tous les conteneurs en cours d’exécution. Ouvrez votre terminal et tapez

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Pour supprimer toutes les images, ouvrez votre terminal et tapez 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Vous pouvez vérifier si des conteneurs sont présents sur l’ordinateur en tapant

    ```cmd/sh
    docker ps -aq 
    ```

    Si vous avez supprimé toutes les images, rien ne doit s’afficher.

4. Vous pouvez vérifier si des images sont présentes sur l’ordinateur en tapant

    ```cmd/sh
    docker images
    ```

    Si vous avez supprimé toutes les images, rien ne doit s’afficher.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Modifier et générer un microservice dans la solution de supervision à distance
> * Générer une image Docker
> * Envoyer une image Docker à votre hub Docker
> * Extraire la nouvelle image Docker
> * Visualiser les modifications 

La prochaine opération à tenter est la [personnalisation du microservice de simulateur d’appareil dans la solution de supervision à distance](iot-accelerators-microservices-example.md)

Pour plus d’informations sur le développement de la solution de supervision à distance, consultez :

* [Guide d’informations de référence pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

