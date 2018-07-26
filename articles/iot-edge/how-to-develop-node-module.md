---
title: Déboguer des modules Node.js pour Azure IoT Edge | Microsoft Docs
description: Utiliser Visual Studio Code pour développer et déboguer des modules Node.js pour Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8032fd2a0150597c55178648511c80233e63a911
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054724"
---
# <a name="develop-and-debug-nodejs-modules-with-azure-iot-edge-for-visual-studio-code"></a>Développer et déboguer des modules Node.js avec Azure IoT Edge pour Visual Studio Code

Vous pouvez envoyer votre logique métier pour qu’elle opère à la périphérie en la transformant en modules pour Azure IoT Edge. Cet article fournit des instructions détaillées pour l’utilisation de Visual Studio Code (VS Code) comme principal outil de développement pour développer des modules C#.

## <a name="prerequisites"></a>Prérequis
Cet article part du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows ou Linux comme machine de développement. Votre appareil IoT Edge peut être un autre appareil physique, ou vous pouvez simuler votre appareil IoT Edge sur votre machine de développement.

> [!NOTE]
> Ce tutoriel de débogage décrit comment attacher un processus dans un conteneur de module et le déboguer avec VS Code. Vous pouvez déboguer des modules Node.js dans des conteneurs linux-amd64, Windows et arm32. Si vous n’êtes pas familiarisé avec les fonctionnalités de débogage de Visual Studio Code, découvrez-en plus sur le [débogage](https://code.visualstudio.com/Docs/editor/debugging). 

Étant donné que cet article utilise Visual Studio Code comme principal outil de développement, installez VS Code, puis ajoutez les extensions nécessaires :
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extension Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Pour créer un module, vous devez disposer de Node.js, qui inclut npm pour générer le dossier de projet, de Docker, pour générer l’image du module, et d’un registre de conteneurs, pour stocker cette image :
* [Node.JS](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   >[!TIP]
   >Vous pouvez utiliser un registre Docker local pour le prototype et à des fins de test, au lieu d’un registre cloud. 

Pour tester votre module sur un appareil, vous avez besoin d’un hub IoT actif avec au moins un appareil IoT Edge. Si vous souhaitez utiliser votre ordinateur comme appareil IoT Edge, vous pouvez suivre les étapes des tutoriels pour [Windows](quickstart.md) ou [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Créer un modèle de solution

Les étapes suivantes vous montrent comment créer un module IoT Edge basé sur .NET core 2.0 à l’aide de Visual Studio Code et de l’extension Azure IoT Edge. Vous commencez par créer une solution, puis vous y générez le premier module. Chaque solution peut contenir plusieurs modules. 

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré**.
2. Dans le terminal intégré, entrez la commande suivante pour installer (ou mettre à jour) la dernière version du modèle de module Azure IoT Edge pour Node.js :

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```
3. Dans Visual Studio Code, sélectionnez **Affichage** > **Palette de commandes**. 
4. Dans la palette de commandes, tapez et exécutez la commande **Azure IoT Edge : nouvelle solution IoT Edge**.

   ![Exécuter la nouvelle solution IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Accédez au dossier où vous souhaitez créer la solution, puis cliquez sur **Sélectionner le dossier**. 
6. Entrez un nom pour votre solution. 
7. Choisissez **Node.js Module** comme modèle pour le premier module dans la solution.
8. Entrez un nom pour votre module. Choisissez un nom qui est unique dans le registre de conteneurs. 
9. Indiquez le référentiel d’image pour le module. VS Code remplissant automatiquement le nom du module, il vous suffit de remplacer **localhost:5000** par vos propres informations de registre. Si vous utilisez un registre Docker local à des fins de test, localhost convient. Si vous utilisez Azure Container Registry, utilisez le serveur de connexion à partir des paramètres de votre registre. Le serveur de connexion ressemble à **\<nom du registre\>.azurecr.io**.

À partir des informations que vous avez fournies, VS Code crée une solution IoT Edge, puis la charge dans une nouvelle fenêtre.

La solution comprend trois éléments : 
* Un dossier **.vscode**, qui contient les configurations de débogage.
* Un dossier **modules**, qui contient des sous-dossiers pour chaque module. Pour l’instant, vous n’en avez qu’un, mais vous pouvez en ajouter dans la palette de commandes avec la commande **Azure IoT Edge : ajouter un module IoT Edge**. 
* Un fichier **.env**, qui répertorie vos variables d’environnement. Si votre registre est un registre ACR, il contient d’ores et déjà un nom d’utilisateur et un mot de passe ACR. 

   >[!NOTE]
   >Le fichier d’environnement est créé uniquement si vous fournissez un référentiel d’images pour le module. Si vous avez accepté les valeurs localhost par défaut pour tester et déboguer localement, vous n’avez pas besoin de déclarer des variables d’environnement. 

* Un fichier **deployment.template.json**, qui répertorie votre nouveau module, ainsi qu’un exemple de module **tempSensor** qui simule les données que vous pouvez utiliser à des fins de test. Pour plus d’informations sur le fonctionnement des manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

## <a name="devlop-your-module"></a>Développer un module

Le code Azure Function par défaut qui est fourni avec la solution se trouve sous **modules** > **\<nom de votre module\>** > **app.js**. Le module et le fichier deployment.template.json sont définis de manière à vous permettre de générer la solution, de l’envoyer vers votre registre de conteneurs et de la déployer sur un appareil pour commencer les tests, sans avoir à utiliser de code. Le module est conçu pour récupérer les entrées d’une source (dans ce cas, le module tempSensor qui simule des données) et les acheminer vers IoT Hub. 

Lorsque vous êtes prêt à personnaliser le modèle Node.js avec votre propre code, utilisez les kits [SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) pour créer des modules répondant aux besoins des solutions IoT, tels que la sécurité, la gestion des appareils et la fiabilité. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Générer et déployer votre module à des fins de débogage

Chaque dossier du module contient plusieurs fichiers Docker pour différents types de conteneurs. Vous pouvez utiliser un de ces fichiers se terminant par l’extension **.debug** pour générer votre module à des fins de test. Les modules C# ne prennent en charge le débogage que dans les conteneurs linux-amd64.

1. Dans VS Code, accédez au fichier `deployment.template.json`. Remplacez le module Node.js createOptions dans **deployment.template.json** par le contenu ci-dessous, puis enregistrez ce fichier : 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

2. Dans la palette de commandes de VS Code, tapez et exécutez la commande **Azure IoT Edge : générer la solution IoT Edge**.
3. Sélectionnez le fichier `deployment.template.json` pour votre solution à partir de la palette de commandes. 
4. Dans l’explorateur des appareils Azure IoT Hub, cliquez avec le bouton droit sur un ID d’appareil IoT Edge, puis sélectionnez **Créer un déploiement pour un appareil IoT Edge**. 
5. Ouvrez le dossier **config** de votre solution, puis sélectionnez le fichier `deployment.json`. Cliquez sur **Select Edge Deployment Manifest** (Sélectionner un manifeste de déploiement Edge). 

Vous pouvez alors constater la bonne création du déploiement avec un ID de déploiement dans le terminal intégré de VS Code.

Vous pouvez vérifier l’état de votre conteneur dans l’explorateur du Docker VS Code ou en exécutant la commande `docker ps` dans le terminal.

## <a name="start-debugging-nodejs-module-in-vs-code"></a>Commencer le débogage du module Node.Js dans VS Code

VS Code conserve les informations de configuration du débogage dans un fichier `launch.json` situé dans un dossier `.vscode` de votre espace de travail. Ce fichier `launch.json` a été généré lorsque vous avez créé une nouvelle solution IoT Edge. Il se met à jour chaque fois que vous ajoutez un nouveau module qui prend en charge le débogage. 

1. Accédez à l’affichage du débogage VS Code, puis sélectionnez le fichier de configuration du débogage pour votre module.

2. Accédez à `app.js`. Ajoutez un point d’arrêt dans ce fichier.

3. Cliquez sur le bouton **Démarrer le débogage** ou appuyez sur **F5**, puis sélectionnez le processus à attacher.

4. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet situé à gauche. 

L’exemple précédent montre comment déboguer des modules Node.js IoT Edge sur des conteneurs. Il a ajouté des ports exposés dans votre conteneur de module createOptions. Une fois le débogage de vos modules Node.js terminé, nous vous recommandons de supprimer ces ports exposés pour les modules IoT Edge prêts à l’emploi.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre module généré, découvrez comment [déployer des modules Azure IoT Edge à partir de Visual Studio Code](how-to-deploy-modules-vscode.md).

Pour développer des modules pour vos appareils IoT Edge, consultez [Comprendre et utiliser les kits Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
