---
title: Tutoriel C# d’Azure IoT Edge | Microsoft Docs
description: Ce tutoriel explique comment créer un module IoT Edge avec un code C# et le déployer sur un appareil Edge.
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 991113b4e3e501d6d058a83baa795a5d7cbaa585
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439677"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Tutoriel : Développer un module C# IoT Edge et le déployer sur votre appareil simulé

Vous pouvez utiliser des modules Azure IoT Edge pour déployer un code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce tutoriel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur. Vous utilisez l’appareil IoT Edge simulé que vous avez créé dans les tutoriels Déployer Azure IoT Edge sur un appareil simulé sous [Windows][lnk-tutorial1-win] ou [Linux][lnk-tutorial1-lin]. Ce tutoriel vous montre comment effectuer les opérations suivantes :    

> [!div class="checklist"]
> * Utilisez Visual Studio Code pour créer un module IoT Edge basé sur le SDK .NET Core 2.0.
> * Utilisez Visual Studio Code et Docker pour créer une image Docker et la publier dans votre registre.
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées


Le module IoT Edge que vous créez dans ce didacticiel filtre les données de température générées par votre appareil. Il envoie uniquement des messages en amont lorsque la température dépasse un seuil spécifié. Ce type d’analyse à la périphérie est utile pour réduire la quantité de données communiquées et stockées dans le cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge :

* Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle comme un appareil Edge, en suivant les étapes décrites dans le Guide de démarrage rapide pour [Linux](quickstart-linux.md) ou pour les [Appareils Windows](quickstart.md).

Ressources cloud :

* Un niveau standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure. 

Ressources de développement :

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pour Visual Studio Code. 
* [SDK .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/)


## <a name="create-a-container-registry"></a>Créer un registre de conteneur
Dans ce tutoriel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et de créer une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce tutoriel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Ce didacticiel utilise Azure Container Registry. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Azure Container Registry**.
2. Donnez un nom à votre registre, choisissez un abonnement et un groupe de ressources, puis définissez la référence (SKU) sur la valeur **De base**. 
3. Sélectionnez **Créer**.
4. Une fois votre registre de conteneurs créé, accédez à celui-ci, puis sélectionnez **Clés d’accès**. 
5. Basculez **Utilisateur administrateur** sur **Activer**.
6. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. Vous utiliserez ces valeurs plus tard dans le tutoriel pour publier l’image Docker dans votre registre et ajouter les informations d’identification du registre dans le runtime Azure IoT Edge. 

## <a name="create-an-iot-edge-module-project"></a>Créer un projet de module IoT Edge
Les étapes suivantes montrent comment créer un projet de module IoT Edge basé sur le SDK .NET Core 2.0 à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Palette de commandes** pour ouvrir la palette de commandes VS Code. 
2. Dans la palette de commandes, entrez et exécutez la commande **Azure: Sign in** (Azure : connexion) et suivez les instructions pour vous connecter à votre compte Azure. Si vous êtes déjà connecté, vous pouvez ignorer cette étape.
3. Dans la palette de commandes, entrez et exécutez la commande **Azure IoT Edge : nouvelle solution IoT Edge**. Dans la palette de commandes, spécifiez les informations suivantes pour créer votre solution : 

   1. Sélectionnez le dossier où vous souhaitez créer la solution. 
   2. Spécifiez un nom pour votre solution ou acceptez le nom par défaut **EdgeSolution**.
   3. Choisissez **Module C#** comme modèle du module. 
   4. Remplacez le nom de module par défaut par **CSharpModule**. 
   5. Spécifiez le registre Azure Container Registry que vous avez créé dans la section précédente comme référentiel d’images pour votre premier module. Remplacez **localhost:5000** par la valeur de serveur de connexion que vous avez copiée. La chaîne finale ressemble à \<nom de registre\>.azurecr.io/csharpmodule.

4.  La fenêtre VS Code charge votre espace de travail de solution IoT Edge : le dossier modules, un dossier \.vscode, un fichier de modèle de manifeste de déploiement et un fichier \.env. Dans l’Explorateur VS Code, ouvrez **modules** > **CSharpModule** > **Program.cs**.

5. En haut de l’espace de noms **CSharpModule**, ajoutez trois instructions **using** pour les types utilisés ultérieurement :

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

