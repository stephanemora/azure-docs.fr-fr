---
title: 'Tutoriel : Envoyer des données Event Hubs vers un entrepôt de données - Event Grid'
description: 'Tutoriel : Décrit comment utiliser Azure Event Grid et Event Hubs pour migrer des données vers un entrepôt SQL Data Warehouse. Une fonction Azure est utilisée pour récupérer un fichier de capture.'
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 6f5bd129b175210cd5b9415a65b8db06d904e24d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73718184"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Tutoriel : Diffuser en continu des Big Data dans un entrepôt de données
Azure [Event Grid](overview.md) est un service de routage des événements intelligent qui vous permet de réagir aux notifications (événements) à partir d’applications et de services. Par exemple, ce service peut déclencher le traitement par une fonction Azure des données Event Hubs capturées dans un Stockage Blob Azure ou dans un référentiel Azure Data Lake Storage, ainsi que la migration des données vers d’autres référentiels de données. Cet [exemple d’intégration Event Hubs et Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) vous indique comment utiliser Event Hubs avec Event Grid pour migrer en toute transparence les données Event Hubs capturées du stockage blob vers un service SQL Data Warehouse.

![Vue d’ensemble de l’application](media/event-grid-event-hubs-integration/overview.png)

Ce diagramme illustre le flux de travail de la solution que vous créez dans ce didacticiel : 

1. Les données envoyées à un hub d’événements Azure sont capturées dans un stockage d’objet blob Azure.
2. Lorsque la capture de données est terminée, un événement est généré et envoyé à Azure Event Grid. 
3. Event Grid transfert ces données d’événements à une application de fonction Azure.
4. L’application de fonction utilise l’URL d’objet blob dans les données d’événement pour récupérer l’objet blob depuis le stockage. 
5. L’application de fonction effectue la migration des données blob vers un entrepôt de données Azure SQL. 

Dans cet article, vous procédez comme suit :

