---
title: Déboguer des modules Node.js pour Azure IoT Edge | Microsoft Docs
description: Utiliser Visual Studio Code pour développer et déboguer des modules Node.js pour Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 51c2154f4132340e00b8fddcfaeb6e999519c48f
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446702"
---
# <a name="use-visual-studio-code-to-develop-and-debug-nodejs-modules-for-azure-iot-edge"></a>Utiliser Visual Studio Code pour développer et déboguer des modules Node.js pour Azure IoT Edge

Vous pouvez envoyer votre logique métier pour qu’elle opère à la périphérie en la transformant en modules pour Azure IoT Edge. Cet article fournit des instructions détaillées pour l’utilisation de Visual Studio Code (VS Code) comme principal outil de développement pour développer des modules Node.js.

## <a name="prerequisites"></a>Prérequis
Cet article part du principe que vous utilisez un ordinateur ou une machine virtuelle Windows, macOS ou Linux comme machine de développement. Vous pouvez utiliser un autre appareil physique comme appareil IoT Edge.

> [!NOTE]
> Cet article décrit deux méthodes classiques pour déboguer votre module Node.js dans VS Code. Une des méthodes consiste à attacher un processus dans un conteneur de module, tandis que l’autre consiste à lancer le code du module en mode débogage. Si vous n’êtes pas familiarisé avec les fonctionnalités de débogage de Visual Studio Code, découvrez-en plus sur le [débogage](https://code.visualstudio.com/Docs/editor/debugging).

Étant donné que cet article utilise Visual Studio Code comme principal outil de développement, installez VS Code, puis ajoutez les extensions nécessaires :
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extension Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Pour créer un module, vous devez disposer de Node.js, qui inclut npm pour générer le dossier de projet, de Docker, pour générer l’image du module, et d’un registre de conteneurs, pour stocker cette image :
* [Node.JS](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Vous pouvez utiliser un registre Docker local pour le prototype et à des fins de test, au lieu d’un registre cloud. 

Afin de configurer l’environnement de développement local pour le débogage, l’exécution et le test de votre solution IoT Edge, vous avez besoin de [l’outil de développement Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Installez [Python (2.7/3.6) et Pip](https://www.python.org/). Pip est inclus dans le programme d’installation de Python. Ensuite, installez **iotedgehubdev** en exécutant la commande ci-dessous dans votre terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Pour tester votre module sur un appareil, vous avez besoin d’un hub IoT actif avec au moins un ID d’appareil IoT Edge créé. Si vous exécutez le démon IoT Edge sur l’ordinateur de développement, vous devrez peut-être arrêter EdgeHub et EdgeAgent avant de passer à l’étape suivante. 

## <a name="create-a-new-solution-template"></a>Créer un modèle de solution

Les étapes suivantes vous montrent comment créer un module IoT Edge basé sur Node.js à l’aide de Visual Studio Code et de l’extension Azure IoT Edge. Vous commencez par créer une solution, puis vous y générez le premier module. Chaque solution peut contenir plusieurs modules. 

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
9. Indiquez le référentiel d’image pour le module. VS Code remplissant automatiquement le nom du module, il vous suffit de remplacer **localhost:5000** par vos propres informations de registre. Si vous utilisez un registre Docker local à des fins de test, localhost convient. Si vous utilisez Azure Container Registry, utilisez le serveur de connexion à partir des paramètres de votre registre. Le serveur de connexion ressemble à **\<nom du registre\>.azurecr.io**. Remplacez uniquement la partie localhost de la chaîne, ne supprimez pas le nom de votre module. La chaîne finale ressemble à \<nom de registre\>.azurecr.io/\<nom de module\>.

   ![Fourniture du référentiel d’images Docker](./media/how-to-develop-node-module/repository.png)

À partir des informations que vous avez fournies, VS Code crée une solution IoT Edge, puis la charge dans une nouvelle fenêtre.

La solution comprend trois éléments : 
* Un dossier **.vscode**, qui contient les configurations de débogage.
* Un dossier **modules**, qui contient des sous-dossiers pour chaque module. Pour l’instant, vous n’en avez qu’un, mais vous pouvez en ajouter dans la palette de commandes avec la commande **Azure IoT Edge : ajouter un module IoT Edge**. 
* Un fichier **.env**, qui répertorie vos variables d’environnement. Si Azure Container Registry est votre registre, celui-ci comprend un nom d’utilisateur et un mot de passe Azure Container Registry.

   >[!NOTE]
   >Le fichier d’environnement est créé uniquement si vous fournissez un référentiel d’images pour le module. Si vous avez accepté les valeurs localhost par défaut pour tester et déboguer localement, vous n’avez pas besoin de déclarer des variables d’environnement. 

* Un fichier **deployment.template.json**, qui répertorie votre nouveau module, ainsi qu’un exemple de module **tempSensor** qui simule les données que vous pouvez utiliser à des fins de test. Pour plus d’informations sur le fonctionnement des manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).
* Un fichier **deployment.debug.template.json** contient la version de débogage des images de votre module avec les options de conteneur appropriées.

## <a name="develop-your-module"></a>Développer votre module

Le code Node.js par défaut qui est fourni avec la solution se trouve sous **modules** > [nom de votre module] > **app.js**. Le module et le fichier deployment.template.json sont définis de manière à vous permettre de générer la solution, de l’envoyer vers votre registre de conteneurs et de la déployer sur un appareil pour commencer les tests, sans avoir à utiliser de code. Le module est conçu pour récupérer les entrées d’une source (dans ce cas, le module tempSensor qui simule des données) et les acheminer vers IoT Hub. 

Lorsque vous êtes prêt à personnaliser le modèle Node.js avec votre propre code, utilisez les kits [SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) pour créer des modules répondant aux besoins des solutions IoT, tels que la sécurité, la gestion des appareils et la fiabilité. 

Visual Studio Code prend en charge Node.js. Pour en savoir plus, consultez l’article décrivant [comment utiliser Node.js dans VS Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="launch-and-debug-module-code-without-container"></a>Lancer et déboguer le code du module sans conteneur

Le module Node.js IoT Edge repose sur le SDK de l’appareil Node.js Azure IoT. Dans le code du module par défaut, vous initialisez un **ModuleClient** avec des paramètres d’environnement et un nom d’entrée, ce qui signifie que le module Node.js IoT Edge a besoin des paramètres d’environnement pour démarrer et s’exécuter, et vous devez également envoyer ou router les messages vers les canaux d’entrée. Votre module Node.js par défaut contient un seul canal d’entrée dont le nom est **input1**.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Configurer un simulateur IoT Edge pour une solution IoT Edge

Dans votre machine de développement, vous pouvez démarrer le simulateur IoT Edge au lieu d’installer le démon de sécurité IoT Edge pour exécuter votre solution IoT Edge. 

1. Dans l’Explorateur d’appareils à gauche, cliquez sur l’ID de votre appareil IoT Edge, sélectionnez **Configurer le simulateur IoT Edge** pour démarrer le simulateur avec la chaîne de connexion de l’appareil.

2. Vous pouvez voir que le simulateur IoT Edge a été correctement configuré dans le terminal intégré.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Configurer un simulateur IoT Edge pour une application à module unique

1. Pour configurer et démarrer le simulateur, dans la palette de commandes VS Code, tapez et sélectionnez **Azure IoT Edge: Démarrer le simulateur du hub IoT Edge pour un seul module**. Vous devez également spécifier le nom de l’entrée de votre application à module unique, tapez **input1** et appuyez sur Entrée. La commande déclenche l’interface CLI **iotedgehubdev** et démarre le simulateur IoT Edge ainsi qu’un conteneur de module d’utilitaire de tests. Vous pouvez voir les sorties ci-dessous dans le terminal intégré si le simulateur a été démarré en mode de module unique. Vous pouvez également voir une commande `curl` pour vous aider à envoyer un message. Vous le réutiliserez ultérieurement.

   ![Configurer un simulateur IoT Edge pour une application à module unique](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Vous pouvez passer à l’Explorateur Docker et voir l’état d’exécution du module.

   ![État du module du simulateur](media/how-to-develop-csharp-module/simulator-status.png)

   Le conteneur **edgeHubDev** est la partie principale du simulateur IoT Edge local. Il peut s’exécuter sur votre machine de développement sans le démon de sécurité IoT Edge, et fournir des paramètres d’environnement pour votre application de module native ou vos conteneurs de module. Le conteneur **d’entrée** a exposé des API REST pour aider les messages de pont à cibler le canal d’entrée du module.

2. Dans la palette de commandes VS Code, tapez et sélectionnez **Azure IoT Edge : Définir les informations d'identification du module sur les paramètres utilisateur** pour définir les paramètres d’environnement du module de `azure-iot-edge.EdgeHubConnectionString` et `azure-iot-edge.EdgeModuleCACertificateFile` dans les paramètres utilisateur. Ces paramètres d’environnement sont référencés dans **.vscode** > **launch.json** et les [paramètres utilisateur de VS Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="debug-nodejs-module-in-launch-mode"></a>Déboguer un module Node.js en mode de lancement

1. Dans le terminal intégré, changez de répertoire pour passer au dossier **NodeModule** et exécutez la commande suivante pour installer les packages Node.

   ```cmd
   npm install
   ```

2. Accédez à `app.js`. Ajoutez un point d’arrêt dans ce fichier.

3. Accédez à la vue de débogage de VS Code. Sélectionnez la configuration de débogage **Débogage local de ModuleName (Node.js)**. 

4. Cliquez sur **Démarrer le débogage** ou appuyez sur **F5**. Vous démarrer la session de débogage.

5. Dans le terminal intégré de VS Code, exécutez la commande suivante pour envoyer un message **Hello World** à votre module. Il s’agit de la commande décrite dans les étapes précédentes de configuration du simulateur IoT Edge. Il se peut que vous deviez créer un autre terminal intégré (ou basculer vers celui-ci s’il existe déjà) si le terminal actuel est bloqué.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Si vous utilisez Windows, vérifiez que l’interpréteur de commandes du terminal intégré de VS Code est **Git Bash** ou **WSL Bash**. Vous ne pouvez pas exécuter la commande `curl` dans PowerShell ou à partir d’une invite de commandes. 
   
   > [!TIP]
   > Vous pouvez aussi utiliser [PostMan](https://www.getpostman.com/) ou d’autres outils d’API pour envoyer des messages à la place de `curl`.

6. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet de gauche. 

7. Pour arrêter une session de débogage, cliquez sur le bouton Arrêter ou appuyez sur **MAJ + F5**. Dans la palette de commandes VS Code, tapez et sélectionnez **Azure IoT Edge : Arrêter le simulateur IoT Edge** pour arrêter et nettoyer le simulateur.


## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Générer le conteneur de module pour le débogage et déboguer en mode d’attachement

Votre solution par défaut contient deux modules : un module de capteur de température simulé et un module de canal Node.js. Le capteur de température simulé envoie des messages au module de canal Node.js, puis les messages sont dirigés vers IoT Hub. Le dossier du module que vous avez créé contient plusieurs fichiers Docker pour différents types de conteneur. Utilisez l’un des fichiers se terminant par l’extension **.debug** pour générer votre module à des fins de test. Actuellement, les modules Node.js prennent uniquement en charge le débogage dans des conteneurs linux-amd64, windows-amd64 et linux-arm32v7.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Configurer un simulateur IoT Edge pour une solution IoT Edge

Dans votre machine de développement, vous pouvez démarrer le simulateur IoT Edge au lieu d’installer le démon de sécurité IoT Edge pour exécuter votre solution IoT Edge. 

1. Dans l’Explorateur d’appareils à gauche, cliquez sur l’ID de votre appareil IoT Edge, sélectionnez **Configurer le simulateur IoT Edge** pour démarrer le simulateur avec la chaîne de connexion de l’appareil.

2. Vous pouvez voir que le simulateur IoT Edge a été correctement configuré dans le terminal intégré.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Générer et exécuter le conteneur pour le débogage et déboguer en mode d’attachement

1. Dans VS Code, accédez au fichier `deployment.debug.template.json`. Dans le menu contextuel, cliquez sur **Générer et exécuter la solution IoT Edge dans le simulateur**. Vous pouvez surveiller tous les journaux du conteneur de module dans la même fenêtre. Vous pouvez aussi accéder à l’Explorateur Docker pour surveiller l’état du conteneur.

3. Accédez à la fenêtre de débogage de Visual Studio Code. Sélectionnez le fichier de configuration de débogage de votre module. Le nom de l’option de débogage doit être similaire à **Débogage local de ModuleName (Node.js)** ou **Débogage local de ModuleName (Node.js dans un conteneur Windows)** selon le type de conteneur sur l’ordinateur de développement.

4. Sélectionnez **Démarrer le débogage** ou **F5**. Sélectionnez le processus à attacher.

5. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet de gauche.

6. Pour arrêter une session de débogage, cliquez sur le bouton Arrêter ou appuyez sur **MAJ + F5**. Dans la palette de commandes VS Code, tapez et sélectionnez **Azure IoT Edge : Arrêter le simulateur IoT Edge**.

> [!NOTE]
> L’exemple précédent montre comment déboguer des modules Node.js IoT Edge sur des conteneurs. Il a ajouté des ports exposés dans votre conteneur de module createOptions. Une fois le débogage de vos modules Node.js terminé, nous vous recommandons de supprimer ces ports exposés pour les modules IoT Edge prêts à l’emploi.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre module généré, découvrez comment [déployer des modules Azure IoT Edge à partir de Visual Studio Code](how-to-deploy-modules-vscode.md).

Pour développer des modules pour vos appareils IoT Edge, consultez [Comprendre et utiliser les kits Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
