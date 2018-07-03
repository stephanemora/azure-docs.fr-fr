---
title: Tutoriel C# d’Azure IoT Edge | Microsoft Docs
description: Ce tutoriel explique comment créer un module IoT Edge avec du code C# et le déployer sur un appareil Edge
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8cd4f87e690d64e1f69ef39f3422ebc73d79974a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053090"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Tutoriel : Développer un module C# IoT Edge et le déployer sur votre appareil simulé

Vous pouvez utiliser des modules IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce didacticiel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur. Vous utilisez l’appareil simulé IoT Edge que vous avez créé dans les didacticiels Déployer Azure IoT Edge sur un appareil simulé dans [Windows][lnk-tutorial1-win] et [Linux][lnk-tutorial1-lin]. Ce tutoriel vous montre comment effectuer les opérations suivantes :    

> [!div class="checklist"]
> * Utiliser le Visual Studio Code pour créer un module IoT Edge basé sur .NET core 2.0
> * Utiliser le Visual Studio Code et Docker pour créer une image docker et la publier dans votre registre 
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées


Le module IoT Edge que vous créez dans ce didacticiel filtre les données de température générées par votre appareil. Il envoie uniquement des messages en amont lorsque la température dépasse un seuil spécifié. Ce type d’analyse à la périphérie est utile pour réduire la quantité de données communiquées et stockées dans le cloud. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.


## <a name="prerequisites"></a>Prérequis

* L’appareil Azure IoT Edge que vous avez créé dans le démarrage rapide pour les appareils [Linux](quickstart-linux.md) ou [Windows](quickstart.md).
* Chaîne de connexion de clé primaire de l’appareil IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) pour Visual Studio Code.
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pour Visual Studio Code. 
* [SDK .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) sur votre ordinateur de développement. 


## <a name="create-a-container-registry"></a>Créer un registre de conteneur
Dans ce didacticiel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et de créer une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce didacticiel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Ce didacticiel utilise Azure Container Registry. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Azure Container Registry**.
2. Donnez un nom à votre registre, choisissez un abonnement et un groupe de ressources, puis définissez la référence (SKU) sur la valeur **De base**. 
3. Sélectionnez **Créer**.
4. Une fois que votre registre de conteneurs est créé, accédez à celui-ci, puis sélectionnez **Clés d’accès**. 
5. Basculez **Utilisateur administrateur** sur **Activer**.
6. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. Vous utiliserez ces valeurs plus tard dans le didacticiel, au moment de la publication de l’image Docker dans votre registre et au moment de l’ajout des informations d’identification du registre dans le runtime Edge. 

## <a name="create-an-iot-edge-module-project"></a>Créer un projet de module IoT Edge
Les étapes suivantes vous montrent comment créer un projet de module IoT Edge basé sur .NET core 2.0 à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.
1. Dans Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré** pour ouvrir le terminal intégré Visual Studio Code.
2. Sélectionnez **Affichage** > **Palette de commandes** pour ouvrir la palette de commandes VS Code. 
3. Dans la palette de commandes, tapez et exécutez la commande **Azure: Sign in** et suivez les instructions pour vous connecter à votre compte Azure. Si vous êtes déjà connecté, vous pouvez ignorer cette étape.
4. Dans la palette de commandes, tapez et exécutez la commande **Azure IoT Edge: New IoT Edge solution**. Dans la palette de commandes, spécifiez les informations suivantes pour créer votre solution : 
   1. Sélectionnez le dossier où vous souhaitez créer la solution. 
   2. Spécifiez un nom pour votre solution ou acceptez le nom par défaut **EdgeSolution**.
   3. Choisissez **Module C#** comme modèle du module. 
   4. Nommez votre module **CSharpModule**. 
   5. Spécifiez le registre Azure Container Registry que vous avez créé dans la section précédente comme dépôt d’images pour votre premier module. Remplacez **localhost:5000** par la valeur de serveur de connexion que vous avez copiée. La chaîne finale ressemble à **\<nom de registre\>.azurecr.io/csharpmodule**.
 
