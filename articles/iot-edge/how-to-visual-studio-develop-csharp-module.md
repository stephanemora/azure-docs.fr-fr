---
title: Développer et déboguer des modules C# pour Azure IoT Edge dans Visual Studio 2017 | Microsoft Docs
description: Utiliser Visual Studio 2017 pour développer et déboguer des modules C# pour Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/24/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: ab4dd1186715fde51fbf188ace902c8092d192d0
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647185"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Utiliser Visual Studio 2017 pour développer et déboguer des modules C# pour Azure IoT Edge (préversion)

Vous pouvez transformer votre logique métier en modules pour Azure IoT Edge. Cet article vous explique comment utiliser Visual Studio 2017 comme outil principal pour développer et déboguer des modules C#.

Azure IoT Edge Tools pour Visual Studio offre les avantages suivants :

- Créez, modifiez, générez, exécutez et déboguez des solutions et des modules Azure IoT Edge sur votre ordinateur de développement local.
- Déployez votre solution Azure IoT Edge sur l’appareil Azure IoT Edge via Azure IoT Hub.
- Codez vos modules Azure IoT en C# tout en bénéficiant de tous les avantages du développement Visual Studio.
- Gérez les appareils et les modules Azure IoT Edge avec une interface utilisateur. 

Cet article vous explique comment utiliser Azure IoT Edge Tools pour Visual Studio 2017 pour développer vos modules IoT Edge en C#. Vous y découvrez aussi comment déployer votre projet sur votre appareil Azure IoT Edge.

## <a name="prerequisites"></a>Prérequis
Cet article part du principe que vous utilisez un ordinateur ou une machine virtuelle Windows comme machine de développement. Vous pouvez utiliser un autre appareil physique comme appareil IoT Edge.

Comme cet article utilise Visual Studio 2017 comme outil de développement principal, installez Visual Studio. Veillez aussi à inclure la **charge de travail Développement Azure** dans votre installation de Visual Studio 2017. Vous pouvez [modifier Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) et ajouter la charge de travail Développement Azure.

Une fois que Visual Studio 2017 est prêt, vous devez aussi :

