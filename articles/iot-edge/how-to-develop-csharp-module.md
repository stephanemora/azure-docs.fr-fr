---
title: Déboguer les modules C# pour Azure IoT Edge | Microsoft Docs
description: Utiliser Visual Studio Code pour développer, générer et déboguer un module C# pour Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/04/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: b1c6209c4d589093d7a29cd8a883d3e5d4ca12f9
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782300"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Utiliser Visual Studio Code pour développer et déboguer des modules C# pour Azure IoT Edge

Vous pouvez transformer votre logique métier en modules pour Azure IoT Edge. Cet article vous montre comment utiliser Visual Studio Code (VS Code) comme outil principal pour développer et déboguer des modules C#.

## <a name="prerequisites"></a>Prérequis
Cet article part du principe que vous utilisez un ordinateur ou une machine virtuelle Windows, macOS ou Linux comme machine de développement. Vous pouvez utiliser un autre appareil physique comme appareil IoT Edge.

> [!NOTE]
> Cet article décrit deux méthodes classiques pour déboguer votre module C# dans VS Code. Une des méthodes consiste à attacher un processus dans un conteneur de module, tandis que l’autre consiste à lancer le code du module en mode débogage. Si vous n’êtes pas familiarisé avec les fonctionnalités de débogage de Visual Studio Code, découvrez-en plus sur le [débogage](https://code.visualstudio.com/Docs/editor/debugging).

