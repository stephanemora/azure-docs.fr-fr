---
title: Stocker des données avec le module SQL Azure IoT Edge | Microsoft Docs
description: Découvrez comment stocker des données localement sur votre appareil IoT Edge avec un module SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5c3b8d350b69996e2bbff4958dd0a3600c1b7518
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566079"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Tutoriel : Stocker des données en périphérie avec les bases de données SQL Server

Utilisez Azure IoT Edge et SQL Server pour stocker et interroger des données en périphérie. Azure IoT Edge est intégré aux fonctionnalités de stockage de base qui mettent en cache les messages si un appareil est mis hors connexion, puis les transmettent quand la connexion est rétablie. Toutefois, vous souhaitez peut-être des capacités de stockage plus avancées, telles que la possibilité d’interroger des données localement. En incorporant des bases de données locales, vos appareils IoT Edge peuvent effectuer des calculs plus complexes sans avoir à gérer une connexion à IoT Hub. Par exemple, un technicien sur le terrain peut visualiser les derniers jours de données de capteur sur un ordinateur localement, même si ces données sont uniquement chargées sur le cloud une fois par mois pour aider à améliorer un modèle Machine Learning.

Cet article fournit des instructions pour le déploiement d’une base de données SQL Server sur un appareil IoT Edge. Azure Functions, exécuté sur l’appareil IoT Edge, structure les données entrantes, puis les envoie vers la base de données. Les étapes décrites dans cet article peuvent également être appliquées aux autres bases de données qui fonctionnent dans des conteneurs, telles que MySQL ou PostgreSQL.

Ce tutoriel vous montre comment effectuer les opérations suivantes : 

> [!div class="checklist"]
> * Utiliser Visual Studio Code pour créer une fonction Azure
> * Déployer une base de données SQL sur votre appareil IoT Edge
> * Utiliser Visual Studio Code pour générer des modules et les déployer sur votre appareil IoT Edge
> * Afficher les données générées

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge :

* Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle comme un appareil Edge, en suivant les étapes décrites dans le Guide de démarrage rapide pour [Linux](quickstart-linux.md) ou pour les [Appareils Windows](quickstart.md).

Ressources cloud :

* Un niveau gratuit ou standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure. 

Ressources de développement :

