---
title: Déployer des fonctions Azure avec Azure IoT Edge | Microsoft Docs
description: Dans ce didacticiel, vous déployez une fonction Azure en tant que module vers un appareil Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/19/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: d0ae009db0d9470942a4ff5d7c09e2cdd7bcdd53
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52165614"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Tutoriel : Déployer des fonctions Azure en tant que modules IoT Edge

Vous pouvez utiliser Azure Functions pour déployer un code qui implémente votre logique métier directement sur vos appareils Azure IoT Edge. Ce tutoriel vous guide dans la création et le déploiement d’une fonction Azure qui filtre des données de capteur sur l’appareil IoT Edge simulé. Vous utilisez l’appareil IoT Edge simulé que vous avez créé dans les tutoriels Déployer Azure IoT Edge sur un appareil simulé sous [Windows](quickstart.md) ou [Linux](quickstart-linux.md). Ce tutoriel vous montre comment effectuer les opérations suivantes :     

> [!div class="checklist"]
> * Utilisez Visual Studio Code pour créer une fonction Azure.
> * Utilisez VS Code et Docker pour créer une image Docker et la publier sur un registre de conteneurs.
> * Déployez le module du registre de conteneurs sur votre appareil IoT Edge.
> * Affichez les données filtrées.

<center>
![Schéma de l’architecture du didacticiel](./media/tutorial-deploy-function/FunctionsTutDiagram.png)
</center>

>[!NOTE]
>Les modules Azure Functions sur Azure IoT Edge sont en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

La fonction Azure que vous créez dans ce tutoriel filtre les données de température générées par votre appareil. La fonction envoie uniquement des messages en amont au hub Azure IoT lorsque la température dépasse un seuil spécifié. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge :

* Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle telle qu’un appareil de périphérie, en suivant les étapes décrites dans le guide de démarrage rapide pour les appareils [Linux](quickstart-linux.md) ou [ Windows](quickstart.md).

Ressources cloud :

* Un niveau gratuit ou standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure. 

