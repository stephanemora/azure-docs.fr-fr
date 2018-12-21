---
title: Tutoriel pour créer un module Node.js personnalisé - Azure IoT Edge | Microsoft Docs
description: Ce didacticiel explique comment créer un module IoT Edge avec du code Node.js et le déployer sur un appareil Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 11/25/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 76bc59b41bf35f6427b1dcb273f3f50c5e70b748
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344055"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>Tutoriel : Développer et déployer un module IoT Edge Node.js sur votre appareil simulé

Vous pouvez utiliser des modules IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce tutoriel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur. Vous utiliserez l’appareil IoT Edge que vous avez créé dans les guides de démarrage rapide. Ce tutoriel vous montre comment effectuer les opérations suivantes :    

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

Ressources cloud :

* Un niveau gratuit ou standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure. 

Ressources de développement :

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pour Visual Studio Code. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Node.js et npm](https://nodejs.org). Le paquet npm est distribué avec Node.js, ce qui signifie que lorsque vous téléchargez Node.js, npm est automatiquement installé sur votre ordinateur.

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Dans ce tutoriel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker pour stocker vos images conteneur. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker connus. Ce didacticiel utilise Azure Container Registry. 

Si vous ne disposez pas d’un registre de conteneurs, suivez ces étapes pour en créer un dans Azure :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Container Registry**.

2. Fournissez les valeurs suivantes pour créer votre registre de conteneurs :

   | Champ | Valeur | 
   | ----- | ----- |
   | Nom du registre | Fournissez un nom unique. |
   | Abonnement | Sélectionnez un abonnement dans la liste déroulante. |
   | Groupe de ressources | Nous vous recommandons d’utiliser le même groupe de ressources pour toutes les ressources de test que vous créez dans le cadre des démarrages rapides et didacticiels IoT Edge. Par exemple, utilisez **IoTEdgeResources**. |
   | Lieu | Choisissez un emplacement proche de vous. |
   | Utilisateur administrateur | Définissez ce champ sur **Activer**. |
   | SKU | Sélectionnez **De base**. | **Terminal**

5. Sélectionnez **Créer**.

6. Une fois votre registre de conteneurs créé, accédez à celui-ci, puis sélectionnez **Clés d’accès**. 

7. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. Vous utiliserez ces valeurs plus loin dans ce tutoriel afin de permettre l’accès au registre de conteneurs. 

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

3. Dans la palette de commandes, tapez et exécutez la commande **Azure: Sign in** (Azure : Se connecter) et suivez les instructions pour vous connecter à votre compte Azure. Si vous êtes déjà connecté, vous pouvez ignorer cette étape.

4. Dans la palette de commandes, tapez et exécutez la commande **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge : Nouvelle solution IoT Edge). Suivez les invites de la palette de commandes pour créer votre solution.

   | Champ | Valeur |
   | ----- | ----- |
   | Sélectionner le dossier | Choisissez l’emplacement sur votre machine de développement pour que VS Code crée les fichiers de la solution. |
   | Provide a solution name (Nommer la solution) | Entrez un nom descriptif pour votre solution ou acceptez le nom par défaut (**EdgeSolution**). |
   | Select module template (Sélectionner un modèle de module) | Choisissez **Module Node.js**. |
   | Provide a module name (Nommer le module) | Nommez votre module **NodeModule**. |
   | Provide Docker image repository for the module (Indiquer le référentiel d’images Docker pour le module) | Un référentiel d’images comprend le nom de votre registre de conteneurs et celui de votre image conteneur. Votre image conteneur est préremplie à partir de la dernière étape. Remplacez **localhost:5000** par la valeur de serveur de connexion de votre registre de conteneurs Azure. Vous pouvez récupérer le serveur de connexion à partir de la page Vue d’ensemble de votre registre de conteneurs dans le Portail Azure. La chaîne finale ressemble à ceci : \<nom de registre\>.azurecr.io/nodemodule. |
 
   ![Fourniture du référentiel d’images Docker](./media/tutorial-node-module/repository.png)

