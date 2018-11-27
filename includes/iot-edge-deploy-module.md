---
title: Fichier Include
description: Fichier Include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 536857a5fe3ec3cc037f21835a4152f93197bcb8
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977395"
---
Une des fonctionnalités clés d’Azure IoT Edge est la capacité de déployer des modules sur vos appareils IoT Edge à partir du cloud. Un module IoT Edge est un package exécutable implémenté en tant que conteneur. Dans cette section, vous déployez un module qui génère des données de télémétrie pour votre appareil simulé.

1. Accédez à votre hub IoT dans le portail Azure.

2. Accédez à **IoT Edge** sous **Gestion automatique des appareils** et sélectionnez votre appareil IoT Edge.

3. Sélectionnez **Définir modules**. Un Assistant en trois étapes s’ouvre dans le portail et vous guide tout au long des processus d’ajout de modules, de spécification de routes et de vérification du déploiement. 

4. Dans l’étape **Ajouter des modules** de l’Assistant, recherchez la section **Modules de déploiement**. Cliquez sur **Ajouter**, puis sélectionnez **Module IoT Edge**.

   ![Ajouter un nouveau module IoT Edge](./media/iot-edge-deploy-module/add-module.png)

5. Dans le champ **Nom**, entrez `tempSensor`.

6. Dans le champ **URI de l’image**, entrez `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.

7. Laissez les autres paramètres inchangés et sélectionnez **Enregistrer**.

   ![Enregistrer le module IoT Edge après avoir entré le nom et l’URI de l’image](./media/iot-edge-deploy-module/name-image.png)

8. Une fois revenu à la première étape de l’Assistant, sélectionnez **Suivant**.

9. À l’étape **Spécifier des routes** de l’Assistant, il doit exister une route par défaut qui envoie tous les messages de tous les modules vers IoT Hub. Si ce n’est pas le cas, ajoutez le code suivant, puis sélectionnez **Suivant**.

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

10. À l’étape **Passer en revue le déploiement** de l’Assistant, sélectionnez **Envoyer**.

11. Revenez à la page de détails de l’appareil et sélectionnez **Actualiser**. Outre le module edgeAgent qui a été créé lors du premier démarrage du service, vous devriez voir un autre module runtime appelé **edgeHub** ainsi que le module **tempSensor** dans la liste.

   Plusieurs minutes peuvent être nécessaires avant que les nouveaux module soient visibles. L’appareil IoT Edge doit récupérer ses nouvelles informations de déploiement sur le cloud, démarrer les conteneurs, puis communiquer son nouvel état à IoT Hub. 

   ![Afficher tempSensor dans la liste des modules déployés](./media/iot-edge-deploy-module/deployed-modules.png)