4. La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. Il existe un dossier **modules**, un dossier **.vscode**, un fichier modèle du manifeste de déploiement et un fichier **.env**. Ouvrez **modules** > **CSharpModule** > **Program.cs**.

5. En haut de l’espace de noms **CSharpModule**, ajoutez trois instructions `using` pour les types utilisés ultérieurement sur :

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

6. Ajoutez la variable `temperatureThreshold` à la classe **Program**. Cette variable définit la valeur que la température mesurée doit dépasser pour que les données soient envoyées à IoT Hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. Ajoutez les classes `MessageBody`, `Machine` et `Ambient` à la classe **Program**. Ces classes définissent le schéma attendu pour le corps des messages entrants.

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

8. Dans la méthode **Init**, le code crée et configure un objet **ModuleClient**. Cet objet permet au module de se connecter au runtime Azure IoT Edge local pour envoyer et recevoir des messages. La chaîne de connexion utilisée dans la méthode **Init** est fournie pour le module par le runtime IoT Edge. Après avoir créé le **ModuleClient**, le code lit la valeur TemperatureThreshold à partir des propriétés souhaitées du module jumeau et enregistre un rappel pour la réception de messages à partir du hub IoT Edge via le point de terminaison **input1**. Remplacez la méthode `SetInputMessageHandlerAsync` par une autre et ajoutez une méthode `SetDesiredPropertyUpdateCallbackAsync` pour les mises à jour de propriétés souhaitées. Pour ce faire, remplacez la dernière ligne de la méthode **Init** par le code suivant :

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine($"Property TemperatureThreshold not exist: {e.Message}"); 
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Ajoutez la méthode `onDesiredPropertiesUpdate` à la classe **Program**. Cette méthode reçoit des mises à jour sur les propriétés souhaitées à partir du double de module et met à jour la variable **temperatureThreshold** en conséquence. Tous les modules ont leur propre double, ce qui vous permet de configurer le code exécuté à l’intérieur d’un module directement à partir du cloud.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
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

10. Remplacez la méthode `PipeMessage` par la méthode `FilterMessages`. Cette méthode est appelée chaque fois que le module reçoit un message d’IoT Edge Hub. Il filtre les messages qui signalent des températures situées sous le seuil de température défini via le double de module. Il ajoute également la propriété **MessageType** au message avec la valeur définie sur **Alerte**. 

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

            // Get message body
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

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. Enregistrez ce fichier.

## <a name="build-your-iot-edge-solution"></a>Générer votre solution IoT Edge

Dans la section précédente vous avez créé une solution IoT Edge et ajouté du code à CSharpModule qui permettra de filtrer les messages où la température de l’ordinateur signalée est inférieure au seuil acceptable. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs. 

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal intégré Visual Studio Code afin de pouvoir envoyer votre image de module à ACR : 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilisez le nom d’utilisateur, le mot de passe et le serveur de connexion que vous avez copiés à partir de votre registre de conteneurs Azure dans la première section. Ou récupérez-les depuis la section **Clés d’accès** de votre registre dans le portail Azure.

2. Dans l’Explorateur VS Code, ouvrez le fichier **deployment.template.json** dans votre espace de travail de solution IoT Edge. Ce fichier indique au `$edgeAgent` de déployer les deux modules : **tempSensor** et **CSharpModule**. La valeur `CSharpModule.image` est définie sur une version amd64 Linux de l’image. Pour en savoir plus sur les manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

3. Dans le fichier **deployment.template.json**, il existe une section **registryCredentials** qui stocke vos informations d’identification du registre Docker. Les paires nom d’utilisateur et mot de passe réelles sont stockées dans le fichier .env qui est ignoré par git.