Ressources de développement :

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [SDK .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Dans ce tutoriel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker pour stocker vos images conteneur. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires. Ce didacticiel utilise Azure Container Registry. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Container Registry**.

    ![Créer un registre de conteneur](./media/tutorial-deploy-function/create-container-registry.png)

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

## <a name="create-a-function-project"></a>Créer un projet Function

L’extension Azure IoT Edge pour Visual Studio Code que vous avez installée conformément aux prérequis offre des fonctionnalités de gestion, ainsi que certains modèles de code. Dans cette section, vous allez utiliser Visual Studio Code pour créer une solution IoT Edge contenant une fonction Azure. 

1. Ouvrez Visual Studio Code sur votre ordinateur de développement.

2. Ouvrez la palette de commandes VS Code en sélectionnant **Affichage** > **Palette de commandes**.

3. Dans la palette de commandes, entrez et exécutez la commande **Azure IoT Edge : nouvelle solution IoT Edge**. Suivez les invites de la palette de commandes pour créer votre solution.

   | Champ | Valeur |
   | ----- | ----- |
   | Sélectionner le dossier | Choisissez l’emplacement sur votre machine de développement pour que VS Code crée les fichiers de la solution. |
   | Fournir un nom de solution | Entrez un nom descriptif pour votre solution, par exemple **FunctionSolution**, ou acceptez le nom par défaut. |
   | Sélectionner un modèle de module | Choisissez **Azure Functions - C#**. |
   | Fournir un nom de module | Nommez votre module **CSharpFunction**. |
   | Fournir le référentiel d’images Docker pour le module | Un référentiel d’images comprend le nom de votre registre de conteneurs et celui de votre image conteneur. Votre image conteneur est préremplie à partir de la dernière étape. Remplacez **localhost:5000** par la valeur de serveur de connexion de votre registre de conteneurs Azure. Vous pouvez récupérer le serveur de connexion à partir de la page Vue d’ensemble de votre registre de conteneurs dans le Portail Azure. La chaîne finale ressemble à \<nom de registre\>.azurecr.io/CSharpFunction. |

   ![Fourniture du référentiel d’images Docker](./media/tutorial-deploy-function/repository.png)

4. La fenêtre VS Code charge votre espace de travail de solution IoT Edge : un dossier \.vscode, un dossier modules, un fichier de modèle de manifeste de déploiement. et un fichier \.env. Dans l’Explorateur Visual Studio Code, ouvrez **modules** > **CSharpFunction** > **CSharpFunction.cs**.

5. Remplacez le contenu du fichier **CSharpFunction.cs** par le code suivant. Ce code reçoit les données de télémétrie concernant la température ambiante et la température de l’appareil. Il transmet uniquement le message à IoT Hub si la température de l’appareil dépasse un seuil défini.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
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
   }
   ```

6. Enregistrez le fichier .

## <a name="build-your-iot-edge-solution"></a>Générer votre solution IoT Edge

Dans la section précédente, vous avez créé une solution IoT Edge et ajouté un code à **CSharpFunction** pour filtrer les messages où la température de la machine signalée est inférieure au seuil acceptable. Vous devez maintenant générer la solution comme image de conteneur et l’envoyer à votre registre de conteneurs.

Dans cette section, vous allez fournir les informations d’identification de votre registre de conteneurs à deux reprises. La première fois, vous spécifiez ces informations pour vous connecter localement à partir de votre ordinateur de développement afin que Visual Studio Code puisse envoyer des images à votre registre. La seconde fois, vous fournissez ces informations dans le fichier **.env** de votre solution IoT Edge, ce qui autorise votre appareil IoT Edge à extraire des images de votre registre. 

1. Ouvrez le terminal intégré VS Code en sélectionnant **Affichage** > **Terminal**. 

2. Connectez-vous à votre registre de conteneurs en entrant la commande ci-après dans le terminal intégré. Utilisez le nom d’utilisateur et le serveur de connexion que vous avez copiés plus tôt à partir de votre registre Azure Container Registry.
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Lorsque vous êtes invité à fournir le mot de passe, collez ce dernier dans votre registre de conteneurs, puis appuyez sur **Entrée**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. Dans l’Explorateur VS Code, ouvrez le fichier **deployment.template.json** dans votre espace de travail de solution IoT Edge. Ce fichier indique au runtime IoT Edge les modules à déployer sur un appareil. Notez que votre module de fonction, **CSharpFunction**, est répertorié avec le module **tempSensor** qui fournit les données de test. Pour en savoir plus sur les manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

   ![Affichage de votre module dans le manifeste de déploiement](./media/tutorial-deploy-function/deployment-template.png)

3. Ouvrez le fichier **.env** dans l’espace de travail de votre solution IoT Edge. Ce fichier ignoré par git stocke les informations d’identification de votre registre de conteneurs pour vous éviter d’avoir à les placer dans le modèle du manifeste de déploiement. Fournissez le **nom d’utilisateur** et le **mot de passe** de votre registre de conteneurs. 

5. Enregistrez ce fichier.

6. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier deployment.template.json et sélectionnez **Build and Push IoT Edge solution** (Générer et envoyer (push) la solution IoT Edge). 

Quand vous indiquez à Visual Studio Code de générer votre solution, il extrait d’abord les informations contenues dans le modèle de déploiement et génère un fichier deployment.json dans un nouveau dossier nommé **config**. Il exécute ensuite deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, mettent les fonctions en conteneur, puis envoient le code au registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution. 

## <a name="view-your-container-image"></a>Afficher votre image de conteneur

Visual Studio Code génère un message de réussite quand l’image de conteneur est envoyée au registre de conteneurs. Si vous voulez confirmer la réussite de l’opération pour vous-même, vous pouvez afficher l’image dans le registre. 

1. Dans le portail Azure, accédez à votre registre Azure Container Registry. 
2. Sélectionnez **Dépôts**.
3. Le référentiel **csharpfunction** doit apparaître dans la liste. Sélectionnez ce référentiel pour afficher plus de détails.
4. Dans la section **Balises**, vous devez voir la balise **0.0.1-amd64**. Cette balise indique la version et la plateforme de l’image que vous avez générée. Ces valeurs sont définies dans le fichier module.json dans le dossier CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Déployer et exécuter la solution

Vous pouvez utiliser le portail Azure pour déployer votre module de fonction sur un appareil IoT Edge, comme vous l’avez fait dans les démarrages rapides. Vous pouvez également déployer et surveiller des modules à partir de Visual Studio Code. Les sections suivantes utilisent l’extension Azure IoT Edge pour VS Code qui figure dans les prérequis. Installez l’extension maintenant, si ce n’est pas déjà fait. 

1. Ouvrez la palette de commandes VS Code en sélectionnant **Affichage** > **Palette de commandes**.

2. Recherchez et exécutez la commande **Azure: Sign in**. Suivez les instructions pour vous connecter à votre compte Azure. 

3. Dans la palette de commandes, recherchez et exécutez la commande **Azure IoT Hub: Select IoT Hub**. 

4. Sélectionnez l’abonnement qui contient votre hub IoT, puis sélectionnez le hub IoT auquel vous souhaitez accéder.

5. Dans l’Explorateur VS Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). 

6. Cliquez avec le bouton droit sur le nom de votre appareil IoT Edge, puis sélectionnez **Créer un déploiement pour un seul appareil**. 

7. Accédez au dossier solution qui contient la fonction **CSharpFunction**. Ouvrez le dossier config, sélectionnez le fichier **deployment.json**, puis cliquez sur **Sélectionner un manifeste de déploiement Edge**.

8. Actualisez la section **Appareils Azure IoT Hub**. Vous devez voir le nouveau module **CSharpFunction** en cours d’exécution avec le module **TempSensor** ainsi que **$edgeAgent** et **$edgeHub**. Plusieurs minutes peuvent être nécessaires avant que les nouveaux modules n’apparaissent. Votre appareil IoT Edge doit récupérer ses nouvelles informations de déploiement dans IoT Hub, démarrer les nouveaux conteneurs, puis communiquer son état à IoT Hub. 

   ![Afficher les modules déployés dans VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Afficher les données générées

Vous pouvez voir tous les messages arrivant dans votre hub IoT en exécutant **Azure IoT Hub: Démarrer l’analyse du message D2C** dans la palette de commandes.

Vous pouvez également filtrer pour afficher tous les messages qui arrivent dans votre hub IoT à partir d’un appareil spécifique. Cliquez avec le bouton droit sur l’appareil dans la section **Appareils Azure IoT Hub** et sélectionnez **Start Monitoring D2C Messages**.

Pour arrêter la surveillance des messages, exécutez la commande **Azure IoT Hub: Stop monitoring D2C message** dans la palette de commandes. 


## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser. Vous pouvez également continuer à utiliser le même appareil IoT Edge comme appareil de test. 

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter les frais. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé une fonction Azure contenant le code pour filtrer les données brutes générées par votre appareil IoT Edge. Quand vous êtes prêt à créer vos propres modules, vous pouvez découvrir comment [développer Azure Functions avec Azure IoT Edge pour Visual Studio Code](how-to-develop-csharp-function.md). 

Passez aux tutoriels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier « en périphérie».

> [!div class="nextstepaction"]
> [Rechercher des moyennes à l’aide d’une fenêtre flottante dans Azure Stream Analytics](tutorial-deploy-stream-analytics.md)

