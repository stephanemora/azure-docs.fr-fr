---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: jbeauregardb
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/01/2021
ms.topic: include
ms.custom: include file
ms.author: jbeauregardb
ms.openlocfilehash: b00fc19d811295f9ae29d69c73117d81acff57c9
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111593109"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install) version 8 ou ultérieure.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../create-communication-resource.md).
- Créez une [ressource Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) dans le portail Azure.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-java-application"></a>Créer une application Java

Ouvrez votre fenêtre de terminal ou de commande. Accédez au répertoire dans lequel vous souhaitez créer votre application Java. Exécutez la commande ci-dessous pour générer le projet Java à partir du modèle maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Vous remarquerez que la tâche « generate » a créé un répertoire portant le même nom que l’`artifactId`. Sous ce répertoire, le répertoire src/main/java contient le code source du projet, `src/test/java directory` contient la source de test, et le fichier `pom.xml` est le modèle objet du projet (le POM).

### <a name="install-the-package"></a>Installer le package

Ouvrez le fichier **pom.xml** dans votre éditeur de texte. Ajoutez les éléments de dépendance suivants au groupe de dépendances.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-monitor-opentelemetry-exporter</artifactId>
      <version>1.0.0-beta.4</version>
    </dependency>
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Accédez au répertoire */src/main/java/com/communication/quickstart*
1. Ouvrez le fichier *App.java* dans votre éditeur
1. Remplacez l’instruction `System.out.println("Hello world!");`
1. Ajoutez des directives `import`.

Utilisez le code suivant pour commencer :

```java
package com.communication.quickstart;

import io.opentelemetry.api.trace.Span;
import io.opentelemetry.api.trace.Tracer;
import io.opentelemetry.context.Scope;
import io.opentelemetry.sdk.OpenTelemetrySdk;
import io.opentelemetry.sdk.trace.SdkTracerProvider;
import io.opentelemetry.sdk.trace.export.SimpleSpanProcessor;
import com.azure.monitor.opentelemetry.exporter.*;
import com.azure.communication.identity.*;

public class App
{
    public static void main( String[] args ) throws Exception
    {
        System.out.println("Azure Communication Services - Export Telemetry to Application Insights");
        // Quickstart code goes here
    }
}
```
## <a name="setting-up-the-telemetry-tracer-with-communication-identity-sdk-calls"></a>Configuration du traceur de télémétrie avec des appels au Kit de développement logiciel (SDK) d’identité de communication

Initialisez un `CommunicationIdentityClient` avec votre chaîne de connexion. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string).

```java

    CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<COMMUNICATION-RESOURCE-CONNECTION-STRING>")
        .buildClient();
```

Tout d’abord, pour créer l’étendue permettant de suivre les demandes sur la plateforme Azure Monitor, vous devez créer une instance d’un objet `AzureMonitorTraceExporter`. Vous devrez fournir la chaîne de connexion à partir de votre ressource Application Insights.

```java
    AzureMonitorTraceExporter exporter = new AzureMonitorExporterBuilder()
        .connectionString("<APPLICATION-INSIGHTS-CONNECTION-STRING>")
        .buildTraceExporter();
```

Cet exportateur vous permettra ensuite de créer les instances suivantes pour pouvoir effectuer le suivi des demandes. Une fois l’`AzureMonitorTraceExporter` créé, ajoutez le code suivant :

```java

    SdkTracerProvider tracerProvider = SdkTracerProvider.builder()
        .addSpanProcessor(SimpleSpanProcessor.create(exporter))
        .build();

    OpenTelemetrySdk openTelemetrySdk = OpenTelemetrySdk.builder()
        .setTracerProvider(tracerProvider)
        .buildAndRegisterGlobal();

    Tracer tracer = openTelemetrySdk.getTracer("Sample");
```
Une fois le traceur initialisé, vous pouvez créer l’étendue chargée du suivi de vos demandes.

```java
    Span span = tracer.spanBuilder("sample-span").startSpan();
    final Scope scope = span.makeCurrent();
    try {
        // Thread bound (sync) calls will automatically pick up the parent span and you don't need to pass it explicitly.
        client.createUser();
    } finally {
        span.end();
        scope.close();
    }
    Thread.sleep(10000);
```

## <a name="run-the-code"></a>Exécuter le code

Accédez au répertoire contenant le fichier *pom.xml*, puis compilez le projet à l’aide de la commande `mvn` suivante.

```console
mvn compile
```

Ensuite, générez le package.

```console
mvn package
```

Exécutez la commande `mvn` suivante pour exécuter l’application.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```