6. Ajoutez la variable **temperatureThreshold** à la classe **Program**. Cette variable définit la valeur que la température mesurée doit dépasser pour que les données soient envoyées à IoT Hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. Ajoutez les classes **MessageBody**, **Machine**, et **Ambient** à la classe **Program**. Ces classes définissent le schéma attendu pour le corps des messages entrants.

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

8. Dans la méthode **Init**, le code crée et configure un objet **ModuleClient**. Cet objet permet au module de se connecter au runtime Azure IoT Edge local pour envoyer et recevoir des messages. La chaîne de connexion utilisée dans la méthode **Init** est fournie au module par le runtime IoT Edge. Après avoir créé le **ModuleClient**, le code lit la valeur **temperatureThreshold** à partir des propriétés souhaitées du jumeau de module. Le code enregistre un rappel pour recevoir des messages du hub IoT Edge via le point de terminaison **input1**. Remplacez la méthode **SetInputMessageHandlerAsync** par une nouvelle, et ajoutez une méthode **SetDesiredPropertyUpdateCallbackAsync** pour les mises à jour des propriétés souhaitées. Pour ce faire, remplacez la dernière ligne de la méthode **Init** par le code suivant :

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine($"Property TemperatureThreshold not exist: {e.Message}"); 
    }

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Ajoutez la méthode **onDesiredPropertiesUpdate** à la classe **Program**. Cette méthode reçoit des mises à jour sur les propriétés souhaitées à partir du double de module et met à jour la variable **temperatureThreshold** en conséquence. Tous les modules ont leur propre module jumeau, ce qui vous permet de configurer le code exécuté à l’intérieur d’un module directement à partir du cloud.

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

10. Remplacez la méthode **PipeMessage** par la méthode **FilterMessages**. Cette méthode est appelée chaque fois que le module reçoit un message d’IoT Edge Hub. Il filtre les messages qui signalent des températures situées sous le seuil de température défini via le double de module. Il ajoute également la propriété **MessageType** au message avec la valeur définie sur **Alerte**. 

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

11. Enregistrez ce fichier.

## <a name="build-your-iot-edge-solution"></a>Générer votre solution IoT Edge

Dans la section précédente, vous avez créé une solution IoT Edge et ajouté un code à **CSharpModule** pour filtrer les messages où la température de la machine signalée est inférieure au seuil acceptable. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs. 

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal intégré Visual Studio Code. Vous pouvez ensuite transmettre votre image de module au registre Azure Container Registry.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilisez le nom d’utilisateur, le mot de passe et le serveur de connexion que vous avez copiés à partir de votre registre Azure Container Registry dans la première section. Vous pouvez également récupérer ces valeurs depuis la section **Clés d’accès** de votre registre dans le portail Azure.

2. Dans l’Explorateur Visual Studio Code, ouvrez le fichier deployment.template.json dans votre espace de travail de solution IoT Edge. Ce fichier indique au **$edgeAgent** de déployer les deux modules : **tempSensor** et **CSharpModule**. La valeur **CSharpModule.image** est définie sur une version amd64 Linux de l’image. 

   Vérifiez que le modèle porte le nom de module approprié, pas la valeur par défaut **SampleModule** nom que vous avez modifié lorsque vous avez créé la solution IoT Edge.

   Pour en savoir plus sur les manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

3. Dans le fichier deployment.template.json, la section **registryCredentials** qui stocke vos informations d’identification du registre Docker. Les paires nom d’utilisateur et mot de passe réelles sont stockées dans le fichier .env qui est ignoré par Git.  

