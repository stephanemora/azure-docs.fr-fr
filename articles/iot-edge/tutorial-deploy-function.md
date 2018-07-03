---
title: Déployer une fonction Azure avec Azure IoT Edge | Microsoft Docs
description: Déployer une fonction Azure en tant que module d’un appareil Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0445817f9ff403156025e38a1e14a3892a9a292b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031190"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules---preview"></a>Tutoriel : Déployer Azure Functions comme modules IoT Edge - préversion

Vous pouvez utiliser Azure Functions pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce didacticiel vous guide à travers la création et le déploiement d’une fonction Azure qui filtre les données de capteur sur l’appareil simulé IoT Edge que vous avez créé dans les didacticiels Déployer Azure IoT Edge sur un appareil simulé pour [Windows][lnk-tutorial1-win]ou [Linux][lnk-tutorial1-lin]. Ce tutoriel vous montre comment effectuer les opérations suivantes :     

> [!div class="checklist"]
> * Utiliser Visual Studio Code pour créer une fonction Azure
> * Utiliser VS Code et Docker pour créer une image Docker et la publier sur un registre de conteneurs 
> * Déployer le module du registre de conteneurs sur votre appareil IoT Edge
> * Afficher les données filtrées

>[!NOTE]
>Les modules Azure Functions sur Azure IoT Edge sont en [préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) publique. 

La fonction Azure que vous créez dans ce didacticiel filtre les données de température générées par votre appareil et envoie uniquement des messages en amont vers Azure IoT Hub lorsque la température dépasse un seuil spécifié. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour tester le module Functions que vous créez dans ce tutoriel, vous avez besoin d’un appareil IoT Edge. Vous pouvez utiliser l’appareil que vous avez configuré dans le guide de démarrage rapide [Linux](quickstart-linux.md) ou [Windows](quickstart.md).

Vérifiez que les prérequis suivants sont remplis sur votre ordinateur de développement : 
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) pour Visual Studio Code.
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pour Visual Studio Code. 
* [SDK .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) sur votre ordinateur de développement. 

## <a name="create-a-container-registry"></a>Créer un registre de conteneur
Dans ce didacticiel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et de créer une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce didacticiel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Ce didacticiel utilise Azure Container Registry. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Container Registry**.

    ![Créer un registre de conteneurs](./media/tutorial-deploy-function/create-container-registry.png)

2. Donnez un nom à votre registre et choisissez un abonnement.
3. Pour le groupe de ressources, il est recommandé d’utiliser le même nom de groupe de ressources qui contient votre hub IoT. En conservant toutes les ressources dans le même groupe, vous pouvez les gérer ensemble. Par exemple, si vous supprimez le groupe de ressources utilisé pour le test, vous supprimez également toutes les ressources de test contenues dans le groupe. 
4. Définissez la référence (SKU) sur la valeur **De base** et basculez **Utilisateur administrateur** sur **Activer**. 
5. Cliquez sur **Créer**.
6. Une fois que votre registre de conteneurs est créé, accédez à celui-ci, puis sélectionnez **Clés d’accès**. 
7. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. Vous utiliserez ces valeurs plus loin dans le didacticiel. 

## <a name="create-a-function-project"></a>Créer un projet Function
Les étapes suivantes vous montrent comment créer une fonction IoT Edge à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.

1. Ouvrez Visual Studio Code.
2. Ouvrez le terminal intégré VS Code en sélectionnant **Affichage** > **Terminal intégré**. 
2. Ouvrez la palette de commandes VS Code en sélectionnant **Affichage** > **Palette de commandes**.
3. Dans la palette de commandes, tapez et exécutez la commande **Azure: Sign in** et suivez les instructions pour vous connecter à votre compte Azure. Si vous êtes déjà connecté, vous pouvez ignorer cette étape.
3. Dans la palette de commandes, tapez et exécutez la commande **Azure IoT Edge: New IoT Edge solution**. Dans la palette de commandes, spécifiez les informations suivantes pour créer votre solution : 

   1. Sélectionnez le dossier où vous souhaitez créer la solution. 
   2. Spécifiez un nom pour votre solution ou acceptez le nom par défaut **EdgeSolution**.
   3. Choisissez **Azure Functions - C#** comme modèle du module. 
   4. Nommez votre module **CSharpFunction**. 
   5. Spécifiez le registre Azure Container Registry que vous avez créé dans la section précédente comme dépôt d’images pour votre premier module. Remplacez **localhost:5000** par la valeur de serveur de connexion que vous avez copiée. La chaîne finale ressemble à **\<nom de registre\>.azurecr.io/csharpfunction**.

4. La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. Il existe un dossier **.vscode**, un dossier **modules**, un fichier modèle du manifeste de déploiement et un fichier **.env**. Ouvrez **modules** > **CSharpFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

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
        public Machine machine {get; set;}
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

6. Enregistrez le fichier .

## <a name="build-your-iot-edge-solution"></a>Générer votre solution IoT Edge

Dans la section précédente vous avez créé une solution IoT Edge et ajouté du code à CSharpFunction qui permettra de filtrer les messages où la température de l’ordinateur signalée est inférieure au seuil acceptable. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs.

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal intégré Visual Studio Code afin de pouvoir envoyer votre image de module à ACR : 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Utilisez le nom d’utilisateur et le serveur de connexion que vous avez copiés plus tôt à partir de votre registre Azure Container Registry. Vous êtes invité à entrer le mot de passe. Collez votre mot de passe dans l’invite et appuyez sur **Entrée**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. Dans l’Explorateur VS Code, ouvrez le fichier **deployment.template.json** dans votre espace de travail de solution IoT Edge. Ce fichier indique au runtime IoT Edge les modules à déployer sur un appareil. Pour en savoir plus sur les manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

