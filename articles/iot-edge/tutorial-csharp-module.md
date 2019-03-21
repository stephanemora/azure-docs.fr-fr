---
title: Didacticiel pour créer un module C# personnalisé - Azure IoT Edge | Microsoft Docs
description: Ce tutoriel explique comment créer un module IoT Edge avec un code C# et le déployer sur un appareil Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: f4afd83e31cf724e734b4e86045f8404e65753c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58088024"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Didacticiel : Développer un module C# IoT Edge et le déployer sur votre appareil simulé

Vous pouvez utiliser des modules Azure IoT Edge pour déployer un code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce tutoriel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur. Vous utiliserez l’appareil IoT Edge simulé que vous avez créé dans les tutoriels Déployer Azure IoT Edge sur un appareil simulé sous [Windows](quickstart.md) ou [Linux](quickstart-linux.md). Ce tutoriel vous montre comment effectuer les opérations suivantes :    

> [!div class="checklist"]
> * Utilisez Visual Studio Code pour créer un module IoT Edge basé sur le kit SDK .NET Core 2.1.
> * Utiliser Visual Studio Code et Docker pour créer une image Docker et la publier dans votre registre
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées

>[!NOTE]
>Vous pouvez également utiliser [Visual Studio 2017 pour développer, déboguer et déployer des modules IoT Edge](how-to-visual-studio-develop-csharp-module.md).

Le module IoT Edge que vous créez dans ce didacticiel filtre les données de température générées par votre appareil. Il envoie uniquement des messages en amont lorsque la température dépasse un seuil spécifié. Ce type d’analyse à la périphérie est utile pour réduire la quantité de données communiquées et stockées dans le cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge :

* Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle comme un appareil Edge, en suivant les étapes décrites dans le Guide de démarrage rapide pour [Linux](quickstart-linux.md) ou pour les [Appareils Windows](quickstart.md).

Ressources cloud :

* Un niveau gratuit ou standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure. 

Ressources de développement :

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Outils Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pour Visual Studio Code. 
* [SDK .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/)


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
Les étapes suivantes montrent comment créer un projet de module IoT Edge basé sur le SDK .NET Core 2.0 à l’aide de Visual Studio Code et des outils Azure IoT.

### <a name="create-a-new-solution"></a>Créer une solution

Créez un modèle de solution C# que vous pouvez personnaliser avec votre propre code. 

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Palette de commandes** pour ouvrir la palette de commandes VS Code. 

2. Dans la palette de commandes, entrez et exécutez la commande **Azure: Sign in** et suivez les instructions pour vous connecter à votre compte Azure. Si vous êtes déjà connecté, vous pouvez ignorer cette étape.

3. Dans la palette de commandes, entrez et exécutez la commande **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge : Nouvelle solution IoT Edge). Suivez les invites de la palette de commandes pour créer votre solution.

   | Champ | Valeur |
   | ----- | ----- |
   | Sélectionner le dossier | Choisissez l’emplacement sur votre machine de développement pour que VS Code crée les fichiers de la solution. |
   | Provide a solution name (Nommer la solution) | Entrez un nom descriptif pour votre solution ou acceptez le nom par défaut (**EdgeSolution**). |
   | Select module template (Sélectionner un modèle de module) | Choisissez **Module C#**. |
   | Provide a module name (Nommer le module) | Nommez votre module **CSharpModule**. |
   | Provide Docker image repository for the module (Indiquer le référentiel d’images Docker pour le module) | Un référentiel d’images comprend le nom de votre registre de conteneurs et celui de votre image conteneur. L’image conteneur est préremplie avec le nom que vous avez indiqué à la dernière étape. Remplacez **localhost:5000** par la valeur de serveur de connexion de votre registre de conteneurs Azure. Vous pouvez récupérer le serveur de connexion à partir de la page Vue d’ensemble de votre registre de conteneurs dans le Portail Azure. <br><br>Le référentiel d’images final ressemble à ceci : \<nom_registre\>.azurecr.io/csharpmodule. |
 
   ![Fourniture du référentiel d’images Docker](./media/tutorial-csharp-module/repository.png)

La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. L’espace de travail de la solution contient cinq composants de niveau supérieur. Le dossier **modules** contient le code C# de votre module, ainsi que les fichiers Docker pour la création de votre module comme image conteneur. Le fichier **\.env** stocke les informations d’identification de votre registre de conteneurs. Le fichier **deployment.template.json** contient les informations utilisées par le runtime IoT Edge pour déployer des modules sur un appareil. Conteneurs de fichiers **deployment.debug.template.json** de la version debug des modules. Dans le cadre de ce didacticiel, vous ne modifierez pas le dossier **\.vscode** ni le fichier **\.gitignore**.

Si vous n’avez pas spécifié de registre de conteneurs lors de la création de votre solution, mais accepté la valeur localhost:5000 par défaut, vous n’aurez pas de fichier \.env. 

