---
title: 'Tutoriel : Ingérer les données des journaux de diagnostic et d’activité dans Azure Data Explorer sans une seule ligne de code'
description: Dans ce tutoriel, vous allez apprendre à ingérer des données dans Azure Data Explorer sans une seule ligne de code et découvrir comment interroger ces données.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: c171962fd6177a01afdb8e9605b09574c99f485e
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889220"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Tutoriel : Ingérer des données dans Azure Data Explorer sans une seule ligne de code

Ce tutoriel va vous apprendre à ingérer des données de journaux de diagnostic et d’activité dans un cluster Azure Data Explorer sans écrire de code. Avec cette méthode d’ingestion simple, vous pouvez commencer rapidement à interroger Azure Data Explorer pour l’analyse de données.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer des tables et un mappage d’ingestion dans une base de données Azure Data Explorer
> * Mettre en forme les données ingérées à l’aide d’une stratégie de mise à jour
> * Créer un [hub d’événements](/azure/event-hubs/event-hubs-about) et le connecter à Azure Data Explorer
> * Effectuer un streaming des données vers un hub d’événements à partir des [journaux de diagnostic Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) et des [journaux d’activité Azure Monitor](/azure/azure-monitor/platform/activity-logs-overview)
> * Interroger les données ingérées à l’aide d’Azure Data Explorer

> [!NOTE]
> Créez toutes les ressources dans le même emplacement ou la même région Azure. Il s’agit d’une exigence pour les journaux de diagnostic Azure Monitor.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md). Dans ce tutoriel, le nom de la base de données est *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-and-activity-logs"></a>Fournisseur de données Azure Monitor : journaux de diagnostic et d’activité

Visualisez et interprétez les données fournies par les journaux de diagnostic et d’activité Azure Monitor. Nous allons créer un pipeline d’ingestion basé sur ces schémas de données.

### <a name="diagnostic-logs-example"></a>Exemple de journaux de diagnostic

Les journaux de diagnostic Azure sont des métriques émises par un service Azure. Elles fournissent des données sur le fonctionnement de ce service. Les données sont agrégées avec un fragment de temps de 1 minute. Chaque événement d’un journal de diagnostic contient un enregistrement. Voici un exemple de schéma d’événement de métrique Azure Data Explorer pour la durée d’une requête :

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

Les journaux d’activité Azure sont des journaux de niveau abonnement qui contiennent une collection d’enregistrements. Les journaux fournissent un insight à propos des opérations effectuées sur les ressources de votre abonnement. Contrairement aux journaux de diagnostic, un événement d’un journal d’activité comporte un tableau d’enregistrements. Nous allons devoir diviser ce tableau d’enregistrements plus tard dans le tutoriel. Voici un exemple d’événement de journal d’activité pour une opération de vérification d’accès :

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

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Configurer un pipeline d’ingestion dans Azure Data Explorer

