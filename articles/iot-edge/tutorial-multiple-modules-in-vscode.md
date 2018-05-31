---
title: Gérer plusieurs modules Azure IoT Edge dans VS Code | Microsoft Docs
description: Utilisez Visual Studio Code pour développer des solutions IoT Edge avec plusieurs modules.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4d9caa7aa95c99fa30e8ec76c5b6362615725622
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166350"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Développer une solution IoT Edge comportant plusieurs modules dans Visual Studio Code - version préliminaire
Vous pouvez utiliser Visual Studio Code pour développer votre solution IoT Edge avec plusieurs modules. Cet article vous présente la création, la mise à jour et le déploiement d’une solution IoT Edge qui dirige les données de capteur sur l’appareil IoT Edge simulé dans Visual Studio Code. 

## <a name="prerequisites"></a>Prérequis


Pour effectuer toutes les étapes indiquées dans cet article, vous avez besoin d’avoir les éléments requis suivants en place :

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [SDK .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd) 
- Modèle AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Un IoT Hub actif avec au moins un appareil IoT Edge


* [Docker pour VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) avec intégration de l’explorateur pour la gestion des images et des conteneurs.


## <a name="prepare-your-first-iot-edge-solution"></a>Préparer votre première solution IoT Edge
1. Dans la palette de commande VS Code, saisissez et exécutez la commande **Edge : nouvelle solution IoT Edge**. Ensuite, sélectionnez le dossier de votre espace de travail, fournissez le nom de la solution (le nom par défaut est **EdgeSolution**), et créez un module C# (**SampleModule**) en tant que premier module utilisateur dans cette solution. Vous devez également spécifier le référentiel d’image Docker pour votre premier module. Le référentiel d’image par défaut est basé sur un registre Docker local (`localhost:5000/<first module name>`). Vous pouvez par ailleurs le remplacer par une instance Azure Container Registry ou Docker Hub.

   > [!NOTE]
   > Si vous utilisez un registre Docker local, assurez-vous qu’il est exécuté en saisissant la commande `docker run -d -p 5000:5000 --restart=always --name registry registry:2` dans la fenêtre de votre console.

2. La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. Le dossier racine contient un dossier `modules`, un dossier `.vscode` et un fichier modèle du manifeste de déploiement. Vous pouvez observer les configurations de débogage dans le dossier `.vscode`. L’ensemble des codes du module utilisateur sont des sous-dossiers du dossier `modules`. L’élément `deployment.template.json` est le modèle du manifeste de déploiement. Certains des paramètres de ce fichier sont analysés à partir de l’élément `module.json`, qui existe dans chaque dossier de module.

3. Ajoutez votre second module dans ce projet de solution. Cette fois, saisissez et exécutez la commande **Edge : ajouter le module IoT Edge**, puis sélectionnez le fichier modèle de déploiement à mettre à jour. Ensuite, sélectionnez une **Fonction Azure - C#** portant le nom **SampleFunction** et son référentiel d’image Docker.

4. Ouvrez le fichier `deployment.template.json` et vérifiez qu’il déclare trois modules en plus du runtime. Le message sera généré à partir du module `tempSensor` et sera dirigé directement via `SampleModule` et `SampleFunction`, puis envoyé vers votre IoT hub. 
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
1. Dans la palette de commandes de VS Code, saisissez et exécutez la commande **Edge : générer la solution IoT Edge**. En fonction du fichier `module.json` de chaque module, cette commande commence à développer, placer en conteneur et envoyer chaque image Docker du module. Il transmet ensuite la valeur requise pour `deployment.template.json` et génère le fichier `deployment.json` contenant des informations du dossier `config`. L’avancement de la génération s’affiche sur le terminal intégré à VS Code.

2. Dans Azure IoT Hub Device Explorer, cliquez avec le bouton droit sur un ID d’appareil IoT Edge, puis sélectionnez **Créer un déploiement pour un appareil Edge**. Sélectionnez `deployment.json` sous le dossier `config`. Vous pouvez alors constater la bonne création du déploiement avec un ID de déploiement dans le terminal intégré de VS Code.

3. Si vous simulez un appareil IoT Edge sur votre machine de développement, vous verrez que l’ensemble des conteneurs d’image du module démarreront en quelques minutes.

## <a name="view-generated-data"></a>Afficher les données générées

1. Pour surveiller les données reçues par IoT Hub, sélectionnez **Affichage** > **Palette de commandes** et recherchez **IoT: Start monitoring D2C message**. 
2. Pour arrêter de surveiller les données, utilisez la commande **IoT: Stop monitoring D2C message** dans la palette de commandes. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les autres scénarios quand vous développez Azure IoT Edge dans Visual Studio Code, consultez :

* [Déboguer un module C# dans VS Code](how-to-vscode-debug-csharp-module.md)
* [Déboguer une fonction C# dans VS Code](how-to-vscode-debug-azure-function.md)