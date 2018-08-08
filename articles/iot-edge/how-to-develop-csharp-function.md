---
title: Déboguer des modules de fonction pour Azure IoT Edge | Microsoft Docs
description: Utiliser Visual Studio Code pour déboguer des fonctions Azure C# avec Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bf2d1af66cc3ecc35dafe3bcd43bf10399d71641
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346713"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Utiliser Visual Studio Code pour développer et déboguer des fonctions Azure pour Azure IoT Edge

Cet article vous montre comment utiliser [Visual Studio Code (VS Code)](https://code.visualstudio.com/) pour déboguer vos fonctions Azure dans Azure IoT Edge.

## <a name="prerequisites"></a>Prérequis
Cet article part du principe que vous utilisez un ordinateur ou une machine virtuelle Windows ou Linux comme machine de développement. Vous pouvez utiliser un autre appareil physique comme appareil IoT Edge. Sinon, vous pouvez aussi simuler votre appareil IoT Edge sur votre machine de développement.

> [!NOTE]
> Ce tutoriel de débogage montre comment attacher un processus dans un conteneur de module et comment le déboguer avec VS Code. Dans les conteneurs Linux amd64, vous pouvez uniquement déboguer des fonctions C#. Si vous n’êtes pas familiarisé avec les fonctionnalités de débogage de Visual Studio Code, découvrez-en plus sur le [débogage](https://code.visualstudio.com/Docs/editor/debugging). 

Cet article utilise Visual Studio Code comme outil de développement principal. Installez VS Code. Ensuite, ajoutez les extensions nécessaires : 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Extension Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Pour créer un module, vous devez disposer du .NET, pour générer le dossier de projet, de Docker, pour générer l’image du module, et d’un registre de conteneurs, pour stocker cette image :
* [SDK .NET Core 2.1](https://www.microsoft.com/net/download)
* [Docker Community Edition](https://docs.docker.com/install/) sur votre ordinateur de développement 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Vous pouvez utiliser un registre Docker local pour le prototype et à des fins de test, au lieu d’un registre cloud. 

Pour tester votre module sur un appareil, vous avez besoin d’un hub IoT actif avec au moins un appareil IoT Edge. Pour utiliser votre ordinateur comme appareil IoT Edge, suivez les étapes décrites dans le guide de démarrage rapide pour [Windows](quickstart.md) ou [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Créer un modèle de solution

Suivez ces étapes pour créer une solution IoT Edge comportant un module de fonction C#. Chaque solution peut comprendre plusieurs modules.

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré**.
3. Sélectionnez **Affichage** > **Palette de commandes**.
4. Dans la palette de commandes, entrez et exécutez la commande **Azure IoT Edge : Nouvelle solution IoT Edge**. 

   ![Exécuter la nouvelle solution IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Accédez au dossier dans lequel vous souhaitez créer la solution. Choisissez **Sélectionner un dossier**. 
6. Entrez un nom pour votre solution. 
7. Choisissez **Azure Functions - C#** comme modèle pour le premier module dans la solution.
8. Entrez un nom pour votre module. Choisissez un nom qui est unique dans le registre de conteneurs. 
9. Indiquez le référentiel d’image pour le module. Par défaut, VS Code nomme le module **localhost:5000**. Remplacez-le par vos propres informations de registre. Si vous utilisez un registre Docker local à des fins de test, **localhost** convient. Si vous utilisez Azure Container Registry, utilisez le serveur de connexion à partir des paramètres de votre registre. Le serveur de connexion ressemble à **\<nom du registre\>.azurecr.io**.

À partir des informations que vous avez fournies, VS Code crée une solution IoT Edge avec un projet Azure Functions, puis la charge dans une nouvelle fenêtre.

La solution comprend quatre éléments : 

* Un dossier **.vscode**, qui contient les configurations de débogage.
* Un dossier **modules**, qui contient des sous-dossiers pour chaque module. À ce stade, vous n’en avez qu’un. Toutefois, vous pouvez en ajouter via la palette de commandes avec la commande **Azure IoT Edge : Ajouter un module IoT Edge**.
* Un fichier **.env**, qui liste vos variables d’environnement. Si Azure Container Registry est votre registre, celui-ci comprend un nom d’utilisateur et un mot de passe Azure Container Registry. 

   >[!NOTE]
   >Le fichier d’environnement est créé uniquement si vous fournissez un référentiel d’images pour le module. Si vous avez accepté les valeurs localhost par défaut pour tester et déboguer localement, vous n’avez pas besoin de déclarer des variables d’environnement. 

* Un fichier **deployment.template.json**, qui répertorie votre nouveau module, ainsi qu’un exemple de module **tempSensor** qui simule les données que vous pouvez utiliser à des fins de test. Pour plus d’informations sur le fonctionnement des manifestes de déploiement, consultez [Découvrez comment utiliser des manifestes de déploiement pour déployer des modules et établir des itinéraires](module-composition.md).

## <a name="develop-your-module"></a>Développer votre module

Le code Azure Function par défaut qui est fourni avec la solution se trouve sous **modules** > **\<nom de votre module\>** > **EdgeHubTrigger-Csharp** > **run.csx**. Le module et le fichier deployment.template.json sont définis de manière à vous permettre de générer la solution, de l’envoyer vers votre registre de conteneurs et de la déployer sur un appareil pour commencer les tests, sans avoir à utiliser de code. Le module est conçu pour récupérer les entrées d’une source (dans ce cas, le module tempSensor qui simule des données) et les acheminer vers IoT Hub. 

Lorsque vous êtes prêt à personnaliser le modèle Azure Functions avec votre propre code, utilisez les [SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) pour créer des modules répondant aux besoins des solutions IoT, tels que la sécurité, la gestion des appareils et la fiabilité. 

## <a name="build-your-module-for-debugging"></a>Générer votre module pour le débogage
1. Pour démarrer le débogage, utilisez **Dockerfile.amd64.debug** afin de regénérer votre image Docker et de redéployer votre solution IoT Edge. Dans l’explorateur VS Code, accédez au fichier `deployment.template.json`. Mettez à jour l’URL de votre image de fonction en ajoutant `.debug` à la fin.

    ![Générer l’image de débogage](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Regénérez votre solution. Dans la palette de commandes VS Code, tapez et exécutez la commande **Azure IoT Edge : Générer la solution IoT Edge**.
3. Dans l’explorateur des appareils Azure IoT Hub, cliquez avec le bouton droit sur un ID d’appareil IoT Edge, puis sélectionnez **Créer un déploiement pour un appareil Edge**. Sélectionnez le fichier `deployment.json` dans le dossier `config`. Vous pouvez alors constater que le déploiement a bien été créé avec un ID de déploiement dans le terminal intégré à VS Code.

Vérifiez l’état de votre conteneur dans l’explorateur du Docker VS Code ou en exécutant la commande `docker images` dans le terminal.

## <a name="start-debugging-c-functions-in-vs-code"></a>Démarrer le débogage des fonctions C# dans VS Code
1. VS Code conserve les informations de configuration du débogage dans un fichier `launch.json` situé dans un dossier `.vscode` de votre espace de travail. Ce fichier `launch.json` a été généré lorsque vous avez créé une nouvelle solution IoT Edge. Il se met à jour chaque fois que vous ajoutez un nouveau module qui prend en charge le débogage. Accédez à la fenêtre de débogage. Sélectionnez le fichier de configuration du débogage correspondant. Le nom de l’option de débogage doit ressembler à **Débogage distant de nom_module (.NET Core)**.

   ![Sélectionner la configuration du débogage](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Accédez à `run.csx`. Ajoutez un point d’arrêt dans la fonction.
3. Sélectionnez **Démarrer le débogage** ou **F5**. Sélectionnez le processus à attacher.
4. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet de gauche. 


> [!NOTE]
> L’exemple précédent montre comment déboguer des fonctions .NET Core IoT Edge dans des conteneurs. Il repose sur la version de débogage de `Dockerfile.amd64.debug`, qui inclut le débogueur en ligne de commande .NET Core (VSDBG) dans votre image conteneur lors de sa création. Nous vous recommandons d’utiliser ou de personnaliser directement le `Dockerfile` sans VSDBG pour les fonctions IoT Edge prêtes pour la production, après avoir débogué les fonctions C#.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre module généré, découvrez comment [déployer des modules Azure IoT Edge à partir de Visual Studio Code](how-to-deploy-modules-vscode.md).

Pour développer des modules pour vos appareils IoT Edge, consultez [Comprendre et utiliser les kits Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
