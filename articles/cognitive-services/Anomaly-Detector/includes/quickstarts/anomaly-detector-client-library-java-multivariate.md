---
title: Démarrage rapide avec la bibliothèque de client Java Détecteur d’anomalies (multivarié)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: 871c93251f76313f66b10bdfabd0e97f6acff433
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108333383"
---
Démarrez avec la bibliothèque de client Détecteur d’anomalies (multivarié) pour Java. Procédez comme suit pour installer le package de démarrage à l’aide des algorithmes fournis par le service. Les nouvelles API de détection d’anomalie multivariée permettent aux développeurs d’intégrer facilement l’intelligence artificielle avancée pour détecter les anomalies à partir de groupes de métriques, sans avoir besoin d’une connaissance du machine learning ni de données étiquetées. Les dépendances et inter-corrélations entre différents signes sont automatiquement comptabilisées comme des facteurs clés. Cela vous permet de protéger de manière proactive vos systèmes complexes contre les défaillances.

Utilisez la bibliothèque de client Détecteur d’anomalies (multivarié) pour Java pour :

* Détecter les anomalies au niveau du système à partir d’un groupe de séries chronologiques.
* Quand des séries chronologiques individuelles ne contiennent pas beaucoup d’informations et que vous devez examiner tous les signes pour détecter un problème.
* La maintenance prédictive des ressources physiques coûteuses avec des dizaines ou des centaines de types de capteurs différents mesurant divers aspects de l’intégrité du système.

[Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector) | [Package (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/) | [Exemple de code](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* La version actuelle du [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* L’[outil de génération Gradle](https://gradle.org/install/) ou un autre gestionnaire de dépendances.
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Créer une ressource Détecteur d’anomalies"  target="_blank">créez une ressource Détecteur d’anomalies </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Attendez qu’elle se déploie, puis cliquez sur le bouton **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Détecteur d’anomalies. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-gradle-project"></a>Créer un projet Gradle

Ce guide de démarrage rapide utilise le gestionnaire de dépendances Gradle. Vous trouverez plus d’informations sur la bibliothèque de client dans le [référentiel central Maven](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `gradle init` à partir de votre répertoire de travail. Cette commande crée des fichiers de build essentiels pour Gradle, notamment *build.gradle.kts*, qui est utilisé au moment de l’exécution pour créer et configurer votre application.

```console
gradle init --type basic
```

Quand vous êtes invité à choisir un **DSL**, sélectionnez **Kotlin**.

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Recherchez *build.gradle.kts* et ouvrez-le avec votre IDE ou votre éditeur de texte habituel. Copiez-y ensuite cette configuration de build. Veillez à inclure les dépendances du projet.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Créer un fichier Java

Créez un dossier pour votre exemple d’application. Entrez la commande suivante à partir de votre répertoire de travail :

```console
mkdir -p src/main/java
```

Accédez au nouveau dossier et créez le fichier *MetricsAdvisorQuickstarts.java*. Ouvrez-le dans votre éditeur ou IDE habituel et ajoutez les instructions `import` suivantes :

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

Créez des variables pour le point de terminaison et la clé Azure de votre ressource. Créez une autre variable pour l’exemple de fichier de données.

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

Pour utiliser les API multivariées Détecteur d’anomalies, vous devez d’abord entraîner vos propres modèles. Les données d’entraînement sont un ensemble de plusieurs séries chronologiques qui répondent aux conditions suivantes :

Chaque série chronologique doit être un fichier CSV comportant deux (et seulement deux) colonnes, « timestamp » et « value » (tout en minuscules) en ligne d’en-tête. Les valeurs « timestamp » doivent être conformes à la norme ISO 8601 ; la colonne « value » peut contenir des entiers ou des nombres décimaux avec n’importe quel nombre de décimales. Par exemple :

|timestamp | value|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

Chaque fichier CSV doit être nommé d’après une variable différente qui sera utilisée pour entraîner le modèle. Par exemple, « température.csv » et « humidité.csv ». Tous les fichiers CSV doivent être compressés dans un seul fichier zip ne contenant aucun sous-dossier. Le fichier zip peut porter le nom de votre choix. Le fichier zip doit être chargé dans le stockage Blob Azure. Une fois que vous avez généré l’URL des signatures d’accès partagé (SAS) des objets blob pour le fichier zip, vous pouvez l’utiliser pour l’entraînement. Reportez-vous à ce document pour savoir comment générer des URL SAS à partir du stockage Blob Azure.

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les tâches suivantes avec la bibliothèque de client Détecteur d’anomalies pour Node.js :

* [Authentifier le client](#authenticate-the-client)
* [Entraîner un modèle](#train-a-model)
* [Détecter les anomalies](#detect-anomalies)
* [Exporter le modèle](#export-model)
* [Supprimer le modèle](#delete-model)

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un objet `anomalyDetectorClient` avec votre point de terminaison et vos informations d’identification.

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>Entraîner un modèle

### <a name="construct-a-model-result-and-train-model"></a>Construire un résultat de modèle et entraîner un modèle

Nous devons tout d’abord construire une demande de modèle. Vérifiez que les heures de début et de fin concordent avec votre source de données.

Pour utiliser les API Détecteur d’anomalies (multivarié), nous devons entraîner notre propre modèle avant d’utiliser la détection. Les données utilisées pour l’entraînement sont un lot de séries chronologiques. Chaque série chronologique doit être dans un fichier CSV avec deux colonnes seulement, **« timestamp »** et **« value »** (les noms de colonne doivent être exactement les mêmes). Chaque fichier CSV doit être nommé d’après chaque variable de la série chronologique. Toutes les séries chronologiques doivent être compressées dans un fichier zip et chargées dans le [Stockage Blob Azure](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Le nom du fichier zip n’a pas d’importance. En guise d’alternative, un fichier meta.json supplémentaire peut être inclus dans le fichier zip si vous souhaitez que le nom de la variable soit différent du nom du fichier .zip. Une fois que nous avons généré une [URL SAS (signatures d’accès partagé) de blob](../../../../storage/common/storage-sas-overview.md), nous pouvons l’utiliser dans le fichier zip pour l’entraînement.

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy().setAlignMode(alignMode).setFillNAMethod(fillNAMethod).setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
;
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
;
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo().setSlidingWindow(window).setAlignPolicy(alignPolicy).setSource(source).setStartTime(startTime).setEndTime(endTime).setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] model_ids = header.split("/");
UUID model_id = UUID.fromString(model_ids[model_ids.length - 1]);
System.out.println(model_id);

Integer skip = 0;
Integer top = 5;
PagedIterable<ModelSnapshot> response = anomalyDetectorClient.listMultivariateModel(skip, top);
Iterator<PagedResponse<ModelSnapshot>> ite = response.iterableByPage().iterator();

while (true) {
    Response<Model> response_model = anomalyDetectorClient.getMultivariateModelWithResponse(model_id, Context.NONE);
    UUID model = response_model.getValue().getModelId();
    System.out.println(response_model.getStatusCode());
    System.out.println(response_model.getValue().getModelInfo().getStatus());
    System.out.println(model);
    if (response_model.getValue().getModelInfo().getStatus() == ModelStatus.READY) {
        break;
    }
}
```

## <a name="detect-anomalies"></a>Détecter les anomalies

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(model_id, detectionRequest, Context.NONE);
String result = detectAnomalyResponse.getDeserializedHeaders().getLocation();

String[] result_list = result.split("/");
UUID result_id = UUID.fromString(result_list[result_list.length - 1]);

while (true) {
    DetectionResult response_result = anomalyDetectorClient.getDetectionResult(result_id);
    if (response_result.getSummary().getStatus() == DetectionStatus.READY) {
        break;
    }
    else if(response_result.getSummary().getStatus() == DetectionStatus.FAILED){

    }
}
```

## <a name="export-model"></a>Exporter le modèle

Pour exporter votre modèle entraîné, utilisez `exportModelWithResponse`.

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Supprimer le modèle

Pour supprimer un modèle existant qui est disponible pour la ressource actuelle, utilisez la fonction `deleteMultivariateModelWithResponse`.

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>Exécution de l'application

Vous pouvez générer l’application avec :

```console
gradle build
```
### <a name="run-the-application"></a>Exécution de l'application

Avant de l’exécuter, vous pouvez vérifier votre code par rapport à l’[exemple de code complet](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java).

Exécutez l’application avec l’objectif `run` :

```console
gradle run
```

## <a name="next-steps"></a>Étapes suivantes

* [Bonnes pratiques concernant le Détecteur d’anomalies (multivarié)](../../concepts/best-practices-multivariate.md)