La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. L’espace de travail de la solution contient cinq composants de niveau supérieur. Le dossier **modules** contient le code Node.js de votre module, ainsi que les fichiers Docker pour la création de votre module comme image conteneur. Le fichier **\.env** stocke les informations d’identification de votre registre de conteneurs. Le fichier **deployment.template.json** contient les informations utilisées par le runtime IoT Edge pour déployer des modules sur un appareil. Conteneurs de fichiers **deployment.debug.template.json** de la version debug des modules. Dans le cadre de ce didacticiel, vous ne modifierez pas le dossier **\.vscode** ni le fichier **\.gitignore**. 

Si vous n’avez pas spécifié de registre de conteneurs lors de la création de votre solution, mais accepté la valeur localhost:5000 par défaut, vous n’aurez pas de fichier \.env. 

<!--
   ![Node.js solution workspace](./media/tutorial-node-module/workspace.png)
-->

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
    var temperatureThreshold = 25;
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

10. Dans l’Explorateur VS Code, ouvrez le fichier **deployment.template.json** dans votre espace de travail de solution IoT Edge. 

   Ce fichier indique `$edgeAgent` pour déployer deux modules : **tempSensor**, qui simule les données de l’appareil, et **NodeModule**. La plateforme par défaut d’IoT Edge est définie sur **amd64** dans la barre d’état Visual Studio Code, ce qui signifie que votre **NodeModule** est défini sur la version amd64 Linux de l’image. Dans la barre d’état, remplacez la plateforme par défaut **amd64** par la plateforme **arm32v7** ou **windows-amd64**, si cela correspond à l’architecture de votre appareil IoT Edge. Pour en savoir plus sur les manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md). 

   Ce fichier contient également les informations d’identification de votre registre. Dans le fichier de modèle, vos nom d’utilisateur et mot de passe sont remplis avec des espaces réservés. Lorsque vous générez le manifeste de déploiement, les champs sont mis à jour avec les valeurs que vous avez ajoutées à **.env**. 

