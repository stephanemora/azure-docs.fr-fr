---
title: Utiliser plusieurs modules Azure IoT Edge dans VS Code | Microsoft Docs
description: Utiliser l’extension IoT pour Visual Studio Code afin de développer plusieurs modules à la fois pour Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d895e10c6b667a05156b2ce0f83f7776b9ba4f0a
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568758"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Développer une solution IoT Edge comportant plusieurs modules dans Visual Studio Code

Vous pouvez utiliser Visual Studio Code pour développer votre solution Azure IoT Edge avec plusieurs modules. Cet article explique comment créer, mettre à jour et déployer une solution IoT Edge qui canalise les données des capteurs sur un appareil IoT Edge dans VS Code. 

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes indiquées dans cet article, vous avez besoin d’avoir les éléments requis suivants en place :

- [Visual Studio Code](https://code.visualstudio.com/)
- [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [SDK .NET Core 2.1](https://www.microsoft.com/net/download)
- Un IoT Hub actif avec au moins un appareil IoT Edge

Vous devez également de [Docker pour VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) avec l’intégration du Device Explorer Azure IoT Hub pour gérer les images et les conteneurs.

## <a name="create-your-iot-edge-solution"></a>Créer votre solution IoT Edge

1. Dans Visual Studio Code, ouvrez le terminal intégré en sélectionnant **Affichage** > **Terminal intégré**. 

1. Dans la **palette de commandes** VS Code, tapez et exécutez la commande **Azure IoT Edge : nouvelle solution IoT Edge**. Sélectionnez votre dossier de travail et indiquez le nom de la solution (EdgeSolution, par défaut). Créez un module C# (avec le nom **PipeModule**) pour le premier module utilisateur de cette solution. Le modèle par défaut du module C# est un module de canal, qui dirige directement les messages d’amont en aval. Vous devez également spécifier le référentiel d’image Docker pour votre premier module. Le référentiel d’image par défaut est basé sur un registre Docker local (**localhost:5000/<first module name>**). Vous pouvez le remplacer par une instance Azure Container Registry ou Docker Hub. 

2. La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. Le dossier racine contient un dossier de **modules**, un dossier **.vscode** et un fichier modèle du manifeste de déploiement. Les configurations de débogage se trouvent dans le dossier .vscode. Tous les codes de module utilisateur sont des sous-dossiers du dossier de modules. Le fichier deployment.template.json est le modèle du manifeste de déploiement. Certains des paramètres de ce fichier sont analysés à partir du fichier module.json, qui existe dans chaque dossier de module.

3. Ajoutez votre second module à ce projet de solution. Il existe plusieurs façons d’ajouter un nouveau module à la solution actuelle. Entrez et exécutez la commande **Azure IoT Edge : Ajouter un module IoT Edge**. Sélectionnez le fichier de modèle de déploiement à mettre à jour. Ou bien cliquez avec le bouton droit sur le dossier modules, ou sur le fichier deployment.template.json, puis sélectionnez **Ajouter un module IoT Edge**. Ensuite, vous pouvez sélectionner le type de module dans une liste déroulante. Sélectionnez un module **Azure Functions - C#** portant le nom **PipeFunction** et son référentiel d’image Docker. Le modèle par défaut du module de fonctions C# est un module de canal, qui dirige directement les messages d’amont en aval.

4. Ouvrez le fichier deployment.template.json. Vérifiez que le fichier déclare trois modules et le runtime. Le message est généré à partir du module tempSensor. Le message est transmis directement via les modules SampleModule et SampleFunction, puis envoyé à votre IoT Hub. 

5. Mettez à jour les itinéraires de ces modules à l’aide du contenu suivant :

   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/PipeModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/PipeModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/PipeFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/PipeFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Enregistrez ce fichier.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Développer et déployer votre solution IoT Edge

1. Dans la **palette de commandes** VS Code, tapez et exécutez la commande **Azure IoT Edge : générer la solution IoT Edge**. En fonction du fichier module.json de chaque dossier de module, la commande commence à développer, placer en conteneur et envoyer chaque image Docker du module. La commande transmet ensuite la valeur requise dans le fichier deployment.template.json et génère le fichier deployment.json avec des informations issues du dossier config. Le terminal intégré à VS Code affiche la progression du développement. 

2. Dans le **Device Explorer** Azure IoT Hub, cliquez avec le bouton droit sur un ID d’appareil IoT Edge, puis sélectionnez la commande **Créer un déploiement pour un appareil Edge**. Sélectionnez le fichier deployment.json dans le dossier config. Le terminal intégré à VS Code affiche le déploiement qui vient d’être créé avec un ID de déploiement.

3. Si vous simulez un appareil IoT Edge sur votre machine de développement, vous pouvez constater que l’ensemble des conteneurs d’image du module démarrent en quelques minutes.

## <a name="view-the-generated-data"></a>Afficher les données générées

1. Pour surveiller les données qui arrivent au niveau de l’IoT Hub, sélectionnez **Vue** > **Palette de commandes**. Sélectionnez ensuite la commande **IoT : Démarrer l’analyse du message D2C**. 
2. Pour arrêter la surveillance des données, utilisez la commande **IoT : Arrêter l’analyse du message D2C** dans la **palette de commandes**. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les autres scénarios quand vous développez avec Azure IoT Edge dans Visual Studio Code, consultez :

* Développez les modules dans VS Code avec [C#](how-to-develop-csharp-module.md) ou [Node.js](how-to-develop-node-module.md).
* Développez des fonctions Azure Functions dans VS Code avec [C#](how-to-develop-csharp-function.md).

Pour développer des modules pour vos appareils IoT Edge, consultez [Comprendre et utiliser les kits Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).