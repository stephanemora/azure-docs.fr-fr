---
title: Tutoriel - Développer des modules C pour Windows à l’aide d’Azure IoT Edge
description: Ce tutoriel explique comment créer des modules IoT Edge avec du code C, et comment le déployer sur des appareils Windows exécutant IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8f019c8f3c560fdfdc0c8e5992389c253c9b0d74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463372"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Tutoriel : Développer des modules IoT Edge en C avec des conteneurs Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article montre comment utiliser Visual Studio pour écrire du code C et le déployer sur un appareil Windows exécutant Azure IoT Edge.

>[!NOTE]
>IoT Edge 1.1 LTS est le dernier canal à prendre en charge les conteneurs Windows. À partir de la version 1.2, les conteneurs Windows ne sont pas pris en charge. Envisagez d'utiliser ou de migrer vers [IoT Edge pour Linux sur Windows](iot-edge-for-linux-on-windows.md) pour exécuter IoT Edge sur les appareils Windows.

Vous pouvez utiliser des modules Azure IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce tutoriel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Utiliser Visual Studio pour créer un module IoT Edge basé sur le SDK C.
> * Utiliser Visual Studio et Docker pour créer une image Docker et la publier sur votre registre
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées

Le module IoT Edge que vous créez dans ce didacticiel filtre les données de température générées par votre appareil. Ce module envoie uniquement des messages en amont lorsque la température dépasse le seuil spécifié. Ce type d’analyse à la périphérie est utile pour réduire la quantité de données communiquées et stockées dans le cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Ce tutoriel montre comment développer un module en C à l’aide de Visual Studio 2019, et comment le déployer sur un appareil Windows. Si vous développez des modules avec des conteneurs Linux, consultez [Développer des modules IoT Edge en C avec des conteneurs Linux](tutorial-csharp-module.md).

Pour connaître les options dont vous disposez pour développer et déployer des modules en C avec des conteneurs Windows, reportez-vous au tableau suivant :

| C | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;et&nbsp;2019 |
| -- | ------------------ | :------------------: |
| Windows AMD64 |  | ![Développer des modules en C pour WinAMD64 dans Visual Studio](./media/tutorial-c-module/green-check.png) |

Avant de commencer ce tutoriel, configurez votre environnement de développement en suivant les instructions fournies dans le tutoriel [Développer des modules IoT Edge avec des conteneurs Windows](tutorial-develop-for-windows.md). Une fois que vous aurez terminé, votre environnement comprendra les prérequis suivants :

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) de niveau gratuit ou standard dans Azure.
* Un [appareil Windows exécutant Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md).
* Un registre de conteneurs comme [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio), configuré avec l’extension [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/), configuré pour exécuter des conteneurs Windows.