3. Recherchez la section **registryCredentials** dans le manifeste de déploiement. Mettez à jour le **nom d’utilisateur**, le **mot de passe** et **l’adresse** avec les informations d’identification de votre registre de conteneurs. Cette section accorde au runtime IoT Edge de votre appareil l’autorisation d’extraire les images de conteneur que vous stockez dans votre registre privé. Les paires nom d’utilisateur et mot de passe réelles sont stockées dans le fichier .env qui est ignoré par git.

5. Enregistrez ce fichier.

6. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier **deployment.template.json** et sélectionnez **Build IoT Edge solution** (Générer la solution IoT Edge). 

Quand vous indiquez à Visual Studio Code de générer votre solution, il extrait d’abord les informations dans le modèle de déploiement et génère un fichier `deployment.json` dans un nouveau dossier **config**. Il exécute ensuite deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, mettent les fonctions en conteneur, puis l’envoient au registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution. 

## <a name="view-your-container-image"></a>Afficher votre image de conteneur

Visual Studio Code génère un message de réussite quand l’image de conteneur est envoyée au registre de conteneurs. Si vous voulez confirmer la réussite de l’opération pour vous-même, vous pouvez afficher l’image dans le registre. 

1. Dans le portail Azure, accédez à votre registre Azure Container Registry. 
2. Sélectionnez **Dépôts**.
3. Le module **csharpfunction** doit être répertorié dans vos dépôts. Sélectionnez le dépôt pour afficher plus de détails.
4. Dans la section **Balises**, vous devez voir **0.0.1-amd64**. Cette balise reflète la version et la plateforme de l’image que vous avez générée. Ces valeurs sont définies dans le fichier **module.json** dans le dossier CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Déployer et exécuter la solution

Vous pouvez utiliser le portail Azure pour déployer votre module Functions sur un appareil IoT Edge comme vous l’avez fait dans les guides de démarrage rapide, mais vous pouvez également déployer et surveiller des modules à partir de Visual Studio Code. Les sections suivantes utilisent l’extension Azure IoT Edge pour VS Code qui figure dans les prérequis. Installez-la maintenant si ce n’est pas déjà fait. 

1. Ouvrez la palette de commandes VS Code en sélectionnant **Affichage** > **Palette de commandes**.

2. Recherchez et exécutez la commande **Azure: Sign in**. Suivez les instructions pour vous connecter à votre compte Azure. 

3. Dans la palette de commandes, recherchez et exécutez la commande **Azure IoT Hub: Select IoT Hub**. 

4. Sélectionnez l’abonnement qui contient votre hub IoT, puis sélectionnez le hub IoT auquel vous souhaitez accéder.

5. Dans l’Explorateur VS Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). 

6. Cliquez avec le bouton droit sur le nom de votre appareil IoT Edge, puis sélectionnez **Create Deployment for IoT Edge device** (Créer un déploiement pour l’appareil IoT Edge). 

7. Accédez au dossier solution qui contient CSharpFunction. Ouvrez le dossier **config** et sélectionnez le fichier **deployment.json**. Cliquez sur **Select Edge Deployment Manifest** (Sélectionner un manifeste de déploiement Edge).

8. Actualisez la section **Appareils Azure IoT Hub**. Vous devez voir le nouveau module **CSharpFunction** en cours d’exécution avec le module **TempSensor** ainsi que **$edgeAgent** et **$edgeHub**. 

   ![Afficher les modules déployés dans VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Afficher les données générées

Vous pouvez voir tous les messages arrivant dans votre hub IoT en exécutant **Azure IoT Hub: Start Monitoring D2C Message** dans la palette de commandes.

Vous pouvez également filtrer pour afficher tous les messages qui arrivent dans votre hub IoT à partir d’un appareil spécifique. Cliquez avec le bouton droit sur l’appareil dans la section **Appareils Azure IoT Hub** et sélectionnez **Start Monitoring D2C Messages**.

Pour arrêter la surveillance des messages, exécutez la commande **Azure IoT Hub: Stop monitoring D2C message** dans la palette de commandes. 


## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

Supprimez le runtime du service IoT Edge selon la plateforme de votre appareil IoT (Windows ou Linux).

#### <a name="windows"></a>Windows

Supprimez le runtime IoT Edge.

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

Supprimez les conteneurs qui ont été créés sur votre appareil. 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

#### <a name="linux"></a>Linux

Supprimez le runtime IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Supprimez les conteneurs qui ont été créés sur votre appareil. 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

Supprimez le runtime de conteneurs.

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un module Azure Functions qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Quand vous êtes prêt à créer vos propres modules, vous pouvez découvrir comment [développer Azure Functions avec Azure IoT Edge pour Visual Studio Code](how-to-develop-csharp-function.md). 

Passez aux tutoriels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier « at the edge » (« en périphérie»).

> [!div class="nextstepaction"]
> [Rechercher des moyennes à l’aide d’une fenêtre flottante dans Azure Stream Analytics](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
