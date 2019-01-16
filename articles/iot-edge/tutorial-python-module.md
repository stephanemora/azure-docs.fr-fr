---
title: Tutoriel pour créer un module Python personnalisé - Azure IoT Edge | Microsoft Docs
description: Ce didacticiel explique comment créer un module IoT Edge avec du code Python et le déployer sur un appareil Edge.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 89c19adc571d500fff54d493072bb9976ce51aa9
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052885"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>Tutoriel : Développer et déployer un module Python IoT Edge sur votre appareil simulé

Vous pouvez utiliser des modules Azure IoT Edge pour déployer un code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce tutoriel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur. Vous utiliserez l’appareil IoT Edge que vous avez créé dans les guides de démarrage rapide. Ce tutoriel vous montre comment effectuer les opérations suivantes :    

> [!div class="checklist"]
> * Utiliser Visual Studio Code pour créer un module IoT Edge Python
> * Utiliser Visual Studio Code et Docker pour créer une image Docker et la publier dans votre registre
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées


Le module IoT Edge que vous créez dans ce didacticiel filtre les données de température générées par votre appareil. Il envoie uniquement des messages en amont lorsque la température dépasse un seuil spécifié. Ce type d’analyse à la périphérie est utile pour réduire la quantité de données communiquées et stockées dans le cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge :

* Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle en tant qu’appareil Edge. Pour cela, suivez les étapes décrites dans le guide de démarrage rapide pour [Linux](quickstart-linux.md).
* Les modules Python pour IoT Edge ne prennent pas en charge les appareils Windows.

Ressources cloud :

* Un niveau gratuit ou standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure. 

Ressources de développement :

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Outils Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pour Visual Studio Code.
* [Extension Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pour Visual Studio Code. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Python](https://www.python.org/downloads/)
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) pour l’installation des packages Python (en général inclus avec votre installation de Python).

>[!Note]
>Vérifiez que votre dossier `bin` se trouve sur le chemin d’accès de votre plateforme. En général, `~/.local/` pour UNIX et macOS, ou `%APPDATA%\Python` sur Windows.

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Dans ce tutoriel, vous utilisez les outils Azure IoT pour Visual Studio Code afin de créer un module et une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

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
   | SKU | Sélectionnez **De base**. | 

5. Sélectionnez **Créer**.

6. Une fois votre registre de conteneurs créé, accédez à celui-ci, puis sélectionnez **Clés d’accès**. 

7. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. Vous utiliserez ces valeurs plus loin dans ce tutoriel afin de permettre l’accès au registre de conteneurs. 

## <a name="create-an-iot-edge-module-project"></a>Créer un projet de module IoT Edge
Les étapes suivantes permettent de créer un module IoT Edge Python à l’aide de Visual Studio Code et des outils Azure IoT Edge.

### <a name="create-a-new-solution"></a>Créer une solution

Utilisez le package Python **cookiecutter** pour créer un modèle de solution Python sur lequel vous pouvez générer. 

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Terminal** pour ouvrir le terminal intégré VS Code.

2. Dans le terminal intégré, entrez la commande suivante pour installer (ou mettre à jour) le modèle **cookiecutter** que vous utilisez pour créer le modèle de solution IoT Edge dans VS Code :

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Vérifiez que le répertoire d’installation de cookiecutter se trouve sur le chemin de votre environnement, afin de pouvoir l’appeler à partir d’une invite de commandes. Le répertoire est compris dans la sortie du script d’installation, par exemple `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`.
   >
   >Redémarrez Visual Studio Code pour noter les modifications apportées au chemin. 

3. Sélectionnez **Affichage** > **Palette de commandes** pour ouvrir la palette de commandes VS Code. 

4. Dans la palette de commandes, entrez et exécutez la commande **Azure: Sign in** et suivez les instructions pour vous connecter à votre compte Azure. Si vous êtes déjà connecté, vous pouvez ignorer cette étape.