Installez le SDK C Azure IoT pour Windows x64 par le biais de vcpkg, en exécutant les commandes suivantes :

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Si vous utilisez Visual Studio 2017 (version 15.7 ou ultérieure), téléchargez et installez Azure IoT Edge Tools pour Visual Studio 2017 à partir de [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Créer un projet de module

Dans cette section, vous allez créer un projet de module IoT Edge basé sur le SDK C à l’aide de Visual Studio et de l’extension Azure IoT Edge Tools. Une fois le modèle de projet créé, ajoutez le nouveau code afin que le module filtre les messages sur la base des propriétés signalées.

### <a name="create-a-new-project"></a>Création d'un projet

Créez un modèle de solution C que vous pouvez personnaliser avec votre propre code.

1. Ouvrez Visual Studio 2019, puis sélectionnez **Créer un projet**.

1. Dans le volet **Créer un projet**, recherchez **IoT Edge**, puis, dans la liste des résultats, sélectionnez le projet **Azure IoT Edge (Windows amd64)** .

   ![Capture d’écran du volet « Créer un projet » dans IoT Edge.](./media/tutorial-c-module-windows/new-project.png)

1. Sélectionnez **Suivant**.

    Le volet **Configurer votre nouveau projet** s’ouvre.

   ![Capture d’écran du volet « Configurer votre nouveau projet ».](./media/tutorial-c-module-windows/configure-project.png)

1. Dans le volet **Configurer votre nouveau projet**, renommez le projet et la solution à l’aide d’un nom plus explicite, comme **CTutorialApp**. 

1. Sélectionnez **Créer** pour créer le projet.

   Le volet **Ajouter un module** s’ouvre.

   ![Capture d’écran du volet « Ajouter un module » pour la configuration de votre projet.](./media/tutorial-c-module-windows/add-application-and-module.png)

1. Dans la page **Configurer votre nouveau projet**, effectuez les actions suivantes :

   a. Dans le volet de gauche, sélectionnez le modèle **C Module**.  
   b. Dans le champ **Nom du module**, entrez **CModule**.  
   c. Dans le champ **URL du dépôt**, remplacez **localhost:5000** par la valeur de **Serveur de connexion** provenant de votre registre de conteneurs Azure, en respectant le format suivant : `<registry name>.azurecr.io/cmodule`

    > [!NOTE]
    > Un référentiel d’images comprend le nom de votre registre de conteneurs et celui de votre image conteneur. Votre image conteneur est préremplie avec le nom du projet de module.  Vous pouvez récupérer le serveur de connexion à partir de la page Vue d’ensemble de votre registre de conteneurs dans le portail Azure.

1. Sélectionnez **Ajouter** pour créer le projet.

### <a name="add-your-registry-credentials"></a>Ajouter les informations d’identification de votre registre

Le manifeste de déploiement partage les informations d’identification de votre registre de conteneurs avec le runtime IoT Edge. Le runtime a besoin de ces informations d’identification pour extraire vos images privées sur l’appareil IoT Edge. Utilisez les informations d’identification de la section **Clés d’accès** dans votre registre de conteneurs Azure.

1. Dans l’Explorateur de solutions Visual Studio, ouvrez le fichier *deployment.template.json*.

1. Recherchez la propriété **registryCredentials** dans les propriétés $edgeAgent souhaitées. L’adresse de registre de la propriété doit être ajoutée automatiquement à l’aide des informations que vous avez fournies lors de la création du projet. Les champs du nom d’utilisateur et du mot de passe doivent contenir des noms de variables. Par exemple :

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Ouvrez le fichier d’environnement (ENV) dans votre solution de module. Dans la mesure où il est masqué par défaut dans l’Explorateur de solutions, vous devrez peut-être sélectionner le bouton **Afficher tous les fichiers** pour l’afficher. Le fichier ENV doit contenir les mêmes variables de nom d’utilisateur et de mot de passe que celles que vous avez vues dans le fichier *deployment.template.json*.

1. Ajoutez les valeurs de **Nom d’utilisateur** et **Mot de passe** provenant de votre registre de conteneurs Azure.

1. Enregistrez les modifications apportées au fichier ENV.

### <a name="update-the-module-with-custom-code"></a>Mettre à jour le module avec du code personnalisé

Le code du module par défaut reçoit des messages dans une file d’attente d’entrée, puis les passe à une file d’attente de sortie. Nous allons ajouter du code supplémentaire afin que le module traite les messages en périphérie avant leur transfert vers votre hub IoT. Mettez à jour le module afin qu’il analyse les données de température dans chaque message et n’envoie le message au hub IoT que si la température dépasse un certain seuil.

1. Dans ce scénario, les données du capteur sont fournies au format JSON. Pour filtrer les messages au format JSON, importez une bibliothèque JSON pour C. Ce didacticiel utilise Parson.

   a. Téléchargez le [référentiel GitHub Parson](https://github.com/kgabis/parson).  
   b. Copiez les fichiers *parson.c* et *parson.h* dans le projet CModule.  
   c. Dans Visual Studio, ouvrez le fichier *CMakeLists.txt* à partir du dossier de projet CModule.  
   d. Au début du fichier, importez les fichiers Parson sous la forme d’une bibliothèque appelée **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. Ajoutez `my_parson` à la liste des bibliothèques de la section « target_link_libraries » du fichier *CMakeLists.txt*.  
   f. Enregistrez le fichier *CMakeLists.txt*.  
   g. Sélectionnez **CModule** > **main.c**. Au bas de la liste des instructions include, ajoutez une nouvelle instruction afin d’inclure `parson.h` pour la prise en charge de JSON :

      ```c
      #include "parson.h"
      ```

1. Dans le fichier *main.c*, ajoutez une variable globale appelée `temperatureThreshold` en regard de la variable `messagesReceivedByInput1Queue`. Cette variable définit la valeur que la température mesurée doit dépasser pour que les données soient envoyées à votre hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Recherchez la fonction `CreateMessageInstance` dans le fichier *main.c*. Remplacez l’instruction *if-else* interne par le code suivant, qui ajoute quelques lignes de fonctionnalité :

   ```c
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
   ```

   Les nouvelles lignes de code de l’instruction *else* ajoutent une propriété au message, qui lui attribue une étiquette d’alerte. Ce code attribue une étiquette d’alerte à tous les messages, car nous allons ajouter une fonctionnalité qui n’envoie des messages au hub IoT que si ceux-ci signalent des températures élevées.

1. Recherchez la fonction `InputQueue1Callback` et remplacez tout le code de la fonction par le code suivant : Cette fonction implémente le filtre de messagerie réel. Lorsqu’un message est reçu, il vérifie si la température signalée dépasse le seuil défini. Si la température dépasse le seuil, la fonction transfère le message par le biais de sa file d’attente de sortie. Si le seuil n’est pas dépassé, la fonction ignore le message.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
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
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Ajoutez une fonction `moduleTwinCallback`. Cette méthode reçoit des mises à jour sur les propriétés souhaitées à partir du double de module et met à jour la variable **temperatureThreshold** en conséquence. Tous les modules ont leur propre jumeau, ce qui vous permet de configurer le code exécuté à l’intérieur d’un module, directement à partir du cloud.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
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

1. Recherchez la fonction `SetupCallbacksForModule`. Remplacez la fonction par le code suivant, qui ajoute une instruction *if else* pour vérifier si le jumeau de module a été mis à jour.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Enregistrez le fichier *main.c*.

1. Ouvrez le fichier *deployment.template.json*.

1. Ajoutez le jumeau de module **CModule** au manifeste de déploiement. Insérez le contenu JSON suivant en bas de la section `moduleContent`, après le jumeau de module `$edgeHub` :

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Capture d’écran montrant l’ajout du jumeau de module au modèle de déploiement.](./media/tutorial-c-module-windows/module-twin.png)

1. Enregistrez le fichier *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Générer et envoyer (push) votre module

Dans la section précédente, vous avez créé une solution IoT Edge et ajouté un code à **CModule** pour filtrer les messages où la température de la machine signalée est inférieure au seuil acceptable. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs.

### <a name="sign-in-to-docker"></a>Se connecter à Docker

Fournissez les informations d’identification du registre de conteneurs à Docker sur votre machine de développement afin qu’il puisse envoyer (push) votre image conteneur à stocker dans le registre.

1. Ouvrez PowerShell ou une fenêtre d’invite de commandes.

1. Connectez-vous à Docker avec les informations d’identification du registre de conteneurs Azure que vous avez enregistrées après avoir créé le registre.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Il se peut que vous receviez un avertissement de sécurité qui recommande l’utilisation de `--password-stdin`. Même s’il s’agit d’une bonne pratique recommandée pour les scénarios de production, elle sort du cadre de ce tutoriel. Pour plus d’informations, consultez les [informations de référence sur docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Générer et envoyer (push)

Votre machine de développement a désormais accès à votre registre de conteneurs ainsi que vos appareils IoT Edge. Il est temps de transformer le code du projet en image conteneur.

1. Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le nom du projet que vous voulez générer. Le nom par défaut est **AzureIotEdgeApp1**. Pour ce tutoriel, nous avons choisi le nom **CTutorialApp**. Étant donné que vous créez un module Windows, l’extension doit être **Windows.Amd64**.

1. Sélectionnez **Générer et envoyer (push) les modules IoT Edge**.

   La commande pour créer et pousser déclenche trois opérations :
   * Tout d’abord, elle crée un nouveau dossier dans la solution nommée *config*, qui contient le manifeste de déploiement complet. Il est créé à partir des informations contenues dans le modèle de déploiement et d’autres fichiers de solution. 
   * Ensuite, elle exécute `docker build` pour créer l’image conteneur en fonction du Dockerfile correspondant à votre architecture cible. 
   * Enfin, elle exécute `docker push` pour pousser le dépôt d’images vers votre registre de conteneurs.

   Ce processus peut prendre plusieurs minutes la première fois, mais il sera plus rapide la prochaine fois que vous exécuterez les commandes.

## <a name="deploy-modules-to-the-device"></a>Déployer des modules sur un appareil

Utilisez Visual Studio Cloud Explorer et l’extension Azure IoT Edge Tools pour déployer le projet de module sur votre appareil IoT Edge. Vous avez déjà préparé un manifeste de déploiement pour votre scénario, à savoir le fichier *deployment.windows-amd64.json* figurant dans le dossier *config*. Il vous suffit alors de sélectionner l’appareil qui recevra le déploiement.

Vérifiez que votre appareil IoT Edge est opérationnel.

1. Dans Visual Studio Cloud Explorer, développez les ressources pour voir la liste des appareils IoT.

1. Cliquez avec le bouton droit sur le nom de l’appareil IoT Edge sur lequel effectuer le déploiement.

1. Sélectionnez **Créer un déploiement**.

1. Dans l’Explorateur de fichiers de Visual Studio, sélectionnez le fichier *deployment.windows-amd64.json* situé dans le dossier *config* de votre solution.

1. Actualisez Cloud Explorer pour afficher les modules déployés qui sont listés sous votre appareil.

## <a name="view-generated-data"></a>Afficher les données générées

Une fois que vous appliquez le manifeste de déploiement à votre appareil IoT Edge, le runtime IoT Edge sur l’appareil collecte les nouvelles informations de déploiement et commence à s’exécuter sur celui-ci. Tous les modules qui sont en cours d’exécution sur l’appareil, mais qui ne sont pas inclus dans le manifeste de déploiement, sont arrêtés. Tous les modules manquant de l’appareil sont démarrés.

Vous pouvez utiliser l’extension IoT Edge Tools pour voir les messages à mesure qu’ils arrivent dans votre hub IoT.

1. Dans Visual Studio Cloud Explorer, sélectionnez le nom de votre appareil IoT Edge.

1. Dans la liste **Actions**, sélectionnez **Démarrer la supervision du point de terminaison d’événements intégré**.

1. Affichez les messages qui arrivent dans votre hub IoT. Les messages peuvent mettre un certain temps à arriver, car l’appareil IoT Edge doit recevoir son nouveau déploiement et démarrer tous les modules. Les modifications apportées au code CModule doivent attendre jusqu’à ce que la température de la machine atteigne 25 degrés et que les messages soient envoyés. Le code ajoute également le type de message **Alerte** à chaque message qui atteint ce seuil de température.

   ![Capture d’écran de la fenêtre Sortie affichant les messages qui arrivent au hub IoT.](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Modifier le jumeau de module

Vous avez utilisé le jumeau de module de CModule pour définir le seuil de température sur 25 degrés. Vous pouvez utiliser le jumeau de module pour changer cette fonctionnalité sans devoir mettre à jour le code du module.

1. Dans Visual Studio, ouvrez le fichier *deployment.windows-amd64.json* 

   *N’ouvrez pas* le fichier *deployment.template*. Dans l’Explorateur de solutions, si vous ne voyez pas le manifeste de déploiement dans le fichier *config*, sélectionnez l’icône **Afficher tous les fichiers** dans la barre d’outils de l’Explorateur de solutions.

1. Recherchez le jumeau de module de CModule et remplacez la valeur du paramètre **temperatureThreshold** par une température supérieure de 5 à 10 degrés par rapport à la dernière température signalée.

1. Enregistrez le fichier *deployment.windows-amd64.json*.

1. Suivez de nouveau les instructions de déploiement pour appliquer le manifeste de déploiement mis à jour à votre appareil.

1. Supervisez les messages appareil-à-cloud entrants. Vous devriez voir les messages s’arrêter jusqu’à ce que le nouveau seuil de température soit atteint.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez réutiliser les ressources et les configurations que vous avez créées dans ce tutoriel. Vous pouvez également continuer à utiliser le même appareil IoT Edge comme appareil de test.

Sinon, pour éviter des frais, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez utilisées ici.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge contenant le code pour filtrer les données brutes générées par votre appareil IoT Edge.

Pour voir comment Azure IoT Edge peut vous aider à déployer des services cloud Azure en vue de traiter et d’analyser des données en périphérie, passez aux tutoriels suivants.

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md)
> [Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Azure Machine Learning](tutorial-deploy-machine-learning.md)
> [Service Custom Vision](tutorial-deploy-custom-vision.md)