La configuration d’un pipeline Azure Data Explorer implique plusieurs étapes, comme la [création de tables et l’ingestion de données](/azure/data-explorer/ingest-sample-data#ingest-data). Vous pouvez également manipuler, mapper et mettre à jour les données.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Se connecter à l’interface utilisateur web d’Azure Data Explorer

Dans votre base de données Azure Data Explorer *TestDatabase*, sélectionnez **Requête** pour ouvrir l’interface utilisateur web d’Azure Data Explorer.

![Page de requête](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Créer les tables cibles

Utilisez l’interface utilisateur web d’Azure Data Explorer pour créer les tables cibles dans la base de données Azure Data Explorer.

#### <a name="the-diagnostic-logs-table"></a>La table des journaux de diagnostic

1. Dans la base de données *TestDatabase*, créez une table nommée *DiagnosticLogsRecords* pour stocker les enregistrements des journaux de diagnostic. Utilisez la commande de contrôle `.create table` suivante :

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Sélectionnez **Exécuter** pour créer la table.

    ![Exécuter une requête](media/ingest-data-no-code/run-query.png)

#### <a name="the-activity-logs-tables"></a>Les tables des journaux d’activité

La structure des journaux d’activité n’étant pas tabulaire, vous devez manipuler les données et développer chaque événement en un ou plusieurs enregistrements. Les données brutes vont être ingérées dans une table intermédiaire nommée *ActivityLogsRawRecords*. À ce moment-là, les données vont être manipulées et développées. Les données développées vont ensuite être ingérées dans la table *ActivityLogsRecords* à l’aide d’une stratégie de mise à jour. Cela signifie que vous devez créer deux tables distinctes pour l’ingestion des journaux d’activité.

1. Créez une table nommée *ActivityLogsRecords* dans la base de données *TestDatabase* pour recevoir les enregistrements des journaux d’activité. Pour créer la table, exécutez la requête Azure Data Explorer suivante :

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Créez la table de données intermédiaire nommée *ActivityLogsRawRecords* dans la base de données *TestDatabase* pour la manipulation des données :

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Créer des mappages de table

 Comme le format des données est `json`, un mappage de données est nécessaire. Le mappage `json` mappe chaque chemin JSON à un nom de colonne de table.

#### <a name="table-mapping-for-diagnostic-logs"></a>Mappage de table pour les journaux de diagnostic

Pour mapper les données des journaux de diagnostic à la table, utilisez la requête suivante :

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[{"column":"Timestamp","path":"$.time"},{"column":"ResourceId","path":"$.resourceId"},{"column":"MetricName","path":"$.metricName"},{"column":"Count","path":"$.count"},{"column":"Total","path":"$.total"},{"column":"Minimum","path":"$.minimum"},{"column":"Maximum","path":"$.maximum"},{"column":"Average","path":"$.average"},{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="table-mapping-for-activity-logs"></a>Mappage de table pour les journaux d’activité

Pour mapper les données des journaux d’activité à la table, utilisez la requête suivante :

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-the-update-policy-for-activity-logs-data"></a>Créer la stratégie de mise à jour pour les données des journaux d’activité

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

2. Ajoutez la [stratégie de mise à jour](/azure/kusto/concepts/updatepolicy) à la table cible. Cette stratégie exécute automatiquement la requête sur toutes les données récemment ingérées dans la table de données intermédiaire *ActivityLogsRawRecords*, et ingère ses résultats dans la table *ActivityLogsRecords* :

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-azure-event-hubs-namespace"></a>Créer un espace de noms Azure Event Hubs

Les journaux de diagnostic Azure permettent d’exporter des métriques vers un compte de stockage ou vers un hub d’événements. Dans ce tutoriel, nous allons router les métriques via un hub d’événements. Dans les étapes suivantes, vous allez créer un espace de noms Event Hubs ainsi qu’un hub d’événements pour les journaux de diagnostic. Azure Monitor va créer le hub d’événements *insights-operational-logs* pour les journaux d’activité.

1. Créez un hub d’événements à l’aide d’un modèle Azure Resource Manager dans le portail Azure. Pour suivre le reste des étapes de l’article, cliquez avec le bouton droit sur le bouton **Déployer sur Azure**, puis sélectionnez **Ouvrir dans une nouvelle fenêtre**. Le bouton **Déployer sur Azure** vous permet d’accéder au Portail Azure.

    [![Bouton Déployer sur Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Créez un espace de noms Event Hubs ainsi qu’un hub d’événements pour les journaux de diagnostic.

    ![Création d’un hub d’événements](media/ingest-data-no-code/event-hub.png)

1. Renseignez le formulaire avec les informations suivantes. Pour tous les paramètres non listés dans le tableau suivant, utilisez les valeurs par défaut.

    **Paramètre** | **Valeur suggérée** | **Description**
    |---|---|---|
    | **Abonnement** | *Votre abonnement* | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour votre hub d’événements.|
    | **Groupe de ressources** | *test-resource-group* | Créez un groupe de ressources. |
    | **Lieu** | Sélectionnez la région qui répond le mieux à vos besoins. | Créez l’espace de noms Event Hubs au même emplacement que les autres ressources.
    | **Nom de l’espace de noms** | *AzureMonitoringData* | Choisissez un nom unique qui identifie votre espace de noms.
    | **Nom du hub d’événements** | *DiagnosticLogsData* | Le hub d’événements se trouve sous l’espace de noms, qui fournit un conteneur d’étendue unique. |
    | **Nom du groupe de consommateurs** | *adxpipeline* | Créez un nom de groupe de consommateurs. Les groupes de consommateurs permettent que chacune des applications de consommation ait une vue distincte du flux d’événements. |
    | | |

## <a name="connect-azure-monitor-logs-to-your-event-hub"></a>Connecter les journaux Azure Monitor à votre hub d’événements

Vous devez maintenant connecter vos journaux de diagnostic et vos journaux d’activité au hub d’événements.

### <a name="connect-diagnostic-logs-to-your-event-hub"></a>Connecter des journaux de diagnostic à votre hub d’événements

Sélectionnez une ressource à partir de laquelle exporter les métriques. Plusieurs types de ressources prennent en charge l’exportation des journaux de diagnostic, notamment l’espace de noms Event Hubs, Azure Key Vault, Azure IoT Hub, et les clusters Azure Data Explorer. Dans ce tutoriel, nous allons utiliser le cluster Azure Data Explorer comme ressource.

1. Sélectionnez votre cluster Kusto dans le Portail Azure.
1. Sélectionnez **Paramètres de diagnostic**, puis le lien **Activer les diagnostics**. 

    ![Paramètres de diagnostic](media/ingest-data-no-code/diagnostic-settings.png)

1. Le volet **Paramètres de diagnostic** s’ouvre. Procédez comme suit :
    1. Donnez à vos données de journaux de diagnostic le nom *ADXExportedData*.
    1. Sous **MÉTRIQUE**, cochez la case **AllMetrics** (facultatif).
    1. Cochez la case **Diffuser vers Event Hub**.
    1. Sélectionnez **Configurer**.

    ![Volet Paramètres de diagnostic](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Dans le volet **Sélectionner un hub d’événements** , configurez la façon d’exporter des données de journaux de diagnostic vers le hub d’événements que vous avez créé :
    1. Dans la liste **Sélectionner l’espace de noms de Event Hub**, sélectionnez *AzureMonitoringData*.
    1. Dans la liste **Sélectionner un nom d’Event Hub**, sélectionnez *diagnosticlogsdata*.
    1. Dans la liste **Sélectionner un nom de stratégie de hub d’événements**, sélectionnez **RootManagerSharedAccessKey**.
    1. Sélectionnez **OK**.

1. Sélectionnez **Enregistrer**.

### <a name="connect-activity-logs-to-your-event-hub"></a>Connecter des journaux d’activité à votre hub d’événements

1. Dans le menu de gauche du portail Azure, sélectionnez **Journal d’activité**.
1. La fenêtre **Journal d’activité** s’ouvre. Sélectionnez **Exporter vers Event Hub**.

    ![Fenêtre Journal d’activité](media/ingest-data-no-code/activity-log.png)

1. La fenêtre **Exporter le journal d’activité** s’ouvre :
 
    ![Fenêtre Exporter le journal d’activité](media/ingest-data-no-code/export-activity-log.png)

1. Dans la fenêtre **Exporter le journal d’activité**, effectuez les étapes suivantes :
      1. Sélectionnez votre abonnement.
      1. Dans la liste **Régions**, choisissez **Sélectionner tout**.
      1. Cochez la case **Exporter vers un hub d’événements**.
      1. Choisissez **Sélectionnez un espace de noms Service Bus** pour ouvrir le volet **Sélectionner un hub d’événements**.
      1. Dans le volet **Sélectionner un hub d’événements** , sélectionnez votre abonnement.
      1. Dans la liste **Sélectionner l’espace de noms de Event Hub**, sélectionnez *AzureMonitoringData*.
      1. Dans la liste **Sélectionner un nom de stratégie de hub d’événements**, sélectionnez le nom de stratégie de hub d’événements par défaut.
      1. Sélectionnez **OK**.
      1. Dans l’angle supérieur gauche de la fenêtre, sélectionnez **Enregistrer**.
   Un hub d’événements nommé *insights-operational-logs* est créé.

### <a name="see-data-flowing-to-your-event-hubs"></a>Visualiser le flux des données vers vos hubs d’événements

1. Attendez quelques minutes jusqu’à ce que la connexion soit définie et que l’exportation des journaux d’activité vers le hub d’événements soit terminée. Accédez à votre espace de noms Event Hubs pour voir les hubs d’événements que vous avez créés.

    ![Hubs d’événements créés](media/ingest-data-no-code/event-hubs-created.png)

1. Visualisez le flux des données vers votre hub d’événements :

    ![Données du hub d’événements](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Connecter un hub d’événements à Azure Data Explorer

Vous devez maintenant créer les connexions de données pour vos journaux de diagnostic et vos journaux d’activité.

### <a name="create-the-data-connection-for-diagnostic-logs"></a>Créer la connexion de données pour les journaux de diagnostic

1. Dans votre cluster Azure Data Explorer nommé *kustodocs*, sélectionnez **Bases de données** dans le menu de gauche.
1. Dans la fenêtre **Bases de données**, sélectionnez votre base de données *TestDatabase*.
1. Dans le menu de gauche, sélectionnez **Ingestion des données**.
1. Dans la fenêtre **Ingestion des données**, cliquez sur **+ Ajouter la connexion de données**.
1. Dans la fenêtre **Connexion aux données**, entrez les informations suivantes :

    ![Connexion des données du hub d’événements](media/ingest-data-no-code/event-hub-data-connection.png)

    Source de données :

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | **Nom de la connexion de données** | *DiagnosticsLogsConnection* | Nom de la connexion que vous souhaitez créer dans l’Explorateur de données Azure.|
    | **Espace de noms du hub d’événements** | *AzureMonitoringData* | Nom choisi précédemment qui identifie votre espace de noms. |
    | **Hub d’événements** | *diagnosticlogsdata* | Hub d’événements que vous avez créé. |
    | **Groupe de consommateurs** | *adxpipeline* | Groupe de consommateurs défini dans le hub d’événements que vous avez créé. |
    | | |

    Table cible :

    Deux options sont disponibles pour le routage : *statique* et *dynamique*. Pour ce tutoriel, vous allez utiliser le routage statique (valeur par défaut), où vous spécifiez le nom de table, le format des données et le mappage. Ne sélectionnez pas **My data includes routing info** (Mes données incluent des informations de routage).

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | **Table** | *DiagnosticLogsRecords* | Table que vous avez créée dans la base de données *TestDatabase*. |
    | **Format de données** | *JSON* | Format utilisé dans la table. |
    | **Mappage de colonnes** | *DiagnosticLogsRecordsMapping* | Mappage que vous avez créé dans la base de données *TestDatabase*. Il mappe les données JSON entrantes aux noms de colonnes et aux types de données de la table *DiagnosticLogsRecords*.|
    | | |

1. Sélectionnez **Créer**.  

### <a name="create-the-data-connection-for-activity-logs"></a>Créer la connexion de données pour les journaux d’activité

Répétez les étapes décrites dans la section « Créer la connexion de données pour les journaux de diagnostic » pour créer la connexion de données pour vos journaux d’activité.

1. Utilisez les paramètres suivants dans la fenêtre **Connexion de données** :

    Source de données :

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | **Nom de la connexion de données** | *ActivityLogsConnection* | Nom de la connexion que vous souhaitez créer dans l’Explorateur de données Azure.|
    | **Espace de noms du hub d’événements** | *AzureMonitoringData* | Nom choisi précédemment qui identifie votre espace de noms. |
    | **Hub d’événements** | *insights-operational-logs* | Hub d’événements que vous avez créé. |
    | **Groupe de consommateurs** | *$Default* | Groupe de consommateurs par défaut. Si nécessaire, vous pouvez créer un autre groupe de consommateurs. |
    | | |

    Table cible :

    Deux options sont disponibles pour le routage : *statique* et *dynamique*. Pour ce tutoriel, vous allez utiliser le routage statique (valeur par défaut), où vous spécifiez le nom de table, le format des données et le mappage. Ne sélectionnez pas **My data includes routing info** (Mes données incluent des informations de routage).

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | **Table** | *ActivityLogsRawRecords* | Table que vous avez créée dans la base de données *TestDatabase*. |
    | **Format de données** | *JSON* | Format utilisé dans la table. |
    | **Mappage de colonnes** | *ActivityLogsRawRecordsMapping* | Mappage que vous avez créé dans la base de données *TestDatabase*. Il mappe les données JSON entrantes aux noms de colonnes et aux types de données de la table *ActivityLogsRawRecords*.|
    | | |

1. Sélectionnez **Créer**.  

## <a name="query-the-new-tables"></a>Interroger les nouvelles tables

Vous avez maintenant un pipeline avec un flux de données. Par défaut, l’ingestion via le cluster prend 5 minutes. Vous devez donc laisser les données circuler quelques minutes avant de commencer l’exécution de requêtes.

### <a name="an-example-of-querying-the-diagnostic-logs-table"></a>Exemple d’interrogation de la table des journaux de diagnostic

La requête suivante analyse les données de durée de requête à partir des enregistrements de journaux dans Azure Data Explorer :

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

### <a name="an-example-of-querying-the-activity-logs-table"></a>Exemple d’interrogation de la table des journaux d’activité

La requête suivante analyse les données à partir des enregistrements de journaux d’activité dans Azure Data Explorer :

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

Consultez l’article suivant pour apprendre à écrire de nombreuses autres requêtes sur les données extraites à partir d’Azure Data Explorer :

> [!div class="nextstepaction"]
> [Écrire des requêtes pour l’Explorateur de données Azure](write-queries.md)
