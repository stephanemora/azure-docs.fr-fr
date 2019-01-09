---
title: Fichier Include
description: Fichier Include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/31/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: dd4873017105db190f9a468ec1f1f77f4e8c9c0e
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977104"
---
Une des fonctionnalités clés d’Azure IoT Edge est la capacité de déployer des modules sur vos appareils IoT Edge à partir du cloud. Un module IoT Edge est un package exécutable implémenté en tant que conteneur. Dans cette section, nous allons déployer un module prédéfini à partir de la [section Modules IoT Edge de la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Ce module génère des données de télémétrie simulées pour votre appareil IoT Edge.

1. Dans le [portail Azure](https://portal.azure.com), entrez **capteur de température simulé** dans la zone de recherche et ouvrez le résultat de la Place de marché.

   ![Capteur de température simulé dans la recherche du portail Azure](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Choisissez l’appareil IoT Edge qui va recevoir le module. Dans le champ **Appareils cibles pour le module IoT Edge**, fournissez les informations suivantes :

   1. **Abonnement** : sélectionnez l’abonnement qui contient le hub IoT utilisé.

   2. **Hub IoT** : sélectionnez le nom du hub IoT que vous utilisez.

   3. **Nom de l’appareil IoT Edge**: si vous avez utilisé le nom de l’appareil suggéré précédemment dans ce démarrage rapide, entrez **myEdgeDevice**. Ou sélectionnez **Rechercher un appareil** et faites votre choix dans la liste d’appareils de votre hub IoT. 
   
   4. Sélectionnez **Créer**.

3. Maintenant que vous avez choisi un module IoT Edge à partir de la place de marché Microsoft Azure, ainsi qu’un appareil IoT Edge pour recevoir le module, vous êtes redirigé vers un Assistant en trois étapes qui vous aide à définir exactement la façon dont le module sera déployé. Dans l’étape **Ajouter des modules** de l’Assistant, notez que le module **SimulatedTemperatureSensor** est rempli automatiquement. Dans les didacticiels, vous allez utiliser cette page pour ajouter des modules supplémentaires à votre déploiement. Pour ce démarrage rapide, déployez simplement ce module. Sélectionnez **Suivant** pour accéder à l’étape suivante de l’Assistant.

4. Dans l’étape **Spécifier des routes** de l’Assistant, vous pouvez définir la façon dont les messages sont transmis entre les modules et IoT Hub. Pour le démarrage rapide, vous souhaitez que tous les messages en provenance de tous les modules soient acheminés vers IoT Hub (`$upstream`). Si le champ n’est pas rempli automatiquement, ajoutez le code suivant, puis sélectionnez **Suivant** :

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

5. Dans l’étape **Passer en revue le déploiement** de l’Assistant, vous pouvez afficher un aperçu du fichier JSON qui définit tous les modules déployés sur votre appareil IoT Edge. Notez que le module **SimulatedTemperatureSensor** est également inclus, ainsi que deux modules système supplémentaires appelés **edgeAgent** et **edgeHub**. Sélectionnez **Envoyer** lorsque vous avez terminé.

   Lorsque vous envoyez un nouveau déploiement vers un appareil IoT Edge, rien n’est envoyé en mode push à votre appareil. Au lieu de cela, l’appareil interroge régulièrement IoT Hub pour connaître les nouvelles instructions. Lorsqu’il voit les nouvelles informations de déploiement, l’appareil les utilise pour extraire les images de module à partir du cloud et exécute localement les modules. Ce processus peut prendre quelques minutes. 

6. Une fois que vous envoyez les détails de déploiement du module, l’Assistant vous renvoie à la page **IoT Edge** de votre hub IoT. Sélectionnez votre appareil dans la liste des appareils IoT Edge pour afficher ses informations détaillées. 

7. Dans la page des informations détaillées de l’appareil, faites défiler jusqu’à la section **Modules**. Doivent figurer trois modules : $edgeAgent, $edgeHub et SimulatedTemperatureSensor. Si un ou plusieurs modules sont répertoriés comme spécifiés dans le déploiement, mais ne sont pas signalés par l’appareil, cela signifie que votre appareil IoT Edge est encore en train de les démarrer. Attendez quelques instants, puis sélectionnez **Actualiser** en haut de la page. 

   ![Afficher SimulatedTemperatureSensor dans la liste des modules déployés](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
