---
title: Fichier Include
description: Fichier Include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/04/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 12661c77c6a950b482187b09e4465c964e6d6652
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494065"
---
Une des fonctionnalités clés d’Azure IoT Edge est la capacité de déployer du code sur vos appareils IoT Edge à partir du cloud. Les **modules IoT Edge** sont des packages exécutables implémentés en tant que conteneurs. Dans cette section, vous allez déployer un module prédéfini à partir de la [section Modules IoT Edge de la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). 

Le module que vous déployez dans cette section simule un capteur et envoie les données générées. Ce module s’avère être un extrait de code utile quand vous prenez en main IoT Edge, car vous pouvez utiliser les données simulées à des fins de développement et de test. Si vous souhaitez voir exactement ce que fait ce module, vous pouvez afficher le [code source du capteur de température simulé](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs). 

Pour déployer votre premier module à partir de la Place de marché Azure, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), entrez **capteur de température simulé** dans la zone de recherche et ouvrez le résultat de la Place de marché.

   ![Capteur de température simulé dans la recherche du portail Azure](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Choisissez l’appareil IoT Edge qui va recevoir le module. Dans la page **Appareils cibles pour le module IoT Edge**, fournissez les informations suivantes :

   1. **Abonnement** : sélectionnez l’abonnement qui contient le hub IoT utilisé.

   2. **Hub IoT** : sélectionnez le nom du hub IoT que vous utilisez.

   3. **Nom de l’appareil IoT Edge**: si vous avez utilisé le nom de l’appareil suggéré précédemment dans ce démarrage rapide, entrez **myEdgeDevice**. Ou sélectionnez **Rechercher un appareil** et faites votre choix dans la liste d’appareils IoT Edge de votre hub IoT. 
   
   4. Sélectionnez **Create** (Créer).

3. Maintenant que vous avez choisi un module IoT Edge à partir de la Place de marché Microsoft Azure, ainsi qu’un appareil IoT Edge pour recevoir le module, vous êtes redirigé vers un Assistant en trois étapes qui vous aide à définir exactement la manière de déployer le module. Dans l’étape **Ajouter des modules** de l’Assistant, notez que le module **SimulatedTemperatureSensor** est rempli automatiquement. Dans les tutoriels, vous utilisez cette page pour ajouter des modules supplémentaires à votre déploiement. Pour ce guide de démarrage rapide, déployez uniquement ce module. Sélectionnez **Suivant** pour accéder à l’étape suivante de l’Assistant.

4. Dans l’étape **Spécifier des routes** de l’Assistant, vous pouvez définir la façon dont les messages sont transmis entre les modules et IoT Hub. Pour le démarrage rapide, vous souhaitez que tous les messages en provenance de tous les modules soient acheminés vers IoT Hub (`$upstream`). Si le champ n’est pas renseigné automatiquement, ajoutez le code suivant :

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```
   Sélectionnez ensuite **Suivant**.

5. Dans l’étape **Passer en revue le déploiement** de l’Assistant, vous pouvez afficher un aperçu du fichier JSON qui définit tous les modules déployés sur votre appareil IoT Edge. Notez que le module **SimulatedTemperatureSensor** est inclus, ainsi que deux modules système supplémentaires appelés **edgeAgent** et **edgeHub**. Sélectionnez **Envoyer** lorsque vous avez terminé.

   Lorsque vous envoyez un nouveau déploiement vers un appareil IoT Edge, rien n’est envoyé en mode push à votre appareil. Au lieu de cela, l’appareil interroge régulièrement IoT Hub pour connaître les nouvelles instructions. Si l’appareil détecte un manifeste de déploiement mis à jour, il utilise les informations sur le nouveau déploiement pour extraire les images de module à partir du cloud, puis commence à exécuter localement les modules. Ce processus peut prendre quelques minutes. 

6. Une fois que vous envoyez les détails de déploiement du module, l’Assistant vous renvoie à la page **IoT Edge** de votre hub IoT. Sélectionnez votre appareil dans la liste des appareils IoT Edge pour afficher ses informations détaillées. 

7. Dans la page des informations détaillées de l’appareil, faites défiler jusqu’à la section **Modules**. Doivent figurer trois modules : $edgeAgent, $edgeHub et SimulatedTemperatureSensor. Si un ou plusieurs modules sont répertoriés comme spécifiés dans le déploiement, mais ne sont pas signalés par l’appareil, votre appareil IoT Edge est encore en train de les démarrer. Attendez quelques instants, puis sélectionnez **Actualiser** en haut de la page. 

   ![Afficher SimulatedTemperatureSensor dans la liste des modules déployés](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
