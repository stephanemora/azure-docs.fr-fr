---
title: Journalisation des diagnostics dans Azure Cosmos DB
description: Découvrez les différentes façons de journaliser et de superviser les données stockées dans Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 8839d7ea93bcb205b1900e63d3ab98394e72cd75
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58904863"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Journalisation des diagnostics dans Azure Cosmos DB 

Après avoir commencé à utiliser une ou plusieurs bases de données Azure Cosmos DB, vous pouvez surveiller comment et quand vos bases de données font l’objet d’un accès. Cet article fournit une vue d’ensemble des journaux d’activité disponibles sur la plateforme Azure. Vous allez apprendre à activer la journalisation des diagnostics pour la surveillance pour envoyer les journaux à [stockage Azure](https://azure.microsoft.com/services/storage/), comment diffuser des journaux sur [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)et comment exporter des journaux vers [Azure Monitor enregistre](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Journaux d’activité disponibles dans Azure

Avant de passer à la surveillance de votre compte Azure Cosmos DB, il est important de clarifier certains points concernant la journalisation et la surveillance. Il existe plusieurs types de journaux d’activité sur la plateforme Azure. Vous avez les [journaux d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), les [journaux de diagnostic Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), les [métriques Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), les événements, le monitoring des pulsations, les journaux d’activité des opérations, etc. Il existe une multitude de journaux d’activité. Vous pouvez voir la liste complète des journaux dans [Azure Monitor enregistre](https://azure.microsoft.com/services/log-analytics/) dans le portail Azure. 

L’illustration suivante montre l’autre genre de journaux Azure disponibles :

![Les différents types de journaux d’activité Azure](./media/logging/azurelogging.png)

Sur l’image, les **Ressources de calcul** représentent les ressources Azure pour lesquelles vous pouvez accéder au système d’exploitation invité Microsoft. Par exemple, les machines virtuelles Azure, les groupes de machines virtuelles identiques et Azure Container Service sont considérés comme des ressources de calcul. Les ressources de calcul génèrent des journaux d’activité, de diagnostic et des applications. Pour plus d’informations, reportez-vous à l’article [Sources de données de supervision dans Azure](../azure-monitor/platform/data-sources.md).

Les **ressources non liées au calcul** sont des ressources dans lesquelles il n’est pas possible d’accéder au système d’exploitation sous-jacent et de les utiliser directement, par exemple, les Groupes de sécurité réseau, Logic Apps, etc. Azure Cosmos DB en est une. Vous pouvez afficher les journaux d’activité des ressources non liées au calcul dans le journal d’activité ou activer l’option Journaux de diagnostic sur le portail. Pour plus d’informations, reportez-vous à l’article [Sources de données dans Azure Monitor](../azure-monitor/platform/data-sources.md).

Le journal d’activité enregistre les opérations au niveau de l’abonnement pour Azure Cosmos DB. Les opérations comme ListKeys et Write DatabaseAccounts sont journalisées par exemple. Les journaux de diagnostic assurent une journalisation plus précise et permettent de consigner des DataPlaneRequests (Create, Read, Query, etc.) et MongoRequests.


Dans cet article, nous allons nous concentrer sur le journal d’activité Azure, les journaux de diagnostic Azure et les métriques Azure. Quelle est la différence entre ces trois types de journaux d’activité ? 

### <a name="azure-activity-log"></a>Journaux d’activité

Le journal d’activité Azure est un journal d’abonnement qui fournit un aperçu de tous les événements relatifs aux abonnements qui se sont produits dans Azure. Le journal d’activité signale les événements de plan de contrôle de vos abonnements sous la catégorie Administratif. Vous pouvez utiliser le journal d’activité pour déterminer « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur des ressources dans votre abonnement. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes. 

Le journal d’activité est différent du journal de diagnostic. Le journal d’activité fournit des données sur les opérations effectuées sur une ressource à partir de l’extérieur (le _plan de contrôle_). Dans le contexte d’Azure Cosmos DB, les opérations de plan de contrôle incluent notamment Créer un conteneur, Répertorier les clés, Supprimer les clés, Répertorier les bases de données, etc. Les journaux de diagnostic sont émis par une ressource et fournissent des informations sur le fonctionnement de cette ressource (le _plan de données_). Les opérations de plan de données du journal de diagnostic incluent par exemple Delete, Insert et ReadFeed.

Les journaux d’activité (opérations de plan de contrôle) peuvent être beaucoup plus complets par nature et inclure l’adresse e-mail complète de l’appelant, l’adresse IP de l’appelant, le nom de la ressource, le nom de l’opération, l’ID du locataire, etc. Le journal d’activité contient plusieurs [catégories](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) de données. Pour plus d’informations sur le schéma de ces catégories, consultez [Schéma d’événements du journal d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Cependant, les journaux de diagnostic peuvent être restrictifs par nature, car les données personnelles n’y sont généralement pas incluses. Si vous avez l’adresse IP de l’appelant, le dernier octet de l’adresse ne sera pas inclus.

### <a name="azure-metrics"></a>Métriques Azure

Les [métriques Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) comprennent le type le plus important de données de télémétrie Azure (aussi appelées _compteurs de performances_), qui sont émises par la plupart des ressources Azure. Les métriques permettent d’afficher des informations sur le débit, le stockage, la cohérence, la disponibilité et la latence de vos ressources Azure Cosmos DB. Pour plus d’informations, consultez la section [Surveillance et débogage à l’aide de métriques dans Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Journaux de diagnostic Azure

Les journaux de diagnostic Azure sont générés par une ressource et fournissent des informations riches et fréquentes sur le fonctionnement de cette ressource. Le contenu de ces journaux d’activité varie en fonction du type de ressource. Les journaux de diagnostic des ressources diffèrent également des journaux de diagnostic de système d’exploitation invité. Les journaux de diagnostic de système d’exploitation invité sont collectés par un agent exécuté sur une machine virtuelle ou un autre type de ressource pris en charge. Les journaux de diagnostic des ressources ne nécessitent aucun agent et capturent les données propres à la ressource à partir de la plateforme Azure elle-même. Les journaux de diagnostic de système d’exploitation invité capturent les données à partir du système d’exploitation et des applications exécutées sur une machine virtuelle.

![Journalisation des diagnostics dans le Stockage, Event Hubs ou les journaux d’activité Azure Monitor](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Quels sont les types d’informations qui sont enregistrées dans les journaux de diagnostic Azure ?

* Toutes les requêtes principales authentifiées (TCP/REST), dans toutes les API, sont enregistrées, y compris les requêtes ayant échoué suite à des demandes, des erreurs système ou des autorisations d’accès incorrectes. Les requêtes Graph, Cassandra et d’API de table initiées par l’utilisateur ne sont pas prises en charge.
* Les opérations sur la base de données même, notamment les opérations CRUD sur l’ensemble des documents, des conteneurs et des bases de données.
* Les opérations sur les clés de compte, notamment la création, la modification ou la suppression de ces clés.
* les requêtes non authentifiées qui génèrent une réponse 401. Par exemple, les requêtes qui ne possèdent pas de jeton de porteur, qui sont incorrectes, qui ont expiré ou qui comportent un jeton non valide.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Activer la journalisation dans le portail Azure

Pour activer la journalisation des diagnostics, vous devez disposer des ressources suivantes :

* Un compte, une base de données et un conteneur Azure Cosmos DB existants. Pour obtenir des instructions sur la création de ces ressources, consultez [Créer un compte de base de données à l’aide du portail Azure](create-sql-api-dotnet.md#create-account), [Exemples Azure CLI](cli-samples.md) ou [Exemples PowerShell](powershell-samples.md).

Pour activer la journalisation des diagnostics dans le portail Azure, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), dans votre compte Azure Cosmos DB, sélectionnez **Journaux de diagnostic** dans le volet de navigation de gauche, puis **Activer les diagnostics**.

    ![Activer la journalisation des diagnostics pour Azure Cosmos DB dans le portail Azure](./media/logging/turn-on-portal-logging.png)

2. Dans la page **Paramètres de diagnostic**, effectuez les étapes suivantes : 

    * **Nom** : Entrez un nom pour les journaux d’activité à créer.

    * **Archiver dans un compte de stockage** : Pour utiliser cette option, vous avez besoin d’un compte de stockage existant auquel vous connecter. Pour créer un compte de stockage dans le portail, consultez [Créer un compte de stockage](../storage/common/storage-create-storage-account.md) et suivez les instructions pour créer un compte Azure Resource Manager à usage général. Revenez ensuite à cette page dans le portail pour sélectionner votre compte de stockage. L’affichage des comptes de stockage nouvellement créés dans le menu déroulant peut prendre quelques minutes.
    * **Diffuser vers un hub d’événements** : pour utiliser cette option, vous avez besoin d’un espace de noms Event Hubs existant et d’un hub d’événements auquel vous connecter. Pour créer un espace de noms Event Hubs, consultez [Créer un espace de noms Event Hubs et un Event Hub à l’aide du portail Azure](../event-hubs/event-hubs-create.md). Revenez ensuite à cette page dans le portail pour sélectionner l’espace de noms Event Hubs et le nom de la stratégie.
    * **Envoyer à Log Analytics** : pour utiliser cette option, utilisez un espace de travail existant ou créez un espace de travail Log Analytics en suivant les étapes permettant de [Créer un espace de travail dans le portail](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace). Pour plus d’informations sur l’affichage de vos journaux dans les journaux Azure Monitor, consultez Qu'afficher les journaux dans les journaux Azure Monitor.
    * **Journaliser DataPlaneRequests** : sélectionnez cette option pour enregistrer les requêtes de back-end depuis la plateforme distribuée Azure Cosmos DB sous-jacente pour les comptes d’API SQL, Graph, MongoDB, Cassandra et Table. Si vous effectuez un archivage dans un compte de stockage, vous pouvez sélectionner la période de rétention des journaux de diagnostic. Les journaux d’activité sont supprimés automatiquement après l’expiration de la période de rétention.
    * **Journaliser MongoRequests** : sélectionnez cette option pour enregistrer les requêtes initiées par l’utilisateur depuis le serveur front-end Azure Cosmos DB, afin de servir les comptes Cosmos configurés avec l’API pour MongoDB d’Azure Cosmos DB. Si vous effectuez un archivage dans un compte de stockage, vous pouvez sélectionner la période de rétention des journaux de diagnostic. Les journaux d’activité sont supprimés automatiquement après l’expiration de la période de rétention.
    * **Requêtes de métrique** : sélectionnez cette option pour stocker des données détaillées dans les [métriques Azure](../azure-monitor/platform/metrics-supported.md). Si vous effectuez un archivage dans un compte de stockage, vous pouvez sélectionner la période de rétention des journaux de diagnostic. Les journaux d’activité sont supprimés automatiquement après l’expiration de la période de rétention.

3. Sélectionnez **Enregistrer**.

    Si vous recevez une erreur indiquant « Failed to update diagnostics for \<workspace name>. The subscription \<subscription id> is not registered to use microsoft.insights (Échec de la mise à jour des diagnostics pour <nom de l’espace de travail>. L’abonnement <id d’abonnement> n’est pas inscrit pour utiliser microsoft.insights), suivez les instructions de la page [Résoudre les problèmes d’Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) pour inscrire le compte, puis recommencez cette procédure.

    Si vous souhaitez modifier la façon dont vos journaux de diagnostic seront enregistrés à l’avenir, revenez à cette page pour modifier les paramètres de journal de diagnostic pour votre compte.

## <a name="turn-on-logging-by-using-azure-cli"></a>Activer la journalisation à l’aide d’Azure CLI

Pour activer la journalisation des métriques et diagnostics à l’aide d’Azure CLI, utilisez les commandes suivantes :

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   `resource` est le nom du compte Azure Cosmos DB. La ressource est au format « /subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/ < Azure_Cosmos_account_name > » le `storage-account` est le nom du compte de stockage pour que vous vous souhaitez envoyer les journaux. Vous pouvez consigner les autres journaux en mettant à jour les valeurs de paramètre de catégorie à « MongoRequests » ou « DataPlaneRequests ». 

- Pour activer la diffusion en continu des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   `resource` est le nom du compte Azure Cosmos DB. Le `event-hub-rule` est l’ID de la règle d’hub d’événement. 

- Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics, utilisez cette commande :

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

## <a name="turn-on-logging-by-using-powershell"></a>Activer la journalisation à l’aide de PowerShell

Pour activer la journalisation des diagnostics à l’aide de PowerShell, vous avez besoin d’Azure PowerShell (version 1.0.1 et ultérieures).

Pour installer Azure PowerShell et l’associer à votre abonnement Azure, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

Si vous avez déjà installé Azure PowerShell et que vous ne connaissez pas la version que vous utilisez, à partir de la console PowerShell, entrez `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Se connecter à vos abonnements
Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :  

```powershell
Connect-AzAccount
```

Dans la fenêtre contextuelle de votre navigateur, entrez votre nom d’utilisateur et votre mot de passe Azure. Azure PowerShell obtient alors tous les abonnements associés à ce compte et utilise par défaut le premier.

Si vous disposez de plusieurs abonnements, vous devrez peut-être en spécifier un en particulier, celui qui a été utilisé pour créer votre coffre de clés Azure. Pour afficher les abonnements de votre compte, entrez la commande suivante :

```powershell
Get-AzSubscription
```

Ensuite, pour spécifier l’abonnement associé au compte Azure Cosmos DB que vous allez journaliser, entrez la commande suivante :

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Si plusieurs abonnements sont associés à votre compte, il est important de spécifier celui que vous souhaitez utiliser.
>
>

Pour plus d’informations sur la configuration d’Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Création d’un nouveau compte de stockage pour vos journaux
Bien que vous puissiez utiliser un compte de stockage existant pour vos journaux d’activité, dans ce didacticiel nous en créons un nouveau qui sera dédié à vos journaux d’activité Azure Cosmos DB. Pour plus de commodité, nous stockons les détails du compte de stockage dans une variable nommée **sa**.

Pour faciliter encore la gestion, dans ce didacticiel nous utilisons le groupe de ressources qui contient la base de données Azure Cosmos DB. Remplacez les valeurs des paramètres **ContosoResourceGroup**, **contosocosmosdblogs** et **USA Centre Nord**, comme il convient :

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Si vous décidez d’utiliser un compte de stockage existant, vous devez utiliser le même abonnement que pour votre abonnement Azure Cosmos DB, ainsi que le modèle de déploiement Resource Manager plutôt que le modèle de déploiement Classic.
>
>

### <a id="identify"></a>Identifier le compte Azure Cosmos DB pour vos journaux
Définissez le nom du compte Azure Cosmos DB sur une variable nommée **account**, où **ResourceName** est le nom du compte Azure Cosmos DB.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Activation de la journalisation
Pour activer la journalisation pour Azure Cosmos DB, utilisez la cmdlet `Set-AzDiagnosticSetting`, ainsi que les variables pour le nouveau compte de stockage, le compte Azure Cosmos DB et la catégorie pour laquelle vous souhaitez activer les journaux. Exécutez la commande suivante et définissez l’indicateur **-Enabled** sur **$true** :

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

La sortie de la commande doit ressembler à l’exemple suivant :

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

La sortie de la commande confirme que la journalisation est maintenant activée pour votre base de données et que les informations sont enregistrées dans votre compte de stockage.

Si vous le souhaitez, vous pouvez également définir la stratégie de rétention pour vos journaux d’activité, par exemple la suppression automatique des anciens journaux d’activité. Spécifiez par exemple la stratégie de rétention avec l’indicateur **-RetentionEnabled** défini sur **$true**. Définissez le paramètre **-RetentionInDays** sur **90** afin que les journaux d’activité de plus de 90 jours soient automatiquement supprimés.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Accéder à vos journaux
Les journaux d’activité Azure Cosmos DB pour la catégorie **DataPlaneRequests** sont stockés dans le conteneur **insights-logs-data-plane-requests** du compte de stockage spécifié. 

Commencez par créer une variable pour le nom du conteneur. Cette variable est utilisée tout au long de la procédure pas à pas.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Pour répertorier tous les objets blob présents dans ce conteneur, saisissez :

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

La sortie de la commande doit ressembler à l’exemple suivant :

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Comme vous pouvez le voir dans cette sortie, les objets blob suivent une convention d’affectation de noms : `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Les valeurs de date et d’heure utilisent UTC.

Le même compte de stockage pouvant être utilisé pour collecter les journaux d’activité de plusieurs ressources, vous pouvez utiliser l’ID complet de ressource dans le nom de l’objet blob pour accéder seulement aux objets blob dont vous avez besoin et les télécharger. Avant cela, nous aborderons le téléchargement de tous les objets blob.

Tout d’abord, créez un dossier pour télécharger les objets blob. Par exemple : 

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Procurez-vous la liste de tous les objets blob :  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Redirigez cette liste via la commande `Get-AzStorageBlobContent` pour télécharger les objets blob dans le dossier de destination :

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Lorsque vous exécutez cette seconde commande, le délimiteur **/** présent dans les noms d’objet blob crée une structure de dossiers complète sous le dossier de destination. Cette structure de dossiers est utilisée pour télécharger et stocker les objets blob en tant que fichiers.

Pour télécharger les objets blob de façon sélective, utilisez des caractères génériques. Par exemple : 

* Si vous disposez de plusieurs bases de données et souhaitez télécharger les journaux d’activité d’une seule d’entre elles nommée **CONTOSOCOSMOSDB3**, utilisez la commande suivante :

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Si vous disposez de plusieurs groupes de ressources et souhaitez télécharger les journaux d’activité d’un seul d’entre eux, utilisez la commande `-Blob '*/RESOURCEGROUPS/<resource group name>/*'` :

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Si vous souhaitez télécharger tous les journaux d’activité du mois de juillet 2017, utilisez la commande `-Blob '*/year=2017/m=07/*'` :

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Vous pouvez également exécuter les commandes suivantes :

* Pour interroger l’état des paramètres de diagnostic de votre ressource de base de données, utilisez la commande `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Pour désactiver la journalisation de la catégorie **DataPlaneRequests** pour votre ressource de compte de base de données, utilisez la commande `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Les objets blob retournés dans chacune de ces requêtes sont stockés sous forme de blob JSON au format texte, comme indiqué dans le code suivant :

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Pour en savoir plus sur les données de chaque blob JSON, consultez [Interpréter vos journaux d’activité Azure Cosmos DB](#interpret).

## <a name="manage-your-logs"></a>Gérer vos journaux d’activité

Les journaux de diagnostic sont disponibles dans votre compte pendant les deux heures qui suivent l’opération Azure Cosmos DB. C’est à vous de gérer vos journaux d’activité dans votre compte de stockage :

* Utilisez les méthodes de contrôle d’accès Azure standard pour assurer la sécurité de vos journaux d’activité et y limiter l’accès.
* Supprimez les journaux d’activité que vous ne souhaitez plus conserver dans votre compte de stockage.
* La période de rétention pour les demandes de plan de données archivées dans un compte de stockage est configurée dans le portail lorsque le paramètre **Journaliser DataPlaneRequests** est sélectionné. Pour modifier ce paramètre, consultez [Activer la journalisation dans le portail Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Voir les journaux d’activité Azure Monitor

Si vous avez sélectionné le **envoyer à Log Analytique** option lorsque vous avez activé la journalisation des diagnostics, diagnostic à partir de votre conteneur, les données sont transférées dans les journaux d’Azure Monitor dans les deux heures. Lorsque vous examinez les journaux Azure Monitor immédiatement après l’activation de la journalisation, vous ne voyez aucune donnée. Il vous suffit alors d’attendre deux heures et de réessayer. 

Avant de consulter vos journaux, vérifiez et si votre espace de travail Analytique de journal a été mis à niveau pour utiliser le nouveau langage de requête Kusto. Pour vérifier, ouvrez le [Azure portal](https://portal.azure.com), sélectionnez **espaces de travail Analytique de journal** à l’extrême gauche, puis sélectionnez le nom de l’espace de travail comme indiqué dans l’image suivante. La page **Espace de travail Log Analytics** est affichée :

![Azure Monitor enregistre dans le portail Azure](./media/logging/azure-portal.png)

>[!NOTE]
>Les espaces de travail OMS sont désormais appelés « espaces de travail Log Analytics ».  

Si le message suivant s’affiche dans la page **Espace de travail Log Analytics**, cela signifie que votre espace de travail n’a pas été mis à niveau pour utiliser le nouveau langage. Pour plus d’informations sur la mise à niveau vers le nouveau langage de requête, consultez [Mise à niveau de votre espace de travail Azure Log Analytics vers la nouvelle recherche dans les journaux](../log-analytics/log-analytics-log-search-upgrade.md). 

![Journaux d’analyse Azure mise à niveau de message](./media/logging/upgrade-notification.png)

Pour afficher vos données de diagnostic dans les journaux Azure Monitor, ouvrez le **recherche dans les journaux** page dans le menu de gauche ou la **gestion** zone de la page, comme indiqué dans l’image suivante :

![Options de recherche dans les journaux dans le portail Azure](./media/logging/log-analytics-open-log-search.png)

Maintenant que vous avez activé la collecte de données, exécutez une recherche dans les journaux d’activité en utilisant le nouveau langage de requête comme indiqué dans l’exemple suivant. Cet exemple permet d’afficher les dix derniers journaux d’activité `AzureDiagnostics | take 10`.

![Exemple de recherche dans les 10 derniers journaux d’activité](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Requêtes

Voici quelques requêtes supplémentaires que vous pouvez entrer dans la zone **Recherche dans les journaux**. Elles vous aideront à surveiller vos conteneurs Azure Cosmos DB. Ces requêtes fonctionnent avec le [nouveau langage](../log-analytics/log-analytics-log-search-upgrade.md). 

Pour en savoir plus sur la signification des données retournées par chaque recherche dans les journaux d’activité, consultez [Interpréter vos journaux d’activité Azure Cosmos DB](#interpret).

* Pour rechercher tous les journaux de diagnostic issus d’Azure Cosmos DB pour la période spécifiée :

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Pour rechercher les 10 derniers événements journalisés :

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Pour rechercher toutes les opérations regroupées par type :

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Pour rechercher toutes les opérations regroupées par **Ressource** :

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Pour rechercher toutes les activités utilisateur regroupées par ressource :

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Cette commande concerne les journaux d’activité et non les journaux de diagnostic.

* Pour rechercher les opérations durant plus de 3 millisecondes :

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Pour rechercher l’agent exécutant les opérations :

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Pour recherche le moment d’exécution des opérations de longue durée :

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Pour plus d’informations sur la façon d’utiliser le nouveau langage de recherche dans les journaux, consultez [présentation des recherches dans les journaux Azure Monitor](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpréter vos journaux

Les données de diagnostic sont stockées dans les journaux de stockage et Azure Monitor Azure utilisent un schéma similaire. 

Le tableau suivant décrit le contenu de chaque entrée de journal.

| Propriété ou champ du stockage Azure | Azure Monitor enregistre la propriété | Description |
| --- | --- | --- |
| **time** | **TimeGenerated** | Date et heure (UTC) de l’opération. |
| **resourceId** | **Ressource** | Le compte Azure Cosmos DB pour lequel les journaux d’activité sont activés.|
| **category** | **Catégorie** | Pour les journaux d’activité Azure Cosmos DB, **DataPlaneRequests** est la seule valeur disponible. |
| **operationName** | **OperationName** | Nom de l’opération. Cette valeur peut être l’une des opérations suivantes : Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed ou Upsert.   |
| **properties** | n/a | Le contenu de ce champ est décrit dans les lignes suivantes. |
| **activityId** | **activityId_g** | GUID unique de l’opération journalisée. |
| **userAgent** | **userAgent_s** | Chaîne qui spécifie l’agent utilisateur client effectuant la demande. Le format est {nom de l’agent utilisateur}/{version}.|
| **requestResourceType** | **requestResourceType_s** | Type de la ressource faisant l’objet de l’accès. Cette valeur peut être l’un des types de ressources suivants : Database, Container, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction ou Offer. |
| **statusCode** | **statusCode_s** | État de réponse de l’opération. |
| **requestResourceId** | **ResourceId** | L’ID de ressource qui se rapporte à la demande. Cette valeur peut pointer vers databaseRid, collectionRid ou documentRid en fonction de l’opération exécutée.|
| **clientIpAddress** | **clientIpAddress_s** | Adresse IP du client. |
| **requestCharge** | **requestCharge_s** | Nombre d’unités de requête utilisées par l’opération. |
| **collectionRid** | **collectionId_s** | ID unique de la collection.|
| **duration** | **duration_s** | Durée de l’opération, en cycles. |
| **requestLength** | **requestLength_s** | Longueur de la demande, en octets. |
| **responseLength** | **responseLength_s** | Longueur de la réponse, en octets.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Cette valeur n’est pas vide lorsque des [jetons de ressource](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) sont utilisés pour l’authentification. La valeur pointe vers l’ID de ressource de l’utilisateur. |

## <a name="next-steps"></a>Étapes suivantes

- Pour comprendre comment activer la journalisation, mais aussi les métriques et les catégories de journaux d’activité prises en charge par les différents services Azure, consultez les articles [Vue d’ensemble des métriques dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) et [Présentation des journaux de diagnostic Azure](../azure-monitor/platform/diagnostic-logs-overview.md).
- Pour en savoir plus sur les concentrateurs d’événements, lisez les articles suivants :
   - [Nouveautés des concentrateurs d’événements Azure ?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Consultez [Télécharger les journaux de métriques et de diagnostics de Stockage Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Lecture [présentation des recherches dans les journaux Azure Monitor](../log-analytics/log-analytics-log-search-new.md).
