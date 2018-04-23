---
title: Déployer une fonction Azure avec Azure IoT Edge | Microsoft Docs
description: Déployer une fonction Azure en tant que module d’un appareil Edge
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: f1c6b5cd07752c6b29234a365b3298d76b639b3a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Déployer une fonction Azure en tant que module IoT Edge - version préliminaire
Vous pouvez utiliser Azure Functions pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce didacticiel vous guide à travers la création et le déploiement d’une fonction Azure qui filtre les données de capteur sur l’appareil simulé IoT Edge que vous avez créé dans les didacticiels Déployer Azure IoT Edge sur un appareil simulé pour [Windows][lnk-tutorial1-win]ou [Linux][lnk-tutorial1-lin]. Ce tutoriel vous montre comment effectuer les opérations suivantes :     

> [!div class="checklist"]
> * Utiliser Visual Studio Code pour créer une fonction Azure
> * Utiliser le Visual Studio Code et Docker pour créer une image docker et la publier dans votre registre 
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées


La fonction Azure que vous créez dans ce didacticiel filtre les données de température générées par votre appareil et envoie uniquement des messages en amont vers Azure IoT Hub lorsque la température dépasse un seuil spécifié. 

## <a name="prerequisites"></a>Prérequis


* L’appareil Azure IoT Edge que vous avez créé dans le démarrage rapide ou le didacticiel précédent.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Docker](https://docs.docker.com/engine/installation/). L’édition Community (CE) pour votre plateforme est suffisante pour ce didacticiel. 
* [Kit de développement logiciel (SDK) .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Créer un registre de conteneur
Dans ce didacticiel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et de créer une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce didacticiel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Ce didacticiel utilise Azure Container Registry. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Azure Container Registry**.
2. Donnez un nom à votre registre, choisissez un abonnement et un groupe de ressources, puis définissez la référence (SKU) sur la valeur **De base**. 
3. Sélectionnez **Créer**.
4. Une fois que votre registre de conteneurs est créé, accédez à celui-ci, puis sélectionnez **Clés d’accès**. 
5. Basculez **Utilisateur administrateur** sur **Activer**.
6. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. Vous utiliserez ces valeurs plus loin dans le didacticiel. 

## <a name="create-a-function-project"></a>Créer un projet Function
Les étapes suivantes vous montrent comment créer une fonction IoT Edge à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.
1. Ouvrez Visual Studio Code.
2. Pour ouvrir le terminal intégré Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré**.
3. Pour installer (ou mettre à jour) le modèle **AzureIoTEdgeFunction** dans dotnet, exécutez la commande suivante dans le terminal intégré :

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Créez un projet pour le nouveau module. La commande suivante crée le dossier du projet, **FilterFunction**, avec le référentiel du conteneur. Le deuxième paramètre doit être au format `<your container registry name>.azurecr.io` si vous utilisez le registre de conteneurs Azure. Dans le dossier de travail actif, entrez la commande suivante :

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction -r <your container registry address>/filterfunction
    ```

3. Sélectionnez **fichier** > **Ouvrir le dossier**, puis accédez au dossier **FilterFunction** et ouvrez le projet dans Visual Studio Code.
4. Dans l’Explorateur de Visual Studio Code, développez le dossier **EdgeHubTrigger-Csharp**, puis ouvrez le fichier **run.csx**.
5. Remplacez le contenu du fichier par le code suivant :

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
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

11. Enregistrez le fichier .

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Créer une image Docker et la publier dans votre registre

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal intégré VS Code : 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Pour trouver le nom d’utilisateur, le serveur de connexion et le mot de passe à utiliser dans cette commande, accédez au [portail Azure] (https://portal.azure.com)). À partir de **Toutes les ressources**, cliquez sur la vignette de votre Azure Container Registry pour ouvrir ses propriétés, puis cliquez sur **Clés d’accès**. Copiez les valeurs dans les champs **Nom d’utilisateur**, **Mot de passe** et **Serveur de connexion**. 

2. Ouvrez **module.json**. Si vous le souhaitez, vous pouvez mettre à jour le `"version"` vers eg. **« 1.0 »**. Le nom du référentiel que vous avez entré dans le paramètre `-r` de `dotnet new aziotedgefunction` s’affiche également.

3. Enregistrez le fichier **module.json**.

4. Dans l’explorateur VS Code, cliquez avec le bouton droit sur le fichier **module.json**, puis sur **Générer et envoyer (push) une image Docker de module IoT Edge**. Dans la liste déroulante contextuelle en haut de la fenêtre VS Code, sélectionnez votre plateforme de conteneur : **amd64** pour un conteneur Linux ou **windows-amd64** pour un conteneur Windows. VS Code place alors vos codes de fonction dans un conteneur et l’envoie (push) dans le registre de conteneurs spécifié.

5. Vous pouvez récupérer l’adresse complète de l’image conteneur avec la balise dans le terminal intégré de VS Code. Pour plus d’informations sur la définition de build et de push, consultez le fichier `module.json`.

## <a name="add-registry-credentials-to-your-edge-device"></a>Ajouter des informations d’identification du registre à votre appareil Edge
Ajoutez les informations d’identification pour votre registre au runtime Edge sur l’ordinateur sur lequel vous exécutez votre appareil Edge. Cela donne l’accès au runtime pour extraire le conteneur. 

- Pour Windows, exécutez la commande ci-dessous :
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Pour Linux, exécutez la commande ci-dessous :
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Exécuter la solution

1. Accédez à votre IoT Hub dans le **portail Azure**.
2. Accédez à **IoT Edge (version préliminaire)** et sélectionnez votre appareil IoT Edge.
1. Sélectionnez **Définir modules**. 
2. Si vous avez déjà déployé le module **tempSensor** sur cet appareil, il a peut être été renseigné automatiquement. Sinon, suivez ces étapes pour l’ajouter :
    1. Sélectionnez **Ajouter un module IoT Edge**.
    2. Dans le champ **Nom**, entrez `tempSensor`.
    3. Dans le champ **URI de l’image**, entrez `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Laissez les autres paramètres inchangés et cliquez sur **Enregistrer**.
1. Ajoutez le module **filterfunction**.
    1. Sélectionnez à nouveau **Ajouter module IoT Edge**.
    2. Dans le champ **Nom**, entrez `filterFunction`.
    3. Dans le champ **URI de l’image**, saisissez l’adresse de votre image, par exemple `<your container registry address>/filterfunction:0.0.1-amd64`. Vous trouverez l’adresse complète de l’image dans la section précédente.
    74. Cliquez sur **Enregistrer**.
2. Cliquez sur **Suivant**.
3. À l’étape **Spécifier des itinéraires**, copiez le JSON ci-dessous dans la zone de texte. Le premier itinéraire transporte les messages du capteur de température au module de filtre par le biais du point de terminaison « input1 ». Le deuxième itinéraire transporte les messages du module de filtre à IoT Hub. Dans cet itinéraire, `$upstream` est une destination spéciale qui indique à Edge Hub d’envoyer les messages à IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Cliquez sur **Suivant**.
5. À l’étape **Vérifier le modèle**, cliquez sur **Envoyer**. 
6. Revenez à la page de détails de l’appareil IoT Edge et cliquez sur **Actualiser**. Vous devez voir le nouveau module **filterfunction** en cours d’exécution avec le module **tempSensor** et le **runtime IoT Edge**. 

## <a name="view-generated-data"></a>Afficher les données générées

Pour pouvoir surveiller les messages appareil vers cloud envoyés par votre appareil IoT Edge à votre IoT Hub :
1. Configurez l’extension Azure IoT Toolkit avec la chaîne de connexion pour votre IoT hub : 
    1. Dans le portail Azure, accédez à votre hub IoT et sélectionnez **Stratégies d’accès partagé**. 
    2. Sélectionnez **iothubowner**, puis copiez la valeur de **Clé primaire de la chaîne de connexion**.
    3. Dans l’Explorateur de Visual Studio Code, cliquez sur **APPAREILS IOT HUB**, puis sur **...**. 
    4. Sélectionnez **Définir la chaîne de connexion IoT Hub** et entrez la chaîne de connexion IoT Hub dans la fenêtre contextuelle. 

2. Pour surveiller les données reçues par IoT Hub, sélectionnez **Affichage** > **Palette de commandes** et recherchez **IoT: Start monitoring D2C message**. 
3. Pour arrêter de surveiller les données, utilisez la commande **IoT: Stop monitoring D2C message** dans la palette de commandes. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une fonction Azure qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Pour continuer l’exploration d’Azure IoT Edge, apprenez à utiliser un appareil IoT Edge en tant que passerelle. 

> [!div class="nextstepaction"]
> [Créer un périphérique de passerelle IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
