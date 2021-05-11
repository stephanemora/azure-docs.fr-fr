---
title: Azure Monitor Application Insights Java
description: APM pour les applications Java s’exécutant dans n’importe quel environnement sans modification du code. Suivi distribué et mise en correspondance d’applications.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: b32b1fb3e0e21374fab2068d337440003005b1e7
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108291313"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Supervision des applications Java sans code avec Azure Monitor Application Insights

La supervision des applications Java sans code se caractérise par sa simplicité : le code n’est pas modifié et l’agent Java peut être activé avec quelques changements de configuration.

 L’agent Java fonctionne dans n’importe quel environnement et vous permet de superviser toutes vos applications Java. En d’autres termes, que vous exécutiez vos applications Java sur des machines virtuelles, locales, dans AKS, sur Windows, sur Linux, entre autres, l’agent Java 3.0 les supervise.

L’ajout du Kit de développement logiciel (SDK) Java Application Insights à votre application n’est plus nécessaire, car l’agent 3.0 collecte automatiquement les demandes, les dépendances et les journaux.

Vous pouvez toujours envoyer des données de télémétrie personnalisées à partir de votre application. L’agent 3.0 en effectue le suivi et la corrélation avec toutes les données de télémétrie collectées automatiquement.

L’agent 3.0 prend en charge Java 8 et versions ultérieures.

## <a name="quickstart"></a>Démarrage rapide

**1. Télécharger l’agent**

> [!WARNING]
> **Si vous effectuez une mise à niveau à partir de la préversion 3.0**
>
> Examinez soigneusement toutes les [options de configuration](./java-standalone-config.md) ci-dessous, car la structure JSON a complètement changé, en plus du nom de fichier désormais en minuscules.

Téléchargez [applicationinsights-agent-3.0.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.3/applicationinsights-agent-3.0.3.jar)

**2. Pointer la machine virtuelle Java (JVM) vers l’agent**

Ajoutez `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` aux arguments JVM de votre application

Les arguments JVM standard incluent `-Xmx512m` et `-XX:+UseG1GC`. Par conséquent, si vous savez où ajouter ces derniers, vous savez déjà où ajouter celui-ci.

Pour obtenir une aide supplémentaire sur la configuration des arguments JVM de votre application, consultez [Conseils pour la mise à jour de vos arguments JVM](./java-standalone-arguments.md).

**3. Pointer l’agent vers votre ressource Application Insights**

Si vous n’avez pas encore de ressource Application Insights, vous pouvez en créer une en suivant les étapes décrites dans le [guide de création de ressources](./create-new-resource.md).

Pointez l’agent sur votre ressource Application Insights, soit en définissant une variable d’environnement :

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Soit en créant un fichier config nommé `applicationinsights.json` et en le plaçant dans le même répertoire que `applicationinsights-agent-3.0.3.jar`, avec le contenu suivant :

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Vous pouvez trouver votre chaîne de connexion dans votre ressource Application Insights :

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Chaîne de connexion Application Insights":::

**4. Et voilà !**

Maintenant, démarrez l’application et accédez à votre ressource Application Insights dans le portail Azure pour voir vos données de supervision.

> [!NOTE]
> L’affichage des données de supervision dans le portail peut prendre quelques minutes.


## <a name="configuration-options"></a>Options de configuration

Dans le fichier `applicationinsights.json`, vous pouvez également configurer :

* Nom du rôle cloud
* Instance de rôle cloud
* échantillonnage
* Métriques JMX
* Dimensions personnalisées
* Processeurs de télémétrie (préversion)
* Journalisation collectée automatiquement
* Métriques de Micrometer collectées automatiquement (y compris les métriques de Spring Boot Actuator)
* Heartbeat
* Proxy HTTP
* Autodiagnostics

Pour plus d’informations, consultez [Options de configuration](./java-standalone-config.md).

## <a name="auto-collected-requests"></a>Requêtes collectées automatiquement

* Consommateurs JMS
* Consommateurs Kafka
* Netty/WebFlux
* Servlets
* Spring Scheduling