* [Visual Studio Code](https://code.visualstudio.com/). 
* Extension [C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* Extension [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pour Visual Studio Code. 
* [SDK .NET Core 2.1](https://www.microsoft.com/net/download). 
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Créer un registre de conteneur
Dans ce tutoriel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et de créer une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce tutoriel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Ce didacticiel utilise Azure Container Registry. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Container Registry**.

    ![Créer un registre de conteneurs](./media/tutorial-deploy-function/create-container-registry.png)

2. Donnez un nom à votre registre et choisissez un abonnement.
3. Pour le groupe de ressources, il est recommandé d’utiliser le même nom de groupe de ressources qui contient votre hub IoT. En conservant toutes les ressources dans le même groupe, vous pouvez les gérer ensemble. Par exemple, si vous supprimez le groupe de ressources utilisé pour le test, vous supprimez également toutes les ressources de test contenues dans le groupe. 
4. Définissez la référence (SKU) sur la valeur **De base** et basculez **Utilisateur administrateur** sur **Activer**. 
5. Cliquez sur **Créer**.
6. Une fois que votre registre de conteneurs est créé, accédez à celui-ci, puis sélectionnez **Clés d’accès**. 
7. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. Vous utiliserez ces valeurs plus loin dans le tutoriel. 

## <a name="create-a-function-project"></a>Créer un projet Function

Pour envoyer des données dans une base de données, vous avez besoin d’un module qui peut structurer les données correctement, puis les stocke dans une table. 

Les étapes suivantes vous montrent comment créer une fonction IoT Edge à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.

1. Ouvrez Visual Studio Code.
2. Ouvrez le terminal intégré VS Code en sélectionnant **Affichage** > **Terminal**.
3. Ouvrez la palette de commandes VS Code en sélectionnant **Affichage** > **Palette de commandes**.
4. Dans la palette de commandes, tapez et exécutez la commande **Azure: Sign in** et suivez les instructions pour vous connecter à votre compte Azure. Si vous êtes déjà connecté, vous pouvez ignorer cette étape.
3. Dans la palette de commandes, tapez et exécutez la commande **Azure IoT Edge: New IoT Edge solution**. Dans la palette de commandes, spécifiez les informations suivantes pour créer votre solution : 
   1. Sélectionnez le dossier où vous souhaitez créer la solution. 
   2. Spécifiez un nom pour votre solution ou acceptez le nom par défaut **EdgeSolution**.
   3. Choisissez **Azure Functions - C#** comme modèle du module. 
   4. Nommez votre module **sqlFunction**. 
   5. Spécifiez le registre Azure Container Registry que vous avez créé dans la section précédente comme dépôt d’images pour votre premier module. Remplacez **localhost:5000** par la valeur de serveur de connexion que vous avez copiée. La chaîne finale ressemble à **\<nom de registre\>.azurecr.io/sqlFunction**.

4. La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. Il existe un dossier **modules**, un dossier **.vscode** et un fichier modèle du manifeste de déploiement. Ouvrez **modules** > **sqlFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

5. Remplacez le contenu du fichier par le code suivant :

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

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

           //Store the data in SQL db
           const string str = "<sql connection string>";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
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

6. À la ligne 24, remplacez la chaîne **\<chaîne de connexion sql\>** par la chaîne suivante. La propriété **Data Source** fait référence au nom de conteneur SQL Server, que vous allez créer avec le nom **SQL** dans la section suivante. 

   ```C#
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Enregistrez le fichier **run.csx**. 

## <a name="add-a-sql-server-container"></a>Ajouter un conteneur SQL Server

Un [manifeste de déploiement](module-composition.md) déclare les modules que le runtime IoT Edge va installer sur votre appareil IoT Edge. Vous avez ajouté le code pour créer un module Function personnalisé dans la section précédente, mais le module SQL Server est déjà généré. Vous devez simplement indiquer au runtime IoT Edge de l’inclure, puis de le configurer sur votre appareil. 

1. Dans l’Explorateur Visual Studio Code, ouvrez le fichier **deployment.template.json**. 
2. Recherchez la section **moduleContent.$edgeAgent.properties.desired.modules**. Deux modules doivent être répertoriés : **tempSensor**, qui génère des données simulées et votre module **sqlFunction**.
3. Si vous utilisez des conteneurs Windows, modifiez la section **sqlFunction.settings.image**.
    ```json
    "image": "${MODULES.sqlFunction.windows-amd64}"
    ```
4. Ajoutez le code suivant pour déclarer un troisième module. Ajoutez une virgule après la section sqlFunction et insérez :

   ```json
   "sql": {
       "version": "1.0",
       "type": "docker",
       "status": "running",
       "restartPolicy": "always",
       "settings": {
           "image": "",
           "createOptions": ""
       }
   }
   ```

   Voici un exemple en cas de confusion avec l’ajout d’un élément JSON. ![Ajouter un conteneur SQL Server](./media/tutorial-store-data-sql-server/view_json_sql.png)

5. Selon le type de conteneurs Docker sur votre appareil IoT Edge, mettez à jour les paramètres **sql.settings** avec le code suivant :

   * Conteneurs Windows :

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Conteneurs Linux :

      ```json
      "image": "mcr.microsoft.com/mssql/server:latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   >[!Tip]
   >Chaque fois que vous créez un conteneur SQL Server dans un environnement de production, vous devez [modifier le mot de passe administrateur par défaut du système](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

6. Enregistrez le fichier **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Générer votre solution IoT Edge

Dans les sections précédentes, vous avez créé une solution avec un module, puis en avez ajouté un autre au modèle du manifeste de déploiement. Maintenant, vous devez générer la solution, créer des images de conteneur pour les modules et envoyer les images au registre de conteneurs. 

1. Dans le fichier .env, donnez au runtime IoT Edge les informations d’identification du registre afin qu’il puisse accéder aux images de module. Recherchez les sections **CONTAINER_REGISTRY_USERNAME** et **CONTAINER_REGISTRY_PASSWORD** et insérez vos informations d’identification après le symbole égal à : 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourContainerReg=<username>
   CONTAINER_REGISTRY_PASSWORD_yourContainerReg=<password>
   ```

2. Enregistrez le fichier .env.
3. Connectez-vous à votre registre de conteneurs à Visual Studio Code afin de pouvoir envoyer les images au registre. Utilisez les informations d’identification que vous avez ajoutées au fichier .env. Entrez la commande suivante dans le terminal intégré :

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Vous êtes invité à entrer le mot de passe. Collez votre mot de passe dans l’invite et appuyez sur **Entrée**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

4. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier **deployment.template.json** et sélectionnez **Build and Push IoT Edge solution** (Générer et envoyer (push) la solution IoT Edge). 

## <a name="deploy-the-solution-to-a-device"></a>Déployer la solution sur un appareil

Vous pouvez définir des modules sur un appareil via le hub IoT, mais vous pouvez également accéder à vos hub IoT et appareils via Visual Studio Code. Dans cette section, vous configurez l’accès à votre hub IoT, puis utilisez VS Code pour déployer la solution sur votre appareil IoT Edge. 

1. Dans la palette de commandes VS Code, sélectionnez **Azure IoT Hub: Select IoT Hub**.
2. Suivez les instructions de l’invite pour vous connecter à votre compte Azure. 
3. Dans la palette de commandes, sélectionnez votre abonnement Azure, puis votre hub IoT. 
4. Dans l’Explorateur VS Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). 
5. Cliquez avec le bouton droit sur l’appareil que vous souhaitez cibler avec votre déploiement et sélectionnez **Créer un déploiement pour un seul appareil**. 
6. Dans l’Explorateur de fichiers, accédez au dossier **config** à l’intérieur de votre solution et choisissez **deployment.json**. Cliquez sur **Select Edge Deployment Manifest** (Sélectionner un manifeste de déploiement Edge). 

Si le déploiement réussit, un message de confirmation est affiché dans la sortie VS Code. Vous pouvez également vérifier que tous les modules sont opérationnels sur votre appareil. 

Sur votre appareil IoT Edge, exécutez la commande suivante pour afficher l’état des modules. Cette opération peut prendre quelques minutes.

   ```PowerShell
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Créer la base de données SQL

Quand vous appliquez le manifeste de déploiement à votre appareil, trois modules sont en cours d’exécution. Le module tempSensor génère des données d’environnement simulées. Le module sqlFunction récupère les données et les met en forme pour une base de données. 

Cette section vous guide tout au long de la configuration de la base de données SQL pour stocker les données de température. 

1. Dans un outil de ligne de commande, connectez-vous à votre base de données. 
   * Conteneur Windows :
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Conteneur Linux : 

      ```bash
      docker exec -it sql bash
      ```

2. Ouvrez l’outil de commande SQL.
   * Conteneur Windows :

      ```cmd
      sqlcmd -S localhost -U SA -P "Strong!Passw0rd"
      ```

   * Conteneur Linux : 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Créez votre base de données : 

   * Conteneur Windows
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
      GO
      ```

   * Conteneur Linux
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Définissez votre table.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Vous pouvez personnaliser votre fichier Docker SQL Server pour configurer automatiquement le déploiement de votre serveur SQL Server sur plusieurs appareils IoT Edge. Pour plus d’informations, consultez le [projet de démonstration du conteneur Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app). 

## <a name="view-the-local-data"></a>Consulter les données locales

Une fois que votre table est créée, le module sqlFunction démarre le stockage des données dans une base de données SQL Server 2017 locale sur votre appareil IoT Edge. 

À partir de l’outil de commande SQL, exécutez la commande suivante pour afficher vos données de table mises en forme : 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Afficher les données locales](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser. Vous pouvez également continuer à utiliser le même appareil IoT Edge comme appareil de test. 

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter les frais. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]



## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un module Azure Functions qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Quand vous êtes prêt à créer vos propres modules, vous pouvez découvrir comment [développer Azure Functions avec Azure IoT Edge pour Visual Studio Code](how-to-develop-csharp-function.md). 

Passez aux tutoriels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier « at the edge » (« en périphérie»).

> [!div class="nextstepaction"]
> [Filtrer les données de capteur à l’aide du code C#](tutorial-csharp-module.md)