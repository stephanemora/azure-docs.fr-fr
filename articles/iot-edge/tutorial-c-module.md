---
title: Tutoriel C d’Azure IoT Edge | Microsoft Docs
description: Ce tutoriel explique comment créer un module IoT Edge avec un code C et le déployer sur un appareil Edge
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 07/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6b7589652f5b180a826f3c0b1fcbe040ff3d386d
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41920293"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Tutoriel : Développer un module C IoT Edge et le déployer sur votre appareil simulé

Vous pouvez utiliser des modules IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce tutoriel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur. Ce tutoriel vous montre comment effectuer les opérations suivantes :    

> [!div class="checklist"]
> * Utiliser Visual Studio Code pour créer un module IoT Edge en C
> * Utiliser Visual Studio Code et Docker pour créer une image Docker et la publier dans un registre de conteneurs 
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées


Le module IoT Edge que vous créez dans ce tutoriel filtre les données de température générées par votre appareil. Il envoie uniquement des messages en amont lorsque la température dépasse un seuil spécifié. Ce type d’analyse à la périphérie est utile pour réduire la quantité de données communiquées et stockées dans le cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge :

* Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle comme un appareil Edge, en suivant les étapes décrites dans le Guide de démarrage rapide pour [Linux](quickstart-linux.md) ou pour les [Appareils Windows](quickstart.md).
* Les modules C pour Azure IoT Edge ne prennent pas en charge les conteneurs Windows. Si votre appareil IoT Edge est une machine Windows, configurez-le pour qu’il [utilise des conteneurs Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

Ressources cloud :

* Un niveau standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure. 

Ressources de développement :

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) pour Visual Studio Code.
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pour Visual Studio Code.
* [Docker CE](https://docs.docker.com/install/). 

>[!Note]
>Les modules C pour Azure IoT Edge ne prennent pas en charge les conteneurs Windows. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-container-registry"></a>Créer un registre de conteneur
Dans ce tutoriel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et de créer une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce tutoriel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Ce didacticiel utilise Azure Container Registry. 

La commande Azure CLI suivante crée un registre dans un groupe de ressources appelé **IoTEdgeResources**. Remplacez **{acr_name}** par un nom unique pour votre registre. 

   ```azurecli-interactive
   az acr create --resource-group IoTEdgeResources --name {acr_name} --sku Basic --admin-enabled true
   ```

Récupérez les informations d’identification de votre registre. 

   ```azurecli-interactive
   az acr credential show --name {acr_name}
   ```

Copiez les valeurs pour **Nom d’utilisateur** et un des mots de passe. Vous utiliserez ces valeurs plus tard dans le didacticiel, au moment de la publication de l’image Docker dans votre registre et au moment de l’ajout des informations d’identification du registre dans le runtime Edge. 

## <a name="create-an-iot-edge-module-project"></a>Créer un projet de module IoT Edge
Les étapes suivantes vous montrent comment créer un projet de module IoT Edge basé sur .NET core 2.0 à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.

### <a name="create-a-new-solution"></a>Créer une solution

Créez un modèle de solution C que vous pouvez personnaliser avec votre propre code. 

1. Sélectionnez **Affichage** > **Palette de commandes** pour ouvrir la palette de commandes VS Code. 

2. Dans la palette de commandes, tapez et exécutez la commande **Azure: Sign in** et suivez les instructions pour vous connecter à votre compte Azure. Si vous êtes déjà connecté, vous pouvez ignorer cette étape.

3. Dans la palette de commandes, tapez et exécutez la commande **Azure IoT Edge: New IoT Edge solution**. Dans la palette de commandes, spécifiez les informations suivantes pour créer votre solution : 

   1. Sélectionnez le dossier où vous souhaitez créer la solution. 
   2. Spécifiez un nom pour votre solution ou acceptez le nom par défaut **EdgeSolution**.
   3. Choisissez **Module C** comme modèle du module. 
   4. Nommez votre module **CModule**. 
   5. Spécifiez le registre Azure Container Registry que vous avez créé dans la section précédente comme dépôt d’images pour votre premier module. Remplacez **localhost:5000** par **\<nom du registre\>.azurecr.io**. Remplacez uniquement la partie localhost de la chaîne, ne supprimez pas le nom de votre module. 

   ![Fourniture du référentiel d’images Docker](./media/tutorial-c-module/repository.png)

La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. L’espace de travail de la solution contient cinq composants de niveau supérieur. Dans le cadre de ce didacticiel, vous ne modifierez pas le dossier **\.vscode** ni le fichier **\.gitignore**. Le dossier **modules** contient le code C de votre module, ainsi que les fichiers Docker pour la création de votre module comme image conteneur. Le fichier **\.env** stocke les informations d’identification de votre registre de conteneurs. Le fichier **deployment.template.json** contient les informations utilisées par le runtime IoT Edge pour déployer des modules sur un appareil. 

Si vous n’avez pas spécifié de registre de conteneurs lors de la création de votre solution, mais accepté la valeur localhost:5000 par défaut, vous n’aurez pas de fichier \.env. 

   ![Espace de travail de la solution C](./media/tutorial-c-module/workspace.png)

### <a name="add-your-registry-credentials"></a>Ajouter les informations d’identification de votre registre

Le fichier d’environnement stocke les informations d’identification de votre registre de conteneurs et les partage avec le runtime IoT Edge. Le runtime a besoin de ces informations d’identification pour extraire vos images privées sur l’appareil IoT Edge. 

1. Dans l’Explorateur VS Code, ouvrez le fichier .env. 
2. Mettre à jour les champs avec les valeurs de **nom d’utilisateur** et de **mot de passe** que vous avez copiées à partir de votre registre de conteneurs Azure. 
3. Enregistrez ce fichier. 

### <a name="update-the-module-with-custom-code"></a>Mettre à jour le module avec du code personnalisé

Ajoutez à votre module C du code lui permettant de lire les données du capteur, de vérifier si la température de la machine signalée a dépassé un seuil acceptable et de transmettre ces informations à l’IoT Hub. 

5. Dans ce scénario, les données du capteur sont fournies au format JSON. Pour filtrer les messages au format JSON, importez une bibliothèque JSON pour C. Ce didacticiel utilise Parson.

   1. Téléchargez le [référentiel Github Parson](https://github.com/kgabis/parson). Copiez les fichiers **parson.c** et **parson.h** dans le dossier **CModule**.

   2. Ouvrez **modules** > **CModule** > **CMakeLists.txt**. Au début du fichier, importez les fichiers Parson sous la forme d’une bibliothèque appelée **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Ajoutez **my_parson** à la liste des bibliothèques dans la fonction **target_link_libraries** de CMakeLists.txt.

   4. Enregistrez le fichier **CMakeLists.txt**.

   5. Ouvrez **modules** > **CModule** > **main.c**. Au bas de la liste des instructions include, ajoutez une nouvelle instruction afin d’inclure `parson.h` pour la prise en charge de JSON :

      ```c
      #include "parson.h"
      ```

6. Dans le fichier **main.c**, ajoutez une variable globale appelée `temperatureThreshold` après la section include. Cette variable définit la valeur que la température mesurée doit dépasser pour que les données soient envoyées à IoT Hub. 

    ```c
    static double temperatureThreshold = 25;
    ```

7. Remplacez la totalité de la fonction `CreateMessageInstance` par le code suivant. Cette fonction alloue un contexte pour le rappel. 

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

            if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
            {
                free(messageInstance);
                messageInstance = NULL;
            }
            else
            {
                messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
                MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
                if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
                {
                    printf("ERROR: Map_AddOrUpdate Failed!\r\n");
                }
            }
        }

        return messageInstance;
    }
    ```

8. Remplacez la totalité de la fonction `InputQueue1Callback` par le code suivant. Cette fonction implémente le filtre de messagerie réel. 

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n", 
                messagesReceivedByInput1Queue, messageBody);

        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

9. Ajoutez une fonction `moduleTwinCallback`. Cette méthode reçoit des mises à jour sur les propriétés souhaitées à partir du double de module et met à jour la variable **temperatureThreshold** en conséquence. Tous les modules ont leur propre double, ce qui vous permet de configurer le code exécuté à l’intérieur d’un module directement à partir du cloud.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n", 
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

10. Remplacez la fonction `SetupCallbacksForModule` par le code suivant. 

    ```c
    static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
    {
        int ret;

        if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else
        {
            ret = 0;
        }

        return ret;
    }
    ```

11. Enregistrez le fichier **main.c**.

## <a name="build-your-iot-edge-solution"></a>Générer votre solution IoT Edge

Dans la section précédente, vous avez créé une solution IoT Edge et ajouté du code à CModule qui permettra de filtrer les messages selon lesquels la température de la machine signalée se situe dans les limites acceptables. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs. 

1. Ouvrez le terminal intégré VS Code en sélectionnant **Affichage** > **Terminal intégré**. 

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal intégré Visual Studio Code. Vous devez vous connecter avec vos informations d’identification Azure Container Registry pour être en mesure d’envoyer votre image de module au registre. 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilisez le nom d’utilisateur, le mot de passe et le serveur de connexion que vous avez copiés à partir de votre Azure Container Registry dans la première section. Ou récupérez-les depuis la section **Clés d’accès** de votre registre dans le portail Azure.

2. Dans l’Explorateur VS Code, ouvrez le fichier **deployment.template.json** dans votre espace de travail de solution IoT Edge. Ce fichier indique au `$edgeAgent` de déployer les deux modules : **tempSensor** et **CModule**. La valeur `CModule.image` est définie sur une version amd64 Linux de l’image. Pour en savoir plus sur les manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

4. Ajoutez le jumeau de module CModule au manifeste de déploiement. Insérez le contenu JSON suivant en bas de la section `moduleContent`, après le jumeau de module `$edgeHub` : 

    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

   ![Ajout du jumeau de module CModule au modèle de déploiement](./media/tutorial-c-module/module-twin.png)

4. Enregistrez le fichier **deployment.template.json**.
5. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier **deployment.template.json** et sélectionnez **Build and Push IoT Edge solution** (Générer et envoyer (push) la solution IoT Edge). 

Quand vous indiquez à Visual Studio Code de générer votre solution, il commence par générer un fichier `deployment.json` dans un nouveau dossier **config**. Les informations du fichier deployment.json sont collectées à partir du fichier de modèle que vous avez mis à jour, du fichier .env que vous avez utilisé pour stocker les informations d’identification de votre registre de conteneurs et du fichier module.json dans le dossier CModule. 

Ensuite, Visual Studio Code exécute deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, mettent `CModule.dll` en conteneur, puis l’envoient au registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution. 

Vous pouvez afficher l’adresse complète de l’image conteneur avec la balise dans le terminal intégré de VS Code. L’adresse de l’image est créée à partir d’informations contenues dans le fichier `module.json`, au format **\<référentiel\> :\<version\>-\<plateforme\>**. Pour ce didacticiel, l’adresse doit ressembler à **myregistry.azurecr.io/cmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Déployer et exécuter la solution

Dans l’article de démarrage rapide que vous avez utilisé pour configurer votre appareil IoT Edge, vous avez déployé un module à l’aide du Portail Azure. Vous pouvez également déployer des modules à l’aide de l’extension Azure IoT Toolkit pour Visual Studio Code. Vous disposez déjà d’un manifeste de déploiement préparé pour votre scénario, le fichier **deployment.json**. Il vous suffit alors de sélectionner l’appareil qui recevra le déploiement.

1. Dans la palette de commandes VS Code, exécutez **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub : Sélectionner l’IoT Hub). 

2. Choisissez l’abonnement et l’IoT Hub qui contiennent l’appareil IoT Edge que vous souhaitez configurer. 

3. Dans l’Explorateur VS Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). 

4. Cliquez avec le bouton droit sur le nom de votre appareil IoT Edge, puis sélectionnez **Create Deployment for Single Device** (Créer un déploiement pour un seul appareil). 

   ![Créer un déploiement pour un seul appareil](./media/tutorial-c-module/create-deployment.png)

5. Sélectionnez le fichier **deployment.json** dans le dossier **config**, puis cliquez sur **Sélectionner un manifeste de déploiement Edge**. N’utilisez pas le fichier deployment.template.json. 

6. Cliquez sur le bouton Actualiser. Vous devez voir le nouveau module **CModule** en cours d’exécution avec le module **TempSensor** ainsi que **$edgeAgent** et **$edgeHub**. 

## <a name="view-generated-data"></a>Afficher les données générées

1. Pour surveiller les données reçues par le hub IoT, cliquez sur **...**, puis sélectionnez **Démarrer l’analyse des messages D2C**.
2. Pour analyser le message D2C pour un appareil spécifique, cliquez avec le bouton droit sur l’appareil dans la liste, puis sélectionnez **Démarrer l’analyse des messages D2C**.
3. Pour arrêter l’analyse des données, exécutez la commande **Azure IoT Hub : Stop monitoring D2C message** (Arrêter l’analyse du message D2C) dans la palette de commandes. 
4. Pour afficher ou mettre à jour le jumeau de module, cliquez avec le bouton droit sur le module dans la liste, puis sélectionnez **Modifier le jumeau de module**. Pour mettre à jour le jumeau de module, enregistrez le fichier JSON du jumeau, cliquez avec le bouton droit sur la zone de l’éditeur et sélectionnez **Mettre à jour le jumeau de module**.
5. Pour afficher les journaux Docker, vous pouvez installer [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pour VS Code et rechercher vos modules en cours d’exécution localement dans l’Explorateur Docker. Dans le menu contextuel, cliquez sur **Afficher les journaux** pour les afficher dans un terminal intégré.
 
## <a name="clean-up-resources"></a>Supprimer les ressources 

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser. Vous pouvez également continuer à utiliser le même appareil IoT Edge comme appareil de test. 

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter les frais. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un module IoT Edge qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Quand vous êtes prêt à créer vos propres modules, vous pouvez découvrir comment [développer un module C avec Azure IoT Edge pour Visual Studio Code](how-to-develop-c-module.md). Vous pouvez passer aux tutoriels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier « en périphérie ».

> [!div class="nextstepaction"]
> [Stocker des données en périphérie avec les bases de données SQL Server](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
