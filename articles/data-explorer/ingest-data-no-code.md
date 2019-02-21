---
title: 'Didacticiel : Ingérer les données des journaux de diagnostic et d’activité dans Azure Data Explorer sans une seule ligne de code'
description: Dans ce tutoriel, vous allez apprendre à ingérer des données dans Azure Data Explorer sans une seule ligne de code et découvrir comment interroger ces données.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: 145a56bee857debdbf028834a3ed378efd8671c8
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447495"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Didacticiel : Ingérer des données dans Azure Data Explorer sans une seule ligne de code

Ce tutoriel va vous apprendre à ingérer les données des journaux de diagnostic et d’activité dans un cluster Azure Data Explorer sans une seule ligne de code. Cette méthode d’ingestion simple vous permet de commencer rapidement à interroger Azure Data Explorer pour l’analyse de données.

Ce didacticiel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer des tables et un mappage d’ingestion dans une base de données Azure Data Explorer
> * Mettre en forme les données ingérées à l’aide d’une stratégie de mise à jour
> * Créer un [hub d’événements](/azure/event-hubs/event-hubs-about) et le connecter à Azure Data Explorer
> * Effectuer un streaming des données vers un hub d’événements à partir des [journaux de diagnostic Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) et des [journaux d’activité Azure Monitor](/azure/azure-monitor/platform/activity-logs-overview)
> * Interroger les données ingérées à l’aide d’Azure Data Explorer

> [!NOTE]
> Créez toutes les ressources dans le même emplacement/la même région Azure. Il s’agit d’une exigence pour les journaux de diagnostic Azure Monitor.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md). Dans ce tutoriel, le nom de la base de données est *AzureMonitoring*.

## <a name="azure-monitoring-data-provider---diagnostic-and-activity-logs"></a>Fournisseur de données Azure Monitoring - journaux de diagnostic et d’activité

Visualisez et interprétez les données fournies par les journaux de diagnostic et d’activité Azure Monitoring. Nous allons créer un pipeline d’ingestion basé sur ces schémas de données.

### <a name="diagnostic-logs-example"></a>Exemple de journaux de diagnostic

Les journaux de diagnostic Azure sont des métriques émises par un service Azure. Elles fournissent des données sur le fonctionnement de ce service. Les données sont agrégées à chaque fragment de temps de 1 minute. Chaque événement des journaux de diagnostic contient un enregistrement. Voici un exemple de schéma d’événement de métrique Azure Data Explorer pour la durée d’une requête :

```json
{
    "count": 14,
    "total": 0,
    "minimum": 0,
    "maximum": 0,
    "average": 0,
    "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
    "time": "2018-12-20T17:00:00.0000000Z",
    "metricName": "QueryDuration",
    "timeGrain": "PT1M"
}
```

### <a name="activity-logs-example"></a>Exemple de journaux d’activité

