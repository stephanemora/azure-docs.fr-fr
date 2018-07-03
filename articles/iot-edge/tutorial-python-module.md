---
title: Didacticiel d’Azure IoT Edge Python | Microsoft Docs
description: Ce didacticiel explique comment créer un module IoT Edge avec du code Python et le déployer sur un appareil Edge
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 884237a851461fe3d7a48708d221909804760ceb
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063120"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>Didacticiel : Développer et déployer un module IoT Edge Python sur votre appareil simulé

Vous pouvez utiliser des modules IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce didacticiel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur. Vous utilisez l’appareil IoT Edge simulé que vous avez créé dans les didacticiels Déployer Azure IoT Edge sur un appareil simulé sous [Windows][lnk-quickstart-win] ou [Linux][lnk-quickstart-lin]. Ce tutoriel vous montre comment effectuer les opérations suivantes :    

> [!div class="checklist"]
> * Utiliser Visual Studio Code pour créer un module IoT Edge Python
> * Utiliser le Visual Studio Code et Docker pour créer une image docker et la publier dans votre registre 
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées


Le module IoT Edge que vous créez dans ce didacticiel filtre les données de température générées par votre appareil. Il envoie uniquement des messages en amont lorsque la température dépasse un seuil spécifié. Ce type d’analyse à la périphérie est utile pour réduire la quantité de données communiquées et stockées dans le cloud. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.


## <a name="prerequisites"></a>Prérequis

* L’appareil Azure IoT Edge que vous avez créé dans le démarrage rapide pour les appareils [Linux](quickstart-linux.md) ou [Windows](quickstart.md).
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extension Python pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) sur le même ordinateur que celui sur lequel Visual Studio Code est installé. L’édition Community (CE) est suffisante pour ce didacticiel. 
* [Python](https://www.python.org/downloads/)
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) pour l’installation des packages Python (en général inclus avec votre installation de Python).

## <a name="create-a-container-registry"></a>Créer un registre de conteneur
Dans ce didacticiel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et de créer une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce didacticiel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Ce didacticiel utilise Azure Container Registry. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Azure Container Registry**.
2. Donnez un nom à votre registre, choisissez un abonnement et un groupe de ressources, puis définissez la référence (SKU) sur la valeur **De base**. 
3. Sélectionnez **Créer**.
4. Une fois que votre registre de conteneurs est créé, accédez à celui-ci, puis sélectionnez **Clés d’accès**. 
5. Basculez **Utilisateur administrateur** sur **Activer**.
6. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. Vous utiliserez ces valeurs plus loin dans le didacticiel. 

## <a name="create-an-iot-edge-module-project"></a>Créer un projet de module IoT Edge
Les étapes suivantes vous montrent comment créer un module IoT Edge Python à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.

### <a name="create-a-new-solution"></a>Créer une solution

Utilisez le package Python **cookiecutter** pour créer un modèle de solution Python sur lequel vous pouvez générer. 

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré** pour ouvrir le terminal intégré Visual Studio Code.

2. Dans le terminal intégré, entrez la commande suivante pour installer (ou mettre à jour) le modèle **cookiecutter** que vous utilisez pour créer le modèle de solution Edge dans VS Code :

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Sélectionnez **Affichage** > **Palette de commandes** pour ouvrir la palette de commandes VS Code. 

4. Dans la palette de commandes, saisissez et exécutez la commande **Azure : Se connecter** et suivez les instructions pour vous connecter à votre compte Azure. Si vous êtes déjà connecté, vous pouvez ignorer cette étape.

5. Dans la palette de commandes, saisissez et exécutez la commande **Azure IoT Edge : Nouvelle solution IoT Edge**. Dans la palette de commandes, fournissez les informations suivantes pour créer votre solution : 

   1. Sélectionnez le dossier où vous souhaitez créer la solution. 
   2. Fournissez un nom pour votre solution ou acceptez le nom par défaut **EdgeSolution**.
   3. Choisissez **Module Python** comme modèle du module. 
   4. Nommez votre module **PythonModule**. 
   5. Spécifiez l’Azure Container Registry que vous avez créé dans la section précédente comme référentiel d’images pour votre premier module. Remplacez **localhost:5000** par la valeur de serveur de connexion que vous avez copiée. La chaîne finale ressemble à **\<nom de registre\>.azurecr.io/pythonmodule**.
 
La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. Il existe un dossier **modules**, un fichier modèle du manifeste de déploiement et un fichier **.env**. 