<!--
   ![C# solution workspace](./media/tutorial-csharp-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Ajouter les informations d’identification de votre registre

Le fichier d’environnement stocke les informations d’identification de votre registre de conteneurs et les partage avec le runtime IoT Edge. Le runtime a besoin de ces informations d’identification pour extraire vos images privées sur l’appareil IoT Edge. 

1. Dans l’Explorateur VS Code, ouvrez le fichier .env. 
2. Mettre à jour les champs avec les valeurs de **nom d’utilisateur** et de **mot de passe** que vous avez copiées à partir de votre registre de conteneurs Azure. 
3. Enregistrez ce fichier. 

### <a name="update-the-module-with-custom-code"></a>Mettre à jour le module avec du code personnalisé

1. Dans l’Explorateur VS Code, ouvrez **modules** > **CSharpModule** > **Program.cs**.

2. En haut de l’espace de noms **CSharpModule**, ajoutez trois instructions **using** pour les types utilisés ultérieurement :

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Ajoutez la variable **temperatureThreshold** à la classe **Program**. Cette variable définit la valeur que la température mesurée doit dépasser pour que les données soient envoyées à IoT Hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Ajoutez les classes **MessageBody**, **Machine**, et **Ambient** à la classe **Program**. Ces classes définissent le schéma attendu pour le corps des messages entrants.

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

5. Dans la méthode **Init**, le code crée et configure un objet **ModuleClient**. Cet objet permet au module de se connecter au runtime Azure IoT Edge local pour envoyer et recevoir des messages. La chaîne de connexion utilisée dans la méthode **Init** est fournie au module par le runtime IoT Edge. Après avoir créé le **ModuleClient**, le code lit la valeur **temperatureThreshold** à partir des propriétés souhaitées du jumeau de module. Le code enregistre un rappel pour recevoir des messages du hub IoT Edge via le point de terminaison **input1**. Remplacez la méthode **SetInputMessageHandlerAsync** par une nouvelle, et ajoutez une méthode **SetDesiredPropertyUpdateCallbackAsync** pour les mises à jour des propriétés souhaitées. Pour ce faire, remplacez la dernière ligne de la méthode **Init** par le code suivant :

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Ajoutez la méthode **onDesiredPropertiesUpdate** à la classe **Program**. Cette méthode reçoit des mises à jour sur les propriétés souhaitées à partir du double de module et met à jour la variable **temperatureThreshold** en conséquence. Tous les modules ont leur propre module jumeau, ce qui vous permet de configurer le code exécuté à l’intérieur d’un module directement à partir du cloud.

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

7. Remplacez la méthode **PipeMessage** par la méthode **FilterMessages**. Cette méthode est appelée chaque fois que le module reçoit un message d’IoT Edge Hub. Il filtre les messages qui signalent des températures situées sous le seuil de température défini via le double de module. Il ajoute également la propriété **MessageType** au message avec la valeur définie sur **Alerte**. 

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
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await moduleClient.SendEventAsync("output1", filteredMessage);
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

8. Enregistrez le fichier Program.cs.

9. Dans l’Explorateur VS Code, ouvrez le fichier **deployment.template.json** dans votre espace de travail de solution IoT Edge. Ce fichier indique à l’agent IoT Edge quels modules déployer, dans cet exemple, **tempSensor** et **CSharpModule**, puis indique au hub IoT Edge comment router les messages entre eux. L’extension Visual Studio Code renseigne automatiquement la plupart des informations dont vous avez besoin dans le modèle de déploiement, mais vérifiez que tout est correct pour votre solution : 

   1. La plateforme par défaut d’IoT Edge est définie sur **amd64** dans la barre d’état Visual Studio Code, ce qui signifie que votre **CSharpModule** est défini sur la version amd64 Linux de l’image. Dans la barre d’état, remplacez la plateforme par défaut **amd64** par la plateforme **arm32v7** ou **windows-amd64**, si cela correspond à l’architecture de votre appareil IoT Edge. 

      ![Mettre à jour la plateforme de l’image du module](./media/tutorial-csharp-module/image-platform.png)

   2. Vérifiez que le modèle porte le nom de module approprié, pas la valeur par défaut **SampleModule** nom que vous avez modifié lorsque vous avez créé la solution IoT Edge.

   3. La section **registryCredentials** stocke vos informations d’identification de registre Docker, pour que l’agent IoT Edge puisse extraire l’image de votre module. Les paires nom d’utilisateur et mot de passe réelles sont stockées dans le fichier .env qui est ignoré par Git. Ajoutez vos informations d’identification au fichier .env si vous ne l’avez pas déjà fait.  

   4. Pour en savoir plus sur les manifestes de déploiement, consultez [Découvrir comment déployer des modules et établir des routes dans IoT Edge](module-composition.md).

10. Ajoutez le jumeau de module **CSharpModule** au manifeste de déploiement. Insérez le contenu JSON suivant en bas de la section **modulesContent**, après le jumeau de module **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Ajouter un jumeau de module au modèle de déploiement](./media/tutorial-csharp-module/module-twin.png)

11. Enregistrez le fichier deployment.template.json.


## <a name="build-your-iot-edge-solution"></a>Générer votre solution IoT Edge

