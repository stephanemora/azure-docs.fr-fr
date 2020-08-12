---
title: 'Tutoriel : Migrer des données d’événement vers SQL Data Warehouse – Azure Event Hubs'
description: 'Tutoriel : Ce didacticiel vous montre comment capturer des données de votre Event Hub dans SQL Data Warehouse à l’aide d’une fonction Azure déclenchée par une grille d’événements.'
services: event-hubs
ms.date: 06/23/2020
ms.topic: tutorial
ms.openlocfilehash: 1a7030014a8926b1db11ad99ba031e51194ddfd9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87833723"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>Tutoriel : Migrer des données Event Hubs capturées vers SQL Data Warehouse à l'aide d'Event Grid et d'Azure Functions

Event Hubs [Capture](./event-hubs-capture-overview.md) est le moyen le plus simple pour fournir automatiquement les données diffusées en continu d’Event Hubs à un compte de stockage Blob Azure ou Azure Data Lake Store. Vous pouvez ensuite traiter et fournir les données vers n’importe quelle destination de stockage de votre choix, SQL Data Warehouse ou Cosmos DB par exemple. Dans ce didacticiel, vous allez apprendre à capturer des données de votre Event Hub dans SQL Data Warehouse à l’aide d’une fonction Azure déclenchée par une [grille d’événements](../event-grid/overview.md).

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

- Tout d’abord, vous créez un Event Hub avec la fonctionnalité **Capture** activée et vous définissez un stockage d’objets blob Azure comme destination. Les données générées par WindTurbineGenerator sont diffusées en continu dans le Event Hub et sont automatiquement capturées dans le stockage Azure sous la forme de fichiers Avro.
- Ensuite, vous créez un abonnement Azure Event Grid avec l’espace de noms Event Hubs comme source et le point de terminaison de Azure Function comme destination.
- Chaque fois qu’un nouveau fichier Avro est remis à l’objet blob de stockage Azure par la fonctionnalité Event Hubs Capture, Event Grid notifie Azure Function avec l’URI d’objet blob. La fonction migre ensuite les données à partir de l’objet blob vers un SQL Data Warehouse.

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
>
> - Déployer l’infrastructure
> - Publier du code vers une application de fonction
> - Créer un abonnement Event Grid à partir de l’application de fonction
> - Diffuser en continu des exemples de données dans le Event Hub.
> - Vérifier les données capturées dans un SQL Data Warehouse

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Visual Studio 2019](https://www.visualstudio.com/vs/). Au cours de l’installation, assurez-vous d’installer les charges de travail suivantes : le développement de bureau .NET, le développement Azure, le développement ASP.NET et web, le développement Node.js et le développement Python
- Téléchargez l’exemple [Git](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/EventHubsCaptureEventGridDemo) L’exemple de solution contient les composants suivants :

  - *WindTurbineDataGenerator* – un éditeur simple qui envoie des données exemples de turbine de vent vers un Event Hub avec la capture activée
  - *FunctionDWDumper* – une Azure Function qui reçoit une notification de Event Grid lorsqu’un fichier Avro est capturé dans l’objet blob de stockage Azure. Elle reçoit le chemin de l’URI de l’objet blob, lit son contenu et envoie ces données vers un SQL Data Warehouse.

  Cet exemple utilise le package Azure.Messaging.EventHubs le plus récent. Vous trouverez l’ancien exemple qui utilise le package Microsoft.Azure.EventHubs [ici](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).

### <a name="deploy-the-infrastructure"></a>Déployer l’infrastructure

Utilisez Azure PowerShell ou Azure CLI pour déployer l’infrastructure nécessaire à ce didacticiel en utilisant ce [modèle Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json). Ce modèle crée les ressources suivantes :

- Un Event Hub avec la fonctionnalité Capture activée
- Un compte de stockage pour les données d’événement capturées
- Un plan de service d’application Azure pour héberger l’application de fonction
- Une application de fonction pour le traitement des fichiers d’événement capturés
- Un serveur SQL logique pour héberger l’entrepôt de données
- SQL Data Warehouse pour stocker les données migrées

Les sections suivantes fournissent des commandes Azure CLI et Azure PowerShell pour le déploiement de l’infrastructure requise pour le didacticiel. Mettez à jour les noms des objets suivants avant d’exécuter les commandes : 

- Groupe de ressources Azure 
- Région pour le groupe de ressources
- Espace de noms Event Hubs
- Event Hub
- Serveur SQL logique
- Utilisateur SQL (et mot de passe)
- Base de données Azure SQL
- Stockage Azure 
- Application Azure Functions

Ces scripts prennent un certain temps pour créer tous les artefacts Azure. Attendez que le script se termine avant de continuer. Si le déploiement échoue pour une raison quelconque, supprimez le groupe de ressources, corrigez le problème signalé et réexécutez la commande. 

#### <a name="azure-cli"></a>Azure CLI

Pour déployer le modèle à l’aide d’Azure CLI, exécutez les commandes suivantes :

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create `
  --resource-group rgDataMigrationSample `
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json `
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
Pour déployer le modèle à l’aide de PowerShell, exécutez les commandes suivantes :

```powershell
New-AzResourceGroup -Name rgDataMigration -Location westcentralus

New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

### <a name="create-a-table-in-sql-data-warehouse"></a>Créer une table dans SQL Data Warehouse

Créez une table dans votre SQL Data Warehouse en exécutant le script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) à l’aide de [Visual Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), [SQL Server Management Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-ssms.md), ou de l’éditeur de requêtes dans le portail. 

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

