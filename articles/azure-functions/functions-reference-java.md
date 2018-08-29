---
title: Information de référence pour les développeurs Java sur Azure Functions | Microsoft Docs
description: Découvrez comment développer des fonctions à l’aide de Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/10/2018
ms.author: routlaw
ms.openlocfilehash: d895258a4c8a38d00932d81600dc8633d7d70112
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2018
ms.locfileid: "42140451"
---
# <a name="azure-functions-java-developer-guide"></a>Guide des développeurs Java sur Azure Functions

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Modèle de programmation 

Votre fonction Azure doit être une méthode de classe sans état qui traite une entrée et produit une sortie. Bien que vous puissiez écrire des méthodes d’instance, votre fonction ne doit pas dépendre d’un champ d’instance de la classe. Toutes les méthodes de fonction doivent présenter un modificateur d’accès `public`.

Vous pouvez placer plusieurs fonctions dans un projet. Évitez de placer vos fonctions dans des fichiers JAR distincts.

## <a name="triggers-and-annotations"></a>Déclencheurs et annotations

 Les fonctions Azure sont appelées par un déclencheur, comme une requête HTTP, un minuteur ou une mise à jour des données. Votre fonction doit traiter ce déclencheur et toutes les autres entrées, puis produire une ou plusieurs sorties.

Utilisez les annotations Java incluses dans le package [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) pour lier des entrées et des sorties à vos méthodes. L’exemple de code faisant appel à des annotations est disponible dans les [documents de référence Java](/java/api/com.microsoft.azure.functions.annotation) pour chaque annotation et dans la documentation de référence de liaison Azure Functions, comme celle des [déclencheurs HTTP](/azure/azure-functions/functions-bindings-http-webhook).

Les entrées et les sorties des déclencheurs peuvent également être définies dans le fichier [function.json](/azure/azure-functions/functions-reference#function-code) pour votre fonction, et non au travers d’annotations. Il n’est pas recommandé d’utiliser `function.json` au lieu d’annotations dans ces circonstances.

> [!IMPORTANT] 
> Vous devez configurer un compte de stockage Azure dans votre fichier [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) pour exécuter les déclencheurs de table, de file d’attente ou Azure Storage Blob en local.

Exemple d’utilisation des annotations :

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

La même fonction, écrite sans annotation :

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

avec le fichier `function.json` correspondant :

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
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

## <a name="third-party-libraries"></a>Bibliothèques tierces 

Azure Functions prend en charge l’utilisation de bibliothèques tierces. Par défaut, toutes les dépendances spécifiées dans votre fichier `pom.xml` de projet sont automatiquement regroupées pendant l’objectif `mvn package`. Pour les bibliothèques qui ne sont pas définies comme des dépendances dans le fichier `pom.xml`, placez-les dans un répertoire `lib` du dossier racine de la fonction. Les dépendances placées dans le répertoire `lib` doivent être ajoutées au chargeur de classes système lors de l’exécution.

## <a name="data-types"></a>Types de données

Vous pouvez utiliser tous les types de données Java pour les données d’entrée et de sortie, y compris les types natifs, les types Java personnalisés et les types Azure spécialisés définis dans le package `azure-functions-java-library`. Le runtime Azure Functions tente de convertir l’entrée reçue selon le type demandé par votre code.

### <a name="strings"></a>Chaînes

Les valeurs passées dans les méthodes de fonction seront transtypées en chaînes si le type de paramètre d’entrée de la fonction présente le type `String`. 

### <a name="plain-old-java-objects-pojos"></a>POJO (Plain Old Java Objects)

Les chaînes mises au format JSON seront transtypées en types Java si la signature d’entrée de la fonction attend ce type Java. Cette conversion vous permet de passer au format JSON et d’utiliser des types Java.

Les types POJO utilisés en tant qu’entrées des fonctions doivent présenter le même modificateur d’accès `public` que les méthodes de fonction dans lesquelles ils sont utilisés. Vous n’êtes pas obligé de déclarer des champs de classe POJO comme `public`. Par exemple, une chaîne JSON `{ "x": 3 }` peut être convertie en type POJO suivant :

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Données binaires

Les données binaires sont représentées en tant qu’élément `byte[]` dans votre code Azure Functions. Liez des entrées et sorties binaires à vos fonctions en définissant le champ `dataType` du fichier function.json sur `binary` :

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

Ensuite, utilisez-le dans le code votre fonction :

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Les valeurs d’entrée vides peuvent être `null` en tant qu’argument de vos fonctions, mais il est recommandé d’utiliser `Optional<T>` pour gérer les valeurs potentiellement vides.


## <a name="function-method-overloading"></a>Surcharge de la méthode de fonction

Vous êtes autorisé à surcharger des méthodes de fonction présentant le même nom, mais des types différents. Par exemple, vous pouvez avoir à la fois `String echo(String s)` et `String echo(MyType s)` dans une classe. Azure Functions décide de la méthode à appeler en fonction du type d’entrée (pour une entrée HTTP, le type MIME `text/plain` entraîne `String` tandis que `application/json` représente `MyType`).

## <a name="inputs"></a>Entrées

Les entrées sont réparties en deux catégories dans Azure Functions : l’une correspond à l’entrée du déclencheur et l’autre, à l’entrée supplémentaire. Bien qu’elles soient différentes dans `function.json`, leur utilisation est identique dans le code Java. L’extrait de code suivant en est un exemple :

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String in,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") MyObject obj
    ) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    public static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

