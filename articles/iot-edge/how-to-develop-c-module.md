---
title: Déboguer les modules C pour Azure IoT Edge | Microsoft Docs
description: Utiliser Visual Studio Code pour développer, générer et déboguer un module C pour Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/13/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3ec7f6043c1d2e8e8f090ffc60822768ab9bc9d9
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983999"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Utiliser Visual Studio Code pour développer et déboguer des modules C pour Azure IoT Edge

Vous pouvez transformer votre logique métier en modules pour Azure IoT Edge. Cet article vous montre comment utiliser Visual Studio Code (VS Code) comme outil principal pour développer et déboguer des modules C.

## <a name="prerequisites"></a>Prérequis
Cet article part du principe que vous utilisez un ordinateur ou une machine virtuelle Windows ou Linux comme machine de développement. Et que vous simulez votre appareil IoT Edge sur votre machine de développement avec le démon de sécurité IoT Edge.

> [!NOTE]
> Ce tutoriel de débogage montre comment attacher un processus dans un conteneur de module et comment le déboguer avec VS Code. Vous pouvez uniquement déboguer des modules C dans des conteneurs Linux amd64. Si vous n’êtes pas familiarisé avec les fonctionnalités de débogage de Visual Studio Code, découvrez-en plus sur le [débogage](https://code.visualstudio.com/Docs/editor/debugging).

Étant donné que cet article utilise Visual Studio Code comme outil de développement principal, installez VS Code. Ensuite, ajoutez les extensions nécessaires :
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extension C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) pour Visual Studio Code.
* [Extension Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Pour créer un module, vous avez besoin de Docker pour générer l’image du module, et d’un registre de conteneurs pour stocker cette image :
* [Docker Community Edition](https://docs.docker.com/install/) sur votre ordinateur de développement 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Vous pouvez utiliser un registre Docker local pour le prototype et à des fins de test, au lieu d’un registre cloud. 

Pour tester votre module sur un appareil, vous avez besoin d’un hub IoT actif avec au moins un appareil IoT Edge. Pour utiliser votre ordinateur comme appareil IoT Edge, suivez les étapes décrites dans le guide de démarrage rapide pour [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Créer un modèle de solution

Effectuez les étapes suivantes pour créer un module IoT Edge basé sur le kit Azure IoT C SDK à l’aide de Visual Studio Code et de l’extension Azure IoT Edge. Vous commencez par créer une solution, puis vous y générez le premier module. Chaque solution peut contenir plusieurs modules. 

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré**.

2. Sélectionnez **Affichage** > **Palette de commandes**. 

3. Dans la palette de commandes, entrez et exécutez la commande **Azure IoT Edge : Nouvelle solution IoT Edge**.

   ![Exécuter la nouvelle solution IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

4. Accédez au dossier dans lequel vous souhaitez créer la solution. Choisissez **Sélectionner un dossier**. 

5. Entrez un nom pour votre solution. 

6. Sélectionnez **Module C** comme modèle pour le premier module de la solution.

7. Entrez un nom pour votre module. Choisissez un nom qui est unique dans le registre de conteneurs. 

8. Indiquez le nom du référentiel d’images du module. Par défaut, VS Code nomme le module **localhost:5000**. Remplacez-le par vos propres informations de registre. Si vous utilisez un registre Docker local à des fins de test, **localhost** convient. Si vous utilisez Azure Container Registry, utilisez le serveur de connexion à partir des paramètres de votre registre. Le serveur de connexion ressemble à **\<nom du registre\>.azurecr.io**. Remplacez uniquement la partie localhost de la chaîne, ne supprimez pas le nom de votre module. 

   ![Fourniture du référentiel d’images Docker](./media/how-to-develop-c-module/repository.png)

À partir des informations que vous avez fournies, VS Code crée une solution IoT Edge, puis la charge dans une nouvelle fenêtre.

   ![Afficher la solution IoT Edge](./media/how-to-develop-c-module/view-solution.png)

La solution comprend quatre éléments : 
* Un dossier **.vscode**, qui contient les configurations de débogage.
* Un dossier **modules**, qui contient des sous-dossiers pour chaque module. À ce stade, vous n’en avez qu’un. Toutefois, vous pouvez en ajouter via la palette de commandes avec la commande **Azure IoT Edge : Ajouter un module IoT Edge**. 
* Un fichier **.env**, qui liste vos variables d’environnement. Si Azure Container Registry est votre registre, celui-ci comprend un nom d’utilisateur et un mot de passe Azure Container Registry. 

   > [!NOTE]
   > Le fichier d’environnement est créé uniquement si vous fournissez un référentiel d’images pour le module. Si vous avez accepté les valeurs localhost par défaut pour tester et déboguer localement, vous n’avez pas besoin de déclarer des variables d’environnement. 

* Un fichier **deployment.template.json**, qui répertorie votre nouveau module, ainsi qu’un exemple de module **tempSensor** qui simule les données que vous pouvez utiliser à des fins de test. Pour plus d’informations sur le fonctionnement des manifestes de déploiement, consultez [Découvrez comment utiliser des manifestes de déploiement pour déployer des modules et établir des itinéraires](module-composition.md). 

## <a name="develop-your-module"></a>Développer votre module

Le code du module C par défaut qui est fourni avec la solution se trouve sous **modules** > [nom de votre module] > **main.c**. Le module et le fichier deployment.template.json sont définis de manière à vous permettre de générer la solution, de l’envoyer vers votre registre de conteneurs et de la déployer sur un appareil pour commencer les tests, sans avoir à utiliser de code. Le module est conçu pour récupérer les entrées d’une source (dans ce cas, le module tempSensor qui simule des données) et les acheminer vers IoT Hub. 

Lorsque vous êtes prêt à personnaliser le modèle C avec votre propre code, utilisez les kits [SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) pour créer des modules répondant aux besoins des solutions IoT, tels que la sécurité, la gestion des appareils et la fiabilité. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Générer et déployer votre module à des fins de débogage

Chaque dossier du module contient plusieurs fichiers Docker pour différents types de conteneurs. Utilisez l’un des fichiers se terminant par l’extension **.debug** pour générer votre module à des fins de test. Les modules C prennent en charge le débogage seulement dans les conteneurs Linux amd64.

1. Dans VS Code, accédez au fichier `deployment.template.json`. Mettez à jour l’URL de votre image de module en ajoutant **.debug** à la fin.

    ![Ajouter **.debug** au nom de votre image](./media/how-to-develop-c-module/image-debug.png)

2. Remplacez le module C createOptions dans **deployment.template.json** par le contenu ci-dessous, puis enregistrez ce fichier : 
    
    ```json
    "createOptions": "{\"HostConfig\": {\"Privileged\": true}}"
    ```

2. Dans la palette de commandes VS Code, tapez et exécutez la commande **Azure IoT Edge : Générer et envoyer par push la solution IoT Edge**.
3. Sélectionnez le fichier `deployment.template.json` pour votre solution à partir de la palette de commandes. 
4. Dans Azure IoT Hub Device Explorer, cliquez avec le bouton droit sur l’ID d’un appareil IoT Edge. Ensuite, sélectionnez **Créer un déploiement pour un seul appareil**. 
5. Ouvrez le dossier **config** de votre solution. Ensuite, sélectionnez le fichier `deployment.json`. Choisissez **Select Edge Deployment Manifest** (Sélectionner un manifeste de déploiement Edge). 

Vous pouvez alors constater que le déploiement a bien été créé avec un ID de déploiement dans le terminal intégré à VS Code.

Vérifiez l’état de votre conteneur dans l’explorateur du Docker VS Code ou en exécutant la commande `docker ps` dans le terminal.

## <a name="start-debugging-c-module-in-vs-code"></a>Commencer le débogage du module C dans VS Code
VS Code conserve les informations de configuration du débogage dans un fichier `launch.json` situé dans un dossier `.vscode` de votre espace de travail. Ce fichier `launch.json` a été généré lorsque vous avez créé une nouvelle solution IoT Edge. Il se met à jour chaque fois que vous ajoutez un nouveau module qui prend en charge le débogage. 

1. Accédez à la fenêtre de débogage de Visual Studio Code. Sélectionnez le fichier de configuration de débogage de votre module. Le nom de l’option de débogage doit ressembler à **Débogage distant de nom_module (C)**.

   ![Sélectionner la configuration du débogage](./media/how-to-develop-c-module/debug-config.png)

2. Accédez à `main.c`. Ajoutez un point d’arrêt dans ce fichier.

3. Sélectionnez **Démarrer le débogage** ou **F5**. Sélectionnez le processus à attacher.

4. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet de gauche. 

L’exemple précédent montre comment déboguer des modules C IoT Edge sur des conteneurs. Il a ajouté des ports exposés dans votre conteneur de module createOptions. Une fois le débogage de vos modules C terminé, nous vous recommandons de supprimer ces ports exposés pour les modules IoT Edge prêts pour la production.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre module généré, découvrez comment [déployer des modules Azure IoT Edge à partir de Visual Studio Code](how-to-deploy-modules-vscode.md).

Pour développer des modules pour vos appareils IoT Edge, consultez [Comprendre et utiliser les kits Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