4. Ajoutez le jumeau de module **CSharpModule** au manifeste de déploiement. Insérez le contenu JSON suivant en bas de la section **moduleContent**, après le jumeau de module **$edgeHub** : 
    ```json
        "CSharpModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Enregistrez ce fichier.

5. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier deployment.template.json et sélectionnez **Générer la solution IoT Edge**. 

Quand vous indiquez à Visual Studio Code de générer votre solution, il extrait d’abord les informations dans le modèle de déploiement et génère un fichier deployment.json dans un nouveau dossier nommé **config**. Il exécute ensuite deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, mettent votre module CSharpModule.dll en conteneur, puis envoient le code au registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution. 

Vous pouvez afficher l’adresse complète de l’image conteneur avec la balise dans le terminal intégré de VS Code. L’adresse de l’image est créée à partir d’informations contenues dans le fichier module.json, au format \<référentiel\> : \<version\>-\<plateforme\>. Pour ce tutoriel, elle doit ressembler à registryname.azurecr.io/csharpmodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Déployer et exécuter la solution

1. Configurez l’extension Azure IoT Toolkit avec la chaîne de connexion pour votre IoT hub : 

    1. Ouvrez l’explorateur VS Code en sélectionnant **Affichage** > **Explorateur**.

    1. Dans l’explorateur, sélectionnez **Appareils Azure IoT Hub**, sélectionnez les points de suspension (**...**), puis choisissez **Sélectionner IoT Hub**. Suivez les instructions pour vous connecter à votre compte Azure et choisissez votre hub IoT. 

       > [!Note]
       > Vous pouvez également effectuer la configuration en choisissant **Définir la chaîne de connexion IoT Hub**. Entrez la chaîne de connexion IoT Hub à laquelle se connecte votre appareil IoT Edge dans la fenêtre contextuelle.

2. Dans l’explorateur des appareils Azure IoT Hub, cliquez avec le bouton droit sur votre appareil IoT Edge, puis sélectionnez **Créer un déploiement pour un appareil IoT Edge**. Sélectionnez le fichier deployment.json dans le dossier config, puis cliquez sur **Sélectionner un manifeste de déploiement Edge**.

3. Actualisez la section **Appareils Azure IoT Hub**. Vous devez voir le nouveau module **CSharpModule** en cours d’exécution avec le module **TempSensor** ainsi que **$edgeAgent** et **$edgeHub**. 

## <a name="view-generated-data"></a>Afficher les données générées

1. Pour surveiller les données reçues par le hub IoT, sélectionnez les points de suspension (**...**), puis **Démarrer l’analyse des messages D2C**.
2. Pour analyser le message D2C pour un appareil spécifique, cliquez avec le bouton droit sur l’appareil dans la liste, puis sélectionnez **Démarrer l’analyse des messages D2C**.
3. Pour arrêter l’analyse des données, exécutez la commande **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub : arrêter l’analyse des messages D2C) dans la palette de commandes. 
4. Pour afficher ou mettre à jour le jumeau de module, cliquez avec le bouton droit sur le module dans la liste, puis sélectionnez **Modifier le jumeau de module**. Pour mettre à jour le jumeau de module, enregistrez le fichier JSON du jumeau, cliquez avec le bouton droit sur la zone de l’éditeur et sélectionnez **Mettre à jour le jumeau de module**.
5. Pour afficher les journaux Docker, installez [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pour VS Code. Vous pouvez trouver localement vos modules en cours d’exécution dans l’Explorateur Docker. Dans le menu contextuel, cliquez sur **Afficher les journaux** pour les afficher dans un terminal intégré.
 
## <a name="clean-up-resources"></a>Supprimer les ressources 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser.

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter les frais. 

> [!IMPORTANT]
> La suppression des ressources et des groupes de ressources Azure est irréversible. Une fois supprimés, le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement les mauvaises ressources ou le mauvais groupe de ressources. Si vous avez créé le hub IoT à l’intérieur d’un groupe de ressources existant qui contient des ressources que vous souhaitez conserver, supprimez uniquement la ressource du hub IoT au lieu de supprimer le groupe de ressources.
>

Pour supprimer le hub IoT uniquement, exécutez la commande suivante en utilisant le nom de votre hub et le nom du groupe de ressources :

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


Pour supprimer un groupe de ressources entier par nom :

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez **Groupes de ressources**.

2. Dans la zone de texte **Filtrer par nom**, entrez le nom du groupe de ressources contenant votre hub IoT. 

3. À droite de votre groupe de ressources, dans la liste des résultats, sélectionnez les points de suspension (**...**), puis **Supprimer le groupe de ressources**.

4. Vous êtes invité à confirmer la suppression du groupe de ressources. Entrez à nouveau le nom de votre groupe de ressources à confirmer, puis sélectionnez **Supprimer**. Après quelques instants, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.



## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge contenant le code pour filtrer les données brutes générées par votre appareil IoT Edge. Quand vous êtes prêt à créer vos propres modules, vous pouvez découvrir comment [développer un module C# avec Azure IoT Edge pour Visual Studio Code](how-to-develop-csharp-module.md). Vous pouvez passer aux didacticiels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier « en périphérie ».

> [!div class="nextstepaction"]
> [Stocker des données en périphérie avec les bases de données SQL Server](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