### <a name="add-your-registry-credentials"></a>Ajouter les informations d’identification de votre registre

Le fichier d’environnement stocke les informations d’identification de votre référentiel de conteneurs et les partage avec le runtime IoT Edge. Le runtime a besoin de ces informations d’identification pour extraire vos images privées sur l’appareil IoT Edge. 

1. Dans l’Explorateur VS Code, ouvrez le fichier **.env**. 
2. Mettre à jour les champs avec les valeurs de **nom d’utilisateur** et de **mot de passe** que vous avez copiées à partir de votre registre de conteneurs Azure. 
3. Enregistrez ce fichier. 

### <a name="update-the-module-with-custom-code"></a>Mettre à jour le module avec du code personnalisé

Chaque modèle est fourni avec un exemple de code, qui utilise des simulations de données de capteur du module **tempSensor** et les achemine vers IoT Hub. Dans cette section, ajoutez le code qui étend pythonModule afin d’analyser les messages avant de les envoyer. 

1. Dans l’Explorateur VS Code, ouvrez **modules** > **PythonModule** > **main.py**.

2. En haut de **main.py**, importez la bibliothèque `json`.

    ```python
    import json
    ```

3. Ajoutez les variables `TEMPERATURE_THRESHOLD` et `TWIN_CALLBACKS` sous les compteurs globaux. Le seuil de température définit la valeur que la température de machine mesurée doit dépasser pour que les données soient envoyées à IoT Hub.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Remplacez la fonction `receive_message_callback` par le code suivant :

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
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

5. Ajoutez une nouvelle fonction appelée `module_twin_callback`. Cette fonction sera appelée après la mise à jour des propriétés souhaitées.

    ```python
    # module_twin_callback is invoked when twin's desired properties are updated.
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

6. Dans la classe `HubManager`, ajoutez une nouvelle ligne à la méthode `__init__` pour initialiser la fonction `module_twin_callback` que vous venez d’ajouter.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Enregistrez ce fichier.

## <a name="build-your-iot-edge-solution"></a>Générer votre solution IoT Edge

Dans la section précédente, vous avez créé une solution IoT Edge et ajouté du code à PythonModule qui permettra de filtrer les messages où la température de la machine signalée est inférieure au seuil acceptable. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs. 

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal intégré Visual Studio Code afin de pouvoir envoyer votre image de module à ACR : 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilisez le nom d’utilisateur, le mot de passe et le serveur de connexion que vous avez copiés à partir de votre Azure Container Registry dans la première section. Ou récupérez-les depuis la section **Clés d’accès** de votre registre dans le portail Azure.

2. Dans l’Explorateur VS Code, ouvrez le fichier **deployment.template.json** dans l’espace de travail de votre solution IoT Edge. 

   Ce fichier indique `$edgeAgent` pour déployer deux modules : **tempSensor**, qui simule les données de l’appareil, et **PythonModule**. La valeur `PythonModule.image` est définie sur une version amd64 Linux de l’image. Pour en savoir plus sur les manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

   Ce fichier contient également les informations d’identification de votre registre. Dans le fichier de modèle, vos nom d’utilisateur et mot de passe sont remplis avec des espaces réservés. Lorsque vous générez le manifeste de déploiement, les champs sont mis à jour avec les valeurs que vous avez ajoutées à **.env**. 

3. Ajoutez le jumeau de module PythonModule au manifeste de déploiement. Insérez le contenu JSON suivant en bas de la section `moduleContent`, après le jumeau de module `$edgeHub` : 
    ```json
        "PythonModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Enregistrez ce fichier.

5. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier **deployment.template.json** et sélectionnez **Build IoT Edge solution** (Générer la solution IoT Edge). 

Lorsque vous indiquez à Visual Studio Code de générer votre solution, il extrait d’abord les informations dans le modèle de déploiement et génère un fichier `deployment.json` dans un nouveau dossier **config**. Il exécute ensuite deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, mettent votre code Python en conteneur, puis l’envoient au registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution. 

Vous pouvez afficher l’adresse complète de l’image conteneur dans la commande `docker build` qui s’exécute dans le terminal intégré VS Code. L’adresse de l’image est créée à partir d’informations contenues dans le fichiers `module.json`, au format **\<référentiel\>:\<version\>-\<plateforme\>**. Pour ce didacticiel, elle doit ressembler à **registryname.azurecr.io/pythonmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Déployer et exécuter la solution

