---
title: Fichier include
description: Fichier include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 6b9ec2017ffa5d4e4148b441aa23ed2eca6ee8b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99628962"
---
Le déploiement de code sur vos appareils IoT Edge à partir du cloud constitue l’une des fonctionnalités clés d’Azure IoT Edge. Les *modules IoT Edge* sont des packages exécutables implémentés en tant que conteneurs. Dans cette section, vous allez déployer un module prédéfini à partir de la [section Modules IoT Edge de la Place de Marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) directement à partir d’Azure IoT Hub.

Le module que vous déployez dans cette section simule un capteur et envoie les données générées. Ce module s’avère être un extrait de code utile quand vous prenez en main IoT Edge, car vous pouvez utiliser les données simulées à des fins de développement et de test. Si vous souhaitez voir exactement ce que fait ce module, vous pouvez afficher le [code source du capteur de température simulé](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Effectuez les étapes suivantes pour déployer votre premier module à partir de la Place de Marché Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre hub IoT.

1. Dans le menu de gauche, sous **Gestion automatique des appareils**, sélectionnez **IoT Edge**.

1. Sélectionnez l’ID de l’appareil cible dans la liste des appareils.

1. Dans la barre supérieure, sélectionnez **Définir des modules**.

   ![Capture d’écran montrant la sélection de la commande Définir des modules.](./media/iot-edge-deploy-module/select-set-modules.png)

1. Sous **Modules IoT Edge**, ouvrez le menu déroulant **Ajouter**, puis sélectionnez **Module de la Place de marché**.

   ![Capture d’écran montrant le menu déroulant Ajouter.](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. Sous **Place de marché de module IoT Edge**, recherchez et sélectionnez le module `Simulated Temperature Sensor`.

   Le module est ajouté à la section Modules IoT Edge avec l’état souhaité **en cours d’exécution**.

1. Sélectionnez **Suivant : Routes** pour passer à l’étape suivante de l’Assistant.

   ![Capture d’écran montrant le passage à l’étape suivante après l’ajout du module.](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. Sous l’onglet **Routes**, supprimez la route par défaut **route**, puis sélectionnez **Suivant : Vérifier + créer** pour passer à l’étape suivante de l’Assistant.

   >[!Note]
   >Les routes sont construites à l’aide de paires nom/valeur. Vous devez voir deux routes sur cette page. La route par défaut **route** envoie tous les messages à IoT Hub (on emploie le terme `$upstream`). Une deuxième route appelée **SimulatedTemperatureSensorToIoTHub** a été créée automatiquement quand vous avez ajouté le module à partir de la Place de Marché Azure. Cette route envoie tous les messages du module de température simulé à IoT Hub. Vous pouvez supprimer la route par défaut, car elle est redondante dans ce cas.

   ![Capture d’écran montrant la suppression de la route par défaut et le passage à l’étape suivante.](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. Passez en revue le fichier JSON, puis sélectionnez **Créer**. Le fichier JSON définit tous les modules que vous déployez sur votre appareil IoT Edge. Vous voyez le module **SimulatedTemperatureSensor** ainsi que les deux modules runtime **edgeAgent** et **edgeHub**.

   >[!Note]
   >Lorsque vous envoyez un nouveau déploiement vers un appareil IoT Edge, rien n’est envoyé en mode push à votre appareil. Au lieu de cela, l’appareil interroge régulièrement IoT Hub pour connaître les nouvelles instructions. Si l’appareil détecte un manifeste de déploiement mis à jour, il utilise les informations sur le nouveau déploiement pour extraire les images de module à partir du cloud, puis commence à exécuter localement les modules. Ce processus peut prendre plusieurs minutes.

1. Une fois que vous avez créé les détails de déploiement du module, l’Assistant vous renvoie à la page de détails de l’appareil. Visualisez l’état du déploiement sous l’onglet **Modules**.

   Vous devez voir trois modules : **$edgeAgent**, **$edgeHub** et **SimulatedTemperatureSensor**. Si la valeur **Oui** apparaît dans la colonne **SPÉCIFIÉ DANS LE DÉPLOIEMENT** mais pas dans la colonne **SIGNALÉ PAR L’APPAREIL** pour un ou plusieurs modules, cela signifie que votre appareil IoT Edge est encore en train de les démarrer. Attendez quelques minutes, puis actualisez la page.

   ![Capture d’écran montrant le capteur de température simulé dans la liste des modules déployés.](./media/iot-edge-deploy-module/view-deployed-modules.png)
