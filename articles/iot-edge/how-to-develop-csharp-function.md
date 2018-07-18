---
title: Modules de fonctions de débogage pour Azure IoT Edge | Microsoft Docs
description: Utiliser Visual Studio Code pour déboguer des fonctions Azure Functions C# avec Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ee5d368bde0f176442a01ba266791f8a9c2cbc3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052825"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Utiliser Visual Studio Code pour développer et déboguer des fonctions Azure Functions pour Azure IoT Edge

Cet article fournit des instructions détaillées pour l’utilisation de [Visual Studio (VS) Code](https://code.visualstudio.com/) pour déboguer vos fonctions Azure Functions sur IoT Edge.

## <a name="prerequisites"></a>Prérequis
Cet article part du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows ou Linux comme machine de développement. Votre appareil IoT Edge peut être un autre appareil physique, ou vous pouvez simuler votre appareil IoT Edge sur votre machine de développement.

> [!NOTE]
> Ce tutoriel de débogage décrit comment attacher un processus dans un conteneur de module et le déboguer avec VS Code. Vous pouvez uniquement déboguer des modules C# dans les conteneurs linux-amd64. Si vous n’êtes pas familiarisé avec les fonctionnalités de débogage de Visual Studio Code, découvrez-en plus sur le [débogage](https://code.visualstudio.com/Docs/editor/debugging). 

Cet article utilise Visual Studio Code comme outil de développement principal. Installez VS Code, puis ajoutez les extensions nécessaires : 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Extension Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Pour créer un module, vous devez disposer du .NET, pour générer le dossier de projet, de Docker, pour générer l’image du module, et d’un registre de conteneurs, pour stocker cette image :
* [SDK .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) sur votre machine de développement. 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Vous pouvez utiliser un registre Docker local pour le prototype et à des fins de test, au lieu d’un registre de cloud. 

Pour tester votre module sur un appareil, vous avez besoin d’un hub IoT actif avec au moins un appareil IoT Edge. Si vous souhaitez utiliser votre ordinateur comme appareil IoT Edge, vous pouvez suivre les étapes des tutoriels pour [Windows](quickstart.md) ou [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Créer un modèle de solution

Les étapes suivantes montrent comment créer une solution IoT Edge qui contient un module de fonction C#. Chaque solution peut contenir plusieurs modules.

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré**.
3. Sélectionnez **Affichage** > **Palette de commandes**.
4. Dans la palette de commandes, tapez et exécutez la commande **Azure IoT Edge : nouvelle solution IoT Edge**. 

   ![Exécuter la nouvelle solution IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Accédez au dossier où vous souhaitez créer la solution, puis cliquez sur **Sélectionner le dossier**. 
6. Entrez un nom pour votre solution. 
7. Choisissez **Azure Functions - C#** comme modèle pour le premier module dans la solution.
8. Entrez un nom pour votre module. Choisissez un nom qui est unique dans le registre de conteneurs. 
9. Indiquez le référentiel d’image pour le module. VS Code remplissant automatiquement le nom du module, il vous suffit de remplacer **localhost:5000** par vos propres informations de registre. Si vous utilisez un registre Docker local à des fins de test, localhost convient. Si vous utilisez Azure Container Registry, utilisez le serveur de connexion à partir des paramètres de votre registre. Le serveur de connexion ressemble à **\<nom du registre\>.azurecr.io**.

À partir des informations que vous avez fournies, VS Code crée une solution IoT Edge avec un projet Function, puis la charge dans une nouvelle fenêtre.

La solution comprend trois éléments : 

* Un dossier **.vscode**, qui contient les configurations de débogage.
* Un dossier **modules**, qui contient des sous-dossiers pour chaque module. Pour l’instant, vous n’en avez qu’un, mais vous pouvez en ajouter par le biais de la palette de commandes avec la commande **Azure IoT Edge : ajouter un module IoT Edge**.
* Un fichier **.env**, qui répertorie vos variables d’environnement. Si votre registre est un registre ACR, il contient d’ores et déjà un nom d’utilisateur et un mot de passe ACR. 
* Un fichier **deployment.template.json**, qui répertorie votre nouveau module, ainsi qu’un exemple de module **tempSensor** qui simule les données que vous pouvez utiliser à des fins de test. Pour plus d’informations sur le fonctionnement des manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Générer votre module de fonction IoT Edge pour le débogage
1. Pour démarrer le débogage, utilisez **Dockerfile.amd64.debug** afin de régénérer votre image docker et de redéployer votre solution Edge. Dans l’explorateur VS Code, accédez au fichier `deployment.template.json`. Mettez à jour l’URL de votre image de fonction en ajoutant `.debug` à la fin.

    ![Générer l’image de débogage](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Regénérez votre solution. Dans la palette de commandes de VS Code, tapez et exécutez la commande **Azure IoT Edge : générer la solution IoT Edge**.
3. Dans l’explorateur des appareils Azure IoT Hub, cliquez avec le bouton droit sur un ID d’appareil IoT Edge, puis sélectionnez **Créer un déploiement pour un appareil Edge**. Sélectionnez le fichier `deployment.json` sous le dossier `config`. Vous pouvez alors constater la bonne création du déploiement avec un ID de déploiement dans le terminal intégré de VS Code.

Vous pouvez vérifier l’état de votre conteneur dans l’explorateur du Docker VS Code ou en exécutant la commande `docker images` dans le terminal.

## <a name="start-debugging-c-function-in-vs-code"></a>Démarrer le débogage de la fonction C# dans VS Code
1. VS Code conserve les informations de configuration du débogage dans un fichier `launch.json` situé dans un dossier `.vscode` de votre espace de travail. Ce fichier `launch.json` a été généré lorsque vous avez créé une nouvelle solution IoT Edge. Il se met à jour chaque fois que vous ajoutez un nouveau module qui prend en charge le débogage. Accédez à l’affichage du débogage et sélectionnez le fichier de configuration du débogage correspondant. Le nom de l’option de débogage doit ressembler à **Débogage distant de nom_module (.NET Core)**.![Sélectionner la configuration du débogage](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Accédez à `run.csx`. Ajoutez un point d’arrêt dans la fonction.
3. Cliquez sur le bouton **Démarrer le débogage** ou appuyez sur **F5**, puis sélectionnez le processus à attacher.
4. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet situé à gauche. 


> [!NOTE]
> L’exemple ci-dessus montre comment déboguer la fonction .Net Core IoT Edge sur les conteneurs. Il repose sur la version de débogage de `Dockerfile.amd64.debug`, qui inclut VSDBG (le débogueur de lignes de commande .NET Core) dans votre image de conteneur lors de sa création. Nous vous recommandons d’utiliser ou de personnaliser directement le `Dockerfile` sans VSDBG pour la fonction IoT Edge prête à l’emploi une fois le débogage de votre fonction C# terminé.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre module généré, découvrez comment [déployer des modules Azure IoT Edge à partir de Visual Studio Code](how-to-deploy-modules-vscode.md).
