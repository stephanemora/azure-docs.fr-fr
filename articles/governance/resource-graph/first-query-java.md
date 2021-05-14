---
title: 'Démarrage rapide : Votre première requête Java'
description: Dans ce guide de démarrage rapide, vous suivez les étapes pour activer les packages Maven Resource Graph pour Java et vous exécutez votre première requête.
ms.date: 03/30/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 04e32a3e147ed68248f7fead488a1f630ffcdb4d
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108751748"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>Démarrage rapide : Exécuter votre première requête Resource Graph à l’aide de Java

La première étape pour utiliser Azure Resource Graph consiste à vérifier que les packages Mavent nécessaires pour Java sont installés. Ce démarrage rapide décrit le processus d’ajout des packages Mavent à votre installation Java.

Au terme de ce processus, vous aurez ajouté les packages Maven à votre installation Java et vous pourrez exécuter votre première requête Resource Graph.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

- Vérifiez que la version la plus récente d’Azure CLI est installée (**2.21.0** au minimum). Si ce n’est pas le cas, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

  > [!NOTE]
  > Azure CLI est requis pour permettre au kit de développement logiciel (SDK) Azure pour Java d’utiliser **l’authentification basée sur l’interface CLI** dans les exemples suivants. Pour des informations sur les autres options, consultez [Bibliothèque de client Azure Identity pour Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).

- [Java Developer Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-long-term-support) version
  8.

- [Apache Maven](https://maven.apache.org/) version 3.6 ou ultérieure

## <a name="create-the-resource-graph-project"></a>Créer le projet Resource Graph

Pour permettre à Java d’interroger Azure Resource Graph, créez et configurez une application avec Maven et installez les packages Maven requis.

1. Initialisez une nouvelle application Java nommée « argQuery » avec un [archétype Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) :

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. Modifiez les répertoires dans le nouveau dossier de projet `argQuery` et ouvrez `pom.xml` dans l’éditeur de votre choix. Ajoutez les nœuds `<dependency>` suivants sous le nœud `<dependencies>` existant :

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
   ```

1. Dans le fichier `pom.xml`, ajoutez le nœud `<properties>` suivant sous le nœud `<project>` de base pour mettre à jour les versions source et cible :

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Dans le fichier `pom.xml`, ajoutez le nœud `<build>` suivant sous le nœud `<project>` de base pour configurer l’objectif et la classe principale du projet à exécuter.

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. Remplacez le `App.java` par défaut dans `\argQuery\src\main\java\com\Fabrikam` par le code suivant et enregistrez le fichier mis à jour :

   ```java
   package com.Fabrikam;

   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;

   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];

           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));

           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);

           QueryResponse response = manager.resourceProviders().resources(queryRequest);

           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. Générez l'application console `argQuery` :

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>Exécuter votre première requête Resource Graph

L’application console Java étant générée, nous pouvons tester une requête Resource Graph simple. La requête retourne les cinq premières ressources Azure avec le nom (**Name**) et le **Type** de chaque ressource.

Dans chaque appel de `argQuery`, vous devez remplacer les variables utilisées par vos propres valeurs :

- Remplacer `{subscriptionId}` par votre ID d’abonnement
- Remplacer `{query}` par votre requête Azure Resource Graph

1. Utilisez Azure CLI pour vous authentifier avec `az login`.

1. Définissez les répertoires sur le dossier de projet `argQuery` que vous avez créé à l’aide de la commande `mvn -B archetype:generate` précédente.

1. Exécutez votre première requête Azure Resource Graph à l’aide de Maven pour compiler l’application console et transmettre les arguments. La propriété `exec.args` identifie les arguments par des espaces. Pour identifier la requête en tant qu’argument unique, nous l’encapsulons avec des guillemets simples (`'`).

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > Comme cet exemple de requête ne fournit pas de modificateur de tri tel que `order by`, l’exécution répétée de cette requête peut produire un ensemble distinct de ressources par requête.

1. Remplacez l’argument par `argQuery.exe` et changez la requête par `order by`, la propriété **Name** :

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > Comme précédemment, l’exécution répétée de cette requête peut produire un ensemble différent de ressources. L’ordre des commandes de requête est important. Dans cet exemple, `order by` vient après `limit`. Cet ordre de commande limite d’abord les résultats de la requête, puis les classe.

1. Remplacez le dernier paramètre par `argQuery.exe` et changez la requête en `order by` pour classer d’abord les résultats selon la propriété **Name**, puis limitez (`limit`) les résultats aux cinq premiers :

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

Si votre environnement ne change pas et si vous exécutez plusieurs fois la requête finale, les résultats retournés sont cohérents et classés en fonction de la propriété **Name**, mais toujours limités aux cinq premiers.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez supprimer l’application console Java et les packages installés, vous pouvez le faire en supprimant le dossier du projet `argQuery`.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une application console Java avec les packages Resource Graph nécessaires et exécuté votre première requête. Pour plus d’informations sur le langage Resource Graph, passez à la page des détails du langage de requête.

> [!div class="nextstepaction"]
> [Obtenir plus d’informations sur le langage de requête](./concepts/query-language.md)
