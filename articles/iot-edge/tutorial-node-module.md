---
title: Didacticiel d’Azure IoT Edge Node.js | Microsoft Docs
description: Ce didacticiel explique comment créer un module IoT Edge avec du code Node.js et le déployer sur un appareil Edge
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6922262856d6fba97349377d5d1b18b75638d88f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436809"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>Didacticiel : Développer et déployer un module IoT Edge Node.js sur votre appareil simulé

Vous pouvez utiliser des modules IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce tutoriel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur. Vous utilisez l’appareil simulé IoT Edge que vous avez créé dans les didacticiels Déployer Azure IoT Edge sur un appareil simulé dans [Windows][lnk-tutorial1-win] et [Linux][lnk-tutorial1-lin]. Ce tutoriel vous montre comment effectuer les opérations suivantes :    

> [!div class="checklist"]
> * Utiliser Visual Studio Code pour créer un module IoT Edge Node.js
> * Utiliser le Visual Studio Code et Docker pour créer une image docker et la publier dans votre registre 
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées


Le module IoT Edge que vous créez dans ce tutoriel filtre les données de température générées par votre appareil. Il envoie uniquement des messages en amont lorsque la température dépasse un seuil spécifié. Ce type d’analyse à la périphérie est utile pour réduire la quantité de données communiquées et stockées dans le cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge :

* Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle comme un appareil Edge, en suivant les étapes décrites dans le Guide de démarrage rapide pour [Linux](quickstart-linux.md) ou pour les [Appareils Windows](quickstart.md).
* Le module Azure Machine Learning ne prend pas en charge les processeurs ARM.

Ressources cloud :

* Un niveau standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure. 

