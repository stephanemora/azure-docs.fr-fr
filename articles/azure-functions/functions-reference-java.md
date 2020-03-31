---
title: Informations de référence pour Azure Functions destinées aux développeurs Java
description: Découvrez comment développer des fonctions à l’aide de Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4af2a860657f6066112146e1f88d81861d9430ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79234937"
---
# <a name="azure-functions-java-developer-guide"></a>Guide des développeurs Java sur Azure Functions

Le runtime Azure Functions prend en charge [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Ce guide contient des informations sur les complexités de l’écriture de fonctions Azure avec Java.

Comme c’est le cas pour d’autres langages, une application de fonction peut avoir une ou plusieurs fonctions. Une fonction Java est une méthode `public` dotée de l’annotation `@FunctionName`. Cette méthode définit l’entrée d’une fonction Java et doit être unique dans un package particulier. Une application de fonction écrite en Java peut avoir plusieurs classes avec plusieurs méthodes publiques annotées avec `@FunctionName`.

Cet article suppose que vous ayez déjà lu l’article [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md). Vous devez également compléter le démarrage rapide de Functions pour créer votre première fonction, à l’aide de [Visual Studio Code](functions-create-first-function-vs-code.md) ou de [Maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Modèle de programmation 

Les concepts de [déclencheurs et liaisons](functions-triggers-bindings.md) sont fondamentaux pour Azure Functions. Les déclencheurs démarrent l’exécution de votre code. Les liaisons vous permettent de transmettre des données et de retourner des données à partir d’une fonction, sans avoir à écrire du code d’accès aux données personnalisées.

## <a name="create-java-functions"></a>Créer des fonctions Java

Pour faciliter la création de fonctions Java, il existe des outils basés sur Maven et des archétypes qui utilisent des modèles Java prédéfinis pour permettre de créer des projets avec un déclencheur de fonction spécifique.    

### <a name="maven-based-tooling"></a>Outils basés sur Maven

Les environnements de développement suivants comportent des outils Azure Functions qui permettent de créer des projets de fonctions Java : 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Les liens d’articles ci-dessus vous montrent comment créer vos premières fonctions dans l’environnement IDE de votre choix. 

### <a name="project-scaffolding"></a>Génération de modèles automatique du projet

Si vous préférez le développement en ligne de commande dans le terminal, le moyen le plus simple de définir la structure de projets de fonctions Java consiste à utiliser des archétypes `Apache Maven`. Il existe actuellement deux archétypes Functions pour Maven :

+ **Archétype Java** : publié sous le groupId et l’artifactId suivants [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/) :

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-archetype 
    ```

    Pour apprendre à utiliser cet archétype, voir le [démarrage rapide Java](functions-create-first-java-maven.md). 

+ **Archétype Kotlin (préversion)** : publié sous le groupId et l’artifactId suivants [com.microsoft.azure:azure-functions-kotlin-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/) :

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-kotlin-archetype
    ```

Le code source de ces archétypes se trouve dans le [dépôt GitHub Archétypes Azure Maven](https://github.com/microsoft/azure-maven-archetypes).


## <a name="folder-structure"></a>Structure de dossiers

Voici la structure de dossiers d’un projet Java Azure Functions :

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

_* Le projet Kotlin semble très similaire, car il est toujours Maven_

Vous pouvez utiliser un fichier [host.json](functions-host-json.md) partagé pour configurer l’application de fonction. Chaque fonction a son propre fichier de code (.java) et un fichier de configuration de liaison (function.json).

Vous pouvez placer plusieurs fonctions dans un projet. Évitez de placer vos fonctions dans des fichiers JAR distincts. L’élément `FunctionApp` dans le répertoire cible correspond à ce qui est déployé dans votre application de fonction dans Azure.

## <a name="triggers-and-annotations"></a>Déclencheurs et annotations

 Les fonctions sont appelées par un déclencheur, comme une requête HTTP, un minuteur ou une mise à jour des données. Votre fonction doit traiter ce déclencheur et toutes les autres entrées, puis produire une ou plusieurs sorties.

Utilisez les annotations Java incluses dans le package [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) pour lier des entrées et des sorties à vos méthodes. Pour plus d’informations, consultez les [Docs de référence Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Vous devez configurer un compte de stockage Azure dans votre fichier [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) pour exécuter les déclencheurs de Stockage Blob Azure, Stockage File d’attente Azure et Stockage Table Azure en local.

Exemple :

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Voici le fichier `function.json` correspondant généré par [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin) :

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

## <a name="customize-jvm"></a>Personnaliser une machine virtuelle Java

Azure Functions vous permet de personnaliser la machine virtuelle Java (JVM) utilisée pour exécuter vos fonctions Java. Les [options de machine virtuelle Java suivantes](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) sont utilisées par défaut :

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Vous pouvez fournir des arguments supplémentaires dans le paramètre d’application nommé `JAVA_OPTS`. Vous pouvez ajouter des paramètres d’application à votre application de fonction déployée sur Azure dans le portail Azure ou Azure CLI.

### <a name="azure-portal"></a>Portail Azure

Dans le [portail Azure](https://portal.azure.com), utilisez l’[onglet Paramètres d’application](functions-how-to-use-azure-function-app-settings.md#settings) pour ajouter le paramètre `JAVA_OPTS`.

### <a name="azure-cli"></a>Azure CLI

Vous pouvez utiliser la commande [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) pour définir `JAVA_OPTS`, comme le montre l’exemple suivant :

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Cet exemple active le mode « headless ». Remplacez également `<APP_NAME>` par le nom de votre application de fonction, et `<RESOURCE_GROUP>` par le groupe de ressources.

> [!WARNING]  
> Dans [Plan de consommation](functions-scale.md#consumption-plan), vous devez ajouter le paramètre `WEBSITE_USE_PLACEHOLDER` avec une valeur de `0`.  
Ce paramètre n’augmente pas les temps de démarrage à froid pour les fonctions Java.

## <a name="third-party-libraries"></a>Bibliothèques tierces 

Azure Functions prend en charge l’utilisation de bibliothèques tierces. Par défaut, toutes les dépendances spécifiées dans votre fichier `pom.xml` de projet sont automatiquement regroupées pendant l’objectif [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage). Pour les bibliothèques qui ne sont pas définies comme des dépendances dans le fichier `pom.xml`, placez-les dans un répertoire `lib` du dossier racine de la fonction. Les dépendances placées dans le répertoire `lib` sont ajoutées au chargeur de classes système lors de l’exécution.

La dépendance `com.microsoft.azure.functions:azure-functions-java-library` est fournie sur le chemin de classe par défaut et ne doit pas être incluse dans le répertoire `lib`. En outre, [azure-fonctions-java-worker](https://github.com/Azure/azure-functions-java-worker) ajoute les dépendances listées [ici](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) à l’instruction classpath.

## <a name="data-type-support"></a>Prise en charge des types de données

Vous pouvez utiliser des objets Plain old Java (POJO), des types définis dans `azure-functions-java-library` ou des types de données dataTypes primitifs comme chaîne et comme entier à lier à des liaisons d’entrée/de sortie.

### <a name="pojos"></a>POJO

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

Si vous attendez des valeurs null, utilisez `Optional<T>`.

## <a name="bindings"></a>Liaisons

Les liaisons d’entrée et de sortie fournissent un moyen déclaratif de se connecter à des données à partir de votre code. Une fonction peut avoir plusieurs liaisons d’entrée et de sortie.

### <a name="input-binding-example"></a>Exemple de liaison d’entrée

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

Vous appelez cette fonction avec une requête HTTP. 
- La charge utile de requête HTTP est transmise comme une `String` pour l’argument `inputReq`.
- Une entrée est récupérée à partir du Stockage Table et est transmise comme `TestInputData` à l’argument `inputData`.

Pour recevoir un lot d’entrées, vous pouvez lier à `String[]`, `POJO[]`, `List<String>` ou `List<POJO>`.

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

Cette fonction est déclenchée chaque fois qu’il existe de nouvelles données dans l’Event Hub configuré. Comme la `cardinality` est définie sur `MANY`, la fonction reçoit un lot de messages du hub d’événements. Les `EventData` de l’Event Hub sont converties en `TestEventData` pour l’exécution de la fonction.

### <a name="output-binding-example"></a>Exemple de liaison de sortie

Vous pouvez lier une liaison de sortie à la valeur de retour avec `$return`. 

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

Vous appelez cette fonction avec une requête HTTP. Elle écrit plusieurs valeurs dans Stockage File d’attente.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage et HttpResponseMessage

 Elles sont définies dans `azure-functions-java-library`. Il s’agit de types d’assistance pour utiliser les fonctions HttpTrigger.

| Type spécialisé      |       Cible        | Utilisation classique                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Déclencheur HTTP     | Obtention des requêtes, en-têtes et méthodes |
| `HttpResponseMessage` | Liaison de sortie HTTP | Renvoi de statut autre que 200   |

## <a name="metadata"></a>Métadonnées

Quelques déclencheurs envoient des [métadonnées de déclencheur](/azure/azure-functions/functions-triggers-bindings) ainsi que des données d’entrée. Vous pouvez utiliser une annotation `@BindingName` à lier à des métadonnées de déclencheur.


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
Dans l’exemple précédent, la valeur `queryValue` est liée au paramètre de chaîne de requête `name` dans l’URL de requête HTTP `http://{example.host}/api/metadata?name=test`. Voici un autre exemple de liaison à `Id` à partir des métadonnées de déclencheur de file d’attente.

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
> Le nom fourni dans l’annotation doit correspondre à la propriété des métadonnées.

## <a name="execution-context"></a>Contexte d’exécution

Le contenu `ExecutionContext` défini dans la bibliothèque `azure-functions-java-library` contient des méthodes d’assistance pour communiquer avec l’environnement d’exécution des fonctions.

### <a name="logger"></a>Enregistreur

Utilisez `getLogger`, défini dans `ExecutionContext`, pour écrire des journaux d’activité à partir du code de fonction.

Exemple :

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

Voici comment configurer votre application de fonction pour écrire la journalisation d’application en utilisant Azure CLI :

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

Avant d’exécuter cette commande, vous devez avoir activé la journalisation du système de fichiers dans le portail Azure ou dans Azure CLI.

## <a name="environment-variables"></a>Variables d'environnement

Dans Functions, les [paramètres de l’application](functions-app-settings.md), par exemple, les chaînes de connexion de service, sont exposées en tant que variables d’environnement pendant l’exécution. Vous pouvez accéder à ces paramètres avec `System.getenv("AzureWebJobsStorage")`.

L’exemple suivant obtient le [paramètre d’application](functions-how-to-use-azure-function-app-settings.md#settings), avec la clé nommée `myAppSetting` :

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le développement Java Azure Functions, voir les ressources suivantes :

* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)
* Développement et débogage locaux avec [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) et [Eclipse](functions-create-maven-eclipse.md)
* [Débogage distant de Java Azure Functions avec Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plug-in Maven pour Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Simplifier la création de fonction via l’objectif `azure-functions:add`, et préparer un répertoire intermédiaire pour le [déploiement de fichier ZIP](deployment-zip-push.md).
