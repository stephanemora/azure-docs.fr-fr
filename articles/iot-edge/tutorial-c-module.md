---
title: Tutoriel pour créer un module C personnalisé - Azure IoT Edge | Microsoft Docs
description: Ce tutoriel explique comment créer un module IoT Edge avec un code C et le déployer sur un appareil Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 659d960881f143655e98c6f1d38696f44def3ae8
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54055096"
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

* Un niveau gratuit ou standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure.

Ressources de développement :

* [Visual Studio Code](https://code.visualstudio.com/).
* [Extension C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) pour Visual Studio Code.
* [Outils Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pour Visual Studio Code.
* [Docker CE](https://docs.docker.com/install/).

>[!Note]
>Les modules C pour Azure IoT Edge ne prennent pas en charge les conteneurs Windows.

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
Les étapes suivantes vous montrent comment créer un projet de module IoT Edge basé sur .NET Core 2.0 à l’aide de Visual Studio Code et des outils Azure IoT.

### <a name="create-a-new-solution"></a>Créer une solution

Créez un modèle de solution C que vous pouvez personnaliser avec votre propre code.

1. Sélectionnez **Affichage** > **Palette de commandes** pour ouvrir la palette de commandes VS Code.

2. Dans la palette de commandes, tapez et exécutez la commande **Azure: Sign in** (Azure : Se connecter) et suivez les instructions pour vous connecter à votre compte Azure. Si vous êtes déjà connecté, vous pouvez ignorer cette étape.

3. Dans la palette de commandes, saisissez et exécutez la commande **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge : Nouvelle solution IoT Edge). Suivez les invites de la palette de commandes pour créer votre solution.

   | Champ | Valeur |
   | ----- | ----- |
   | Sélectionner le dossier | Choisissez l’emplacement sur votre machine de développement pour que VS Code crée les fichiers de la solution. |
   | Provide a solution name (Nommer la solution) | Entrez un nom descriptif pour votre solution ou acceptez le nom par défaut (**EdgeSolution**). |
   | Select module template (Sélectionner un modèle de module) | Choisissez **Module C**. |
   | Provide a module name (Nommer le module) | Nommez votre module **CModule**. |
   | Provide Docker image repository for the module (Indiquer le référentiel d’images Docker pour le module) | Un référentiel d’images comprend le nom de votre registre de conteneurs et celui de votre image conteneur. Votre image conteneur est préremplie à partir de la dernière étape. Remplacez **localhost:5000** par la valeur de serveur de connexion de votre registre de conteneurs Azure. Vous pouvez récupérer le serveur de connexion à partir de la page Vue d’ensemble de votre registre de conteneurs dans le Portail Azure. La chaîne finale ressemble à ceci : \<nom de registre\>.azurecr.io/cmodule. |
 
   ![Fourniture du référentiel d’images Docker](./media/tutorial-c-module/repository.png)

La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. L’espace de travail de la solution contient cinq composants de niveau supérieur. Le dossier **modules** contient le code C de votre module, ainsi que les fichiers Docker pour la création de votre module comme image conteneur. Le fichier **\.env** stocke les informations d’identification de votre registre de conteneurs. Le fichier **deployment.template.json** contient les informations utilisées par le runtime IoT Edge pour déployer des modules sur un appareil. Conteneurs de fichiers **deployment.debug.template.json** de la version debug des modules. Dans le cadre de ce didacticiel, vous ne modifierez pas le dossier **\.vscode** ni le fichier **\.gitignore**.

Si vous n’avez pas spécifié de registre de conteneurs lors de la création de votre solution, mais accepté la valeur localhost:5000 par défaut, vous n’aurez pas de fichier \.env.

<!--
   ![C solution workspace](./media/tutorial-c-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Ajouter les informations d’identification de votre registre

Le fichier d’environnement stocke les informations d’identification de votre registre de conteneurs et les partage avec le runtime IoT Edge. Le runtime a besoin de ces informations d’identification pour extraire vos images privées sur l’appareil IoT Edge.

1. Dans l’Explorateur VS Code, ouvrez le fichier .env.
2. Mettre à jour les champs avec les valeurs de **nom d’utilisateur** et de **mot de passe** que vous avez copiées à partir de votre registre de conteneurs Azure.
3. Enregistrez ce fichier.

### <a name="update-the-module-with-custom-code"></a>Mettre à jour le module avec du code personnalisé

Ajoutez à votre module C du code lui permettant de lire les données du capteur, de vérifier si la température de la machine signalée a dépassé un seuil acceptable et de transmettre ces informations à l’IoT Hub.

5. Dans ce scénario, les données du capteur sont fournies au format JSON. Pour filtrer les messages au format JSON, importez une bibliothèque JSON pour C. Ce didacticiel utilise Parson.

   1. Téléchargez le [référentiel GitHub Parson](https://github.com/kgabis/parson). Copiez les fichiers **parson.c** et **parson.h** dans le dossier **CModule**.

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

11. Enregistrez le fichier main.c.

12. Dans l’Explorateur VS Code, ouvrez le fichier **deployment.template.json** dans votre espace de travail de solution IoT Edge. Ce fichier indique à l’agent IoT Edge quels modules déployer, dans cet exemple, **tempSensor** et **CModule**, puis indique au hub IoT Edge comment router les messages entre eux. L’extension Visual Studio Code renseigne automatiquement la plupart des informations dont vous avez besoin dans le modèle de déploiement, mais vérifiez que tout est correct pour votre solution : 

   1. La plateforme par défaut d’IoT Edge est définie sur **amd64** dans la barre d’état Visual Studio Code, ce qui signifie que votre **CModule** est défini sur la version amd64 Linux de l’image. Dans la barre d’état, remplacez la plateforme par défaut **amd64** par la plateforme **arm32v7** ou **windows-amd64**, si cela correspond à l’architecture de votre appareil IoT Edge. 

      ![Mettre à jour la plateforme de l’image du module](./media/tutorial-c-module/image-platform.png)

   2. Vérifiez que le modèle porte le nom de module approprié, pas la valeur par défaut **SampleModule** nom que vous avez modifié lorsque vous avez créé la solution IoT Edge.

   3. La section **registryCredentials** stocke vos informations d’identification de registre Docker, pour que l’agent IoT Edge puisse extraire l’image de votre module. Les paires nom d’utilisateur et mot de passe réelles sont stockées dans le fichier .env qui est ignoré par Git. Ajoutez vos informations d’identification au fichier .env si vous ne l’avez pas déjà fait.  

   4. Pour en savoir plus sur les manifestes de déploiement, consultez [Découvrir comment déployer des modules et établir des routes dans IoT Edge](module-composition.md).

13. Ajoutez le jumeau de module CModule au manifeste de déploiement. Insérez le contenu JSON suivant en bas de la section `moduleContent`, après le jumeau de module `$edgeHub` :

    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

   ![Ajout du jumeau de module CModule au modèle de déploiement](./media/tutorial-c-module/module-twin.png)

14. Enregistrez le fichier **deployment.template.json**.

## <a name="build-and-push-your-solution"></a>Générer et envoyer (push) votre solution

Dans la section précédente, vous avez créé une solution IoT Edge et ajouté du code à CModule qui permettra de filtrer les messages selon lesquels la température de la machine signalée se situe dans les limites acceptables. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs.

1. Ouvrez le terminal intégré VS Code en sélectionnant **Affichage** > **Terminal intégré**.

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal intégré Visual Studio Code. Vous devez vous connecter avec vos informations d’identification Azure Container Registry pour être en mesure d’envoyer votre image de module au registre.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilisez le nom d’utilisateur, le mot de passe et le serveur de connexion que vous avez copiés à partir de votre Azure Container Registry dans la première section. Ou récupérez-les depuis la section **Clés d’accès** de votre registre dans le portail Azure.

2. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier **deployment.template.json** et sélectionnez **Build and Push IoT Edge solution** (Générer et envoyer (push) la solution IoT Edge).

Quand vous indiquez à Visual Studio Code de générer votre solution, il commence par générer un fichier `deployment.json` dans un nouveau dossier **config**. Les informations du fichier deployment.json sont collectées à partir du fichier de modèle que vous avez mis à jour, du fichier .env que vous avez utilisé pour stocker les informations d’identification de votre registre de conteneurs et du fichier module.json dans le dossier CModule.

Ensuite, Visual Studio Code exécute deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, mettent `CModule.dll` en conteneur, puis l’envoient au registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution.

Vous pouvez afficher l’adresse complète de l’image conteneur avec la balise dans le terminal intégré de VS Code. L’adresse de l’image est créée à partir d’informations contenues dans le fichier `module.json`, au format **\<référentiel\> :\<version\>-\<plateforme\>**. Pour ce didacticiel, l’adresse doit ressembler à **myregistry.azurecr.io/cmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Déployer et exécuter la solution

Dans l’article de démarrage rapide que vous avez utilisé pour configurer votre appareil IoT Edge, vous avez déployé un module à l’aide du Portail Azure. Vous pouvez également déployer des modules à l’aide de l’extension Azure IoT Hub Toolkit (anciennement Azure IoT Toolkit) pour Visual Studio Code. Vous disposez déjà d’un manifeste de déploiement préparé pour votre scénario, le fichier **deployment.json**. Il vous suffit alors de sélectionner l’appareil qui recevra le déploiement.

1. Dans la palette de commandes VS Code, exécutez **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub : Sélectionner IoT Hub).

2. Choisissez l’abonnement et l’IoT Hub qui contiennent l’appareil IoT Edge que vous souhaitez configurer.

3. Dans l’Explorateur VS Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub).

4. Cliquez avec le bouton droit sur le nom de votre appareil IoT Edge, puis sélectionnez **Create Deployment for Single Device** (Créer un déploiement pour un seul appareil).

   ![Créer un déploiement pour un seul appareil](./media/tutorial-c-module/create-deployment.png)

5. Sélectionnez le fichier **deployment.json** dans le dossier **config**, puis cliquez sur **Sélectionner un manifeste de déploiement Edge**. N’utilisez pas le fichier deployment.template.json.

6. Cliquez sur le bouton Actualiser. Vous devez voir le nouveau module **CModule** en cours d’exécution avec le module **TempSensor** ainsi que **$edgeAgent** et **$edgeHub**.

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

Dans ce tutoriel, vous avez créé un module IoT Edge qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Quand vous êtes prêt à créer vos propres modules, vous pouvez découvrir comment [développer un module C avec Azure IoT Edge pour Visual Studio Code](how-to-develop-c-module.md). Vous pouvez passer aux tutoriels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier « en périphérie ».

> [!div class="nextstepaction"]
> [Stocker des données en périphérie avec les bases de données SQL Server](tutorial-store-data-sql-server.md)

