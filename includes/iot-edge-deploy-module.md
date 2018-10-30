---
title: Fichier Include
description: Fichier Include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 983c65ba6e8b87f1dd66fcfdb50eac088ffab5d0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49960221"
---
Une des fonctionnalités clés d’Azure IoT Edge est la capacité de déployer des modules sur vos appareils IoT Edge à partir du cloud. Un module IoT Edge est un package exécutable implémenté en tant que conteneur. Dans cette section, vous déployez un module qui génère des données de télémétrie pour votre appareil simulé.

1. Accédez à votre hub IoT dans le portail Azure.
1. Accédez à **IoT Edge** sous **Gestion automatique des appareils** et sélectionnez votre appareil IoT Edge.
1. Sélectionnez **Définir modules**.
1. Dans la section **Modules de déploiement** de la section **Ajouter des modules**, cliquez sur **Ajouter** puis sélectionnez **Module IoT Edge**.
1. Dans le champ **Nom**, entrez `tempSensor`.
1. Dans le champ **URI de l’image**, entrez `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
1. Laissez les autres paramètres inchangés et sélectionnez **Enregistrer**.

   ![Enregistrer le module IoT Edge après avoir entré le nom et l’URI de l’image](./media/iot-edge-deploy-module/name-image.png)

1. Revenez à l’étape **Ajouter des modules** et sélectionnez **Suivant**.
1. À l’étape **Spécifier des routes**, vous devez avoir un itinéraire par défaut qui envoie tous les messages depuis tous les modules vers IoT Hub. Si ce n’est pas le cas, ajoutez le code suivant, puis sélectionnez **Suivant**.

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

1. À l’étape **Vérifier le déploiement**, sélectionnez **Envoyer**.
1. Revenez à la page de détails de l’appareil et sélectionnez **Actualiser**. Outre le module edgeAgent qui a été créé lors du premier démarrage du service, vous devriez voir un autre module runtime appelé **edgeHub** ainsi que le module **tempSensor** dans la liste.

   ![Afficher tempSensor dans la liste des modules déployés](./media/iot-edge-deploy-module/deployed-modules.png)