Les journaux d’activité Azure sont des journaux de niveau abonnement qui contiennent une collection d’enregistrements. Les journaux fournissent un insight à propos des opérations effectuées sur les ressources de votre abonnement. Contrairement aux journaux de diagnostic, un événement des journaux d’activité comporte un groupe d’enregistrements. Nous aurons besoin de diviser ce groupe d’enregistrements plus tard dans le tutoriel. Voici un exemple d’événement de journal d’activité pour une opération de vérification d’accès :

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```

## <a name="set-up-ingestion-pipeline-in-azure-data-explorer"></a>Configurer le pipeline d’ingestion dans Azure Data Explorer 

La configuration du pipeline Azure Data Explorer comporte diverses étapes, notamment la [création de tables et l’ingestion des données](/azure/data-explorer/ingest-sample-data#ingest-data). Vous pouvez également manipuler, mapper et mettre à jour les données.

### <a name="connect-to-azure-data-explorer-web-ui"></a>Connexion à l’IU web d’Azure Data Explorer

1. Dans votre base de données Azure Data Explorer *AzureMonitoring*, sélectionnez **Requête** pour ouvrir l’IU (interface utilisateur) web d’Azure Data Explorer.

    ![Requête](media/ingest-data-no-code/query-database.png)

### <a name="create-target-tables"></a>Créer des tables cibles

Utilisez l’IU web d’Azure Data Explorer pour créer les tables cibles dans la base de données Azure Data Explorer.

#### <a name="diagnostic-logs-table"></a>Table des journaux de diagnostic

1. Créez une table *DiagnosticLogsRecords* dans la base de données *AzureMonitoring*. Cette table doit recevoir les enregistrements des journaux de diagnostic via la commande de contrôle `.create table` :

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Sélectionnez **Exécuter** pour créer la table.

    ![Exécuter la requête](media/ingest-data-no-code/run-query.png)

#### <a name="activity-logs-tables"></a>Tables des journaux d’activité

La structure des journaux d’activité n’étant pas tabulaire, vous devez manipuler les données et développer chaque événement en un ou plusieurs enregistrements. Les données brutes vont être ingérées dans une table intermédiaire, *ActivityLogsRawRecords*. À ce moment-là, les données vont être manipulées et développées. Les données développées vont ensuite être ingérées dans la table *ActivityLogsRecords* à l’aide d’une stratégie de mise à jour. Vous devez donc créer deux tables distinctes pour l’ingestion des journaux d’activité.

1. Créez une table *ActivityLogsRecords* dans la base de données *AzureMonitoring*. Cette table doit recevoir les enregistrements des journaux d’activité. Exécutez la requête Azure Data Explorer suivante pour créer la table :

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Créez la table de données intermédiaire *ActivityLogsRawRecords* dans la base de données *AzureMonitoring* pour la manipulation des données :

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Créer des mappages de table

 Dans la mesure où le format de données est `json`, un mappage de données est nécessaire. Le mappage `json` mappe chaque chemin JSON à un nom de colonne de table.

#### <a name="diagnostic-logs-table-mapping"></a>Mappage de la table des journaux de diagnostic

Utilisez la requête suivante pour mapper les données à la table :

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[{"column":"Timestamp","path":"$.time"},{"column":"ResourceId","path":"$.resourceId"},{"column":"MetricName","path":"$.metricName"},{"column":"Count","path":"$.count"},{"column":"Total","path":"$.total"},{"column":"Minimum","path":"$.minimum"},{"column":"Maximum","path":"$.maximum"},{"column":"Average","path":"$.average"},{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="activity-logs-table-mapping"></a>Mappage de la table des journaux d’activité

Utilisez la requête suivante pour mapper les données à la table :

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-update-policy"></a>Créer une stratégie de mise à jour

1. Créez une [fonction](/azure/kusto/management/functions) qui développe la collection d’enregistrements pour que chaque valeur de la collection reçoive une ligne distincte. Utilisez l’opérateur [`mvexpand`](/azure/kusto/query/mvexpandoperator) :

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mvexpand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Ajoutez une [stratégie de mise à jour](/azure/kusto/concepts/updatepolicy) à la table cible. Elle exécute automatiquement la requête sur toutes les données récemment ingérées dans la table de données intermédiaire *ActivityLogsRawRecords*, et ingère ses résultats dans la table *ActivityLogsRecords* :

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-event-hub-namespace"></a>Créer un espace de noms de hub d’événements

Les journaux de diagnostic Azure permettent d’exporter des métriques vers un compte de stockage ou un hub d’événements. Dans ce tutoriel, nous routons les métriques via un hub d’événements. Au cours des étapes suivantes, vous allez créer un espace de noms de hub d’événements ainsi qu’un hub d’événements pour les journaux de diagnostic. Azure Monitoring va créer le hub d’événements *insights-operational-logs* pour les journaux d’activité.

1. Créez un hub d’événements à l’aide d’un modèle Azure Resource Manager dans le Portail Azure. Utilisez le bouton suivant pour démarrer le déploiement. Cliquez avec le bouton droit et sélectionnez **Ouvrir dans une nouvelle fenêtre** pour pouvoir suivre le reste des étapes de l’article. Le bouton **Déployer sur Azure** vous permet d’accéder au Portail Azure.

    [![Déployer sur Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Créez un espace de noms de hub d’événements ainsi qu’un hub d’événements pour les journaux de diagnostic.

    ![Création d’un hub d’événements](media/ingest-data-no-code/event-hub.png)

    Renseignez le formulaire avec les informations suivantes. Utilisez les valeurs par défaut pour tous les paramètres non listés dans le tableau suivant.

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour votre hub d’événements.|
    | Groupe de ressources | *test-resource-group* | Créez un groupe de ressources. |
    | Lieu | Sélectionnez la région qui répond le mieux à vos besoins. | Créez l’espace de noms de hub d’événements au même emplacement que les autres ressources.
    | Nom de l’espace de noms | *AzureMonitoringData* | Choisissez un nom unique qui identifie votre espace de noms.
    | Nom du hub d’événements | *DiagnosticLogsData* | Le hub d’événements se trouve sous l’espace de noms, qui fournit un conteneur d’étendue unique. |
    | Nom du groupe de consommateurs | *adxpipeline* | Créez un nom de groupe de consommateurs. Permet à plusieurs applications de consommation d’avoir chacune une vue distincte du flux d’événements. |
    | | |

## <a name="connect-azure-monitoring-logs-to-event-hub"></a>Connecter les journaux Azure Monitoring au hub d’événements

### <a name="diagnostic-logs-connection-to-event-hub"></a>Connexion des journaux de diagnostic au hub d’événements

Sélectionnez une ressource à partir de laquelle exporter les métriques. Plusieurs types de ressource permettent d’activer l’exportation des journaux de diagnostic, notamment l’espace de noms de hub d’événements, KeyVault, IoT Hub et le cluster Azure Data Explorer. Dans ce tutoriel, nous utilisons le cluster Azure Data Explorer en tant que ressource.

1. Sélectionnez votre cluster Kusto dans le Portail Azure.

    ![Paramètres de diagnostic](media/ingest-data-no-code/diagnostic-settings.png)

1. Sélectionnez **Paramètres de diagnostic** dans le menu de gauche.
1. Cliquez sur le lien **Activer les diagnostics**. La fenêtre **Paramètres de diagnostic** s’ouvre.

    ![Fenêtre Paramètres de diagnostic](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Dans le volet **Paramètres de diagnostic** :
    1. Nommez vos données de journaux de diagnostic : *ADXExportedData*
    1. Cochez la case **AllMetrics** (facultatif).
    1. Cochez la case **Diffuser vers Event Hub**.
    1. Cliquez sur **Configurer**

1. Dans le volet **Sélectionner un hub d’événements**, configurez l’exportation vers le hub d’événements que vous avez créé :
    1. Dans la liste déroulante, **sélectionnez l’espace de noms de hub d’événements** *AzureMonitoringData*.
    1. Dans la liste déroulante, **sélectionnez le nom de hub d’événements** *diagnosticlogsdata*.
    1. Dans la liste déroulante, **sélectionnez le nom de stratégie du hub d’événements**.
    1. Cliquez sur **OK**.

1. Cliquez sur **Enregistrer**. L’espace de noms, le nom et le nom de stratégie du hub d’événements s’affichent dans la fenêtre.

    ![Enregistrer les paramètres de diagnostic](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="activity-logs-connection-to-event-hub"></a>Connexion des journaux d’activité au hub d’événements

1. Dans le menu de gauche du Portail Azure, sélectionnez **Journal d’activité**
1. La fenêtre **Journal d’activité** s’ouvre. **Cliquez sur Exporter vers Event Hub**

    ![Journal d’activité](media/ingest-data-no-code/activity-log.png)

1. Dans la fenêtre **Exporter le journal d’activité** :
 
    ![Exporter le journal d’activité](media/ingest-data-no-code/export-activity-log.png)

    1. Sélectionnez votre abonnement.
    1. Dans la liste déroulante **Régions**, choisissez **Tout sélectionner**
    1. Cochez la case **Exporter vers un hub d’événements**.
    1. Cliquez sur **Sélectionnez un espace de noms Service Bus** pour ouvrir le volet **Sélectionner un hub d’événements**.
    1. Dans le volet **Sélectionner un hub d’événements**, dans les menus déroulants, sélectionnez votre abonnement, votre espace de noms d’événement *AzureMonitoringData* ainsi que la valeur par défaut du nom de stratégie du hub d’événements.
    1. Cliquez sur **OK**.
    1. Dans le coin supérieur droit de la fenêtre, cliquez sur **Enregistrer**. Un hub d’événements nommé *insights-operational-logs* est créé.

### <a name="see-data-flowing-to-your-event-hubs"></a>Voir le flux des données vers vos hubs d’événements

1. Attendez quelques minutes jusqu’à ce que la connexion soit définie et que l’exportation des journaux d’activité vers le hub d’événements soit effectuée. Accédez à votre espace de noms de hub d’événements pour voir les hubs d’événements que vous avez créés.

    ![Hubs d’événements créés](media/ingest-data-no-code/event-hubs-created.png)

1. Visualisez le flux des données vers votre hub d’événements :

    ![Données des hubs d’événements](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-event-hub-to-azure-data-explorer"></a>Connecter le hub d’événements à Azure Data Explorer

### <a name="diagnostic-logs-data-connection"></a>Connexion de données des journaux de diagnostic

1. Dans votre cluster Azure Data Explorer *kustodocs*, dans le menu de gauche, sélectionnez **Bases de données**.
1. Dans la fenêtre **Bases de données**, sélectionnez le nom de votre base de données, *AzureMonitoring*.
1. Dans le menu de gauche, sélectionnez **Ingestion des données**
1. Dans la fenêtre **Ingestion des données**, cliquez sur **+ Ajouter la connexion de données**
1. Dans la fenêtre **Connexion aux données**, entrez les informations suivantes :

    ![Connexion au hub d’événements](media/ingest-data-no-code/event-hub-data-connection.png)

    Source de données :

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Nom de la connexion de données | *DiagnosticsLogsConnection* | Nom de la connexion que vous souhaitez créer dans l’Explorateur de données Azure.|
    | Espace de noms du hub d’événements | *AzureMonitoringData* | Nom choisi précédemment qui identifie votre espace de noms. |
    | Event Hub | *diagnosticlogsdata* | Hub d’événements que vous avez créé. |
    | Groupe de consommateurs | *adxpipeline* | Groupe de consommateurs défini dans le hub d’événements que vous avez créé. |
    | | |

    Table cible :

    Deux options sont disponibles pour le routage : *statique* et *dynamique*. Pour ce tutoriel, vous utilisez le routage statique (par défaut), où vous spécifiez le nom de table, le format de fichier et le mappage. Par conséquent, ne sélectionnez pas **My data includes routing info** (Mes données incluent des informations de routage).

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Table | *DiagnosticLogsRecords* | Table que vous avez créée dans la base de données *AzureMonitoring*. |
    | Format de données | *JSON* | Format de la table. |
    | Mappage de colonnes | *DiagnosticLogsRecordsMapping* | Mappage que vous avez créé dans la base de données *AzureMonitoring*. Il permet de mapper les données JSON entrantes aux noms de colonnes et aux types de données de *DiagnosticLogsRecords*.|
    | | |

1. Cliquez sur **Créer**  

### <a name="activity-logs-data-connection"></a>Connexion de données des journaux d’activité

Répétez les étapes décrites dans la section [Connexion de données des journaux de diagnostic](#diagnostic-logs-data-connection) pour créer la connexion de données des journaux d’activité.

1. Insérez les paramètres suivants dans la fenêtre **Connexion aux données** :

    Source de données :

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Nom de la connexion de données | *ActivityLogsConnection* | Nom de la connexion que vous souhaitez créer dans l’Explorateur de données Azure.|
    | Espace de noms du hub d’événements | *AzureMonitoringData* | Nom choisi précédemment qui identifie votre espace de noms. |
    | Event Hub | *insights-operational-logs* | Hub d’événements que vous avez créé. |
    | Groupe de consommateurs | *$Default* | Groupe de consommateurs par défaut. Si nécessaire, vous pouvez créer un autre groupe de consommateurs. |
    | | |

    Table cible :

    Deux options sont disponibles pour le routage : *statique* et *dynamique*. Pour ce tutoriel, vous utilisez le routage statique (par défaut), où vous spécifiez le nom de table, le format de fichier et le mappage. Par conséquent, ne sélectionnez pas **My data includes routing info** (Mes données incluent des informations de routage).

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Table | *ActivityLogsRawRecords* | Table que vous avez créée dans la base de données *AzureMonitoring*. |
    | Format de données | *JSON* | Format de la table. |
    | Mappage de colonnes | *ActivityLogsRawRecordsMapping* | Mappage que vous avez créé dans la base de données *AzureMonitoring*. Il permet de mapper les données JSON entrantes aux noms de colonnes et aux types de données de *ActivityLogsRawRecords*.|
    | | |

1. Cliquez sur **Créer**  

## <a name="query-the-new-tables"></a>Interroger les nouvelles tables

Vous avez un pipeline avec un flux de données. Par défaut, l’ingestion via le cluster dure 5 minutes. Vous devez donc laisser au flux de données quelques minutes avant de commencer l’exécution des requêtes.

### <a name="diagnostic-logs-table-query-example"></a>Exemple d’interrogation de la table des journaux de diagnostic

La requête suivante analyse les données de durée de requête à partir des enregistrements de journaux de diagnostic Azure Data Explorer :

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Résultats de la requête :

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="activity-logs-table-query-example"></a>Exemple d’interrogation de la table des journaux d’activité

La requête suivante analyse les données à partir des enregistrements de journaux d’activité Azure Data Explorer :

```kusto
ActivityLogsRecords
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Résultats de la requête :
|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

## <a name="next-steps"></a>Étapes suivantes

Apprenez à écrire de nombreuses autres requêtes sur les données extraites à partir d’Azure Data Explorer grâce à l’article suivant :

> [!div class="nextstepaction"]
> [Écrire des requêtes pour Azure Data Explorer](write-queries.md)