Étant donné que cet article utilise Visual Studio Code comme outil de développement principal, installez VS Code. Ensuite, ajoutez les extensions nécessaires :
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Extension Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Pour créer un module, vous devez disposer du .NET, pour générer le dossier de projet, de Docker, pour générer l’image du module, et d’un registre de conteneurs, pour stocker cette image :
* [SDK .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker Community Edition](https://docs.docker.com/install/) sur votre ordinateur de développement 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Vous pouvez utiliser un registre Docker local pour le prototype et à des fins de test, au lieu d’un registre cloud. 

Afin de configurer l’environnement de développement local pour le débogage, l’exécution et le test de votre solution IoT Edge, vous avez besoin de [l’outil de développement Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/), et d’installer [Python (2.7/3.6) et Pip](https://www.python.org/). Ensuite, installez **iotedgehubdev** en exécutant la commande ci-dessous dans votre terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Pour tester votre module sur un appareil, vous avez besoin d’un hub IoT actif avec au moins un appareil IoT Edge. Pour utiliser votre ordinateur comme appareil IoT Edge, suivez les étapes décrites dans le guide de démarrage rapide pour [Windows](quickstart.md) ou [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-with-c-module"></a>Créer une solution avec un module C#

Effectuez les étapes suivantes pour créer un module IoT Edge basé sur .NET Core 2.0 à l’aide de Visual Studio Code et de l’extension Azure IoT Edge. Vous commencez par créer une solution, puis vous y générez le premier module. Chaque solution peut contenir plusieurs modules. 

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré**.
3. Sélectionnez **Affichage** > **Palette de commandes**. 
4. Dans la palette de commandes, entrez et exécutez la commande **Azure IoT Edge : Nouvelle solution IoT Edge**.

   ![Exécuter la nouvelle solution IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Accédez au dossier dans lequel vous souhaitez créer la solution. Choisissez **Sélectionner un dossier**. 
6. Entrez un nom pour votre solution. 
7. Sélectionnez **Module C#** comme modèle pour le premier module de la solution.
8. Entrez un nom pour votre module. Choisissez un nom qui est unique dans le registre de conteneurs. 
9. Indiquez le nom du référentiel d’images du module. Par défaut, VS Code nomme le module **localhost:5000**. Remplacez-le par vos propres informations de registre. Si vous utilisez un registre Docker local à des fins de test, **localhost** convient. Si vous utilisez Azure Container Registry, utilisez le serveur de connexion à partir des paramètres de votre registre. Le serveur de connexion ressemble à **\<nom du registre\>.azurecr.io**. Remplacez uniquement la partie localhost de la chaîne, ne supprimez pas le nom de votre module.

   ![Fourniture du référentiel d’images Docker](./media/how-to-develop-csharp-module/repository.png)

À partir des informations que vous avez fournies, VS Code crée une solution IoT Edge, puis la charge dans une nouvelle fenêtre.

   ![Afficher la solution IoT Edge](./media/how-to-develop-csharp-module/view-solution.png)

La solution comprend quatre éléments : 
* Un dossier **.vscode**, qui contient les configurations de débogage.
* Un dossier **modules**, qui contient des sous-dossiers pour chaque module. À ce stade, vous n’en avez qu’un. Toutefois, vous pouvez en ajouter via la palette de commandes avec la commande **Azure IoT Edge : Ajouter un module IoT Edge**. 
* Un fichier **.env**, qui liste vos variables d’environnement. Si Azure Container Registry est votre registre, celui-ci comprend un nom d’utilisateur et un mot de passe Azure Container Registry. 

   >[!NOTE]
   >Le fichier d’environnement est créé uniquement si vous fournissez un référentiel d’images pour le module. Si vous avez accepté les valeurs localhost par défaut pour tester et déboguer localement, vous n’avez pas besoin de déclarer des variables d’environnement. 

* Un fichier **deployment.template.json**, qui répertorie votre nouveau module, ainsi qu’un exemple de module **tempSensor** qui simule les données que vous pouvez utiliser à des fins de test. Pour plus d’informations sur le fonctionnement des manifestes de déploiement, consultez [Découvrez comment utiliser des manifestes de déploiement pour déployer des modules et établir des itinéraires](module-composition.md). 

## <a name="develop-your-module"></a>Développer votre module

Le code du module C# par défaut qui est fourni avec la solution se trouve sous **modules** > ** [nom de votre module] ** > **Program.cs**. Le module et le fichier deployment.template.json sont définis de manière à vous permettre de générer la solution, de l’envoyer vers votre registre de conteneurs et de la déployer sur un appareil pour commencer les tests, sans avoir à utiliser de code. Le module est conçu pour récupérer les entrées d’une source (dans ce cas, le module tempSensor qui simule des données) et les acheminer vers IoT Hub. 

Lorsque vous êtes prêt à personnaliser le modèle C# avec votre propre code, utilisez les [SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) pour créer des modules répondant aux besoins des solutions IoT, tels que la sécurité, la gestion des appareils et la fiabilité. 

La prise en charge de C# dans VS Code est optimisée pour le développement .NET Core multiplateforme. Pour en savoir plus, consultez l’article décrivant [comment utiliser C# dans VS Code](https://code.visualstudio.com/docs/languages/csharp).

## <a name="launch-and-debug-module-code-without-container"></a>Lancer et déboguer le code du module sans conteneur
Le module C# IoT Edge est une application .Net Core. Il dépend du SDK d’appareil Azure IoT C#. Dans le code du module par défaut, vous initialisez un **ModuleClient** avec des paramètres d’environnement et un nom d’entrée, ce qui signifie que le module IoT Edge C# a besoin des paramètres d’environnement pour démarrer et s’exécuter, et vous devez également envoyer ou router les messages vers les canaux d’entrée. Votre module C# par défaut contient un seul canal d’entrée dont le nom est **input1**.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Configurer un simulateur IoT Edge pour une application à module unique

1. Pour configurer et démarrer le simulateur, dans la palette de commandes VS Code, tapez et sélectionnez **Azure IoT Edge: Démarrer le simulateur du hub IoT Edge pour un seul module**. Vous devez également spécifier le nom de l’entrée de votre application à module unique, tapez **input1** et appuyez sur Entrée. La commande déclenche l’interface CLI **iotedgehubdev** et démarre le simulateur IoT Edge ainsi qu’un conteneur de module d’utilitaire de tests. Vous pouvez voir les sorties ci-dessous dans le terminal intégré si le simulateur a été démarré en mode de module unique. Vous pouvez également voir une commande `curl` pour vous aider à envoyer un message. Vous le réutiliserez ultérieurement.

   ![Configurer un simulateur IoT Edge pour une application à module unique](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Vous pouvez passer à l’Explorateur Docker et voir l’état d’exécution du module.

   ![État du module du simulateur](media/how-to-develop-csharp-module/simulator-status.png)

   Le conteneur **edgeHubDev** est la partie principale du simulateur IoT Edge local. Il peut s’exécuter sur votre machine de développement sans le démon de sécurité IoT Edge, et fournir des paramètres d’environnement pour votre application de module native ou vos conteneurs de module. Le conteneur **d’entrée** a exposé des API REST pour aider les messages de pont à cibler le canal d’entrée du module.

2. Dans la palette de commandes VS Code, tapez et sélectionnez **Azure IoT Edge : Définir les informations d'identification du module sur les paramètres utilisateur** pour définir les paramètres d’environnement du module de `azure-iot-edge.EdgeHubConnectionString` et `azure-iot-edge.EdgeModuleCACertificateFile` dans les paramètres utilisateur. Ces paramètres d’environnement sont référencés dans **.vscode** > **launch.json** et les [paramètres utilisateur de VS Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="build-module-app-and-debug-in-launch-mode"></a>Générer une application de module et déboguer en mode de lancement

1. Dans le terminal intégré, ouvrez le dossier **CSharpModule**, exécutez la commande suivante pour générer une application .Net Core.

    ```cmd
    dotnet build
    ```

   > [!TIP]
   > Vous pouvez aussi utiliser [PostMan](https://www.getpostman.com/) ou d’autres outils d’API pour envoyer des messages à la place de `curl`.

2. Accédez à `program.cs`. Ajoutez un point d’arrêt dans ce fichier.

3. Accédez à la vue de débogage de VS Code. Sélectionnez la configuration de débogage **Débogage local de Nom_Module (.NET Core)**. 

4. Cliquez sur **Démarrer le débogage** ou appuyez sur **F5**. Vous démarrer la session de débogage.

5. Dans le terminal intégré de VS Code, exécutez la commande suivante pour envoyer un message **Hello World** à votre module. Il s’agit de la commande décrite dans les étapes précédentes de configuration du simulateur IoT Edge.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Si vous utilisez Windows, vérifiez que l’interpréteur de commandes du terminal intégré de VS Code est **Git Bash** ou **WSL Bash**. Vous ne pouvez pas exécuter la commande `curl` dans PowerShell ou à partir d’une invite de commandes. 
   
   > [!TIP]
   > Vous pouvez aussi utiliser [PostMan](https://www.getpostman.com/) ou d’autres outils d’API pour envoyer des messages à la place de `curl`.

6. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet de gauche. 

    ![Surveiller des variables](media/how-to-develop-csharp-module/single-module-variables.png)

7. Pour arrêter une session de débogage, cliquez sur le bouton Arrêter ou appuyez sur **MAJ + F5**. Dans la palette de commandes VS Code, tapez et sélectionnez **Azure IoT Edge : Arrêter le simulateur IoT Edge**.

## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Générer le conteneur de module pour le débogage et déboguer en mode d’attachement

Votre solution par défaut contient deux modules : un module de capteur de température simulé et un module de canal C#. Le capteur de température simulé envoie des messages au module de canal C#, puis les messages sont dirigés vers IoT Hub. Le dossier du module que vous avez créé contient plusieurs fichiers Docker pour différents types de conteneur. Utilisez l’un des fichiers se terminant par l’extension **.debug** pour générer votre module à des fins de test. Actuellement, les modules C# prennent en charge le débogage seulement dans les conteneurs Linux amd64 en mode d’attachement. 

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Configurer un simulateur IoT Edge pour une solution IoT Edge

Dans votre machine de développement, vous pouvez démarrer le simulateur IoT Edge au lieu d’installer le démon de sécurité IoT Edge pour exécuter votre solution IoT Edge. 

1. Dans l’Explorateur d’appareils à gauche, cliquez sur l’ID de votre appareil IoT Edge, sélectionnez **Configurer le simulateur IoT Edge** pour démarrer le simulateur avec la chaîne de connexion de l’appareil.

2. Vous pouvez voir que le simulateur IoT Edge a été correctement configuré dans le terminal intégré.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Générer et exécuter le conteneur pour le débogage et déboguer en mode d’attachement

1. Dans VS Code, accédez au fichier `deployment.template.json`. Mettez à jour l’URL de votre image de module C# en ajoutant **.debug** à la fin.

   ![Ajouter **.debug** au nom de votre image](./media/how-to-develop-csharp-module/image-debug.png)

2. Accédez à `program.cs`. Ajoutez un point d’arrêt dans ce fichier.
3. Dans l’Explorateur de fichiers de VS Code, sélectionnez le fichier `deployment.template.json` de votre solution, dans le menu contextuel, cliquez sur **Générer et exécuter la solution IoT Edge dans le simulateur**. Vous pouvez surveiller tous les journaux du conteneur de module dans la même fenêtre. Vous pouvez aussi accéder à l’Explorateur Docker pour surveiller l’état du conteneur.

   ![Surveiller des variables](media/how-to-develop-csharp-module/view-log.png)

5. Accédez à la fenêtre de débogage de Visual Studio Code. Sélectionnez le fichier de configuration de débogage de votre module. Le nom de l’option de débogage doit ressembler à **Débogage distant de nom_module (.NET Core)**.

   ![Sélectionner une configuration](media/how-to-develop-csharp-module/debug-config.png)

6. Sélectionnez **Démarrer le débogage** ou **F5**. Sélectionnez le processus à attacher.

7. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet de gauche.

8. Pour arrêter une session de débogage, cliquez sur le bouton Arrêter ou appuyez sur **MAJ + F5**. Dans la palette de commandes VS Code, tapez et sélectionnez **Azure IoT Edge : Arrêter le simulateur IoT Edge**.

> [!NOTE]
> L’exemple précédent montre comment déboguer des modules .NET Core IoT Edge dans des conteneurs. Il repose sur la version de débogage de `Dockerfile.debug`, qui inclut le débogueur en ligne de commande .NET Core (VSDBG) dans votre image conteneur lors de sa création. Une fois le débogage de vos modules C# terminé, nous vous recommandons d’utiliser ou de personnaliser directement `Dockerfile` sans VSDBG pour les modules IoT Edge prêts pour la production.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre module généré, découvrez comment [déployer des modules Azure IoT Edge à partir de Visual Studio Code](how-to-deploy-modules-vscode.md).

Pour développer des modules pour vos appareils IoT Edge, consultez [Comprendre et utiliser les kits Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