Ressources de développement :

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pour Visual Studio Code. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Node.js et npm](https://nodejs.org). Le paquet npm est distribué avec Node.js, ce qui signifie que lorsque vous téléchargez Node.js, npm est automatiquement installé sur votre ordinateur.

## <a name="create-a-container-registry"></a>Créer un registre de conteneur
Dans ce tutoriel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et de créer une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce tutoriel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Ce didacticiel utilise Azure Container Registry. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Azure Container Registry**.
2. Donnez un nom à votre registre, choisissez un abonnement et un groupe de ressources, puis définissez la référence (SKU) sur la valeur **De base**. 
3. Sélectionnez **Créer**.
4. Une fois que votre registre de conteneurs est créé, accédez à celui-ci, puis sélectionnez **Clés d’accès**. 
5. Basculez **Utilisateur administrateur** sur **Activer**.
6. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. Vous utiliserez ces valeurs plus loin dans le tutoriel. 

## <a name="create-an-iot-edge-module-project"></a>Créer un projet de module IoT Edge
Les étapes suivantes vous montrent comment créer un module IoT Edge Node.js à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.

### <a name="create-a-new-solution"></a>Créer une solution

Utilisez **npm** pour créer un modèle de solution Node.js sur lequel vous pouvez compiler. 

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré** pour ouvrir le terminal intégré Visual Studio Code.

2. Dans le terminal intégré, entrez la commande suivante pour installer **yeoman** et le générateur du module Azure IoT Edge Node.js : 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Sélectionnez **Affichage** > **Palette de commandes** pour ouvrir la palette de commandes VS Code. 

3. Dans la palette de commandes, tapez et exécutez la commande **Azure: Sign in** et suivez les instructions pour vous connecter à votre compte Azure. Si vous êtes déjà connecté, vous pouvez ignorer cette étape.

4. Dans la palette de commandes, tapez et exécutez la commande **Azure IoT Edge: New IoT Edge solution**. Dans la palette de commandes, spécifiez les informations suivantes pour créer votre solution : 

   1. Sélectionnez le dossier où vous souhaitez créer la solution. 
   2. Spécifiez un nom pour votre solution ou acceptez le nom par défaut **EdgeSolution**.
   3. Choisissez **Module Node.js** comme modèle du module. 
   4. Nommez votre module **NodeModule**. 
   5. Spécifiez le registre Azure Container Registry que vous avez créé dans la section précédente comme dépôt d’images pour votre premier module. Remplacez **localhost:5000** par la valeur de serveur de connexion que vous avez copiée. La chaîne finale ressemble à **\<nom de registre\>.azurecr.io/nodemodule**.
 
La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. Il existe un dossier **.vscode**, un dossier **modules**, un fichier **.env** et un fichier modèle du manifeste de déploiement.

### <a name="add-your-registry-credentials"></a>Ajouter les informations d’identification de votre registre

Le fichier d’environnement stocke les informations d’identification de votre référentiel de conteneurs et les partage avec le runtime IoT Edge. Le runtime a besoin de ces informations d’identification pour extraire vos images privées sur l’appareil IoT Edge. 

1. Dans l’Explorateur VS Code, ouvrez le fichier **.env**. 
2. Mettre à jour les champs avec les valeurs de **nom d’utilisateur** et de **mot de passe** que vous avez copiées à partir de votre registre de conteneurs Azure. 
3. Enregistrez ce fichier. 

### <a name="update-the-module-with-custom-code"></a>Mettre à jour le module avec du code personnalisé

Chaque modèle est fourni avec un exemple de code, qui utilise des données de capteur simulées du module **tempSensor** et les achemine vers IoT Hub. Dans cette section, ajoutez du code pour que NodeModule analyse les messages avant leur envoi. 

1. Dans l’Explorateur VS Code, ouvrez **modules** > **NodeModule** > **app.js**.

5. Ajoutez une variable de seuil de température sous les modules de nœud requis. Le seuil de température définit la valeur que la température mesurée doit dépasser pour que les données soient envoyées à IoT Hub.

    ```javascript
    var temperatureThreshold = 30;
    ```

6. Remplacez l’intégralité de la fonction `PipeMessage` par la fonction `FilterMessage`.
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

7. Remplacez le nom de fonction `pipeMessage` avec `filterMessage` dans la fonction `client.on()`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

8. Copiez l’extrait de code suivant dans le fonction de rappel `client.open()`, une fois `client.on()` à l’intérieur de l’instruction `else`. Cette fonction sera appelée après la mise à jour des propriétés souhaitées.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

9. Enregistrez ce fichier.

## <a name="build-your-iot-edge-solution"></a>Générer votre solution IoT Edge

Dans la section précédente, vous avez créé une solution IoT Edge et ajouté du code à NodeModule qui permettra de filtrer les messages où la température de la machine signalée est inférieure au seuil acceptable. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs. 

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal intégré Visual Studio Code afin de pouvoir envoyer votre image de module à ACR : 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilisez le nom d’utilisateur, le mot de passe et le serveur de connexion que vous avez copiés à partir de votre Azure Container Registry dans la première section. Ou récupérez-les depuis la section **Clés d’accès** de votre registre dans le portail Azure.

2. Dans l’Explorateur VS Code, ouvrez le fichier **deployment.template.json** dans votre espace de travail de solution IoT Edge. 

   Ce fichier indique `$edgeAgent` pour déployer deux modules : **tempSensor**, qui simule les données de l’appareil, et **NodeModule**. La valeur `NodeModule.image` est définie sur une version amd64 Linux de l’image. Pour en savoir plus sur les manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

   Ce fichier contient également les informations d’identification de votre registre. Dans le fichier de modèle, vos nom d’utilisateur et mot de passe sont remplis avec des espaces réservés. Lorsque vous générez le manifeste de déploiement, les champs sont mis à jour avec les valeurs que vous avez ajoutées à **.env**. 

4. Ajoutez le jumeau de module NodeModule au manifeste de déploiement. Insérez le contenu JSON suivant en bas de la section `moduleContent`, après le jumeau de module `$edgeHub` : 
    ```json
        "NodeModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```
5. Enregistrez ce fichier.
6. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier **deployment.template.json** et sélectionnez **Build IoT Edge solution** (Générer la solution IoT Edge). 

Quand vous indiquez à Visual Studio Code de générer votre solution, il extrait d’abord les informations dans le modèle de déploiement et génère un fichier `deployment.json` dans un nouveau dossier **config**. Il exécute ensuite deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, mettent votre code Node.js en conteneur, puis l’envoient au registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution. 

Vous pouvez afficher l’adresse complète de l’image conteneur dans la commande `docker build` qui s’exécute dans le terminal intégré VS Code. L’adresse de l’image est créée à partir d’informations contenues dans le fichier `module.json`, au format **\<référentiel\> :\<version\>-\<plateforme\>**. Pour ce tutoriel, elle doit ressembler à **registryname.azurecr.io/nodemodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Déployer et exécuter la solution

Vous pouvez utiliser le portail Azure pour déployer votre module Node.js sur un appareil IoT Edge comme vous l’avez fait dans les guides de démarrage rapide, mais vous pouvez également déployer et surveiller des modules à partir de Visual Studio Code. Les sections suivantes utilisent l’extension Azure IoT Edge pour VS Code qui figure dans les prérequis. Installez-la maintenant si ce n’est pas déjà fait. 

1. Ouvrez la palette de commandes VS Code en sélectionnant **Affichage** > **Palette de commandes**.

2. Recherchez et exécutez la commande **Azure: Sign in**. Suivez les instructions pour vous connecter à votre compte Azure. 

3. Dans la palette de commandes, recherchez et exécutez la commande **Azure IoT Hub: Select IoT Hub**. 

4. Sélectionnez l’abonnement qui contient votre hub IoT, puis sélectionnez le hub IoT auquel vous souhaitez accéder.

5. Dans l’Explorateur VS Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). 