Dans la section précédente, vous avez créé une solution IoT Edge et ajouté un code à **CSharpModule** pour filtrer les messages où la température de la machine signalée est inférieure au seuil acceptable. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs. 

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal intégré Visual Studio Code. Vous pouvez ensuite transmettre votre image de module au registre Azure Container Registry.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilisez le nom d’utilisateur, le mot de passe et le serveur de connexion que vous avez copiés à partir de votre registre Azure Container Registry dans la première section. Vous pouvez également récupérer ces valeurs depuis la section **Clés d’accès** de votre registre dans le portail Azure.

2. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier deployment.template.json et sélectionnez **Build and Push IoT Edge solution** (Générer et envoyer (push) la solution IoT Edge). 

Quand vous indiquez à Visual Studio Code de générer votre solution, il extrait d’abord les informations contenues dans le modèle de déploiement et génère un fichier deployment.json dans un nouveau dossier nommé **config**. Il exécute ensuite deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, mettent votre module CSharpModule.dll en conteneur, puis envoient le code au registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution. 

Vous pouvez afficher l’adresse complète de l’image conteneur avec la balise dans le terminal intégré de VS Code. L’adresse de l’image est créée à partir d’informations contenues dans le fichier module.json, au format \<référentiel\> : \<version\>-\<plateforme\>. Pour ce tutoriel, elle doit ressembler à registryname.azurecr.io/csharpmodule:0.0.1-amd64.

>[!TIP]
>Si vous recevez une erreur pendant la création et l’envoi (push) de votre module, effectuez les vérifications suivantes :
>* Vous êtes-vous connecté à Docker dans Visual Studio Code à l’aide des informations d’identification de votre registre de conteneurs ? Ces informations d’identification sont différentes de celles que vous utilisez pour vous connecter au portail Azure.
>* Votre référentiel de conteneurs est-il correct ? Ouvrez **modules** > **cmodule** > **module.json** et recherchez le champ **repository**. Le référentiel d’images doit se présenter ainsi : **\<nom_registre\>.azurecr.io/csharpmodule**. 
>* Les conteneurs que vous créez sont-ils du même type que ceux exécutés sur votre machine de développement ? Visual Studio Code crée par défaut des conteneurs Linux amd64. Si votre machine de développement exécute des conteneurs Windows ou des conteneurs Linux arm32v7, mettez à jour la plateforme sur la barre d’état bleue au bas de la fenêtre Visual Studio Code pour qu’elle corresponde à votre plateforme de conteneurs.

## <a name="deploy-and-run-the-solution"></a>Déployer et exécuter la solution

Dans l’article de démarrage rapide que vous avez utilisé pour configurer votre appareil IoT Edge, vous avez déployé un module à l’aide du Portail Azure. Vous pouvez également déployer des modules à l’aide de l’extension Azure IoT Hub Toolkit (anciennement Azure IoT Toolkit) pour Visual Studio Code. Vous disposez déjà d’un manifeste de déploiement préparé pour votre scénario, le fichier **deployment.json**. Il vous suffit alors de sélectionner l’appareil qui recevra le déploiement.

1. Dans la palette de commandes VS Code, exécutez **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub : Sélectionner IoT Hub). 

2. Choisissez l’abonnement et l’IoT Hub qui contiennent l’appareil IoT Edge que vous souhaitez configurer. 

3. Dans l’Explorateur VS Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). 

4. Cliquez avec le bouton droit sur le nom de votre appareil IoT Edge, puis sélectionnez **Create Deployment for Single Device** (Créer un déploiement pour un seul appareil). 

   ![Créer un déploiement pour un seul appareil](./media/tutorial-csharp-module/create-deployment.png)

5. Sélectionnez le fichier **deployment.json** dans le dossier **config**, puis cliquez sur **Sélectionner un manifeste de déploiement Edge**. N’utilisez pas le fichier deployment.template.json. 

6. Cliquez sur le bouton Actualiser. Vous devez voir le nouveau module **CSharpModule** en cours d’exécution avec le module **TempSensor** ainsi que **$edgeAgent** et **$edgeHub**.  

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
5. Pour afficher les journaux Docker, installez [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pour VS Code. Vous pouvez trouver localement vos modules en cours d’exécution dans l’Explorateur Docker. Dans le menu contextuel, cliquez sur **Afficher les journaux** pour les afficher dans un terminal intégré.
 
## <a name="clean-up-resources"></a>Supprimer des ressources 

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser. Vous pouvez également continuer à utiliser le même appareil IoT Edge comme appareil de test. 

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter les frais. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge contenant le code pour filtrer les données brutes générées par votre appareil IoT Edge. Quand vous êtes prêt à créer vos propres modules, vous pouvez découvrir comment [développer un module C# avec Azure IoT Edge pour Visual Studio Code](how-to-develop-csharp-module.md). Vous pouvez passer aux didacticiels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier « en périphérie ».

> [!div class="nextstepaction"]
> [Stocker des données en périphérie avec les bases de données SQL Server](tutorial-store-data-sql-server.md)