4. Ajoutez le jumeau de module CSharpModule au manifeste de déploiement. Insérez le contenu JSON suivant en bas de la section `moduleContent`, après le jumeau de module `$edgeHub` : 
    ```json
        "CSharpModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Enregistrez ce fichier.
5. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier **deployment.template.json** et sélectionnez **Générer la solution IoT Edge**. 

Quand vous indiquez à Visual Studio Code de générer votre solution, il extrait d’abord les informations dans le modèle de déploiement et génère un fichier `deployment.json` dans un nouveau dossier **config**. Il exécute ensuite deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, mettent `CSharpModule.dll` en conteneur, puis l’envoient au registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution. 

Vous pouvez afficher l’adresse complète de l’image conteneur avec la balise dans le terminal intégré de VS Code. L’adresse de l’image est créée à partir d’informations contenues dans le fichier `module.json`, au format **\<référentiel\> :\<version\>-\<plateforme\>**. Pour ce tutoriel, elle doit ressembler à **registryname.azurecr.io/csharpmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Déployer et exécuter la solution

1. Configurez l’extension Azure IoT Toolkit avec la chaîne de connexion pour votre IoT hub : 
    1. Ouvrez l’explorateur VS Code en sélectionnant **Affichage** > **Explorateur**. 
    2. Dans l’explorateur, cliquez sur **APPAREILS AZURE IOT HUB**, puis cliquez sur **...**. Cliquez sur **Sélectionner IoT Hub**. Suivez les instructions pour vous connecter à votre compte Azure et choisissez votre hub IoT. 
       Notez que vous pouvez également effectuer la configuration en cliquant sur **Définition de la chaîne de connexion de l’IoT Hub**. Entrez la chaîne de connexion IoT Hub à laquelle se connecte votre appareil IoT Edge dans la fenêtre contextuelle.

2. Dans l’explorateur des appareils Azure IoT Hub, cliquez avec le bouton droit sur votre appareil IoT Edge, puis cliquez sur **Créer un déploiement pour un appareil IoT Edge**. Sélectionnez le fichier **deployment.json** dans le dossier **config**, puis cliquez sur **Sélectionner un manifeste de déploiement Edge**.

3. Cliquez sur le bouton Actualiser. Vous devez voir le nouveau module **CSharpModule** en cours d’exécution avec le module **TempSensor** ainsi que **$edgeAgent** et **$edgeHub**. 

## <a name="view-generated-data"></a>Afficher les données générées

1. Pour surveiller les données reçues par le hub IoT, cliquez sur **...**, puis sélectionnez **Démarrer l’analyse des messages D2C**.
2. Pour analyser le message D2C pour un appareil spécifique, cliquez avec le bouton droit sur l’appareil dans la liste, puis sélectionnez **Démarrer l’analyse des messages D2C**.
3. Pour arrêter la surveillance des données, exécutez la commande **Azure IoT Hub: Stop monitoring D2C message** dans la palette de commandes. 
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
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Pour supprimer un groupe de ressources entier par nom :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et cliquez sur **Groupes de ressources**.

2. Dans la zone de texte **Filtrer par nom...**, saisissez le nom du groupe de ressources contenant votre IoT Hub. 

3. À droite de votre groupe de ressources dans la liste des résultats, cliquez sur **...**, puis sur **Supprimer le groupe de ressources**.

4. Il vous sera demandé de confirmer la suppression du groupe de ressources. Saisissez de nouveau le nom de votre groupe de ressources pour confirmer, puis cliquez sur **Supprimer**. Après quelques instants, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.



## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Quand vous êtes prêt à créer vos propres modules, vous pouvez découvrir comment [développer un module C# avec Azure IoT Edge pour Visual Studio Code](how-to-develop-csharp-module.md). Vous pouvez passer aux tutoriels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier « en périphérie ».

> [!div class="nextstepaction"]
> [Stocker des données en périphérie avec les bases de données SQL Server](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
