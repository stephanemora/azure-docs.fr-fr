---
title: Superviser les applications Java sur tout environnement - Azure Monitor Application Insights
description: Supervision des performances des applications Java s’exécutant dans tout environnement sans instrumenter l’application. Suivi distribué et mise en correspondance d’applications.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 08e5b68ea5e5ec63531bb4f9c6b4483e9afbb9bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91370032"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Supervision des applications Java sans code avec Azure Monitor Application Insights - préversion publique

La supervision des applications Java sans code se caractérise par sa simplicité : le code n’est pas modifié et l’agent Java peut être activé avec quelques changements de configuration.

 L’agent Java fonctionne dans n’importe quel environnement et vous permet de superviser toutes vos applications Java. En d’autres termes, que vous exécutiez vos applications Java sur des machines virtuelles, locales, dans AKS, sur Windows, sur Linux, entre autres, l’agent Java 3.0 les supervise.

L’ajout du kit SDK Application Insights pour Java à votre application n’est plus nécessaire, car l’agent 3.0 collecte automatiquement les demandes, les dépendances et les journaux.

Vous pouvez toujours envoyer des données de télémétrie personnalisées à partir de votre application. L’agent 3.0 en effectue le suivi et la corrélation avec toutes les données de télémétrie collectées automatiquement.

L’agent 3.0 prend en charge Java 8 et versions ultérieures.

## <a name="quickstart"></a>Démarrage rapide

**1. Télécharger l’agent**

Téléchargez [applicationinsights-agent-3.0.0-PREVIEW.7.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.7/applicationinsights-agent-3.0.0-PREVIEW.7.jar)

**2. Pointer la machine virtuelle Java (JVM) vers l’agent**

Ajoutez `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.7.jar` aux arguments JVM de votre application

Les arguments JVM standard incluent `-Xmx512m` et `-XX:+UseG1GC`. Par conséquent, si vous savez où ajouter ces derniers, vous savez déjà où ajouter celui-ci.

Pour obtenir une aide supplémentaire sur la configuration des arguments JVM de votre application, consultez [Préversion 3.0 : conseils pour la mise à jour de vos arguments JVM](./java-standalone-arguments.md).

**3. Pointer l’agent vers votre ressource Application Insights**

Si vous n’avez pas encore de ressource Application Insights, vous pouvez en créer une en suivant les étapes décrites dans le [guide de création de ressources](./create-new-resource.md).

Pointez l’agent sur votre ressource Application Insights, soit en définissant une variable d’environnement :

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Soit en créant un fichier config nommé `ApplicationInsights.json` et en le plaçant dans le même répertoire que `applicationinsights-agent-3.0.0-PREVIEW.7.jar`, avec le contenu suivant :

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Vous pouvez trouver votre chaîne de connexion dans votre ressource Application Insights :

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Chaîne de connexion Application Insights":::

**4. Et voilà !**

Maintenant, démarrez l’application et accédez à votre ressource Application Insights dans le portail Azure pour voir vos données de supervision.

> [!NOTE]
> L’affichage des données de supervision dans le portail peut prendre quelques minutes.


## <a name="configuration-options"></a>Options de configuration

Dans le fichier `ApplicationInsights.json`, vous pouvez également configurer :

* Nom du rôle cloud
* Instance de rôle cloud
* Capture du journal des applications
* Métriques JMX
* Micrometer
* Heartbeat
* échantillonnage
* Proxy HTTP
* Autodiagnostics

Pour plus d’informations, consultez [Préversion publique 3.0 : options de configuration](./java-standalone-config.md).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Demandes, dépendances, journaux et métriques collectés automatiquement

### <a name="requests"></a>Demandes

* Consommateurs JMS
* Consommateurs Kafka
* Netty/WebFlux
* Servlets
* Spring Scheduling

### <a name="dependencies-with-distributed-trace-propagation"></a>Dépendances avec propagation de suivi distribué

* Apache HttpClient et HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* JMS
* Kafka
* Client Netty
* OkHttp

### <a name="other-dependencies"></a>Autres dépendances

* Cassandra
* JDBC
* MongoDB (asynchrone et synchrone)
* Redis (Lettuce et Jedis)

### <a name="logs"></a>Journaux d’activité

