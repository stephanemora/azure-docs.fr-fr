---
title: Gérer plusieurs modules Azure IoT Edge dans VS Code | Microsoft Docs
description: Utilisez Visual Studio Code pour développer des solutions Azure IoT Edge avec plusieurs modules.
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a624994f93e7a3cbb04db2a0ec0b2b823a12ee7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763032"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code-preview"></a>Développer une solution IoT Edge comportant plusieurs modules dans Visual Studio Code (version préliminaire)

Vous pouvez utiliser Visual Studio Code pour développer votre solution Azure IoT Edge avec plusieurs modules. Cet article explique comment créer, mettre à jour et déployer une solution IoT Edge qui canalise les données des capteurs sur un appareil IoT Edge dans VS Code. 

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes indiquées dans cet article, vous avez besoin d’avoir les éléments requis suivants en place :

- [Visual Studio Code](https://code.visualstudio.com/)
- [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [SDK .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd)
- Modèle AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Un IoT Hub actif avec au moins un appareil IoT Edge

Vous devez également de [Docker pour VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) avec l’intégration du Device Explorer Azure IoT Hub pour gérer les images et les conteneurs.

## <a name="prepare-your-first-iot-edge-solution"></a>Préparer votre première solution IoT Edge

1. Dans la **palette de commande** VS Code, saisissez et exécutez la commande **Edge : nouvelle solution IoT Edge**. Sélectionnez votre dossier de travail et indiquez le nom de la solution (EdgeSolution, par défaut). Créez un module c# (avec le nom **SampleModule**) pour le premier module utilisateur de cette solution. Vous devez également spécifier le référentiel d’image Docker pour votre premier module. Le référentiel d’image par défaut est basé sur un registre Docker local (**localhost:5000/<first module name>**). Vous pouvez le remplacer par une instance Azure Container Registry ou Docker Hub.

   > [!NOTE]
   > Si vous utilisez un registre Docker local, assurez-vous que le registre est en cours d’exécution. Entrez la commande suivante dans la fenêtre de votre console :
   > 
   > `docker run -d -p 5000:5000 --restart=always --name registry registry:2`

2. La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. Le dossier racine contient un dossier de **modules**, un dossier **.vscode** et un fichier modèle du manifeste de déploiement. Les configurations de débogage se trouvent dans le dossier .vscode. Tous les codes de module utilisateur sont des sous-dossiers du dossier de modules. Le fichier deployment.template.json est le modèle du manifeste de déploiement. Certains des paramètres de ce fichier sont analysés à partir du fichier module.json, qui existe dans chaque dossier de module.

3. Ajoutez votre second module à ce projet de solution. Entrez et exécutez la commande **Edge : Ajouter un module IoT Edge**. Sélectionnez le fichier de modèle de déploiement à mettre à jour. Sélectionnez un module **Fonction Azure - C#** portant le nom **SampleFunction** et son référentiel d’image Docker.

4. Ouvrez le fichier deployment.template.json. Vérifiez que le fichier déclare trois modules et le runtime. Le message est généré à partir du module tempSensor. Le message est transmis directement via les modules SampleModule et SampleFunction, puis envoyé à votre IoT Hub. 

5. Mettez à jour les itinéraires de ces modules à l’aide du contenu suivant :

   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Enregistrez ce fichier.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Développer et déployer votre solution IoT Edge

1. Dans la **palette de commande** VS Code, saisissez et exécutez la commande **Edge : Développer une solution IoT Edge**. En fonction du fichier module.json de chaque dossier de module, la commande commence à développer, placer en conteneur et envoyer chaque image Docker du module. La commande transmet ensuite la valeur requise dans le fichier deployment.template.json et génère le fichier deployment.json avec des informations issues du dossier config. Le terminal intégré à VS Code affiche la progression du développement.

2. Dans le **Device Explorer** Azure IoT Hub, cliquez avec le bouton droit sur un ID d’appareil IoT Edge, puis sélectionnez la commande **Créer un déploiement pour un appareil Edge**. Sélectionnez le fichier deployment.json dans le dossier config. Le terminal intégré à VS Code affiche le déploiement qui vient d’être créé avec un ID de déploiement.

3. Si vous simulez un appareil IoT Edge sur votre machine de développement, vous verrez que l’ensemble des conteneurs d’image du module démarrent en quelques minutes.

## <a name="view-the-generated-data"></a>Afficher les données générées

1. Pour surveiller les données qui arrivent au niveau de l’IoT Hub, sélectionnez **Vue** > **Palette de commandes**. Sélectionnez ensuite la commande **IoT : Démarrer l’analyse du message D2C**. 
2. Pour arrêter la surveillance des données, utilisez la commande **IoT : Arrêter l’analyse du message D2C** dans la **palette de commandes**. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les autres scénarios quand vous développez avec Azure IoT Edge dans Visual Studio Code, consultez :

* [Déboguer un module C# dans VS Code](how-to-vscode-debug-csharp-module.md)
* [Déboguer une fonction C# dans VS Code](how-to-vscode-debug-azure-function.md)
