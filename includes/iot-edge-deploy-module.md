---
title: Fichier Include
description: Fichier Include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/7/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 1a750a97cdc940c0f0a3d7e33d6be0d33f811425
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53108020"
---
Une des fonctionnalités clés d’Azure IoT Edge est la capacité de déployer des modules sur vos appareils IoT Edge à partir du cloud. Un module IoT Edge est un package exécutable implémenté en tant que conteneur. Dans cette section, nous allons déployer un module prédéfini à partir de la [section Modules IoT Edge de la Place de marché Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Ce module génère des données de télémétrie pour votre appareil simulé.

1. Dans le portail Azure, entrez `Simulated Temperature Sensor` dans la zone de recherche et ouvrez le résultat de la Place de marché.

   ![Capteur de température simulé dans la recherche du portail Azure](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Dans le champ **Abonnement**, sélectionnez l’abonnement du hub IoT que vous utilisez, si ce n’est déjà fait.

3. Dans le champ **IoT Hub**, sélectionnez le nom du hub IoT que vous utilisez, si ce n’est déjà fait.

4. Cliquez sur **Rechercher un appareil** , sélectionnez votre appareil IoT Edge (nommé `myEdgeDevice`), puis sélectionnez **Créer**.

5. À l’étape **Ajouter des modules** de l’Assistant, cliquez sur le module **SimulatedTemperatureSensor** pour vérifier ses paramètres de configuration, cliquez sur **Enregistrer**, puis sélectionnez **Suivant**.

6. À l’étape **Spécifier des routes** de l’Assistant, vérifiez que les routes sont configurées correctement avec la route par défaut qui envoie tous les messages de tous les modules vers IoT Hub (`$upstream`). Si ce n’est pas le cas, ajoutez le code suivant, puis sélectionnez **Suivant**.

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

7. À l’étape **Passer en revue le déploiement** de l’Assistant, sélectionnez **Envoyer**.

8. Revenez à la page de détails de l’appareil et sélectionnez **Actualiser**. En plus du module edgeAgent qui a été créé lors du premier démarrage du service, vous devriez voir un autre module runtime appelé **edgeHub** et le module **SimulatedTemperatureSensor** dans la liste.

   Plusieurs minutes peuvent être nécessaires avant que les nouveaux module soient visibles. L’appareil IoT Edge doit récupérer ses nouvelles informations de déploiement sur le cloud, démarrer les conteneurs, puis communiquer son nouvel état à IoT Hub. 

   ![Afficher SimulatedTemperatureSensor dans la liste des modules déployés](./media/iot-edge-deploy-module/deployed-modules-marketplace-temp.png)