* java.util.logging
* Log4j
* SLF4J/Logback

### <a name="metrics"></a>Mesures

* Micrometer (y compris les métriques de Spring Boot Actuator)
* Métriques JMX

## <a name="sending-custom-telemetry-from-your-application"></a>Envoi de données de télémétrie personnalisées à partir de votre application

Notre objectif dans 3.0+ est de vous permettre d’envoyer vos données de télémétrie personnalisées à l’aide d’API standard.

Nous prenons en charge Micrometer, l’API OpenTelemetry et les frameworks de journalisation les plus connus. Application Insights pour Java 3.0 capture automatiquement les données de télémétrie et les met en corrélation avec toutes les données de télémétrie collectées automatiquement.

### <a name="supported-custom-telemetry"></a>Données de télémétrie personnalisées prises en charge

Le tableau ci-dessous représente les types de données de télémétrie personnalisées actuellement pris en charge que vous pouvez activer pour compléter l’agent Java 3.0. Pour résumer, les métriques personnalisées sont prises en charge via Micrometer, les exceptions et les traces personnalisées peuvent être activées via des frameworks de journalisation, et tout type de données de télémétrie personnalisées est pris en charge par le [Kit de développement logiciel (SDK) Application Insights Java 2.x](#sending-custom-telemetry-using-application-insights-java-sdk-2x). 

|                     | Micrometer | Log4j, logback, JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **Événements personnalisés**   |            |                     |  Oui    |
| **Métriques personnalisées**  |  Oui       |                     |  Oui    |
| **Dépendances**    |            |                     |  Oui    |
| **Exceptions**      |            |  Oui                |  Oui    |
| **Affichages de pages**      |            |                     |  Oui    |
| **Demandes**        |            |                     |  Oui    |
| **Traces**          |            |  Oui                |  Oui    |

Nous n’avons pas l’intention de publier un kit SDK avec Application Insights 3.0 pour le moment.

Application Insights pour Java 3.0 écoute déjà les données de télémétrie envoyées au kit SDK Application Insights pour Java 2.x. Cette fonctionnalité est une partie essentielle de la mise à niveau pour les utilisateurs 2.x existants car elle comble un écart important dans notre prise en charge de la télémétrie personnalisée jusqu’à la disponibilité générale de l’API OpenTelemetry.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Envoi de données de télémétrie personnalisées à l’aide du kit SDK Application Insights pour Java 2.x

Ajoutez `applicationinsights-core-2.6.0.jar` à votre application (toutes les versions 2.x sont prises en charge par Application Insights pour Java 3.0, mais il est préférable d’utiliser la dernière version si vous avez le choix) :

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Créez un TelemetryClient :

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

et utilisez-le pour envoyer des données de télémétrie personnalisées.

### <a name="events"></a>Événements

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Mesures

Vous pouvez envoyer des données de télémétrie de métriques via [Micrometer](https://micrometer.io):

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Vous pouvez également utiliser le kit SDK Application Insights pour Java 2.x :

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>Les dépendances

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>Journaux d’activité
Vous pouvez envoyer des données de télémétrie de journal personnalisées par le biais de votre framework de journalisation favori.

Vous pouvez également utiliser le kit SDK Application Insights pour Java 2.x :

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Exceptions
Vous pouvez envoyer des données de télémétrie d’exception personnalisées par le biais de votre framework de journalisation favori.

Vous pouvez également utiliser le kit SDK Application Insights pour Java 2.x :

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Mise à niveau à partir du kit SDK Application Insights pour Java 2.x

Si vous utilisez déjà le kit SDK Application Insights pour Java 2.x dans votre application, il n’est pas nécessaire de le supprimer. L’agent Java 3.0 le détecte, capture et met en corrélation toutes les données de télémétrie personnalisées que vous envoyez via le kit SDK Java 2.x, tout en supprimant toute collecte automatique effectuée par le kit SDK Java 2.x pour empêcher une capture dupliquée.

Si vous utilisez l’agent Application Insights 2.x, vous devez supprimer l’argument JVM `-javaagent:` qui pointait vers l’agent 2.x.

> [!NOTE]
> Remarque : Les TelemetryInitializers et TelemetryProcessors du kit SDK Java 2.x ne sont pas exécutés lors de l’utilisation de l’agent 3.0.