5. Dans la palette de commandes, entrez et exécutez la commande **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge : Nouvelle solution IoT Edge). Suivez les invites de la palette de commandes pour créer votre solution.

   | Champ | Valeur |
   | ----- | ----- |
   | Sélectionner le dossier | Choisissez l’emplacement sur votre machine de développement pour que VS Code crée les fichiers de la solution. |
   | Provide a solution name (Nommer la solution) | Entrez un nom descriptif pour votre solution ou acceptez le nom par défaut (**EdgeSolution**). |
   | Select module template (Sélectionner un modèle de module) | Choisissez **Module Python**. |
   | Provide a module name (Nommer le module) | Nommez votre module **PythonModule**. |
   | Provide Docker image repository for the module (Indiquer le référentiel d’images Docker pour le module) | Un référentiel d’images comprend le nom de votre registre de conteneurs et celui de votre image conteneur. Votre image conteneur est préremplie à partir de la dernière étape. Remplacez **localhost:5000** par la valeur de serveur de connexion de votre registre de conteneurs Azure. Vous pouvez récupérer le serveur de connexion à partir de la page Vue d’ensemble de votre registre de conteneurs dans le Portail Azure. La chaîne finale ressemble à \<nom de registre\>.azurecr.io/pythonmodule. |
 
   ![Fourniture du référentiel d’images Docker](./media/tutorial-python-module/repository.png)

La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. L’espace de travail de la solution contient cinq composants de niveau supérieur. Le dossier **modules** contient le code Python de votre module, ainsi que les fichiers Docker pour la création de votre module comme image conteneur. Le fichier **\.env** stocke les informations d’identification de votre registre de conteneurs. Le fichier **deployment.template.json** contient les informations utilisées par le runtime IoT Edge pour déployer des modules sur un appareil. Conteneurs de fichiers **deployment.debug.template.json** de la version debug des modules. Dans le cadre de ce didacticiel, vous ne modifierez pas le dossier **\.vscode** ni le fichier **\.gitignore**.  

Si vous n’avez pas spécifié de registre de conteneurs lors de la création de votre solution, mais accepté la valeur localhost:5000 par défaut, vous n’aurez pas de fichier \.env. 

<!--
   ![Python solution workspace](./media/tutorial-python-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Ajouter les informations d’identification de votre registre

Le fichier d’environnement stocke les informations d’identification de votre référentiel de conteneurs et les partage avec le runtime IoT Edge. Le runtime a besoin de ces informations d’identification pour extraire vos images privées sur l’appareil IoT Edge. 

1. Dans l’Explorateur VS Code, ouvrez le fichier .env. 
2. Mettre à jour les champs avec les valeurs de **nom d’utilisateur** et de **mot de passe** que vous avez copiées à partir de votre registre de conteneurs Azure. 
3. Enregistrez ce fichier. 

### <a name="update-the-module-with-custom-code"></a>Mettre à jour le module avec du code personnalisé

Chaque modèle contient un exemple de code, qui utilise des simulations de données de capteur du module **tempSensor** et les achemine vers le hub IoT. Dans cette section, ajoutez le code qui développe le module **PythonModule** afin d’analyser les messages avant de les envoyer. 

1. Dans l’Explorateur VS Code, ouvrez **modules** > **PythonModule** > **main.py**.

2. En haut du fichier **main.py**, importez la bibliothèque **json** :

    ```python
    import json
    ```

3. Ajoutez les variables **TEMPERATURE_THRESHOLD** et **TWIN_CALLBACKS** sous les compteurs globaux. Le seuil de température définit la valeur que la température de la machine mesurée doit dépasser pour que les données soient envoyées au hub IoT.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Remplacez la fonction **receive_message_callback** par le code suivant :

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. Ajoutez une nouvelle fonction appelée **module_twin_callback**. Cette fonction sera appelée après la mise à jour des propriétés souhaitées.

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. Dans la classe **HubManager**, ajoutez une nouvelle ligne à la méthode **__init__** pour initialiser la fonction **module_twin_callback** que vous venez d’ajouter :

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Enregistrez le fichier main.py.

8. Dans l’Explorateur VS Code, ouvrez le fichier **deployment.template.json** dans votre espace de travail de solution IoT Edge. Ce fichier indique à l’agent IoT Edge quels modules déployer, dans cet exemple, **tempSensor** et **PythonModule**, puis indique au hub IoT Edge comment router les messages entre eux. L’extension Visual Studio Code renseigne automatiquement la plupart des informations dont vous avez besoin dans le modèle de déploiement, mais vérifiez que tout est correct pour votre solution : 

   1. La plateforme par défaut d’IoT Edge est définie sur **amd64** dans la barre d’état Visual Studio Code, ce qui signifie que votre **PythonModule** est défini sur la version amd64 Linux de l’image. Dans la barre d’état, remplacez la plateforme par défaut **amd64** par la plateforme **arm32v7** ou **windows-amd64**, si cela correspond à l’architecture de votre appareil IoT Edge. 

      ![Mettre à jour la plateforme de l’image du module](./media/tutorial-python-module/image-platform.png)

   2. Vérifiez que le modèle porte le nom de module approprié, pas la valeur par défaut **SampleModule** nom que vous avez modifié lorsque vous avez créé la solution IoT Edge.

   3. La section **registryCredentials** stocke vos informations d’identification de registre Docker, pour que l’agent IoT Edge puisse extraire l’image de votre module. Les paires nom d’utilisateur et mot de passe réelles sont stockées dans le fichier .env qui est ignoré par Git. Ajoutez vos informations d’identification au fichier .env si vous ne l’avez pas déjà fait.  

   4. Pour en savoir plus sur les manifestes de déploiement, consultez [Découvrir comment déployer des modules et établir des routes dans IoT Edge](module-composition.md).

9. Ajoutez le jumeau de module **PythonModule** au manifeste de déploiement. Insérez le contenu JSON suivant en bas de la section **moduleContent**, après le jumeau de module **$edgeHub** : 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Ajouter un jumeau de module au modèle de déploiement](./media/tutorial-python-module/module-twin.png)

