---
title: Tutoriel - Développer des modules C# pour Windows avec Azure IoT Edge
description: Ce tutoriel explique comment créer des modules IoT Edge avec du code C#, et comment le déployer sur des appareils Windows exécutant IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: edbe2b8370b943aa93a1cef425c64e9f11feb735
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705589"
---
# <a name="tutorial-develop-c-iot-edge-modules-for-windows-devices"></a>Tutoriel : Développer des modules IoT Edge en C# pour les appareils Windows

Cet article montre comment utiliser Visual Studio pour écrire du code C# et le déployer sur un appareil Windows exécutant Azure IoT Edge.

Vous pouvez utiliser des modules Azure IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce tutoriel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Utiliser Visual Studio pour créer un module IoT Edge basé sur le SDK C#
> * Utiliser Visual Studio et Docker pour créer une image Docker et la publier sur votre registre
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées

Le module IoT Edge que vous créez dans ce didacticiel filtre les données de température générées par votre appareil. Ce module envoie uniquement des messages en amont lorsque la température dépasse le seuil spécifié. Ce type d’analyse à la périphérie est utile pour réduire la quantité de données communiquées et stockées dans le cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Ce tutoriel montre comment développer un module en C# à l’aide de Visual Studio 2019, et comment le déployer sur un appareil Windows. Si vous développez des modules pour les appareils Linux, consultez [Développer un module IoT Edge en C# pour les appareils Linux](tutorial-csharp-module.md).

Pour connaître les options dont vous disposez pour développer et déployer des modules en C# sur des appareils Windows, reportez-vous au tableau suivant :

