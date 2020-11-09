---
title: Démarrage rapide de Metrics Advisor Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 10/14/2020
ms.author: mbullwin
ms.openlocfilehash: cf4404bbfe7e8f0ad664c9ca8dda07ff61be12d9
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186943"
---
[Documentation de référence](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md) | [Package (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/) | [Exemples C#](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Une fois que vous disposez de votre abonnement Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Créer une ressource Metrics Advisor"  target="_blank">créez une ressource Metrics Advisor<span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour déployer votre instance Metrics Advisor.  
* Votre propre base de données SQL avec des données de séries chronologiques.
  
> [!TIP]
> * Vous trouverez des exemples Metrics Advisor Python sur [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples).
> * 10 à 30 minutes peuvent être nécessaires pour que votre ressource Metrics Advisor déploie une instance de service que vous puissiez utiliser. Cliquez sur **Accéder à la ressource** une fois le déploiement réussi. Après le déploiement, vous pouvez commencer à utiliser votre instance Metrics Advisor avec le portail web et l’API REST.
> * Vous trouverez l’URL de l’API REST dans le portail Azure, dans la section **Vue d’ensemble** de votre ressource. Voici le résultat :
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>Configuration

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Après avoir installé Python, vous pouvez installer la bibliothèque de client avec :

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>Créer une application Python

Créez un fichier Python et importez les bibliothèques suivantes.

```python
import os
import datetime
```

Créez des variables pour le point de terminaison et la clé Azure de votre ressource.

> [!IMPORTANT]
> Accédez au portail Azure. Si la ressource Metrics Advisor que vous avez créée dans la section **Prérequis** a été déployée, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. Vos clés d’abonnement et le point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. <br><br>Pour récupérer votre clé API, vous devez accéder à [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net). Sélectionnez les éléments appropriés : **Annuaire** , **Abonnements** et **Espace de travail** pour votre ressource, puis choisissez **Bien démarrer**. Vous pourrez ensuite récupérer vos clés API à partir de [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key).   
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Pour plus d’informations, consultez l’article sur la [sécurité](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) de Cognitive Services.

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>Modèle objet

Les classes suivantes gèrent certaines des fonctionnalités principales du kit SDK Metrics Advisor Python.

|Name|Description|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **Utilisé pour**  : <br> – Lister les incidents <br> – Lister les causes premières des incidents <br> – Récupérer les données de série chronologique d’origine et les données de série chronologique enrichies par le service <br> – Lister les alertes <br> – Ajouter des commentaires pour ajuster votre modèle |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html?highlight=metricsadvisoradministrationclient#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient)| **Vous permet d’effectuer les tâches suivantes :** <br> – Gérer les flux de données <br> – Créer, configurer, récupérer, lister et supprimer des configurations de détection des anomalies <br> – Créer, configurer, récupérer, lister et supprimer des configurations d’alertes d’anomalies <br> – Gérer les hooks  | |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **Ce que Metrics Advisor ingère de votre source de données. Un `DataFeed` contient des lignes de :** <br> – Horodatages <br> – Zéro ou plusieurs dimensions <br> – Une ou plusieurs mesures  |
| [Mesure](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=metric#azure.ai.metricsadvisor.models.Metric) | Un objet `Metric` est une mesure quantifiable utilisée pour surveiller et évaluer l’état d’un processus d’entreprise spécifique. Il peut s’agir d’une combinaison de plusieurs valeurs de séries chronologiques divisées en dimensions. Par exemple, une métrique d’intégrité du Web peut contenir des dimensions pour le nombre d’utilisateurs et le marché en-US. |

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Metrics Advisor pour Python :

* [Authentifier le client](#authenticate-the-client)
* [Ajouter un flux de données](#add-a-data-feed)
* [Vérifier l’état d’ingestion](#check-the-ingestion-status)
* [Paramétrer la configuration de détection et la configuration d’alerte](#configure-anomaly-detection)
* [Créer une configuration d’alerte](#create-an-alert-configuration)
* [Interroger les résultats de la détection des anomalies](#query-the-alert)

## <a name="authenticate-the-client"></a>Authentifier le client

Dans cet exemple, le client est un objet `MetricsAdvisorAdministrationClient` qui utilise votre point de terminaison et un objet `MetricsAdvisorKeyCredential` qui contient vos clés. Vous n’avez pas besoin de copier cet exemple de code. Les méthodes que vous créerez ultérieurement instancieront le client. L’autre client est appelé `MetricsAdvisorClient`. Vous trouverez plus d’informations sur ce client dans la [documentation de référence](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient).

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>Ajouter un flux de données

Dans une nouvelle méthode, créez des instructions import comme dans l’exemple ci-dessous. Remplacez `sql_server_connection_string` par votre propre chaîne de connexion au serveur SQL Server, puis remplacez `query` par une requête qui retourne vos données à un horodatage unique. Vous devrez également ajuster les valeurs `metric` et `dimension` en fonction de vos données personnalisées.

> [!IMPORTANT]
> La requête doit retourner au plus un enregistrement pour chaque combinaison de dimensions, à chaque horodatage. Et tous les enregistrements retournés par la requête doivent avoir les mêmes horodatages. Metrics Advisor exécutera cette requête pour chaque horodatage afin d’ingérer vos données. Pour plus d’informations et des exemples, consultez la [Section FAQ sur les requêtes](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data). 

Créez un client avec vos clés et votre point de terminaison, puis utilisez `client.create_data_feed()` pour configurer le nom, la source, la précision et le schéma. Vous pouvez également définir l’heure d’ingestion, les paramètres de cumul, etc.


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        Metric,
        Dimension,
        DataFeedOptions,
        DataFeedRollupSettings,
        DataFeedMissingDataPointFillSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
        name="My data feed",
        source=SQLServerDataFeed(
            connection_string=sql_server_connection_string,
            query=query,
        ),
        granularity="Daily",
        schema=DataFeedSchema(
            metrics=[
                Metric(name="cost", display_name="Cost"),
                Metric(name="revenue", display_name="Revenue")
            ],
            dimensions=[
                Dimension(name="category", display_name="Category"),
                Dimension(name="city", display_name="City")
            ],
            timestamp_column="Timestamp"
        ),
        ingestion_settings=datetime.datetime(2019, 10, 1),
        options=DataFeedOptions(
            data_feed_description="cost/revenue data feed",
            rollup_settings=DataFeedRollupSettings(
                rollup_type="AutoRollup",
                rollup_method="Sum",
                rollup_identification_value="__CUSTOM_SUM__"
            ),
            missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
                fill_type="SmartFilling"
            ),
            access_mode="Private"
        )
    )

    return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>Vérifier l’état d’ingestion

Dans une nouvelle méthode, créez une instruction import comme dans l’exemple ci-dessous. Remplacez `data_feed_id` par l’ID du flux de données que vous avez créé. Créez un client avec vos clés et votre point de terminaison, puis utilisez `client.get_data_feed_ingestion_progress()` pour obtenir la progression de l’ingestion. Affichez les détails, tels que les derniers horodatages actifs et réussis.


```python
def sample_get_data_feed_ingestion_progress():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    progress = client.get_data_feed_ingestion_progress(data_feed_id)

    print("Latest active timestamp: {}".format(progress.latest_active_timestamp))
    print("Latest successful timestamp: {}".format(progress.latest_success_timestamp))
sample_get_data_feed_ingestion_progress()
```

## <a name="configure-anomaly-detection"></a>Configurer la détection des anomalies

Dans une nouvelle méthode, créez des instructions import comme dans l’exemple ci-dessous. Remplacez `metric_id` par l’ID de la métrique que vous souhaitez configurer. Créez un client avec vos clés et votre point de terminaison, puis utilisez `client.create_metric_anomaly_detection_configuration` pour créer une nouvelle configuration de détection. Les conditions de seuil spécifient les paramètres pour la détection des anomalies.

```python
def sample_create_detection_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    change_threshold_condition = ChangeThresholdCondition(
        anomaly_detector_direction="Both",
        change_percentage=20,
        shift_point=10,
        within_range=True,
        suppress_condition=SuppressCondition(
            min_number=5,
            min_ratio=2
        )
    )
    hard_threshold_condition = HardThresholdCondition(
        anomaly_detector_direction="Up",
        upper_bound=100,
        suppress_condition=SuppressCondition(
            min_number=2,
            min_ratio=2
        )
    )
    smart_detection_condition = SmartDetectionCondition(
        anomaly_detector_direction="Up",
        sensitivity=10,
        suppress_condition=SuppressCondition(
            min_number=2,
            min_ratio=2
        )
    )

    detection_config = client.create_metric_anomaly_detection_configuration(
        name="my_detection_config",
        metric_id=metric_id,
        description="anomaly detection config for metric",
        whole_series_detection_condition=MetricDetectionCondition(
            cross_conditions_operator="OR",
            change_threshold_condition=change_threshold_condition,
            hard_threshold_condition=hard_threshold_condition,
            smart_detection_condition=smart_detection_condition
        )
    )

    return detection_config
sample_create_detection_config()
```


## <a name="create-a-hook"></a>Créer un hook

Dans une nouvelle méthode, créez des instructions import comme dans l’exemple ci-dessous. Créez un client avec vos clés et votre point de terminaison, puis utilisez `client.create_hook()` pour créer un hook. Entrez une description, la liste des adresses e-mail auxquelles envoyer l’alerte et un lien externe pour recevoir l’alerte.  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    hook = client.create_hook(
        name="email hook",
        hook=EmailHook(
            description="my email hook",
            emails_to_alert=["alertme@contoso.com"],
            external_link="https://adwiki.azurewebsites.net/articles/howto/alerts/create-hooks.html"
        )
    )

    return hook
sample_create_hook()
```

##  <a name="create-an-alert-configuration"></a>Créer une configuration d’alerte

Dans une nouvelle méthode, créez des instructions import comme dans l’exemple ci-dessous. Remplacez `anomaly_detection_configuration_id` par l’ID de la configuration de détection des anomalies, et remplacez `hook_id` par le hook que vous avez créé précédemment. Créez un client avec vos clés et votre point de terminaison, puis utilisez `client.create_anomaly_alert_configuration()` pour créer une configuration d’alerte. `metric_alert_configurations` est une liste d’objets `MetricAlertConfiguration` qui spécifient les conditions et la portée pour chaque configuration.

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    alert_config = client.create_anomaly_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=anomaly_detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=anomaly_detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>Interroger l’alerte

Dans une nouvelle méthode, créez une instruction import comme dans l’exemple ci-dessous. Remplacez `alert_id` par l’ID de votre alerte et remplacez `alert_config_id` par l’ID de configuration de l’alerte. Créez un client avec vos clés et votre point de terminaison, puis utilisez `client.list_anomalies_for_alert()` pour lister une configuration d’alerte. 

```python
def sample_list_anomalies_for_alert(alert_config_id, alert_id):

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
    alert_id = "<replace-with-your-alert-id>"
    alert_config_id = "<replace-with-your-alert-configuration-id"
    client = MetricsAdvisorClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    results = client.list_anomalies_for_alert(
            alert_configuration_id=alert_config_id,
            alert_id=alert_id,
        )
    for result in results:
        print("Create on: {}".format(result.created_on))
        print("Severity: {}".format(result.severity))
        print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `python` de votre fichier de démarrage rapide.

```console
python quickstart-file.py
```