10. Enregistrez le fichier deployment.template.json.

## <a name="build-and-push-your-solution"></a>Générer et envoyer (push) votre solution

Dans la section précédente, vous avez créé une solution IoT Edge et ajouté du code dans le module **PythonModule** pour filtrer les messages où la température de la machine signalée est inférieure au seuil acceptable. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs. 

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal intégré Visual Studio Code. Vous pouvez ensuite transmettre votre image de module au registre Azure Container Registry : 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilisez le nom d’utilisateur, le mot de passe et le serveur de connexion que vous avez copiés à partir de votre registre Azure Container Registry dans la première section. Vous pouvez également récupérer ces valeurs depuis la section **Clés d’accès** de votre registre dans le portail Azure.

2. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier deployment.template.json et sélectionnez **Build and Push IoT Edge solution** (Générer et envoyer (push) la solution IoT Edge). 

Quand vous indiquez à Visual Studio Code de générer votre solution, il extrait d’abord les informations contenues dans le modèle de déploiement et génère un fichier deployment.json dans un nouveau dossier nommé **config**. Il exécute ensuite deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, mettent le code Python en conteneur, puis envoient le code vers le registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution. 

Vous pouvez afficher l’adresse complète de l’image conteneur dans la commande `docker build` qui s’exécute dans le terminal intégré VS Code. L’adresse de l’image est créée à partir d’informations contenues dans le fichier module.json, au format \<référentiel\>:\<version\>-\<plateforme\>. Pour ce didacticiel, elle doit ressembler à registryname.azurecr.io/pythonmodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Déployer et exécuter la solution

Dans l’article de démarrage rapide que vous avez utilisé pour configurer votre appareil IoT Edge, vous avez déployé un module à l’aide du Portail Azure. Vous pouvez également déployer des modules à l’aide de l’extension Azure IoT Hub Toolkit (anciennement Azure IoT Toolkit) pour Visual Studio Code. Vous disposez déjà d’un manifeste de déploiement préparé pour votre scénario, le fichier **deployment.json**. Il vous suffit alors de sélectionner l’appareil qui recevra le déploiement.

1. Dans la palette de commandes VS Code, exécutez **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub : Sélectionner IoT Hub). 

2. Choisissez l’abonnement et l’IoT Hub qui contiennent l’appareil IoT Edge que vous souhaitez configurer. 

3. Dans l’Explorateur VS Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). 

4. Cliquez avec le bouton droit sur le nom de votre appareil IoT Edge, puis sélectionnez **Create Deployment for Single Device** (Créer un déploiement pour un seul appareil). 

   ![Créer un déploiement pour un seul appareil](./media/tutorial-python-module/create-deployment.png)

