---
title: Utiliser Java et Maven pour publier une fonction – Azure Functions
description: Créez et publiez une fonction simple déclenchée par HTTP dans Azure avec Java et Maven.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, functions, traitement des événements, calcul, architecture sans serveur
ms.service: azure-functions
ms.topic: quickstart
ms.devlang: java
ms.date: 08/10/2018
ms.author: routlaw
ms.reviewer: glenga
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 5447fdcfa86c35b7c5cf079ae8446c30785e893f
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299411"
---
# <a name="quickstart-use-java-to-create-and-publish-a-function-to-azure-functions"></a>Démarrage rapide : Utiliser Java pour créer et publier une fonction dans Azure Functions

Cet article vous indique comment générer et publier une fonction Java sur Azure Functions avec l’outil en ligne de commande Maven. Lorsque vous avez terminé, le code de votre fonction s’exécute sur le [plan de consommation](functions-scale.md#consumption-plan) dans Azure et peut être déclenché à l’aide d’une requête HTTP.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour développer des fonctions avec Java, les éléments suivants doivent être installés :

- [Java Developer Kit (JDK)](https://aka.ms/azure-jdks), version 8
- [Apache Maven](https://maven.apache.org), version 3.0 ou ultérieure
- [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure)
- [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.6.666 ou ultérieure

> [!IMPORTANT]
> Pour pouvoir effectuer ce démarrage rapide, vous devez définir la variable d’environnement JAVA_HOME sur l’emplacement d’installation du JDK.

## <a name="generate-a-new-functions-project"></a>Générer un nouveau projet Functions

Dans un dossier vide, exécutez la commande suivante pour générer le projet Functions à partir d’un [archétype Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Si vous rencontrez des problèmes avec l’exécution d’une commande, jetez un coup d’œil à la version `maven-archetype-plugin` utilisée. Étant donné que vous exécutez la commande dans un répertoire vide dépourvu de fichier `.pom`, celle-ci essaie peut-être d’utiliser un plug-in de l’ancienne version à partir de `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` si vous avez mis à niveau Maven à partir d’une version antérieure. Dans ce cas, essayez de supprimer le répertoire `maven-archetype-plugin` et de réexécuter la commande.

### <a name="windows"></a>Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    "-DarchetypeGroupId=com.microsoft.azure" ^
    "-DarchetypeArtifactId=azure-functions-archetype"
```

Maven vous invite à entrer les valeurs nécessaires pour terminer la génération du projet. Pour les valeurs de _groupId_, _artifactId_ et _version_, consultez les informations de référence sur les [conventions de nommage de Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). La valeur de _appName_ doit être unique dans Azure : par défaut, Maven génère donc un nom d’application basé sur la valeur précédemment entrée pour _artifactId_. La valeur de _packageName_ détermine le package Java pour le code de fonction généré.

Les identificateurs `com.fabrikam.functions` et `fabrikam-functions` ci-dessous sont utilisés en tant qu’exemples et pour faciliter la lecture des étapes ultérieures de ce démarrage rapide. Vous êtes invité à fournir vos propres valeurs pour Maven dans cette étape.

```Output
Define value for property 'groupId' (should match expression '[A-Za-z0-9_\-\.]+'): com.fabrikam.functions
Define value for property 'artifactId' (should match expression '[A-Za-z0-9_\-\.]+'): fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus: :
Define value for property 'resourceGroup' java-functions-group: :
Confirm properties configuration: Y
```

Maven crée les fichiers projet dans un nouveau dossier avec le nom de _artifactId_, dans cet exemple `fabrikam-functions`. Le code prêt à l’emploi généré dans le projet est une fonction [déclenchée par HTTP](/azure/azure-functions/functions-bindings-http-webhook) qui renvoie le corps de la demande. Remplacez *src/main/java/com/fabrikam/functions/Function.java* par le code suivant : 

```java
package com.fabrikam.functions;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class Function {
    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java
     * 2. curl {your host}/api/HttpTrigger-Java?name=HTTP%20Query
     */
    @FunctionName("HttpTrigger-Java")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
        } else {
            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        }
    }
}

```

## <a name="enable-extension-bundles"></a>Activer les offres groupées d’extension

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Accédez au répertoire du projet nouvellement créé (celui contenant vos fichiers host.json et pom.xml), puis générez et exécutez la fonction avec Maven :

```CMD
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Si vous rencontrez cette exception : `javax.xml.bind.JAXBException` avec Java 9, consultez la solution de contournement sur [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Vous voyez cette sortie lorsque la fonction s’exécute localement sur votre système et quand elle est prête à répondre aux requêtes HTTP :

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/HttpTrigger-Java
```

Déclenchez la fonction à partir de la ligne de commande en utilisant cURL dans une nouvelle fenêtre de terminal :

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java -d LocalFunction
```

```Output
Hello LocalFunction!
```

Utilisez `Ctrl-C` dans le terminal pour arrêter le code de la fonction.

## <a name="deploy-the-function-to-azure"></a>Déployer la fonction sur Azure

Le processus de déploiement sur Azure Functions utilise les informations d’identification du compte provenant d’Azure CLI. [Connectez-vous à Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) avant de continuer.

```azurecli
az login
```

Déployez votre code dans une nouvelle application de fonction en utilisant la cible Maven `azure-functions:deploy`. Cela active le mode [Déploiement Zip avec exécution à partir d’un package](functions-deployment-technologies.md#zip-deploy).

> [!NOTE]
> Quand vous utilisez Visual Studio Code pour déployer votre application de fonction, n’oubliez pas de choisir un abonnement payant, sinon vous obtenez une erreur. Votre abonnement est indiqué sur le côté gauche de l’IDE.

```azurecli
mvn azure-functions:deploy
```

Quand le déploiement est terminé, vous voyez l’URL que vous pouvez utiliser pour accéder à votre application de fonction Azure :

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

Testez l’application de fonction en cours d’exécution sur Azure avec `cURL`. Vous devez modifier l’URL de l’exemple ci-dessous pour qu’elle corresponde à l’URL déployée de votre application de fonction à l’étape précédente.

> [!NOTE]
> Veillez à définir les **Droits d’accès** sur `Anonymous`. Lorsque vous choisissez le niveau par défaut de `Function`, vous êtes invité à présenter la [clé de fonction](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) dans les demandes d’accès à votre point de terminaison de fonction.

```azurecli
curl -w "\n" https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Effectuer des modifications et redéployer

Modifiez le fichier source `src/main.../Function.java` dans le projet généré pour modifier le texte retourné par votre application de fonction. Remplacez cette ligne :

```java
return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
```

par ce qui suit :

```java
return request.createResponseBuilder(HttpStatus.OK).body("Hi, " + name).build();
```

Enregistrez les modifications. Exécutez le package propre mvn et redéployez l’application en exécutant `azure-functions:deploy` à partir du terminal comme vous l’avez fait précédemment. L’application de fonction sera mise à jour et cette requête :

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

retournera la sortie mise à jour suivante :

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une application de fonction Java avec un déclencheur HTTP simple et vous l’avez déployée sur Azure Functions.

- Pour plus d’informations sur le développement de fonctions Java, consultez le [Guide du développeur de fonctions Java](functions-reference-java.md).
- Ajoutez des fonctions supplémentaires avec différents déclencheurs à votre projet en utilisant la cible Maven `azure-functions:add`.
- Écrivez et déboguez des fonctions en local avec [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) et [Eclipse](functions-create-maven-eclipse.md). 
- Déboguez les fonctions déployées dans Azure avec Visual Studio Code. Pour obtenir des instructions, consultez la documentation relative aux [applications Java sans serveur](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) Visual Studio Code.

> [!NOTE] 
> Vous pouvez également créer un projet Azure Functions basé sur Kotlin en utilisant azure-functions-kotlin-archetype à la place. Pour plus d’informations, consultez le [dépôt GitHub](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype).