12. Ajoutez le jumeau de module NodeModule au manifeste de déploiement. Insérez le contenu JSON suivant en bas de la section `moduleContent`, après le jumeau de module `$edgeHub` : 

   ```json
       "NodeModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Ajouter un jumeau de module au modèle de déploiement](./media/tutorial-node-module/module-twin.png)

13. Enregistrez ce fichier.


## <a name="build-your-iot-edge-solution"></a>Générer votre solution IoT Edge

Dans la section précédente, vous avez créé une solution IoT Edge et ajouté du code à NodeModule qui permettra de filtrer les messages où la température de la machine signalée est inférieure au seuil acceptable. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs. 

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal intégré Visual Studio Code afin de pouvoir envoyer votre image de module à ACR : 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilisez le nom d’utilisateur, le mot de passe et le serveur de connexion que vous avez copiés à partir de votre Azure Container Registry dans la première section. Ou récupérez-les depuis la section **Clés d’accès** de votre registre dans le portail Azure.

2. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier **deployment.template.json** et sélectionnez **Build and Push IoT Edge solution** (Générer et envoyer (push) la solution IoT Edge). 

Quand vous indiquez à Visual Studio Code de générer votre solution, il extrait d’abord les informations dans le modèle de déploiement et génère un fichier `deployment.json` dans un nouveau dossier **config**. Il exécute ensuite deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, mettent votre code Node.js en conteneur, puis l’envoient au registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution. 

Vous pouvez afficher l’adresse complète de l’image conteneur dans la commande `docker build` qui s’exécute dans le terminal intégré VS Code. L’adresse de l’image est créée à partir d’informations contenues dans le fichier `module.json`, au format **\<référentiel\> :\<version\>-\<plateforme\>**. Pour ce tutoriel, elle doit ressembler à **registryname.azurecr.io/nodemodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Déployer et exécuter la solution

Dans l’article de démarrage rapide que vous avez utilisé pour configurer votre appareil IoT Edge, vous avez déployé un module à l’aide du Portail Azure. Vous pouvez également déployer des modules à l’aide de l’extension Azure IoT Hub Toolkit (anciennement Azure IoT Toolkit) pour Visual Studio Code. Vous disposez déjà d’un manifeste de déploiement préparé pour votre scénario, le fichier **deployment.json**. Il vous suffit alors de sélectionner l’appareil qui recevra le déploiement.

1. Dans la palette de commandes VS Code, exécutez **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub : Sélectionner IoT Hub). 

2. Choisissez l’abonnement et l’IoT Hub qui contiennent l’appareil IoT Edge que vous souhaitez configurer. 

3. Dans l’Explorateur VS Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). 

4. Cliquez avec le bouton droit sur le nom de votre appareil IoT Edge, puis sélectionnez **Create Deployment for Single Device** (Créer un déploiement pour un seul appareil). 

   ![Créer un déploiement pour un seul appareil](./media/tutorial-node-module/create-deployment.png)

5. Sélectionnez le fichier **deployment.json** dans le dossier **config**, puis cliquez sur **Sélectionner un manifeste de déploiement Edge**. N’utilisez pas le fichier deployment.template.json. 

6. Cliquez sur le bouton Actualiser. Vous devez voir le nouveau module **NodeModule** en cours d’exécution avec le module **TempSensor** ainsi que **$edgeAgent** et **$edgeHub**. 


## <a name="view-generated-data"></a>Afficher les données générées

Une fois que vous appliquez le manifeste de déploiement à votre appareil IoT Edge, le runtime IoT Edge sur l’appareil collecte les nouvelles informations de déploiement et commence à s’exécuter sur celui-ci. Tous les modules en cours d’exécution sur l’appareil qui ne sont pas inclus dans le manifeste de déploiement sont arrêtés. Tous les modules manquant de l’appareil sont démarrés. 

Vous pouvez afficher l’état de votre appareil IoT Edge dans la section **Appareils Azure IoT Hub** de l’explorateur de Visual Studio Code. Développez les détails de votre appareil pour afficher la liste des modules déployés et en cours d’exécution. 

Vous pouvez voir l’état de vos modules de déploiement directement sur l’appareil IoT Edge à l’aide de la commande `iotedge list`. Vous devriez voir quatre modules : les deux modules du runtime IoT Edge, tempSensor, et le module personnalisé que vous avez créé au cours de ce didacticiel. Cela peut prendre quelques minutes pour démarrer tous les modules, réexécutez alors la commande si vous ne les voyez pas tous au début. 

Pour afficher les messages générés par n’importe quel module, utilisez la commande `iotedge logs <module name>`. 

Vous pouvez afficher les messages dès leur arrivée à votre hub IoT à l’aide de Visual Studio Code. 

1. Pour surveiller les données reçues par le hub IoT, cliquez sur **...**, puis sélectionnez **Démarrer l’analyse des messages D2C**.
2. Pour analyser le message D2C pour un appareil spécifique, cliquez avec le bouton droit sur l’appareil dans la liste, puis sélectionnez **Démarrer l’analyse des messages D2C**.
3. Pour cesser la surveillance des messages, exécutez la commande **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub : Arrêter de surveiller les messages D2C) dans la palette de commandes. 
4. Pour afficher ou mettre à jour le jumeau de module, cliquez avec le bouton droit sur le module dans la liste, puis sélectionnez **Modifier le jumeau de module**. Pour mettre à jour le jumeau de module, enregistrez le fichier JSON du jumeau, cliquez avec le bouton droit sur la zone de l’éditeur et sélectionnez **Mettre à jour le jumeau de module**.
5. Pour afficher les journaux Docker, vous pouvez installer [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pour VS Code et rechercher vos modules en cours d’exécution localement dans l’Explorateur Docker. Dans le menu contextuel, cliquez sur **Afficher les journaux** pour les afficher dans un terminal intégré. 

## <a name="clean-up-resources"></a>Supprimer des ressources 

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser. Vous pouvez également continuer à utiliser le même appareil IoT Edge comme appareil de test. 

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter les frais. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un module IoT Edge qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Vous pouvez continuer avec un des didacticiels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Déployer Azure Function en tant que module](tutorial-deploy-function.md)
> [Déployer Azure Stream Analytics en tant que module](tutorial-deploy-stream-analytics.md)