5. Sélectionnez le fichier **deployment.json** dans le dossier **config**, puis cliquez sur **Sélectionner un manifeste de déploiement Edge**. N’utilisez pas le fichier deployment.template.json. 

6. Cliquez sur le bouton Actualiser. Vous devez voir le nouveau module **PythonModule** en cours d’exécution avec le module **TempSensor** ainsi que **$edgeAgent** et **$edgeHub**. 

## <a name="view-generated-data"></a>Afficher les données générées

Une fois que vous appliquez le manifeste de déploiement à votre appareil IoT Edge, le runtime IoT Edge sur l’appareil collecte les nouvelles informations de déploiement et commence à s’exécuter sur celui-ci. Tous les modules en cours d’exécution sur l’appareil qui ne sont pas inclus dans le manifeste de déploiement sont arrêtés. Tous les modules manquant de l’appareil sont démarrés. 

Vous pouvez afficher l’état de votre appareil IoT Edge dans la section **Appareils Azure IoT Hub** de l’explorateur de Visual Studio Code. Développez les détails de votre appareil pour afficher la liste des modules déployés et en cours d’exécution. 

Vous pouvez voir l’état de vos modules de déploiement directement sur l’appareil IoT Edge à l’aide de la commande `iotedge list`. Vous devriez voir quatre modules : les deux modules du runtime IoT Edge, tempSensor, et le module personnalisé que vous avez créé au cours de ce didacticiel. Cela peut prendre quelques minutes pour démarrer tous les modules, réexécutez alors la commande si vous ne les voyez pas tous au début. 

Pour afficher les messages générés par n’importe quel module, utilisez la commande `iotedge logs <module name>`. 

Vous pouvez afficher les messages dès leur arrivée à votre hub IoT à l’aide de Visual Studio Code. 

1. Pour surveiller les données reçues par le hub IoT, sélectionnez les points de suspension (**...**), puis **Démarrer l’analyse des messages D2C**.
2. Pour analyser le message D2C pour un appareil spécifique, cliquez avec le bouton droit sur l’appareil dans la liste, puis sélectionnez **Démarrer l’analyse des messages D2C**.
3. Pour arrêter la surveillance des données, exécutez la commande **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub : Arrêter de surveiller les messages D2C) dans la palette de commandes. 
4. Pour afficher ou mettre à jour le jumeau de module, cliquez avec le bouton droit sur le module dans la liste, puis sélectionnez **Modifier le jumeau de module**. Pour mettre à jour le jumeau de module, enregistrez le fichier JSON du jumeau, cliquez avec le bouton droit sur la zone de l’éditeur et sélectionnez **Mettre à jour le jumeau de module**.
5. Pour afficher les journaux Docker, installez [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pour VS Code. Vous pouvez rechercher localement vos modules en cours d’exécution dans l’Explorateur Docker. Dans le menu contextuel, cliquez sur **Afficher les journaux** pour les afficher dans le terminal intégré. 

## <a name="clean-up-resources"></a>Supprimer des ressources 

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser. Vous pouvez également continuer à utiliser le même appareil IoT Edge comme appareil de test. 

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter les frais. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

### <a name="delete-local-resources"></a>Supprimer des ressources locales

Si vous souhaitez supprimer le runtime IoT Edge et les ressources associées à partir de votre appareil, utilisez les commandes suivantes. 

Supprimez le runtime IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Lorsque le runtime IoT Edge est supprimé, les conteneurs qu’il a créés sont arrêtés, mais existent toujours sur votre appareil. Affichez tous les conteneurs.

   ```bash
   sudo docker ps -a
   ```

Supprimez les conteneurs de runtime qui ont été créés sur votre appareil.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Supprimez tous les conteneurs qui ont été répertoriés dans la sortie `docker ps` en faisant référence aux noms de conteneur. 

Supprimez le runtime de conteneurs.

   ```bash
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge contenant le code pour filtrer les données brutes générées par votre appareil IoT Edge. Vous pouvez passer aux didacticiels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier « en périphérie ».

> [!div class="nextstepaction"]
> [Déployer Azure Functions en tant que module](tutorial-deploy-function.md)
> [Déployer Azure Stream Analytics en tant que module](tutorial-deploy-stream-analytics.md)
