---
title: Démarrage rapide de Metrics Advisor Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 10/14/2020
ms.author: mbullwin
ms.openlocfilehash: ff8a09e32a44f51571cca93655f91080e5df9a50
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186935"
---
[Documentation de référence](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src) | [Artefact (Maven)](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor) | [Exemples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* La version actuelle du [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* L’[outil de génération Gradle](https://gradle.org/install/) ou un autre gestionnaire de dépendances.
* Une fois que vous disposez de votre abonnement Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Créer une ressource Metrics Advisor"  target="_blank">créez une ressource Metrics Advisor<span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour déployer votre instance Metrics Advisor.  
* Votre propre base de données SQL avec des données de séries chronologiques.
  
  
> [!TIP]
> * Vous trouverez des exemples Metrics Advisor Java sur [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples).
> * 10 à 30 minutes peuvent être nécessaires pour que votre ressource Metrics Advisor déploie une instance de service que vous puissiez utiliser. Cliquez sur **Accéder à la ressource** une fois le déploiement réussi. Après le déploiement, vous pouvez commencer à utiliser votre instance Metrics Advisor avec le portail web et l’API REST. 
> * Vous trouverez l’URL de l’API REST dans le portail Azure, dans la section **Vue d’ensemble** de votre ressource. Voici le résultat :
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>Configuration

### <a name="create-a-new-gradle-project"></a>Créer un projet Gradle

Ce guide de démarrage rapide utilise le gestionnaire de dépendances Gradle. Vous trouverez plus d’informations sur la bibliothèque de client dans le [référentiel central Maven](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `gradle init` à partir de votre répertoire de travail. Cette commande crée des fichiers de build essentiels pour Gradle, notamment *build.gradle.kts* , qui est utilisé au moment de l’exécution pour créer et configurer votre application.

```console
gradle init --type basic
```

Quand vous êtes invité à choisir un **DSL** , sélectionnez **Kotlin**.

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Recherchez *build.gradle.kts* et ouvrez-le avec votre IDE ou votre éditeur de texte habituel. Copiez-y ensuite cette configuration de build. Veillez à inclure les dépendances du projet.

```kotlin
dependencies {
    compile("com.azure:azure-ai-metricsadvisor:1.0.0-beta.1")
}
```

### <a name="create-a-java-file"></a>Créer un fichier Java

Créez un dossier pour votre exemple d’application. Entrez la commande suivante à partir de votre répertoire de travail :

```console
mkdir -p src/main/java
```

Accédez au nouveau dossier et créez le fichier *MetricsAdvisorQuickstarts.java*. Ouvrez-le dans votre éditeur ou IDE habituel et ajoutez les instructions `import` suivantes :

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez sur [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples), qui contient les exemples de code utilisés dans ce guide de démarrage rapide.

Dans la classe `MetricsAdvisorQuickstarts` de l’application, créez des variables pour la clé et le point de terminaison de votre ressource.


> [!IMPORTANT]
> Accédez au portail Azure. Si la ressource Metrics Advisor que vous avez créée dans la section **Prérequis** a été déployée, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. Vos clés d’abonnement et le point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. <br><br>Pour récupérer votre clé API, vous devez accéder à [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net). Sélectionnez les éléments appropriés : **Annuaire** , **Abonnements** et **Espace de travail** pour votre ressource, puis choisissez **Bien démarrer**. Vous pourrez ensuite récupérer vos clés API à partir de [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key).   
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Pour plus d’informations, consultez l’article sur la [sécurité](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) de Cognitive Services.

```java
private static String SUBSCRIPTION_KEY = "<replace-with-your-metrics-advisor-subscription-key-here>";
private static String API_KEY = "<replace-with-your-metrics-advisor-api-key-here>"
private static String ENDPOINT = "<replace-with-your-metrics-advisor-endpoint-here>";
```

Dans la méthode `Main()` de l’application, ajoutez des appels pour les méthodes utilisées dans ce guide de démarrage rapide. Vous les créerez ultérieurement.

```java
static void Main(string[] args){

    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Modèle objet

Les classes suivantes gèrent certaines des fonctionnalités principales du kit SDK Metrics Advisor Java.

|Name|Description|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/MetricsAdvisorClient.html) | **Utilisé pour**  : <br> – Lister les incidents <br> – Lister les causes premières des incidents <br> – Récupérer les données de série chronologique d’origine et les données de série chronologique enrichies par le service <br> – Lister les alertes <br> – Ajouter des commentaires pour ajuster votre modèle |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/administration/MetricsAdvisorAdministrationClient.html)| **Vous permet d’effectuer les tâches suivantes :** <br> – Gérer les flux de données <br> – Paramétrer les configurations de détection des anomalies <br> – Paramétrer les configurations d’alertes d’anomalies <br> – Gérer les hooks  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/models/DataFeed.html)| **Ce que Metrics Advisor ingère de votre source de données. Un `DataFeed` contient des lignes de :** <br> – Horodatages <br> – Zéro ou plusieurs dimensions <br> – Une ou plusieurs mesures  |
| [Mesure](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/models/Metric.html) | Un objet `Metric` est une mesure quantifiable utilisée pour surveiller et évaluer l’état d’un processus d’entreprise spécifique. Il peut s’agir d’une combinaison de plusieurs valeurs de séries chronologiques divisées en dimensions. Par exemple, une métrique d’intégrité du Web peut contenir des dimensions pour le nombre d’utilisateurs et le marché en-US. |

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Metrics Advisor pour Java :

* [Authentifier le client](#authenticate-the-client)
* [Ajouter un flux de données](#add-a-data-feed)
* [Vérifier l’état d’ingestion](#check-the-ingestion-status)
* [Configurer la détection des anomalies](#configure-anomaly-detection)
* [Créer un hook](#create-a-hook)
* [Créer une configuration d’alerte](#create-an-alert-configuration)
* [Interroger l’alerte](#query-the-alert)

## <a name="authenticate-the-client"></a>Authentifier le client

### <a name="create-a-metrics-advisor-client-using-metricsadvisorkeycredential"></a>Créer un client Metrics Advisor à l’aide de MetricsAdvisorKeyCredential

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorClient metricsAdvisorClient = new MetricsAdvisorClientBuilder()
    .endpoint(ENDPOINT)
    .credential(credential)
    .buildClient();
```

### <a name="create-a-metrics-administration-client-using-metricsadvisorkeycredential"></a>Créer un client Metrics Administration à l’aide de MetricsAdvisorKeyCredential

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorAdministrationClient metricsAdvisorAdministrationClient =
    new MetricsAdvisorAdministrationClientBuilder()
        .endpoint(ENDPOINT)
        .credential(credential)
        .buildClient();

``` 

## <a name="add-a-data-feed"></a>Ajouter un flux de données

Remplacez `sql_server_connection_string` par votre propre chaîne de connexion au serveur SQL Server, puis remplacez `query` par une requête qui retourne vos données à un horodatage unique. Vous devrez également ajuster les valeurs `metric` et `dimension` en fonction de vos données personnalisées.

> [!IMPORTANT]
> La requête doit retourner au plus un enregistrement pour chaque combinaison de dimensions, à chaque horodatage. Et tous les enregistrements retournés par la requête doivent avoir les mêmes horodatages. Metrics Advisor exécutera cette requête pour chaque horodatage afin d’ingérer vos données. Pour plus d’informations et des exemples, consultez la [Section FAQ sur les requêtes](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data). 

```java
final DataFeed createdSqlDataFeed = metricsAdvisorAdministrationClient.createDataFeed(
    "My data feed name",
    new SQLServerDataFeedSource("sql_server_connection_string", "query"),
    new DataFeedGranularity().setGranularityType(DataFeedGranularityType.DAILY),
    new DataFeedSchema(Arrays.asList(
        new Metric().setName("cost"),
        new Metric().setName("revenue")))
        .setDimensions(Arrays.asList(
            new Dimension().setName("category"),
            new Dimension().setName("city"))),
    new DataFeedIngestionSettings(OffsetDateTime.parse("2020-01-01T00:00:00Z")),
    new DataFeedOptions()
        .setDescription("My data feed description")
        .setRollupSettings(
            new DataFeedRollupSettings()
                .setAutoRollup(DataFeedAutoRollUpMethod.SUM, Arrays.asList("cost"), "__CUSTOM_SUM__"))
        .setMissingDataPointFillSettings(
            new DataFeedMissingDataPointFillSettings()
                .setFillType(DataSourceMissingDataPointFillType.SMART_FILLING))
        .setAccessMode(DataFeedAccessMode.PUBLIC));

System.out.printf("Data feed Id : %s%n", createdSqlDataFeed.getId());
System.out.printf("Data feed name : %s%n", createdSqlDataFeed.getName());
System.out.printf("Is the query user is one of data feed administrator : %s%n", createdSqlDataFeed.isAdmin());
System.out.printf("Data feed created time : %s%n", createdSqlDataFeed.getCreatedTime());
System.out.printf("Data feed granularity type : %s%n",
    createdSqlDataFeed.getGranularity().getGranularityType());
System.out.printf("Data feed granularity value : %d%n",
    createdSqlDataFeed.getGranularity().getCustomGranularityValue());
System.out.println("Data feed related metric Ids:");
createdSqlDataFeed.getMetricIds().forEach(metricId -> System.out.println(metricId));
System.out.printf("Data feed source type: %s%n", createdSqlDataFeed.getSourceType());
createdSqlDataFeed.getSchema().getMetrics().forEach(metric -> {
    System.out.printf("metric name: %s metric id:%s", metric.getName(), metric.getId());
});

System.out.printf("Data feed sql server query: %s%n",
    ((SQLServerDataFeedSource) createdSqlDataFeed.getSource()).getQuery());
```

## <a name="check-the-ingestion-status"></a>Vérifier l’état d’ingestion

Cet exemple vérifie l’état d’ingestion d’une source de flux de données précédemment fournie.

```java
String dataFeedId = "<use-the-data-feed-id-from-createdSqlDataFeed.getId()"; 

metricsAdvisorAdministrationClient.listDataFeedIngestionStatus(
    dataFeedId,
    new ListDataFeedIngestionOptions(
        OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.parse("2020-09-09T00:00:00Z"))
).forEach(dataFeedIngestionStatus -> {
    System.out.printf("Message : %s%n", dataFeedIngestionStatus.getMessage());
    System.out.printf("Timestamp value : %s%n", dataFeedIngestionStatus.getTimestamp());
    System.out.printf("Status : %s%n", dataFeedIngestionStatus.getStatus());
});
```
## <a name="configure-anomaly-detection"></a>Configurer la détection des anomalies 

Cet exemple montre comment un utilisateur peut configurer une configuration de détection des anomalies pour ses données.

```java
String metricId = "<metric-id-from-adding-data-feed>";

ChangeThresholdCondition changeThresholdCondition = new ChangeThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.BOTH)
    .setChangePercentage(20)
    .setShiftPoint(10)
    .setWithinRange(true)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

HardThresholdCondition hardThresholdCondition = new HardThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.DOWN)
    .setLowerBound(5.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(1));

SmartDetectionCondition smartDetectionCondition = new SmartDetectionCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.UP)
    .setSensitivity(10.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

final AnomalyDetectionConfiguration anomalyDetectionConfiguration =
    metricsAdvisorAdministrationClient.createMetricAnomalyDetectionConfiguration(
        metricId,
        new AnomalyDetectionConfiguration("My Anomaly detection configuration")
            .setDescription("anomaly detection config description")
            .setWholeSeriesDetectionCondition(
                new MetricWholeSeriesDetectionCondition()
                    .setChangeThresholdCondition(changeThresholdCondition)
                    .setHardThresholdCondition(hardThresholdCondition)
                    .setSmartDetectionCondition(smartDetectionCondition)
                    .setCrossConditionOperator(DetectionConditionsOperator.OR))
    );
```

## <a name="create-a-hook"></a>Créer un hook

Cet exemple crée un hook par e-mail qui reçoit des alertes d’incident d’anomalie.

```java
Hook emailHook = new EmailHook("email hook")
    .setDescription("my email hook")
    .addEmailToAlert("alertme@alertme.com")
    .setExternalLink("https://adwiki.azurewebsites.net/articles/howto/alerts/create-hooks.html");

final Hook hook = metricsAdvisorAdministrationClient.createHook(emailHook);
EmailHook createdEmailHook = (EmailHook) hook;
System.out.printf("Hook Id: %s%n", createdEmailHook.getId());
System.out.printf("Hook Name: %s%n", createdEmailHook.getName());
System.out.printf("Hook Description: %s%n", createdEmailHook.getDescription());
System.out.printf("Hook External Link: %s%n", createdEmailHook.getExternalLink());
System.out.printf("Hook Emails: %s%n", String.join(",", createdEmailHook.getEmailsToAlert()));
```

##  <a name="create-an-alert-configuration"></a>Créer une configuration d’alerte

Cet exemple montre comment un utilisateur peut configurer une configuration d’alerte pour les anomalies détectées dans ses données.

```java
String detectionConfigurationId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String detectionConfigurationId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

final AnomalyAlertConfiguration anomalyAlertConfiguration
    = metricsAdvisorAdministrationClient.createAnomalyAlertConfiguration(
        new AnomalyAlertConfiguration("My Alert config name")
            .setDescription("alert config description")
            .setMetricAlertConfigurations(
                Arrays.asList(
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId1,
                        MetricAnomalyAlertScope.forWholeSeries()),
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId2,
                        MetricAnomalyAlertScope.forWholeSeries())
                        .setAlertConditions(new MetricAnomalyAlertConditions()
                            .setSeverityCondition(new SeverityCondition()
                                .setMaxAlertSeverity(Severity.HIGH)))
                ))
            .setCrossMetricsOperator(MetricAnomalyAlertConfigurationsOperator.AND)
            .setIdOfHooksToAlert(Arrays.asList(hookId1, hookId2)));