- Téléchargez et installez [l’extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) à partir de Visual Studio Marketplace pour créer un projet IoT Edge dans Visual Studio 2017.
- [Docker Community Edition](https://docs.docker.com/install/) sur votre machine de développement pour générer et exécuter vos images de module. Vous devez configurer correctement Docker CE pour qu’il s’exécute en mode conteneur Linux ou Windows.
- Afin de configurer l’environnement de développement local pour le débogage, l’exécution et le test de votre solution IoT Edge, vous avez besoin de [l’outil de développement Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Installez [Python (2.7/3.6) et Pip](https://www.python.org/). Ensuite, installez **iotedgehubdev** en exécutant la commande ci-dessous dans votre terminal. Vérifiez que votre version de l’outil de développement Azure IoT pour EdgeHub est supérieure à 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   - Vous pouvez utiliser un registre Docker local pour le prototype et à des fins de test, au lieu d’un registre cloud. 

- Pour tester votre module, vous avez besoin d’un hub IoT actif avec au moins un ID d’appareil IoT Edge créé. Si vous exécutez le démon de sécurité IoT Edge sur la machine de développement, il peut être nécessaire d’arrêter EdgeHub et EdgeAgent avant de commencer le développement dans Visual Studio. 

### <a name="check-your-tools-version"></a>Vérifier la version des outils

1. Dans le menu **Outils**, choisissez **Extensions et mises à jour**. Développez **Installé > Outils** : vous devez normalement trouver **Azure IoT Edge** et **Cloud Explorer**.

2. Notez la version installée. Vous pouvez comparer cette version avec la version la plus récente sur Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.azureiotedgetools))

3. Si votre version est antérieure, mettez à jour vos outils dans Visual Studio en suivant les instructions de la section suivante.

### <a name="update-your-tools"></a>Mettre à jour les outils

1. Dans la boîte de dialogue **Extensions et mises à jour**, développez **Mises à jour > Visual Studio Marketplace**, choisissez **Azure IoT Edge** ou **Cloud Explorer**, et sélectionnez **Mettre à jour**.

2. Une fois la mise à jour des outils téléchargée, fermez Visual Studio pour déclencher la mise à jour des outils à l’aide du programme d’installation VSIX.

3. Dans le programme d’installation, choisissez **OK** pour démarrer, puis Modifier pour mettre à jour les outils.

4. Une fois la mise à jour terminée, choisissez Fermer et redémarrez Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Créer un projet Azure IoT Edge

Le modèle de projet Azure IoT Edge dans Visual Studio crée un projet qui peut être déployé sur des appareils Azure IoT Edge dans Azure IoT Hub. Vous commencez par créer une solution Azure IoT Edge, puis vous y générez le premier module C#. Chaque solution IoT Edge peut contenir plusieurs modules. 

1. Dans Visual Studio, sélectionnez **Nouveau** > **Projet** dans le menu **Fichier**.

2. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Installé**, développez **Visual C# > Cloud**, sélectionnez **Azure IoT Edge**, tapez un **Nom** pour votre projet, puis cliquez sur **OK**. Le nom du projet par défaut est **AzureIoTEdgeApp1**. 

   ![Nouveau projet](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

3. Dans la fenêtre **Configuration du module IoT Edge**, sélectionnez le type **Module C#**, et spécifiez le nom de votre module et le dépôt pour l’image du module.  Par défaut, Visual Studio nomme le module **localhost:5000**. Remplacez-le par vos propres informations de registre. Si vous utilisez un registre Docker local à des fins de test, localhost convient. Si vous utilisez Azure Container Registry, utilisez le serveur de connexion à partir des paramètres de votre registre. Le serveur de connexion se présente comme ceci : **<registry name>.azurecr.io**. Remplacez uniquement la partie localhost de la chaîne, ne supprimez pas le nom de votre module. Le nom par défaut du module est **IoTEdgeModule1**

4. Cliquez sur **OK** pour créer le projet Azure IoT Edge avec un module C#.

Vous disposez maintenant d’un projet **AzureIoTEdgeApp1** projet et d’un projet **IoTEdgeModule1** dans notre solution. Le projet **AzureIoTEdgeApp1** a un fichier **deployment.template.json**. Ce fichier définit les modules que vous voulez générer et déployer pour votre solution IoT Edge, et définit les routes entre les modules. La solution par défaut a un module **tempSensor** et un module **IoTEdgeModule1**. Le module **tempSensor** génère des données simulées à destination du module **IoTEdgeModule1**, tandis que le code par défaut dans le module **IoTEdgeModule1** est un module de messages de canal, qui dirige directement les messages reçus vers IoT Hub.

Le projet **IoTEdgeModule1** est une application console .NET Core 2.1. Il contient les fichiers **Dockerfile** dont vous avez besoin pour votre appareil IoT Edge qui s’exécute avec le conteneur Windows ou le conteneur Linux. Le fichier **module.json** fichier décrit les métadonnées d’un module. Le fichier **program.cs** est le code du module, qui a comme dépendance le SDK Azure IoT Device.

## <a name="develop-your-module"></a>Développer votre module

Le code du module C# par défaut qui est fourni avec la solution se trouve dans **IoTEdgeModule1** > **Program.cs**. Le module et le fichier deployment.template.json sont définis de manière à vous permettre de générer la solution, de l’envoyer vers votre registre de conteneurs et de la déployer sur un appareil pour commencer les tests, sans avoir à utiliser de code. Le module est conçu pour récupérer les entrées d’une source (dans ce cas, le module tempSensor qui simule des données) et les acheminer vers IoT Hub. 

Lorsque vous êtes prêt à personnaliser le modèle C# avec votre propre code, utilisez les [SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) pour créer des modules répondant aux besoins des solutions IoT, tels que la sécurité, la gestion des appareils et la fiabilité. 

## <a name="initialize-iotegehubdev-with-iot-edge-device-connection-string"></a>Initialiser **iotegehubdev** avec la chaîne de connexion d’un appareil IoT Edge

1. Vous devez obtenir la chaîne de connexion d’un appareil IoT Edge. Vous devrez peut-être copier la valeur de « Chaîne de connexion principale » à partir de Cloud Explorer dans Visual Studio 2017 comme suit. Ne copiez pas la chaîne de connexion de l’appareil non-Edge, l’icône d’appareil IoT Edge est différente de celle de l’appareil non-Edge.

   ![Copier la chaîne de connexion d’un appareil IoT Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

2. Vous devez cliquer avec le bouton droit sur le projet **AzureIoTEdgeApp1** pour ouvrir le menu contextuel, puis cliquer sur **Définir la chaîne de connexion d’un appareil Edge**. La fenêtre du programme d’installation Azure IoT Edge s’affiche.

   ![Ouvrir la fenêtre Définir la chaîne de connexion Edge](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

3. Dans la fenêtre du programme d’installation, entrez la chaîne de connexion que vous avez obtenue à la première étape et cliquez sur le bouton **OK**.

>[!NOTE]
>Vous n’avez à suivre cette procédure qu’une seule fois. En effet, exécutez cette étape juste une fois sur une machine et toutes les solutions Azure IoT Edge suivantes l’obtiendront gratuitement. Bien sûr, vous pouvez réexécuter cette étape si la chaîne de connexion n’est pas valide ou si vous devez changer de chaîne de connexion.

## <a name="build-and-debug-single-c-module"></a>Générer et déboguer un seul module C#

En règle générale, nous voulons tester/déboguer chaque module avant de l’exécuter au sein d’une solution entière avec plusieurs modules.

1. Sélectionnez **IoTEdgeModule1** comme projet de démarrage dans le menu contextuel.

   ![Définir le projet de démarrage](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

2. Appuyez sur **F5** ou cliquez sur le bouton qui se trouve en dessous pour exécuter le module. La première fois, 10 à 20 secondes peuvent être nécessaires.

   ![Exécuter le module](./media/how-to-visual-studio-develop-csharp-module/run-module.png)


3. Vous devez voir une application console .NET Core démarrée si le module a été initialisé avec succès.

   ![Module en cours d’exécution](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

4. Vous pouvez maintenant définir un point d’arrêt dans **PipeMessage** dans **Program.cs**, puis envoyer un message en exécutant la commande suivante dans votre **Bash Git** ou votre **Bash WSL** (ne l’exécutez pas dans CMD ni dans Powershell) (vous pouvez également trouver cette commande dans la fenêtre Sortie de Visual Studio) :

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Déboguer un seul module](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Le point d’arrêt doit être déclenché. Vous pouvez regarder les variables dans la fenêtre Variables locales de Visual Studio.

   > [!TIP]
   > Vous pouvez aussi utiliser [PostMan](https://www.getpostman.com/) ou d’autres outils d’API pour envoyer des messages à la place de `curl`.

5. Appuyez sur **Ctrl+F5** ou cliquez sur le bouton Arrêter pour arrêter le débogage.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Générer et déboguer une solution IoT Edge avec plusieurs modules

Une fois que nous avons terminé de développer un module, nous voulons ensuite exécuter et déboguer la solution entière avec plusieurs modules.

1. Ajoutez votre second module C# dans la solution. Cliquez avec le bouton droit sur **AzureIoTEdgeApp1** et sélectionnez **Ajouter** -> **Nouveau module IoT Edge**. Le nom par défaut du second module est **IoTEdgeModule2** et il s’agit toujours d’un module de canal.

2. Accédez à **deployment.template.json**. Vous voyez que **IoTEdgeModule2** a été ajouté dans la section **modules**. Remplacez la section **routes** par ce qui suit. Si vous avez personnalisé les noms de vos modules, veillez à mettre à jour les noms dans le code suivant après le remplacement.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

3. Définissez le projet **AzureIoTEdgeApp1** comme projet de démarrage dans le menu contextuel.

4. Définissez des points d’arrêt et appuyez sur F5. Vous pouvez exécuter et déboguer plusieurs modules simultanément. Vous voyez normalement plusieurs fenêtres d’applications console .NET Core, chaque fenêtre indiquant votre module C#. 

   ![Déboguer plusieurs modules](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

5. Appuyez sur **Ctrl+F5** ou cliquez sur le bouton Arrêter pour arrêter le débogage.

## <a name="build-and-push-images"></a>Générer et envoyer (push) des images

1. Vérifiez que **AzureIoTEdgeApp1** est votre projet de démarrage. Sélectionnez une configuration **Debug** ou **Release** pour la génération de vos images.

    > [!NOTE]
    > Quand vous choisissez **Debug**, Visual Studio utilise `Dockerfile.debug` pour générer des images Docker. Ceci inclut le débogueur en ligne de commande .NET Core (VSDBG) dans votre image conteneur lors de sa génération. Nous vous recommandons d’utiliser la configuration **Release** qui utilise `Dockerfile` sans VSDBG pour les modules IoT Edge prêts pour la production.

2. Si vous utilisez un registre privé comme Azure Container Registry, exécutez Docker login avec la commande suivante dans votre terminal. Si vous utilisez le registre local, vous pouvez [exécuter un registre local](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server> 
    ```

3. Si vous utilisez un registre privé comme Azure Container Registry, vous devez placer le nom d’utilisateur et le mot de passe du registre dans `deployment.template.json` sous les paramètres d’exécution avec le contenu suivant. N’oubliez pas de remplacer l’espace réservé avec votre nom d’utilisateur et votre mot de passe d’administrateur.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

4. Cliquez avec le bouton droit sur **AzureIoTEdgeApp1** et choisissez l’élément de menu contextuel **Générer et envoyer (push) la solution Edge** : ceci crée et envoie l’image Docker pour chaque module.

   ![Générer et envoyer (push) des images](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)


## <a name="deploy-the-solution"></a>Déployer la solution

Dans l’article de démarrage rapide que vous avez utilisé pour configurer votre appareil IoT Edge, vous avez déployé un module à l’aide du Portail Azure. Vous pouvez également déployer des modules avec Cloud Explorer pour Visual Studio. Vous disposez déjà d’un manifeste de déploiement préparé pour votre scénario : le fichier `deployment.json`. Il vous suffit alors de sélectionner l’appareil qui recevra le déploiement.

1. Ouvrez **Cloud Explorer** en cliquant sur **Afficher** > **Cloud Explorer**. Vérifiez que vous vous êtes connecté à Visual Studio 2017 avec votre compte.

2. Dans **Cloud Explorer**, développez votre abonnement, recherchez votre hub Azure IoT et l’appareil Azure IoT Edge que vous voulez déployer.

3. Cliquez avec le bouton droit sur l’appareil IoT Edge pour créer un déploiement pour celui-ci : vous devez choisir le fichier manifeste de déploiement sous `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

>>[!NOTE]
>>Vous ne devez pas sélectionner `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`.

4. Cliquez sur le bouton Actualiser. Vous voyez normalement les nouveaux modules en cours d’exécution, ainsi que le module **TempSensor**, et **$edgeAgent** et **$edgeHub**.

## <a name="view-generated-data"></a>Afficher les données générées

1. Pour surveiller le message D2C pour un appareil spécifique, cliquez sur l’appareil dans la liste, puis cliquez sur **Démarrer la surveillance des messages D2C** dans la fenêtre **Action**.

2. Pour arrêter la surveillance des données, cliquez sur l’appareil dans la liste, puis sélectionnez **Arrêter la surveillance des messages D2C** dans la fenêtre **Action**.

## <a name="next-steps"></a>Étapes suivantes

Pour développer des modules pour vos appareils IoT Edge, consultez [Comprendre et utiliser les kits Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