## <a name="publish-code-to-the-functions-app"></a>Publier du code vers l’application de fonction

1. Ouvrez la solution *EventHubsCaptureEventGridDemo* dans Visual Studio 2019.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur *FunctionEGDWDumper*, puis sélectionnez **Publier**.

   ![Publier une application de fonction](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. Sélectionnez **Azure Function App** et **Sélectionner existant**. Sélectionnez **Publier**.

   ![Cibler une application de fonction](./media/store-captured-data-data-warehouse/pick-target.png)

1. Sélectionnez l’application de fonction que vous avez déployée grâce au modèle. Sélectionnez **OK**.

   ![Sélectionner l’application de fonction](./media/store-captured-data-data-warehouse/select-function-app.png)

1. Lorsque Visual Studio a configuré le profil, sélectionnez **Publier**.

   ![Select publish](./media/store-captured-data-data-warehouse/select-publish.png)

Après la publication de la fonction, vous êtes prêt à vous abonner à l’événement de capture à partir de Event Hubs !


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>Créer un abonnement Event Grid à partir de l’application de fonction
 
1. Accédez au [portail Azure](https://portal.azure.com/). Sélectionnez votre groupe de ressources et l’application de fonction.

   ![Afficher l’application de fonction](./media/store-captured-data-data-warehouse/view-function-app.png)

1. Sélectionnez la fonction.

   ![Sélectionner une fonction](./media/store-captured-data-data-warehouse/select-function.png)

1. Sélectionnez **Ajouter un abonnement Event Grid**.

   ![Ajouter un abonnement](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Donnez un nom à l’abonnement Event Grid. Utilisez les **espaces de noms Event Hubs** comme type d’événement. Indiquez des valeurs pour sélectionner votre instance de l’espace de noms Event Hubs. Conservez le point de terminaison de l’abonné fourni. Sélectionnez **Create** (Créer).

   ![Créer un abonnement](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Générer un exemple de données  
Vous avez configuré votre Event Hub, SQL Data Warehouse, l’application de fonction Azure et l’abonnement à Event Grid. Vous pouvez exécuter WindTurbineDataGenerator.exe pour générer des flux de données au Event Hub après avoir mis à jour la chaîne de connexion et le nom de votre Event Hub dans le code source. 

1. Dans le portail, sélectionnez l’espace de noms du hub d’événements. Sélectionnez **Chaînes de connexion**.

   ![Sélectionner des chaînes de connexion](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. Sélectionner **RootManageSharedAccessKey**

   ![Sélectionner une clé](./media/store-captured-data-data-warehouse/show-root-key.png)

3. Copier **Chaîne de connexion - Clé primaire**

   ![Copier la clé](./media/store-captured-data-data-warehouse/copy-key.png)

4. Retournez à votre projet Visual Studio. Dans le projet *WindTurbineDataGenerator*, ouvrez *program.cs*.

5. Mettez à jour les valeurs pour **EventHubConnectionString** et **EventHubName** avec la chaîne de connexion et le nom de votre Event Hub. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compilez la solution puis exécutez l’application WindTurbineGenerator.exe. 

## <a name="verify-captured-data-in-data-warehouse"></a>Vérifier les données capturées dans un Data Warehouse
Après quelques minutes, interrogez la table dans votre SQL Data Warehouse. Vous observez que les données générées par WindTurbineDataGenerator ont été diffusées en continu vers votre Event Hub, capturées dans un conteneur de stockage Azure et puis migrées dans la table SQL Data Warehouse par Azure Function.  

## <a name="next-steps"></a>Étapes suivantes 
Vous pouvez utiliser des outils de visualisation de données puissants avec votre Data Warehouse pour obtenir des insights actionnables.

Cet article montre comment utiliser [Power BI avec SQL Data Warehouse](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)