Vous pouvez utiliser le portail Azure pour déployer votre module Python sur un appareil IoT Edge comme vous l’avez fait dans les démarrages rapides, mais vous pouvez également déployer et surveiller des modules à partir de Visual Studio Code. Les sections suivantes utilisent l’extension Azure IoT Edge pour VS Code qui figure dans les prérequis. Installez-la maintenant si ce n’est pas déjà fait. 

1. Ouvrez la palette de commandes VS Code en sélectionnant **Affichage** > **Palette de commandes**.

2. Recherchez et exécutez la commande **Azure : Se connecter**. Suivez les instructions pour vous connecter à votre compte Azure. 

3. Dans la palette de commandes, recherchez et exécutez la commande **Azure IoT Hub : Sélectionner IoT Hub**. 

4. Sélectionnez l’abonnement qui contient votre IoT Hub, puis sélectionnez l’IoT Hub auquel vous souhaitez accéder.

5. Dans l’Explorateur VS Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). 

6. Cliquez avec le bouton droit sur le nom de votre appareil IoT Edge, puis sélectionnez **Create Deployment for IoT Edge device** (Créer un déploiement pour l’appareil IoT Edge). 

7. Accédez au dossier solution qui contient PythonModule. Ouvrez le dossier **config** et sélectionnez le fichier **deployment.json**. Cliquez sur **Select Edge Deployment Manifest** (Sélectionner un manifeste de déploiement Edge).

8. Actualisez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). Vous devez voir le nouveau module **PythonModule** en cours d’exécution avec le module **TempSensor** ainsi que **$edgeAgent** et **$edgeHub**. 

## <a name="view-generated-data"></a>Afficher les données générées

1. Pour surveiller les données reçues par l’IoT Hub, cliquez sur **...** , puis sélectionnez **Start Monitoring D2C Messages** (Démarrer l’analyse des messages D2C).
2. Pour analyser le message D2C pour un appareil spécifique, cliquez avec le bouton droit sur l’appareil dans la liste, puis sélectionnez **Start Monitoring D2C Messages** (Démarrer l’analyse des messages D2C).
3. Pour arrêter l’analyse des données, exécutez la commande **Azure IoT Hub : Stop monitoring D2C message** (Arrêter l’analyse du message D2C) dans la palette de commandes. 
4. Pour afficher ou mettre à jour le jumeau de module, cliquez avec le bouton droit sur le module dans la liste, puis sélectionnez **Edit module twin** (Modifier le jumeau de module). Pour mettre à jour le jumeau de module, enregistrez le fichier JSON du jumeau, cliquez avec le bouton droit sur la zone de l’éditeur et sélectionnez **Update Module Twin** (Mettre à jour le jumeau de module).
5. Pour afficher les journaux Docker, vous pouvez installer [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pour VS Code et recherchez vos modules en cours d’exécution localement dans l’Explorateur Docker. Dans le menu contextuel, cliquez sur **Show Logs** (Afficher les journaux) pour les afficher dans un terminal intégré. 

## <a name="clean-up-resources"></a>Supprimer des ressources 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations déjà créées afin de les réutiliser.

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales créées dans cet article pour éviter des frais. 

> [!IMPORTANT]
> La suppression de ressources et groupe de ressources Azure est irréversible. Une fois supprimés, le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. Si vous avez créé l’IoT Hub à l’intérieur d’un groupe de ressources existant qui concerne des ressources que vous souhaitez conserver, supprimer uniquement la ressource de l’ IoT Hub au lieu de supprimer le groupe de ressources.
>

Pour supprimer l’IoT Hub uniquement, exécutez la commande suivante en utilisant le nom de votre hub et le nom du groupe de ressources :

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Pour supprimer un groupe de ressources entier par nom :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et cliquez sur **Groupes de ressources**.

2. Dans la zone de texte **Filtrer par nom...**, saisissez le nom du groupe de ressources contenant votre IoT Hub. 

3. À droite de votre groupe de ressources dans la liste des résultats, cliquez sur **...**, puis sur **Supprimer le groupe de ressources**.

4. Il vous sera demandé de confirmer la suppression du groupe de ressources. Saisissez de nouveau le nom de votre groupe de ressources pour confirmer, puis cliquez sur **Supprimer**. Après quelques instants, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Vous pouvez continuer avec un des didacticiels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Déployer Azure Function en tant que module](tutorial-deploy-function.md)
> [Déployer Azure Stream Analytics en tant que module](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