> [!div class="checklist"]
> * Utiliser un modèle Azure Resource Manager pour déployer l’infrastructure : hub d’événements, compte de stockage, application de fonction, entrepôt de données SQL.
> * Créer une table dans l’entrepôt de données.
> * Ajouter du code à l’application de fonction.
> * S’abonner à l’événement. 
> * Exécuter une application qui envoie des données vers le hub d’événements.
> * Afficher les données migrées dans l’entrepôt de données.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour suivre ce didacticiel, vous avez besoin de :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Visual studio 2019](https://www.visualstudio.com/vs/) avec des charges de travail pour : le développement de bureau .NET, le développement Azure, le développement ASP.NET et web, le développement Node.js et le développement Python.
* Télécharger l’[exemple de projet EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) sur votre ordinateur.

## <a name="deploy-the-infrastructure"></a>Déployer l’infrastructure
Dans cette étape, vous déployez l’infrastructure requise avec un [modèle Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Lorsque vous déployez le modèle, les ressources suivantes sont créées :

* Un hub d’événements avec la fonctionnalité Capture activée.
* Un compte de stockage pour fichiers capturés. 
* Un plan App Service pour héberger l’application de fonction.
* Application de fonction pour traiter l’événement
* SQL Server pour héberger l’entrepôt de données
* SQL Data Warehouse pour stocker les données migrées

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Lancer Azure Cloud Shell dans le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
2. Sélectionnez le bouton **Cloud Shell** en haut.

    ![Portail Azure](media/event-grid-event-hubs-integration/azure-portal.png)
3. Cloud Shell s’ouvre dans la partie inférieure du navigateur.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Dans Cloud Shell, si vous avez la possibilité de choisir entre **Bash** et **PowerShell**, sélectionnez **Bash**. 
5. Si vous utilisez Cloud Shell pour la première fois, créez un compte de stockage en sélectionnant **Créer le stockage**. Azure Cloud Shell requiert un compte de stockage Azure pour stocker des fichiers. 

    ![Créez le stockage pour Cloud Shell](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Attendez que le Cloud Shell soit initialisé. 

    ![Créez le stockage pour Cloud Shell](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure

1. Créez un groupe de ressources Azure en exécutant la commande CLI suivante : 
    1. Copiez et collez la commande suivante dans la fenêtre Cloud Shell

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. Spécifiez un nom pour le **groupe de ressources**
    2. Appuyez sur **Entrée**. 

        Voici un exemple :
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Déployez toutes les ressources mentionnées dans la section précédente (hub d’événements, compte de stockage, application de fonction, entrepôt de données SQL) en exécutant la commande CLI suivante : 
    1. Copiez et collez la commande dans la fenêtre Cloud Shell. Vous pouvez également copier/coller la commande dans l’éditeur de votre choix, définir des valeurs, puis la copiez dans Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Donnez des valeurs aux entités suivantes :
        1. Nom du groupe de ressources créé précédemment.
        2. Nom de l’espace de noms Event Hub. 
        3. Nom du hub d’événements. Vous pouvez laisser la valeur telle quelle (hubdatamigration).
        4. Nom du serveur SQL.
        5. Nom de l’utilisateur SQL et mot de passe. 
        6. Nom de l’entrepôt de données SQL.
        7. Nom du compte de stockage. 
        8. Nom de l’application de fonction. 
    3.  Appuyez sur **Entrée** dans la fenêtre Cloud Shell pour exécuter la commande. Ce processus peut prendre un certain temps dans la mesure où vous créez beaucoup de ressources. Dans le résultat de la commande, assurez-vous qu’il n’y a eu aucun échec. 
    

### <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell

1. Dans Azure Cloud Shell, passez au mode PowerShell. Sélectionnez flèche bas dans le coin supérieur gauche d’Azure Cloud Shell, puis sélectionnez **PowerShell**.

    ![Passer à PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Créez un groupe de ressources Azure en exécutant la commande suivante : 
    1. Copiez et collez la commande suivante dans la fenêtre Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. Spécifiez un nom pour le **groupe de ressources**.
    3. Appuyez sur Entrée. 
3. Déployez toutes les ressources mentionnées dans la section précédente (hub d’événements, compte de stockage, application de fonction, entrepôt de données SQL) en exécutant la commande suivante :
    1. Copiez et collez la commande dans la fenêtre Cloud Shell. Vous pouvez également copier/coller la commande dans l’éditeur de votre choix, définir des valeurs, puis la copiez dans Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Donnez des valeurs aux entités suivantes :
        1. Nom du groupe de ressources créé précédemment.
        2. Nom de l’espace de noms Event Hub. 
        3. Nom du hub d’événements. Vous pouvez laisser la valeur telle quelle (hubdatamigration).
        4. Nom du serveur SQL.
        5. Nom de l’utilisateur SQL et mot de passe. 
        6. Nom de l’entrepôt de données SQL.
        7. Nom du compte de stockage. 
        8. Nom de l’application de fonction. 
    3.  Appuyez sur **Entrée** dans la fenêtre Cloud Shell pour exécuter la commande. Ce processus peut prendre un certain temps dans la mesure où vous créez beaucoup de ressources. Dans le résultat de la commande, assurez-vous qu’il n’y a eu aucun échec. 

### <a name="close-the-cloud-shell"></a>Fermer Cloud Shell 
Fermez Cloud Shell en sélectionnant le bouton **Cloud Shell** dans le portail (ou) le bouton **X** dans le coin supérieur droit de la fenêtre Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Vérifier que les ressources ont été créées

1. Dans le portail Azure, sélectionnez **Groupes de ressources** dans le menu de gauche. 
2. Filtrez la liste des groupes de ressources en entrant le nom de votre groupe de ressources dans la zone de recherche. 
3. Sélectionnez votre groupe de ressources dans la liste.

    ![Sélectionnez votre groupe de ressources](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Vérifiez que les ressources suivantes s’affichent dans le groupe de ressources :

    ![Ressources comprises dans le groupe de ressources](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>Créer une table dans SQL Data Warehouse
Créez une table dans votre entrepôt de données en exécutant le script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Pour exécuter le script, vous pouvez utiliser Visual Studio ou l’éditeur de requête dans le portail. Les étapes suivantes montrent comment utiliser l’éditeur de requête : 

1. Dans la liste des ressources comprises dans le groupe de ressources, sélectionnez votre entrepôt de données SQL. 
2. Dans la page de l’entrepôt de données SQL, sélectionnez **Éditeur de requête (préversion)** dans le menu de gauche. 

    ![Page de l’entrepôt de données SQL](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Entrez le nom d’**utilisateur** et le **mot de passe** du serveur SQL, puis sélectionnez **OK**. 

    ![Authentification SQL Server](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. Dans la fenêtre de requête, copiez et exécutez le script SQL suivant : 

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

    ![Exécuter une requête SQL](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Ne fermez pas cet onglet ou cette fenêtre pour pouvoir vérifier que les données ont été créées à la fin du didacticiel. 


## <a name="publish-the-azure-functions-app"></a>Publie l’application Azure Functions

1. Lancez Visual Studio.
2. Ouvrez la solution **EventHubsCaptureEventGridDemo.sln** que vous avez téléchargée depuis [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) dans le cadre des conditions préalables.
3. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **FunctionEGDWDumper**, puis sélectionnez **Publier**.

   ![Publier une application de fonction](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Si l’écran suivant apparaît, sélectionnez **Démarrer**. 

   ![Démarre le bouton de publication](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. Sur la page **Choisir une cible de publication**, sélectionnez l’option **Sélectionner un élément existant** puis sélectionnez **Créer le profil**. 

   ![Choisir une cible de publication](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. Sur la page App Service, sélectionnez votre **abonnement Azure**, sélectionnez l’**application de fonction** dans votre groupe de ressources, puis sélectionnez **OK**. 

   ![Page App Service](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Lorsque Visual Studio a configuré le profil, sélectionnez **Publier**.

   ![Select publish](media/event-grid-event-hubs-integration/select-publish.png)

Après la publication de la fonction, vous êtes prêt à vous abonner à l’événement.

## <a name="subscribe-to-the-event"></a>S’abonner à l’événement

1. Accédez au [portail Azure](https://portal.azure.com) depuis une nouvelle fenêtre ou un nouvel onglet dans un navigateur web.
2. Dans le portail Azure, sélectionnez **Groupes de ressources** dans le menu de gauche. 
3. Filtrez la liste des groupes de ressources en entrant le nom de votre groupe de ressources dans la zone de recherche. 
4. Sélectionnez votre groupe de ressources dans la liste.

    ![Sélectionnez votre groupe de ressources](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Sélectionnez le plan App Service dans la liste. 
5. Sur la page Plan App Service, sélectionnez **Applications** dans le menu de gauche, puis sélectionnez l’application de fonction. 

    ![Sélectionnez vos applications de fonction](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Développez l’application de fonction, les fonctions, puis sélectionnez votre fonction. 

    ![Sélectionner votre application de fonction](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. Sélectionnez **Ajouter un abonnement Event Grid** dans la barre d’outils. 
8. Sur la page **Créer un abonnement Event Grid**, procédez comme suit : 
    1. Dans la section **Détails de la rubrique**, procédez comme suit :
        1. Sélectionnez votre abonnement Azure.
        2. Sélectionnez le groupe de ressource Azure.
        3. Sélectionner l’espace de noms Event Hubs.
    2. Sur la page **Détails de l’abonnement aux événements**, entrez un nom pour l’abonnement (exemple : captureEventSub), puis sélectionnez **Créer**. 

        ![Créer un abonnement Event Grid](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Exécuter l’application pour générer des données
Vous avez terminé de configurer votre hub d’événements, SQL Data Warehouse, l’application de fonction Azure et l’abonnement à un événement. Avant d’exécuter une application qui génère des données pour le hub d’événements, vous devez configurer certaines valeurs.

1. Dans le portail Azure, accédez à votre groupe de ressources comme précédemment. 
2. Sélectionner l’espace de noms Event Hubs.
3. Sur la page **Espace de noms Event Hubs**, cliquez sur **Stratégies d’accès partagé** dans le menu de gauche.
4. Sélectionnez **RootManageSharedAccessKey** dans la liste des stratégies. 
5. Cliquez sur le bouton copier à côté de la zone de texte **Clé primaire de la chaîne de connexion**. 

    ![Chaîne de connexion pour les espaces de noms Event Hub](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Retournez à votre solution Visual Studio. 
2. Dans le projet WindTurbineDataGenerator, ouvrez **program.cs**.
5. Remplacez les deux valeurs constantes. Utilisez la valeur copiée pour **EventHubConnectionString**. Utilisez **hubdatamigration** comme nom du hub d’événements. Si vous avez utilisé un nom différent pour le hub d’événements, spécifiez-le. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Générez la solution. Exécutez l’application **WindTurbineGenerator.exe**. 
7. Après quelques minutes, interrogez la table dans votre entrepôt de données pour les données migrées.

    ![Résultats de la requête](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Données d’événements générées par le hub d’événements
Event Grid distribue des données d’événement pour les abonnés. L’exemple suivant montre les données d’événement générées lorsque des données en diffusion via un hub d’événements sont capturées dans un objet blob. Notez en particulier que la propriété `fileUrl` dans l’objet `data` pointe vers l’objet blob dans le stockage. L’application de fonction utilise cette URL pour récupérer le fichier blob avec des données capturées.

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


## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les différences entre les services de messagerie Azure, consultez [Choisir entre des services Azure qui remettent des messages](compare-messaging-services.md).
* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour découvrir Event Hubs Capture, consultez [Activer Event Hubs Capture à l’aide du portail Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Pour plus d’informations sur la configuration et l’exécution de l’exemple, consultez [Exemple Event Hubs Capture et Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