6. Cliquez avec le bouton droit sur le nom de votre appareil IoT Edge, puis sélectionnez **Create Deployment for IoT Edge device** (Créer un déploiement pour l’appareil IoT Edge). 

7. Accédez au dossier solution qui contient NodeModule. Ouvrez le dossier **config** et sélectionnez le fichier **deployment.json**. Cliquez sur **Select Edge Deployment Manifest** (Sélectionner un manifeste de déploiement Edge).

8. Actualisez la section **Appareils Azure IoT Hub**. Vous devez voir le nouveau module **NodeModule** en cours d’exécution avec le module **TempSensor** ainsi que **$edgeAgent** et **$edgeHub**. 


## <a name="view-generated-data"></a>Afficher les données générées

1. Pour surveiller les données reçues par le hub IoT, cliquez sur **...**, puis sélectionnez **Démarrer l’analyse des messages D2C**.
2. Pour analyser le message D2C pour un appareil spécifique, cliquez avec le bouton droit sur l’appareil dans la liste, puis sélectionnez **Démarrer l’analyse des messages D2C**.
3. Pour arrêter l’analyse des données, exécutez la commande **Azure IoT Hub : Stop monitoring D2C message** (Arrêter l’analyse du message D2C) dans la palette de commandes. 
4. Pour afficher ou mettre à jour le jumeau de module, cliquez avec le bouton droit sur le module dans la liste, puis sélectionnez **Modifier le jumeau de module**. Pour mettre à jour le jumeau de module, enregistrez le fichier JSON du jumeau, cliquez avec le bouton droit sur la zone de l’éditeur et sélectionnez **Mettre à jour le jumeau de module**.
5. Pour afficher les journaux Docker, vous pouvez installer [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pour VS Code et rechercher vos modules en cours d’exécution localement dans l’Explorateur Docker. Dans le menu contextuel, cliquez sur **Afficher les journaux** pour les afficher dans un terminal intégré. 

## <a name="clean-up-resources"></a>Supprimer des ressources 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations déjà créées afin de les réutiliser.

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales créées dans cet article pour éviter des frais. 

> [!IMPORTANT]
> La suppression de ressources et de groupe de ressources Azure est irréversible. Une fois supprimés, le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. Si vous avez créé l’IoT Hub à l’intérieur d’un groupe de ressources existant qui concerne des ressources que vous souhaitez conserver, supprimer uniquement la ressource de l’ IoT Hub au lieu de supprimer le groupe de ressources.
>

Pour supprimer l’IoT Hub uniquement, exécutez la commande suivante en utilisant le nom de votre hub et le nom du groupe de ressources :

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


Pour supprimer un groupe de ressources entier par nom :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et cliquez sur **Groupes de ressources**.

2. Dans la zone de texte **Filtrer par nom...**, saisissez le nom du groupe de ressources contenant votre IoT Hub. 

3. À droite de votre groupe de ressources dans la liste des résultats, cliquez sur **...**, puis sur **Supprimer le groupe de ressources**.

4. Il vous sera demandé de confirmer la suppression du groupe de ressources. Saisissez de nouveau le nom de votre groupe de ressources pour confirmer, puis cliquez sur **Supprimer**. Après quelques instants, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un module IoT Edge qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Vous pouvez continuer avec un des didacticiels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Déployer Azure Function en tant que module](tutorial-deploy-function.md)
> [Déployer Azure Stream Analytics en tant que module](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