```

## <a name="query-the-alert"></a>Interroger l’alerte

Cet exemple montre comment un utilisateur peut interroger des alertes déclenchées pour une configuration de détection d’alerte et obtenir des anomalies pour cette alerte.

```java
String alertConfigurationId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
metricsAdvisorClient.listAlerts(
    alertConfigurationId,
    new ListAlertOptions(OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.now(),
        TimeMode.ANOMALY_TIME))
    .forEach(alert -> {
        System.out.printf("Alert Id: %s%n", alert.getId());
        System.out.printf("Alert created on: %s%n", alert.getCreatedTime());

        // List anomalies for returned alerts
        metricsAdvisorClient.listAnomaliesForAlert(
            alertConfigurationId,
            alert.getId())
            .forEach(anomaly -> {
                System.out.printf("Anomaly was created on: %s%n", anomaly.getCreatedTime());
                System.out.printf("Anomaly severity: %s%n", anomaly.getSeverity().toString());
                System.out.printf("Anomaly status: %s%n", anomaly.getStatus());
                System.out.printf("Anomaly related series key: %s%n", anomaly.getSeriesKey().asMap());
            });
    });
```

Vous pouvez générer l’application avec :

```console
gradle build
```
### <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec l’objectif `run` :

```console
gradle run
```