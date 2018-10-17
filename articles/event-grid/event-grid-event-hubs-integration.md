---
title: Intégration d’Azure Event Grid et Event Hubs
description: Décrit comment utiliser Azure Event Grid et Event Hubs pour migrer des données vers un entrepôt SQL Data Warehouse
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: aad7a24d8b0e0bc74815cad3604db1cc21a6db96
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163224"
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Diffuser en continu des Big Data dans un entrepôt de données

Azure [Event Grid](overview.md) est un service de routage des événements intelligent qui vous permet de réagir aux notifications à partir d’applications et de services. Par exemple, ce service peut déclencher le traitement par une fonction Azure des données Event Hubs capturées dans un Stockage Blob Azure ou dans un référentiel Data Lake Storage, ainsi que la migration des données vers d’autres référentiels de données. Cet [exemple Event Hubs Capture et Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) indique comment utiliser Event Hubs Capture avec Event Grid pour migrer en toute transparence les données Event Hubs du stockage blob vers un service SQL Data Warehouse.

![Vue d’ensemble de l’application](media/event-grid-event-hubs-integration/overview.png)

Comme les données sont envoyées au hub d’événements, Capture extrait les données à partir du flux et génère des objets blob de stockage avec les données dans le format Avro. Lorsque Capture génère l’objet blob, il déclenche un événement. Event Grid distribue les données relatives à l’événement aux abonnés. Dans ce cas, les données d’événement sont envoyées au point de terminaison Azure Functions. Les données d’événement incluent le chemin d’accès de l’objet blob généré. La fonction utilise cette URL pour récupérer le fichier et l’envoyer à l’entrepôt de données.

Dans cet article, vous découvrirez comment :

* Déployer l’infrastructure suivante :
  * Hub d’événements avec Capture activé
  * Compte de stockage pour les fichiers de Capture
  * Plan de service d’application Azure pour héberger l’application de fonction
  * Application de fonction pour traiter l’événement
  * SQL Server pour héberger l’entrepôt de données
  * SQL Data Warehouse pour stocker les données migrées
* Créer une table dans l’entrepôt de données
* Ajouter du code à l’application de fonction
* S’abonner à l’événement
* Exécuter une application qui envoie des données vers le hub d’événements
* Afficher les données migrées dans l’entrepôt de données

## <a name="about-the-event-data"></a>À propos des données d’événement

Event Grid distribue des données d’événement pour les abonnés. L’exemple suivant montre les données d’événement pour créer un fichier Capture. En particulier, notez la propriété `fileUrl` de l’objet `data`. L’application de fonction obtient cette valeur et l’utilise pour récupérer le fichier Capture.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin de :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* [Visual studio 2017 Version 15.3.2 ou supérieure](https://www.visualstudio.com/vs/) avec des charges de travail pour : le développement de bureau .NET, le développement Azure, le développement ASP.NET et web, le développement Node.js et le développement Python.
* Le téléchargement de l’[exemple de projet EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) sur votre ordinateur est terminé.

## <a name="deploy-the-infrastructure"></a>Déployer l’infrastructure

Pour simplifier cet article, vous pouvez déployer l’infrastructure requise avec un modèle Resource Manager. Pour afficher les ressources déployées, référez-vous au [modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json).

Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

Pour PowerShell, utilisez la commande suivante :

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

Fournissez une valeur pour le mot de passe lorsque vous y êtes invité.

## <a name="create-a-table-in-sql-data-warehouse"></a>Créer une table dans SQL Data Warehouse

Ajoutez une table à votre entrepôt de données en exécutant le script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Pour exécuter le script, utilisez Visual Studio ou l’éditeur de requête dans le portail.

Le script à exécuter :

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-the-azure-functions-app"></a>Publie l’application Azure Functions

1. Ouvre l’[exemple de projet EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) dans Visual Studio 2017 (15.3.2 ou supérieur).

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **FunctionEGDWDumper**, puis sélectionnez **Publier**.

   ![Publier une application de fonction](media/event-grid-event-hubs-integration/publish-function-app.png)

1. Sélectionnez **Azure Function App** et **Sélectionner existant**. Sélectionnez **Publier**.

   ![Cibler une application de fonction](media/event-grid-event-hubs-integration/pick-target.png)

1. Sélectionnez l’application de fonction que vous avez déployée grâce au modèle. Sélectionnez **OK**.

   ![Sélectionner l’application de fonction](media/event-grid-event-hubs-integration/select-function-app.png)

1. Lorsque Visual Studio a configuré le profil, sélectionnez **Publier**.

   ![Select publish](media/event-grid-event-hubs-integration/select-publish.png)

Après la publication de la fonction, vous êtes prêt à vous abonner à l’événement.

## <a name="subscribe-to-the-event"></a>S’abonner à l’événement

1. Accédez au [portail Azure](https://portal.azure.com/). Sélectionnez votre groupe de ressources et l’application de fonction.

   ![Afficher l’application de fonction](media/event-grid-event-hubs-integration/view-function-app.png)

1. Sélectionnez la fonction.

   ![Sélectionner une fonction](media/event-grid-event-hubs-integration/select-function.png)

1. Sélectionnez **Ajouter un abonnement Event Grid**.

   ![Ajouter un abonnement](media/event-grid-event-hubs-integration/add-event-grid-subscription.png)

9. Donnez un nom à l’abonnement Event Grid. Utilisez les **espaces de noms Event Hubs** comme type d’événement. Indiquez des valeurs pour sélectionner votre instance de l’espace de noms Event Hubs. Conservez le point de terminaison de l’abonné fourni. Sélectionnez **Créer**.

   ![Créer un abonnement](media/event-grid-event-hubs-integration/set-subscription-values.png)

## <a name="run-the-app-to-generate-data"></a>Exécuter l’application pour générer des données

Vous avez terminé de configurer votre hub d’événements, SQL Data Warehouse, l’application de fonction Azure et l’abonnement à un événement. La solution est prête à la migration de données à partir du hub d’événements vers l’entrepôt de données. Avant d’exécuter une application qui génère des données pour le hub d’événements, vous devez configurer certaines valeurs.

1. Dans le portail, sélectionnez l’espace de noms du hub d’événements. Sélectionnez **Chaînes de connexion**.

   ![Sélectionner des chaînes de connexion](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Sélectionner **RootManageSharedAccessKey**

   ![Sélectionner une clé](media/event-grid-event-hubs-integration/show-root-key.png)

3. Copier **Chaîne de connexion - Clé primaire**

   ![Copier la clé](media/event-grid-event-hubs-integration/copy-key.png)

4. Retournez à votre projet Visual Studio. Dans le projet WindTurbineDataGenerator, ouvrez **program.cs**.

5. Remplacez les deux valeurs constantes. Utilisez la valeur copiée pour **EventHubConnectionString**. Utilisez **hubdatamigration** comme nom du hub d’événements.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Générez la solution. Exécutez l’application WindTurbineGenerator.exe. Après quelques minutes, interrogez la table dans votre entrepôt de données pour les données migrées.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les différences entre les services de messagerie Azure, consultez [Choisir entre des services Azure qui remettent des messages](compare-messaging-services.md).
* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour découvrir Event Hubs Capture, consultez [Activer Event Hubs Capture à l’aide du portail Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Pour plus d’informations sur la configuration et l’exécution de l’exemple, consultez [Exemple Event Hubs Capture et Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).