---
title: Information de référence pour les développeurs Java sur Azure Functions | Microsoft Docs
description: Découvrez comment développer des fonctions à l’aide de Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: ce7eb546c342ffd20557a95d5293d83b39ec3afb
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507194"
---
# <a name="azure-functions-java-developer-guide"></a>Guide des développeurs Java sur Azure Functions

Le runtime Azure Functions prend en charge [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/).

Ce guide contient des informations sur les complexités de l’écriture de fonctions Azure avec Java.

Une fonction Java est un `public` méthode décorée avec l’annotation `@FunctionName`. Cette méthode définit l’entrée pour une fonction java et doit être unique dans un package donné. 

Cet article suppose que vous ayez déjà lu l’article [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md). Vous devez également compléter le démarrage rapide de Functions pour créer votre première fonction, à l’aide de [Visual Studio Code](functions-create-first-function-vs-code.md) ou [à l’aide de maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Modèle de programmation 

Les concepts de [déclencheurs et liaisons](functions-triggers-bindings.md) sont fondamentaux pour Azure Functions. Les déclencheurs démarrent l’exécution de votre code. Les liaisons vous permettent de transmettre des données et de retourner des données à partir d’une fonction, sans avoir à écrire du code d’accès aux données personnalisées.

## <a name="folder-structure"></a>Structure de dossiers

Voici la structure de dossiers d’un Java Azure Function :

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

Il existe un fichier [host.json](functions-host-json.md) partagé que vous pouvez utiliser pour configurer l’application de fonction. Chaque fonction a son propre fichier de code (.java) et un fichier de configuration de liaison (function.json).

Vous pouvez placer plusieurs fonctions dans un projet. Évitez de placer vos fonctions dans des fichiers JAR distincts. L’élément FunctionApp dans le répertoire cible correspond à ce qui est déployé dans votre application de fonction dans Azure.

## <a name="triggers-and-annotations"></a>Déclencheurs et annotations

 Les fonctions Azure sont appelées par un déclencheur, comme une requête HTTP, un minuteur ou une mise à jour des données. Votre fonction doit traiter ce déclencheur et toutes les autres entrées, puis produire une ou plusieurs sorties.

Utilisez les annotations Java incluses dans le package [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) pour lier des entrées et des sorties à vos méthodes. Pour plus d’informations, consultez [documents de référence Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Vous devez configurer un compte de stockage Azure dans votre fichier [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) pour exécuter les déclencheurs de table, de file d’attente ou Azure Storage Blob en local.

Exemple :

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

voici le fichier `function.json` correspondant généré par [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin) :

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>Disponibilité et prise en charge du runtime JDK 

Téléchargez et utilisez le JDK Java 8 [Azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) à partir d’[Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) pour le développement local d’applications de fonction Java. Azure Functions utilise le runtime du JDK Java 8 Azul lorsque vous déployez vos applications de fonctions sur le cloud.

Le [support Azure](https://azure.microsoft.com/support/) des problèmes avec les JDK et les applications de fonction est disponible avec dans un [plan de support qualifié](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Personnaliser la machine virtuelle Java

Functions vous permet de personnaliser la machine de virtuelle Java (JVM) utilisée pour exécuter vos fonctions Java. Le [suivant les options JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) sont utilisés par défaut :

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Vous pouvez fournir des arguments supplémentaires dans le paramètre d’application nommé `JAVA_OPTS`. Vous pouvez ajouter des paramètres de l’application à votre application de fonction déployée sur Azure dans une des manières suivantes :

### <a name="azure-portal"></a>Portail Azure

Dans le [portail Azure](https://portal.azure.com), utilisez le [onglet Paramètres de l’Application](functions-how-to-use-azure-function-app-settings.md#settings) pour ajouter le `JAVA_OPTS` paramètre.

### <a name="azure-cli"></a>Azure CLI

Le [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) commande peut être utilisée pour définir `JAVA_OPTS`, comme dans l’exemple suivant :

    ```azurecli-interactive
    az functionapp config appsettings set --name <APP_NAME> \
    --resource-group <RESOURCE_GROUP> \
    --settings "JAVA_OPTS=-Djava.awt.headless=true"
    ```
Cet exemple active le mode « headless ». Remplacez `<APP_NAME>` avec le nom de votre function app et `<RESOURCE_GROUP> ` avec le groupe de ressources.

> [!WARNING]  
> Lors de l’exécution un [plan de consommation](functions-scale.md#consumption-plan), vous devez ajouter le `WEBSITE_USE_PLACEHOLDER` définition avec la valeur `0`.  
Ce paramètre n’augmente pas les heures de démarrage à froid pour les fonctions de Java.

## <a name="third-party-libraries"></a>Bibliothèques tierces 

Azure Functions prend en charge l’utilisation de bibliothèques tierces. Par défaut, toutes les dépendances spécifiées dans votre fichier `pom.xml` de projet sont automatiquement regroupées pendant l’objectif [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage). Pour les bibliothèques qui ne sont pas définies comme des dépendances dans le fichier `pom.xml`, placez-les dans un répertoire `lib` du dossier racine de la fonction. Les dépendances placées dans le répertoire `lib` doivent être ajoutées au chargeur de classes système lors de l’exécution.

La dépendance `com.microsoft.azure.functions:azure-functions-java-library` est fournie sur le chemin de classe par défaut et ne doit pas être incluse dans le répertoire `lib`. En outre, les dépendances répertoriées [ici](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) sont ajoutées à l’instruction classpath par [azure-fonctions-java-worker](https://github.com/Azure/azure-functions-java-worker).

## <a name="data-type-support"></a>Prise en charge des types de données

Vous pouvez utiliser des objets Plain old Java (POJO), des types définis dans `azure-functions-java-library` ou des types de données dataTypes primitifs comme chaîne ou comme entier à lier à des liaisons d’entrée/de sortie.

### <a name="plain-old-java-objects-pojos"></a>POJO (Plain Old Java Objects)

Pour la conversion des données d’entrée en POJO, [azure-fonctions-java-worker](https://github.com/Azure/azure-functions-java-worker) utilise une bibliothèque [gson](https://github.com/google/gson). Les types POJO utilisés comme entrées de fonctions doivent être `public`.

### <a name="binary-data"></a>Données binaires

Liez des entrées et sorties binaires à `byte[]` en définissant le champ `dataType` dans votre fichier function.json sur `binary` :

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Utilisez `Optional<T>` si des valeurs Null sont attendues

## <a name="bindings"></a>Liaisons

Les liaisons d’entrée et de sortie fournissent un moyen déclaratif de se connecter à des données à partir de votre code. Une fonction peut avoir plusieurs liaisons d’entrée et de sortie.

### <a name="example-input-binding"></a>Exemple de liaison d’entrée

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Cette fonction est appelée avec une requête HTTP. 
- La charge utile de requête HTTP est transmise comme une `String` pour l’argument `inputReq`
- Une entrée est récupérée à partir du Stockage Table Azure et est transmise comme `TestInputData` à l’argument `inputData`.

Pour recevoir un lot d’entrées, vous pouvez lier à `String[]`, `POJO[]`, `List<String>`, ou `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Cette fonction est déclenchée chaque fois qu’il existe de nouvelles données dans l’Event Hub configuré. Comme la `cardinality` est définie sur `MANY`, la fonction reçoit un lot de messages de l’Event Hub. EventData de l’Event Hub sont converties en `TestEventData` pour l’exécution de la fonction.

### <a name="example-output-binding"></a>Exemple de liaison de sortie

Vous pouvez lier une liaison de sortie à la valeur de retour avec `$return` 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

S’il existe plusieurs liaisons de sortie, utilisez la valeur de retour pour un seul d’entre eux.

Pour envoyer plusieurs valeurs de sortie, utilisez la `OutputBinding<T>` définie dans le package `azure-functions-java-library`. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Cette fonction est appelée sur un HttpRequest et écrit plusieurs valeurs dans la file d’attente Azure.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage et HttpResponseMessage

 Les types HttpRequestMessage et HttpResponseMessage sont définis dans `azure-functions-java-library` et sont des types d’assistance pour travailler avec les fonctions HttpTrigger

| Type spécialisé      |       Cible        | Utilisation classique                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Déclencheur HTTP     | Obtention des requêtes, en-têtes et méthodes |
| `HttpResponseMessage` | Liaison de sortie HTTP | État de retour autre que 200   |

## <a name="metadata"></a>Métadonnées

Quelques déclencheurs envoient des [métadonnées de déclencheur](/azure/azure-functions/functions-triggers-bindings) ainsi que des données d’entrée. Vous pouvez utiliser une annotation `@BindingName` à lier à des métadonnées de déclencheur


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
Dans l’exemple ci-dessus, le `queryValue` est lié au paramètre de chaîne de requête `name` dans l’URL de requête HTTP `http://{example.host}/api/metadata?name=test`. Voici un autre exemple de liaison à `Id` à partir des métadonnées de déclencheur de file d’attente

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> Le nom fourni dans l’annotation doit correspondre à la propriété des métadonnées

## <a name="execution-context"></a>Contexte d’exécution

Le contenu `ExecutionContext` défini dans la bibliothèque `azure-functions-java-library` contient des méthodes d’assistance pour communiquer avec l’environnement d’exécution des fonctions.

### <a name="logger"></a>Enregistreur

Utilisez `getLogger`, défini dans `ExecutionContext`, pour écrire des journaux d’activité à partir du code de fonction.

Exemple :

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Afficher les journaux d’activité et le suivi

Vous pouvez utiliser Azure CLI pour diffuser en continu la journalisation Java stdout et stderr, ainsi que toute autre journalisation d’application. 

Configurez votre application de fonction pour écrire la journalisation d’application en utilisant Azure CLI :

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Pour obtenir une sortie de journalisation pour votre application de fonction en utilisant Azure CLI, ouvrez une nouvelle session d’invite de commandes, Bash ou Terminal, puis entrez la commande suivante :

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
La commande [az webapp log tail](/cli/azure/webapp/log) permet de filtrer la sortie en utilisant l’option `--provider`. 

Pour télécharger les fichiers journaux sous la forme d’un fichier zip unique en utilisant Azure CLI, ouvrez une nouvelle session d’invite de commandes, Bash ou Terminal, puis entrez la commande suivante :

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Vous devez activer la journalisation dans le portail Azure ou Azure CLI avant d’exécuter cette commande de système de fichiers.

## <a name="environment-variables"></a>Variables d’environnement

Dans Functions, les [paramètres de l’application](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), par exemple, les chaînes de connexion de service, sont exposées en tant que variables d’environnement pendant l’exécution. Vous pouvez accéder à ces paramètres avec `System.getenv("AzureWebJobsStorage")`

Exemple :

Ajouter [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) avec le nom testAppSetting et la valeur testAppSettingValue

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le développement Java de Fonction Azure, voir les ressources suivantes :

* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)
* Développement et débogage locaux avec [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) et [Eclipse](functions-create-maven-eclipse.md). 
* [Débogage distant de Java Azure Functions avec Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plug-in Maven pour Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) : simplifier la création de fonction via l’objectif `azure-functions:add`, et préparer un répertoire intermédiaire pour le [déploiement de fichier ZIP](deployment-zip-push.md).