Lorsque cette fonction est déclenchée, la requête HTTP est transmise à la fonction par le biais de `String in`. Une entrée est récupérée à partir du Stockage Table Azure basé sur l’ID dans l’URL d’itinéraire et elle est mise à disposition en tant que `obj` dans le corps de fonction.

## <a name="outputs"></a>Outputs

Les sorties peuvent être exprimées en tant que paramètres de sortie ou de valeur renvoyée. S’il n’existe qu’une seule sortie, nous vous recommandons d’utiliser la valeur renvoyée. Lorsqu’il y a plusieurs sorties, vous devez utiliser les paramètres de sortie.

La valeur renvoyée correspond à la forme de sortie la plus simple : vous renvoyez simplement la valeur de n’importe quel type, et le runtime Azure Functions tente de la marshaler vers le type réel (comme une réponse HTTP).  Vous pouvez appliquer des annotations de sortie à la méthode de fonction (la propriété de nom de l’annotation doit être $return) pour définir la sortie de la valeur de retour.

Pour générer plusieurs valeurs de sortie, utilisez le type `OutputBinding<T>` défini dans le package `azure-functions-java-library`. Si vous devez créer une réponse HTTP et envoyer (push) un message vers une file d’attente, vous pouvez écrire quelque chose comme ce qui suit :

Par exemple, une fonction de copie du contenu de l’objet blob peut être définie sous la forme du code suivant. L’annotation `@StorageAccount` est utilisée ici pour empêcher la duplication de la propriété de connexion de `@BlobTrigger` et de `@BlobOutput`.

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Utilisez `OutputBinding<byte[]`> pour rendre une valeur de sortie binaire (pour les paramètres). Pour les valeurs de retour, utilisez simplement `byte[]`.

## <a name="specialized-types"></a>Types spécialisés

Parfois, une fonction doit disposer d’un contrôle détaillé sur les entrées et les sorties. Les types spécialisés du package `azure-functions-java-core` vous permettent de manipuler les informations des requêtes et d’adapter l’état de retour d’un déclencheur HTTP :

| Type spécialisé      |       Cible        | Utilisation classique                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Déclencheur HTTP     | Obtention des requêtes, en-têtes et méthodes |
| `HttpResponseMessage<T>` | Liaison de sortie HTTP | État de retour autre que 200   |

> [!NOTE] 
> Vous pouvez également utiliser l’annotation `@BindingName` pour obtenir des requêtes et en-têtes HTTP. Par exemple, `@BindingName("name") String query` itère les en-têtes et requêtes des demandes HTTP et passe cette valeur à la méthode. Par exemple, l’élément `query` aura la valeur `"test"` si l’URL de la demande est la suivante : `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Métadonnées

Les métadonnées proviennent de différentes sources, telles que les en-têtes HTTP, les requêtes HTTP et les [métadonnées de déclencheur](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Utilisez l’annotation `@BindingName` avec le nom des métadonnées pour obtenir cette valeur.

Par exemple, l’élément `queryValue` de l’extrait de code suivant est `"test"` si l’URL demandée est `http://{example.host}/api/metadata?name=test`.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="execution-context"></a>Contexte d’exécution

Interagissez avec l’environnement d’exécution Azure Functions au travers de l’objet `ExecutionContext` défini dans le package `azure-functions-java-library`. Utilisez l’objet `ExecutionContext` pour exploiter les informations des appels et du runtime Functions dans votre code.

### <a name="logging"></a>Journalisation

Vous pouvez accéder à l’enregistreur d’événements du runtime Functions via l’objet `ExecutionContext`. Cet enregistreur est lié à Azure Monitor et vous permet de signaler les avertissements et erreurs générés lors de l’exécution de la fonction.

L’exemple de code suivant enregistre un message d’avertissement lorsque le corps de demande reçue est vide.

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

## <a name="environment-variables"></a>Variables d’environnement

Conservez des informations confidentielles comme des clés ou des jetons en dehors de votre code source pour des raisons de sécurité. Utilisez des clés et des jetons dans votre code de fonction en les lisant à partir de variables d’environnement.

Pour définir des variables d’environnement lors de l’exécution locale d’Azure Functions, vous pouvez choisir d’ajouter ces variables au fichier local.settings.json. S’il en manque une dans le dossier racine de votre projet de fonction, vous pouvez la créer. Voici à quoi doit ressembler le fichier :

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Chaque mise en correspondance des valeurs / de la clé dans le mappage `values` sera rendue disponible lors de l’exécution en tant que variable d’environnement, accessible par un appel `System.getenv("<keyname>")`, par exemple, `System.getenv("AzureWebJobsStorage")`. L’ajout d’une paire clé / valeur supplémentaire est accepté et conseillé.

> [!NOTE]
> Si cette mesure est prise, assurez-vous d’ajouter le fichier local.settings.json au fichier à ignorer de votre référentiel, afin qu’elle ne soit pas validée.

Maintenant que votre code dépend de ces variables d’environnement, vous pouvez vous connecter au Portail Azure pour définir la même paire clé/valeur dans vos paramètres d’application de fonction, pour que votre code fonctionne de façon équivalente lors du test local et lors du déploiement vers Azure.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)
* [Débogage distant de Java Azure Functions avec Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
