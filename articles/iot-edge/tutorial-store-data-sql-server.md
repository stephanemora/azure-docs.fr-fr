---
title: Stocker des données avec le module SQL Azure IoT Edge | Microsoft Docs
description: Découvrez comment stocker des données localement sur votre appareil IoT Edge avec un module SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/01/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: b0d26704d287f2e02541cc667250af8e8005f864
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833991"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Tutoriel : Stocker des données en périphérie avec les bases de données SQL Server

Utilisez Azure IoT Edge et SQL Server pour stocker et interroger des données en périphérie. Azure IoT Edge comprend des fonctionnalités de stockage de base qui mettent en cache les messages quand un appareil est mis hors connexion, puis qui les transmettent une fois la connexion rétablie. Toutefois, vous souhaitez peut-être des capacités de stockage plus avancées, telles que la possibilité d’interroger des données localement. En incorporant des bases de données locales, vos appareils IoT Edge peuvent effectuer des calculs plus complexes sans avoir à gérer une connexion à IoT Hub. Par exemple, un capteur d’ordinateur charge des données dans le cloud une fois par mois dans le but de créer des rapports sur un module de machine learning et de l’améliorer. Cependant, si un technicien de terrain travaille sur cet ordinateur, il peut accéder localement aux données de capteur qui ont été enregistrées au cours des derniers jours.

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
* [Extension C# pour Visual Studio Code (technologie OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [SDK .NET Core 2.1](https://www.microsoft.com/net/download). 
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Dans ce tutoriel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

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

## <a name="create-a-function-project"></a>Créer un projet Function

Pour envoyer des données dans une base de données, vous avez besoin d’un module qui peut structurer les données correctement, puis les stocke dans une table. 

Les étapes suivantes vous montrent comment créer une fonction IoT Edge à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.

1. Ouvrez Visual Studio Code.

2. Ouvrez la palette de commandes VS Code en sélectionnant **Affichage** > **Palette de commandes**.

3. Dans la palette de commandes, tapez et exécutez la commande **Azure IoT Edge: New IoT Edge solution**. Dans la palette de commandes, spécifiez les informations suivantes pour créer votre solution : 

   | Champ | Valeur |
   | ----- | ----- |
   | Sélectionner le dossier | Choisissez l’emplacement sur votre machine de développement pour que VS Code crée les fichiers de la solution. |
   | Provide a solution name (Nommer la solution) | Entrez un nom descriptif pour votre solution, par exemple **SqlSolution**, ou acceptez le nom par défaut. |
   | Select module template (Sélectionner un modèle de module) | Choisissez **Azure Functions - C#**. |
   | Provide a module name (Nommer le module) | Nommez votre module **sqlFunction**. |
   | Provide Docker image repository for the module (Indiquer le référentiel d’images Docker pour le module) | Un référentiel d’images comprend le nom de votre registre de conteneurs et celui de votre image conteneur. Votre image conteneur est préremplie à partir de la dernière étape. Remplacez **localhost:5000** par la valeur de serveur de connexion de votre registre de conteneurs Azure. Vous pouvez récupérer le serveur de connexion à partir de la page Vue d’ensemble de votre registre de conteneurs dans le Portail Azure. La chaîne finale ressemble à ceci : \<nom de registre\>.azurecr.io/sqlFunction. |

   La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. 
   
4. Dans votre solution IoT Edge, ouvrez le fichier \.env. 

   Chaque fois que vous créez une solution IoT Edge, VS Code vous invite à fournir vos informations d’identification de registre dans le fichier \.env. Ce fichier est ignoré par git et l’extension IoT Edge l’utilise ultérieurement pour permettre à votre appareil IoT Edge d’accéder au registre. 

   Si vous n’avez pas fourni votre registre de conteneurs à l’étape précédente mais que vous avez accepté la valeur par défaut localhost:5000, vous n’avez pas de fichier \.env.

5. Dans le fichier .env, donnez au runtime IoT Edge les informations d’identification du registre afin qu’il puisse accéder aux images de module. Recherchez les sections **CONTAINER_REGISTRY_USERNAME** et **CONTAINER_REGISTRY_PASSWORD** et insérez vos informations d’identification après le symbole égal à : 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourregistry=<username>
   CONTAINER_REGISTRY_PASSWORD_yourregistry=<password>
   ```

6. Enregistrez le fichier .env.

7. Dans l’Explorateur Visual Studio Code, ouvrez **modules** > **sqlFunction** > **sqlFunction.cs**.

8. Remplacez le contenu du fichier par le code suivant :

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
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
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

6. À la ligne 35, remplacez la chaîne **\<sql connection string\>** par la chaîne suivante. La propriété **Data Source** référence le nom du conteneur SQL Server, que vous allez créer et nommer **SQL** dans la section suivante. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Enregistrez le fichier **sqlFunction.cs**. 

8. Ouvrez le fichier **sqlFunction.csproj**.

9. Recherchez le groupe de références de package, puis ajoutez-en une nouvelle pour SqlClient. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

10. Enregistrez le fichier **sqlFunction.csproj**.

## <a name="add-a-sql-server-container"></a>Ajouter un conteneur SQL Server

Un [manifeste de déploiement](module-composition.md) déclare les modules que le runtime IoT Edge va installer sur votre appareil IoT Edge. Vous avez fourni du code pour créer un module Function personnalisé dans la section précédente, cependant, le module SQL Server est déjà généré. Vous devez simplement indiquer au runtime IoT Edge de l’inclure, puis de le configurer sur votre appareil. 

1. Dans l’Explorateur Visual Studio Code, ouvrez le fichier **deployment.template.json**. 

2. Recherchez la section **modules**. Deux modules doivent être répertoriés : **tempSensor**, qui génère des données simulées et votre module **sqlFunction**.

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
     "env":{},
     "settings": {
       "image": "",
       "createOptions": ""
     }
   }
   ```

   ![Ajouter un conteneur SQL Server](./media/tutorial-store-data-sql-server/view_json_sql.png)

5. Selon le type des conteneurs Docker présents sur votre appareil IoT Edge, mettez à jour les paramètres du module **sql** avec le code suivant :
   * Conteneurs Windows :

      ```json
      "env": {
        "ACCEPT_EULA": {"value": "Y"},
        "SA_PASSWORD": {"value": "Strong!Passw0rd"}
      },
      "settings": {
        "image": "microsoft/mssql-server-windows-developer",
        "createOptions": {
          "HostConfig": {
            "Mounts": [{"Target": "C:\\\\mssql","Source": "sqlVolume","Type": "volume"}],
            "PortBindings": {
              "1433/tcp": [{"HostPort": "1401"}]
            }
          }
        }
      }
      ```

   * Conteneurs Linux :

      ```json
      "env": {
        "ACCEPT_EULA": {"value": "Y"},
        "SA_PASSWORD": {"value": "Strong!Passw0rd"}
      },
      "settings": {
        "image": "mcr.microsoft.com/mssql/server:latest",
        "createOptions": {
          "HostConfig": {
            "Mounts": [{"Target": "/var/opt/mssql","Source": "sqlVolume","Type": "volume"}],
            "PortBindings": {
              "1433/tcp": [{"HostPort": "1401"}]
            }
          }
        }
      }
      ```

   >[!Tip]
   >Chaque fois que vous créez un conteneur SQL Server dans un environnement de production, vous devez [modifier le mot de passe administrateur par défaut du système](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

6. Enregistrez le fichier **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Générer votre solution IoT Edge

Dans les sections précédentes, vous avez créé une solution avec un module, puis en avez ajouté un autre au modèle du manifeste de déploiement. Maintenant, vous devez générer la solution, créer des images de conteneur pour les modules et envoyer les images au registre de conteneurs. 

1. Connectez-vous à votre registre de conteneurs à Visual Studio Code afin de pouvoir envoyer les images au registre. Utilisez les informations d’identification que vous avez ajoutées au fichier .env. Entrez la commande suivante dans le terminal intégré :

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    
    Vous êtes invité à entrer le mot de passe. Collez votre mot de passe dans l’invite de commandes (votre mot de passe est masqué pour des raisons de sécurité), puis appuyez sur **Entrée**. 

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier **deployment.template.json** et sélectionnez **Build and Push IoT Edge solution** (Générer et envoyer (push) la solution IoT Edge). 

Quand vous indiquez à Visual Studio Code de générer votre solution, il extrait d’abord les informations contenues dans le modèle de déploiement et génère un fichier deployment.json dans un nouveau dossier nommé **config**. Il exécute ensuite deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, conteneurisent le module, puis envoient (push) le code au registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution. 

## <a name="deploy-the-solution-to-a-device"></a>Déployer la solution sur un appareil

Vous pouvez définir des modules sur un appareil via le hub IoT, mais vous pouvez également accéder à vos hub IoT et appareils via Visual Studio Code. Dans cette section, vous configurez l’accès à votre hub IoT, puis utilisez VS Code pour déployer la solution sur votre appareil IoT Edge. 

1. Dans la palette de commandes VS Code, sélectionnez **Azure IoT Hub: Select IoT Hub**.

2. Suivez les instructions de l’invite pour vous connecter à votre compte Azure. 

3. Dans la palette de commandes, sélectionnez votre abonnement Azure, puis votre hub IoT. 

4. Dans l’Explorateur VS Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). 

5. Cliquez avec le bouton droit sur l’appareil que vous souhaitez cibler avec votre déploiement et sélectionnez **Créer un déploiement pour un seul appareil**. 

   ![Créer un déploiement pour un seul appareil](./media/tutorial-store-data-sql-server/create-deployment.png)

6. Dans l’Explorateur de fichiers, accédez au dossier **config** à l’intérieur de votre solution, puis choisissez **deployment.amd64**. Cliquez sur **Select Edge Deployment Manifest** (Sélectionner un manifeste de déploiement Edge). 

Si le déploiement réussit, un message de confirmation est affiché dans la sortie VS Code. 

Actualisez l’état de votre appareil dans la section Appareils Azure IoT Hub de VS Code. Les nouveaux modules sont listés et commencent à générer des rapports à mesure qu’ils s’exécutent au cours des minutes suivantes pendant l’installation et le démarrage des conteneurs. Vous pouvez également vérifier que tous les modules sont opérationnels sur votre appareil. Sur votre appareil IoT Edge, exécutez la commande suivante pour afficher l’état des modules. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Créer la base de données SQL

Quand vous appliquez le manifeste de déploiement à votre appareil, trois modules sont en cours d’exécution. Le module tempSensor génère des données d’environnement simulées. Le module sqlFunction récupère les données et les met en forme pour une base de données. Cette section vous guide tout au long de la configuration de la base de données SQL pour stocker les données de température. 

Exécutez les commandes suivantes sur votre appareil IoT Edge. Ces commandes se connectent au module **sql** en cours d’exécution sur votre appareil, et créent une base de données et une table pour contenir les données de température qui lui sont envoyées. 

1. Dans un outil en ligne de commande sur votre appareil IoT Edge, connectez-vous à votre base de données. 
   * Conteneur Windows :
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Conteneur Linux : 

      ```bash
      sudo docker exec -it sql bash
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