## <a name="auto-collected-dependencies"></a>Dépendances collectées automatiquement

Dépendances collectées automatiquement et propagation de trace distribuée en aval :

* Apache HttpClient et HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* JMS
* Kafka
* Client Netty
* OkHttp

Dépendances collectées automatiquement (sans propagation de trace distribuée en aval) :

* Cassandra
* JDBC
* MongoDB (asynchrone et synchrone)
* Redis (Lettuce et Jedis)

## <a name="auto-collected-logs"></a>Journaux collectés automatiquement

* java.util.logging
* Log4j (y compris les propriétés MDC)
* SLF4J/Logback (y compris les propriétés MDC)

## <a name="auto-collected-metrics"></a>Métriques collectées automatiquement

* Micrometer (y compris les métriques de Spring Boot Actuator)
* Métriques JMX

## <a name="azure-sdks-preview"></a>Kits de développement logiciel (SDK) Azure (préversion)

Consultez les [options de configuration](./java-standalone-config.md#auto-collected-azure-sdk-telemetry-preview) pour activer cette fonctionnalité d’évaluation et capturer automatiquement la télémétrie émise par ces Kits de développement logiciel (SDK) Azure :

* [App Configuration](/java/api/overview/azure/data-appconfiguration-readme) 1.1.10+
* [Recherche cognitive](/java/api/overview/azure/search-documents-readme) 11.3.0+
* [Communication Chat](/java/api/overview/azure/communication-chat-readme) 1.0.0+
* [Communication Common](/java/api/overview/azure/communication-common-readme) 1.0.0+
* [Communication Identity](/java/api/overview/azure/communication-identity-readme) 1.0.0+
* [Communication Sms](/java/api/overview/azure/communication-sms-readme) 1.0.0+
* [Cosmos DB](/java/api/overview/azure/cosmos-readme) 4.13.0+
* [Event Grid](/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0+
* [Event Hubs](/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0+
* [Event Hubs - Magasin de points de contrôle de Stockage Blob Azure](/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1+
* [Form Recognizer](/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6+
* [Identity](/java/api/overview/azure/identity-readme) 1.2.4+
* [Key Vault - Certificats](/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6+
* [Key Vault - Clés](/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6+
* [Key Vault - Secrets](/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6+
* [Service Bus](/java/api/overview/azure/messaging-servicebus-readme) 7.1.0+
* [Analyse de texte](/java/api/overview/azure/ai-textanalytics-readme) 5.0.4+

[//]: # "les noms et les liens ci-dessus ont été scrapés de https://azure.github.io/azure-sdk/releases/latest/java.html"
[//]: # "et synchronisés avec la version la plus ancienne dans Maven Central, basée sur azure-core 1.14.0"
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "pour (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "    continue"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + name + '](' + link + ') ' + version"
[//]: # "}"
[//]: # "console.log(str)"

## <a name="send-custom-telemetry-from-your-application"></a>Envoyer des données de télémétrie personnalisées à partir de votre application

Notre objectif dans 3.0+ est de vous permettre d’envoyer vos données de télémétrie personnalisées à l’aide d’API standard.

Nous prenons en charge Micrometer, les frameworks de journalisation populaires, et le kit de développement logiciel (SDK) Application Insights Java 2.x.
Application Insights pour Java 3.0 capture automatiquement les données de télémétrie envoyées via ces API et les met en corrélation avec les données de télémétrie collectées automatiquement.

### <a name="supported-custom-telemetry"></a>Données de télémétrie personnalisées prises en charge

Le tableau ci-dessous représente les types de données de télémétrie personnalisées actuellement pris en charge que vous pouvez activer pour compléter l’agent Java 3.0. Pour résumer, les métriques personnalisées sont prises en charge via Micrometer, les exceptions et les traces personnalisées peuvent être activées via des frameworks de journalisation, et tout type de données de télémétrie personnalisées est pris en charge par le [Kit de développement logiciel (SDK) Application Insights Java 2.x](#send-custom-telemetry-using-the-2x-sdk).

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

### <a name="send-custom-metrics-using-micrometer"></a>Envoyer des métriques personnalisées à l'aide de Micrometer

Ajoutez Micrometer à votre application :

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Utilisez le [registre global](https://micrometer.io/docs/concepts#_global_registry) de Micrometer pour créer un compteur :

```java
static final Counter counter = Metrics.counter("test_counter");
```

et utilisez-le pour enregistrer les métriques :

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Envoyer des traces et des exceptions personnalisées à l'aide de votre framework de journalisation préféré

Les journalisations Log4j, Logback et java.util.logging sont instrumentées automatiquement, et la journalisation effectuée via ces frameworks de journalisation est collectée automatiquement sous forme de données de télémétrie de trace et d'exception.

Par défaut, la journalisation n'est collectée que lorsqu'elle est effectuée au niveau INFO ou à un niveau supérieur.
Consultez les [options de configuration](./java-standalone-config.md#auto-collected-logging) pour savoir comment modifier ce niveau.

Si vous souhaitez joindre des dimensions personnalisées à vos journaux, vous pouvez utiliser [Log4j 1.2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html) ou [Logback MDC](http://logback.qos.ch/manual/mdc.html). Application Insights pour Java 3.0 capturera automatiquement ces propriétés MDC sous forme de dimensions personnalisées sur vos données de télémétrie de trace et d’exception.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Envoyer une télémétrie personnalisée à l’aide du kit SDK 2.x

Ajoutez `applicationinsights-core-2.6.2.jar` à votre application (toutes les versions 2.x sont prises en charge par Application Insights pour Java 3.0, mais il est préférable d’utiliser la dernière version si vous avez le choix) :

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

Créez un TelemetryClient :

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

et utilisez-le pour envoyer des données de télémétrie personnalisées :

##### <a name="events"></a>Événements

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Mesures

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Les dépendances

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>Journaux d’activité

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Exceptions

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Ajouter des dimensions personnalisées de requête à l’aide du kit SDK 2.x

> [!NOTE]
> Cette fonctionnalité n’est disponible que dans la version 3.0.2 et les versions ultérieures

Ajoutez `applicationinsights-web-2.6.2.jar` à votre application (toutes les versions 2.x sont prises en charge par Application Insights pour Java 3.0, mais il est préférable d’utiliser la dernière version si vous avez le choix) :

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

et ajoutez des dimensions personnalisées dans votre code :

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>Définir le user_Id de la télémétrie de requête à l’aide du kit SDK 2.x

> [!NOTE]
> Cette fonctionnalité n’est disponible que dans la version 3.0.2 et les versions ultérieures

Ajoutez `applicationinsights-web-2.6.2.jar` à votre application (toutes les versions 2.x sont prises en charge par Application Insights pour Java 3.0, mais il est préférable d’utiliser la dernière version si vous avez le choix) :

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

et définissez `user_Id` dans votre code :

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>Remplacer le nom de la télémétrie de requête à l’aide du kit SDK 2.x

> [!NOTE]
> Cette fonctionnalité n’est disponible que dans la version 3.0.2 et les versions ultérieures

Ajoutez `applicationinsights-web-2.6.2.jar` à votre application (toutes les versions 2.x sont prises en charge par Application Insights pour Java 3.0, mais il est préférable d’utiliser la dernière version si vous avez le choix) :

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

et définissez le nom dans votre code :

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>Procurez-vous l'ID de la télémétrie de requête et l'ID de l'opération à l'aide du kit de développement logiciel (SDK) 2.x

> [!NOTE]
> Cette fonctionnalité n’est disponible que dans la version 3.0.3 et les versions ultérieures

Ajoutez `applicationinsights-web-2.6.2.jar` à votre application (toutes les versions 2.x sont prises en charge par Application Insights pour Java 3.0, mais il est préférable d’utiliser la dernière version si vous avez le choix) :

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

et procurez-vous l'ID de la télémétrie de requête et l'ID de l'opération à partir de votre code :

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```