| C# | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;et&nbsp;2019 |
| -- | :------------------: | :------------------: |
| Développement pour Windows AMD64 | ![Développer des modules en C# pour WinAMD64 dans Visual Studio Code](./media/tutorial-c-module/green-check.png) | ![Développer des modules en C# pour WinAMD64 dans Visual Studio](./media/tutorial-c-module/green-check.png) |
| Débogage pour Windows AMD64 |   | ![Déboguer des modules en C# pour WinAMD64 dans Visual Studio](./media/tutorial-c-module/green-check.png) |

Avant de commencer ce tutoriel, configurez votre environnement de développement en suivant les instructions fournies dans le tutoriel [Développer des modules IoT Edge pour les appareils Windows](tutorial-develop-for-windows.md). Une fois que vous aurez terminé, votre environnement comprendra les prérequis suivants :

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) de niveau gratuit ou standard dans Azure.
* Un [appareil Windows exécutant Azure IoT Edge](quickstart.md).
* Un registre de conteneurs comme [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio), configuré avec l’extension [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/), configuré pour exécuter des conteneurs Windows.

> [!TIP]
> Si vous utilisez Visual Studio 2017 (version 15.7 ou ultérieure), téléchargez et installez Azure IoT Edge Tools pour Visual Studio 2017 à partir de [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Créer un projet de module

Dans cette section, vous allez créer un projet de module IoT Edge à l’aide de Visual Studio et de l’extension Azure IoT Edge Tools. Une fois le modèle de projet créé, ajoutez le nouveau code afin que le module filtre les messages sur la base des propriétés signalées.

### <a name="create-a-new-project"></a>Créer un projet

Azure IoT Edge Tools fournit des modèles de projet pour tous les langages de module IoT Edge pris en charge dans Visual Studio. Ces modèles comprennent tous les fichiers, ainsi que le code dont vous avez besoin pour déployer un module de travail en vue de tester IoT Edge. Vous pouvez également les personnaliser avec votre propre logique métier.

1. Ouvrez Visual Studio 2019, puis sélectionnez **Créer un projet**.

1. Dans le volet **Créer un projet**, recherchez **IoT Edge**, puis, dans la liste des résultats, sélectionnez le projet **Azure IoT Edge (Windows amd64)** .

   ![Capture d’écran du volet « Créer un projet » dans IoT Edge.](./media/tutorial-csharp-module-windows/new-project.png)

1. Sélectionnez **Suivant**.

    Le volet **Configurer votre nouveau projet** s’ouvre.

   ![Capture d’écran du volet « Configurer votre nouveau projet ».](./media/tutorial-csharp-module-windows/configure-project.png)

1. Dans le volet **Configurer votre nouveau projet**, renommez le projet et la solution à l’aide d’un nom plus explicite, comme **CSharpTutorialApp**. 

1. Sélectionnez **Créer** pour créer le projet.

   Le volet **Ajouter un module** s’ouvre.

   ![Capture d’écran du volet « Ajouter un module » pour la configuration de votre projet.](./media/tutorial-csharp-module-windows/add-application-and-module.png)

1. Dans la page **Configurer votre nouveau projet**, effectuez les actions suivantes :

   a. Dans le volet de gauche, sélectionnez le modèle **C# Module**.  
   b. Dans le champ **Nom du module**, entrez **CSharpModule**.  
   c. Dans le champ **URL du dépôt**, remplacez **localhost:5000** par la valeur de **Serveur de connexion** provenant de votre registre de conteneurs Azure, en respectant le format suivant : `<registry name>.azurecr.io/csharpmodule`

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

1. Dans Visual Studio, sélectionnez **CSharpModule** > **Program.cs**.

1. En haut de l’espace de noms **CSharpModule**, ajoutez trois instructions **using** pour les types utilisés ultérieurement :

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

1. Ajoutez la variable **temperatureThreshold** à la classe **Program** après la variable counter. La variable temperatureThreshold définit la valeur que la température mesurée doit dépasser pour que les données soient envoyées au hub IoT.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Ajoutez les classes **MessageBody**, **Machine** et **Ambient** à la classe **Program** après la déclaration des variables. Ces classes définissent le schéma attendu pour le corps des messages entrants.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}
    }
    ```

1. Recherchez la méthode **Init**. Cette méthode crée et configure un objet **ModuleClient** qui permet au module de se connecter au runtime Azure IoT Edge local pour envoyer et recevoir des messages. De plus, le code enregistre un rappel pour recevoir des messages du hub IoT Edge via le point de terminaison **input1**.

   Remplacez la méthode Init entière par le code suivant :

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime.
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties.
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   En plus de définir la connexion au runtime IoT Edge avec l’objet ModuleClient, cette méthode Init mise à jour ajoute de nouvelles fonctionnalités. Elle lit les propriétés souhaitées du jumeau de module pour récupérer la valeur **temperatureThreshold**. Ensuite, elle crée un rappel qui écoute les futures mises à jour des propriétés souhaitées du jumeau de module. Avec ce rappel, vous pouvez mettre à jour le seuil de température dans le jumeau de module à distance, les modifications étant alors incorporées dans le module.

   La méthode Init mise à jour change également la méthode **SetInputMessageHandlerAsync** existante. Dans l’exemple de code, les messages entrants sur *input1* sont traités avec la fonction *PipeMessage*, mais nous voulons changer cela afin d’utiliser la fonction *FilterMessages* que nous allons créer au cours des étapes suivantes.

1. Ajoutez une nouvelle méthode **onDesiredPropertiesUpdate** à la classe **Program**. Cette méthode reçoit des mises à jour sur les propriétés souhaitées à partir du jumeau de module et met à jour la variable **temperatureThreshold** en conséquence. Tous les modules ont leur propre module jumeau, ce qui vous permet de configurer le code exécuté à l’intérieur d’un module directement à partir du cloud.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

1. Supprimez l’exemple de méthode **PipeMessage** et remplacez-le par une nouvelle méthode **FilterMessages**. Cette méthode est appelée chaque fois que le module reçoit un message d’IoT Edge Hub. Elle filtre les messages qui signalent des températures situées sous le seuil de température défini via le jumeau de module. La méthode ajoute également la propriété **MessageType** au message avec la valeur définie sur **Alerte**.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using(var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

1. Enregistrez le fichier *Program.cs*.

1. Ouvrez le fichier *deployment.template.json* dans votre solution IoT Edge. Ce fichier indique à l’agent IoT Edge quels sont les modules à déployer, et au hub IoT Edge comment router les messages entre eux. Ici, les modules à déployer sont **SimulatedTemperatureSensor** et **CSharpModule**.

1. Ajoutez le jumeau de module **CSharpModule** au manifeste de déploiement. Insérez le contenu JSON suivant en bas de la section `modulesContent`, après le jumeau de module **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Capture d’écran montrant l’ajout du jumeau de module au modèle de déploiement.](./media/tutorial-csharp-module-windows/module-twin.png)

1. Enregistrez le fichier *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Générer et envoyer (push) votre module

Dans la section précédente, vous avez créé une solution IoT Edge et ajouté un code à **CSharpModule** pour filtrer les messages où la température de la machine signalée est inférieure au seuil acceptable. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs.

### <a name="sign-in-to-docker"></a>Se connecter à Docker

1. Utilisez la commande suivante pour vous connecter à Docker sur votre machine de développement. Utilisez le nom d’utilisateur, le mot de passe et le serveur de connexion de votre registre de conteneurs Azure. Vous pouvez récupérer ces valeurs dans la section **Clés d’accès** de votre registre dans le portail Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Il se peut que vous receviez un avertissement de sécurité qui recommande l’utilisation de `--password-stdin`. Même s’il s’agit d’une bonne pratique recommandée pour les scénarios de production, elle sort du cadre de ce tutoriel. Pour plus d’informations, consultez les [informations de référence sur docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Générer et envoyer (push)

1. Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le nom du projet que vous voulez générer. Le nom par défaut est **AzureIotEdgeApp1**, et puisque vous créez un module Windows, l’extension doit être **Windows.Amd64**.

1. Sélectionnez **Créer et pousser les modules IoT Edge**.

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

1. Affichez les messages qui arrivent dans votre hub IoT. Les messages peuvent mettre un certain temps à arriver, car l’appareil IoT Edge doit recevoir son nouveau déploiement et démarrer tous les modules. Les modifications apportées au code CSharpModule doivent attendre jusqu’à ce que la température de la machine atteigne 25 degrés et que les messages soient envoyés. Le code ajoute également le type de message **Alerte** à chaque message qui atteint ce seuil de température.

   ![Capture d’écran de la fenêtre Sortie affichant les messages qui arrivent au hub IoT.](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Modifier le jumeau de module

Vous avez utilisé le jumeau de module de CSharpModule pour configurer le seuil de température sur 25 degrés. Vous pouvez utiliser le jumeau de module pour changer cette fonctionnalité sans devoir mettre à jour le code du module.

1. Dans Visual Studio, ouvrez le fichier *deployment.windows-amd64.json* 

   *N’ouvrez pas* le fichier *deployment.template*. Dans l’Explorateur de solutions, si vous ne voyez pas le manifeste de déploiement dans le fichier *config*, sélectionnez l’icône **Afficher tous les fichiers** dans la barre d’outils de l’Explorateur de solutions.

1. Recherchez le jumeau de module de CSharpModule et remplacez la valeur du paramètre **temperatureThreshold** par une température supérieure de 5 à 10 degrés par rapport à la dernière température signalée.

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
