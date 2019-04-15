---
title: Concevoir une application web Java sur Linux - Azure App Service
description: Concevez, déployez et mettez à l’échelle des applications web Java Spring Boot avec Azure App Service sur Linux et Azure Cosmos DB.
author: rloutlaw
ms.author: routlaw
manager: angerobe
ms.service: app-service-web
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 792346edf1d2b2326f7f5f5f53304ceca347508d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901115"
---
# <a name="tutorial-build-a-java-web-app-using-spring-and-azure-cosmos-db"></a>Didacticiel : Création d’une application web Java avec Spring et Azure Cosmos DB

Ce didacticiel vous guide au travers du processus de création, de configuration, de déploiement et de mise à l’échelle des applications web Java sur Azure. Lorsque vous avez terminé, une application [Spring Boot](https://projects.spring.io/spring-boot/) stocke des données dans [Azure Cosmos DB](/azure/cosmos-db) s’exécutant sous [Azure App Service sur Linux](/azure/app-service/containers).

![Application Java s’exécutant dans Azure App Service](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une base de données Cosmos DB.
> * Connecter un exemple d’application à la base de données et la tester localement
> * Déployer un exemple d’application dans Azure
> * Diffuser des journaux de diagnostic à partir d’App Service
> * Ajouter des instances supplémentaires pour effectuer la montée en charge de l’exemple d’application

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* [Azure CLI](https://docs.microsoft.com/cli/azure/overview), installé sur votre propre ordinateur. 
* [Git](https://git-scm.com/)
* [Java JDK](https://aka.ms/azure-jdks)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Cloner l’exemple d’application de liste de tâches et préparer le référentiel

Ce didacticiel utilise un exemple d’application de liste de tâches avec une interface utilisateur web qui appelle une API REST Spring soutenue par [Spring Data Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb). Le code pour l’application est disponible sur [GitHub](https://github.com/Microsoft/spring-todo-app). Pour en savoir plus sur l’écriture d’applications Java à l’aide de Spring et Cosmos DB, consultez le [Didacticiel Spring Boot Starter avec l’API SQL Azure Cosmos DB](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db ) et [le démarrage rapide Spring Data Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Exécutez les commandes suivantes dans votre terminal pour cloner l’exemple de dépôt et configurer l’exemple d’environnement d’application.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Créer une base de données Azure Cosmos DB

Suivez ces étapes pour créer une base de données Azure Cosmos DB dans votre abonnement. L’application de liste de tâches se connecte à cette base de données et stocke ses données lors de l’exécution, entraînant la persistance de l’application, quel que soit l’endroit d’où vous l’exécutez.

1. Connectez-vous à votre interface de ligne de commande Azure et définissez votre abonnement si vous en avez plusieurs connectés à vos informations d’identification.

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. Créez un groupe de ressources Azure, en notant son nom.

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Créez une base de données Azure Cosmos du type `GlobalDocumentDB`. Le nom de la base de données Cosmos DB doit être uniquement composé de lettres minuscules. Notez le champ `documentEndpoint` de la réponse à la commande.

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Obtenez votre clé d’Azure Cosmos DB pour vous connecter à l’application. Conserver le `primaryMasterKey` et `documentEndpoint` à proximité, car vous en aurez besoin à l’étape suivante.

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>Configurer les propriétés de l’application de liste de tâches

Ouvrez un terminal sur votre ordinateur. Copiez l’exemple de fichier de script dans le référentiel cloné afin que vous puissiez le personnaliser pour votre base de données Cosmos DB que vous venez de créer.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Modifiez `.scripts/set-env-variables.sh` dans votre éditeur favori et fournissez les informations de connexion Azure Cosmos DB. Pour la configuration d’App Service Linux, utilisez la même région qu’avant (`your-resource-group-region`) ainsi que le groupe de ressources (`your-azure-group-name`) utilisé lors de la création de la base de données Cosmos DB. Choisissez un WEBAPP_NAME unique, dans la mesure où il ne peut pas être identique à un nom d’application web au sein d’un déploiement Azure.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Ensuite, exécutez le script :

```bash
source .scripts/set-env-variables.sh
```
   
Ces variables d’environnement sont utilisées dans `application.properties` au sein de l’application de liste de tâches. Les champs dans le fichier de propriétés définissent une configuration de référentiel par défaut pour Spring Data :

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

L’exemple d’application utilise ensuite l’annotation `@Document` importée depuis `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` pour configurer un type d’entité à stocker et gérer par Cosmos DB :

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Utilisez Maven pour exécuter l’exemple.

```bash
mvn package spring-boot:run
```

La sortie doit se présenter comme suit.

```bash
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

Vous pouvez accéder à l’application de liste de tâches Spring localement en utilisant ce lien une fois l’application lancée : [http://localhost:8080/](http://localhost:8080/).

 ![](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Si vous voyez des exceptions à la place du message « Started TodoApplication », vérifiez que le script `bash` de l’étape précédente a correctement exporté les variables d’environnement et que les valeurs sont correctes pour la base de données Azure Cosmos DB créée.

## <a name="configure-azure-deployment"></a>Configurer un déploiement Azure

Ouvrez le fichier `pom.xml` dans le répertoire `initial/spring-boot-todo` et ajoutez la configuration suivante du [plug-in Maven pour Azure App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md).

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.4.0</version>
            <configuration>
            <deploymentType>jar</deploymentType>
            
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>jre8</linuxRuntime>
            
            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property>
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>
            
        </configuration>
    </plugin>            
    ...
</plugins>
```

## <a name="deploy-to-app-service-on-linux"></a>Déployer vers App Service sous Linux

Utilisez l’objectif Maven `azure-webapp:deploy` pour déployer l’application de liste de tâches dans Azure App Service sous Linux.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.4.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Authenticate with Azure CLI 2.0
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2018-10-28T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

La sortie contient l’URL vers votre application déployée (dans cet exemple, `https://spring-todo-app.azurewebsites.net`). Vous pouvez copier cette URL dans votre navigateur web ou exécuter la commande suivante dans la fenêtre de votre terminal pour charger votre application.

```bash
open https://spring-todo-app.azurewebsites.net
```

Vous devez voir l’application exécutée avec l’URL distante dans la barre d’adresses :

 ![](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="view-logs-to-troubleshoot-the-app"></a>Afficher des journaux d’activité pour dépanner l’application

Activer la journalisation pour l’application web Java déployée dans App Service sous Linux :

```bash
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
  --web-server-logging filesystem
```

Diffusez ensuite les journaux d’activité de l’application web vers votre terminal :

```bash
az webapp log tail --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME}
```

Vous verrez les lignes de sortie les plus récentes et les nouvelles requêtes faites auprès de l’application de liste de tâches sont diffusées sur la console. Pour quitter la console, utilisez la commande CTRL + C.

```bash
bash-3.2$ az webapp log tail --name ${WEBAPP_NAME}  --resource-group ${RESOURCEGROUP_NAME}
2018-10-28T22:50:17  Welcome, you are now connected to log-streaming service.
2018-10-28T22:44:56.265890407Z   _____                               
2018-10-28T22:44:56.265930308Z   /  _  \ __________ _________   ____  
2018-10-28T22:44:56.265936008Z  /  /_\  \___   /  |  \_  __ \_/ __ \ 
2018-10-28T22:44:56.265940308Z /    |    \/    /|  |  /|  | \/\  ___/ 
2018-10-28T22:44:56.265944408Z \____|__  /_____ \____/ |__|    \___  >
2018-10-28T22:44:56.265948508Z         \/      \/                  \/ 
2018-10-28T22:44:56.265952508Z A P P   S E R V I C E   O N   L I N U X
2018-10-28T22:44:56.265956408Z Documentation: https://aka.ms/webapp-linux
2018-10-28T22:44:56.266260910Z Setup openrc ...
2018-10-28T22:44:57.396926506Z Service `hwdrivers' needs non existent service `dev'
2018-10-28T22:44:57.397294409Z  * Caching service dependencies ... [ ok ]
2018-10-28T22:44:57.474152273Z Starting ssh service...
...
...
2018-10-28T22:46:13.432160734Z [INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
2018-10-28T22:46:13.744859424Z [INFO] TomcatWebServer - Tomcat started on port(s): 80 (http) with context path ''
2018-10-28T22:46:13.783230205Z [INFO] TodoApplication - Started TodoApplication in 57.209 seconds (JVM running for 70.815)
2018-10-28T22:46:14.887366993Z 2018-10-28 22:46:14.887  INFO 198 --- [p-nio-80-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-10-28T22:46:14.887637695Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization started
2018-10-28T22:46:14.998479907Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization completed in 111 ms

2018-10-28T22:49:20.572059062Z Sun Oct 28 22:49:20 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:25.850543080Z Sun Oct 28 22:49:25 GMT 2018 DELETE ======= /api/todolist/{4f41ab03-1b12-4131-a920-fe5dfec106ca} ======= 
2018-10-28T22:49:26.047126614Z Sun Oct 28 22:49:26 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:30.201740227Z Sun Oct 28 22:49:30 GMT 2018 POST ======= /api/todolist ======= Milk
2018-10-28T22:49:30.413468872Z Sun Oct 28 22:49:30 GMT 2018 GET ======= /api/todolist =======
```

## <a name="scale-out-the-todo-app"></a>Scale out de l’application de liste de tâches

Faites la montée en charge de l’application en ajoutant un autre worker :

```bash
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez pas besoin de ces ressources pour un autre didacticiel (voir [Étapes suivantes](#next)), vous pouvez les supprimer en exécutant la commande suivante dans Cloud Shell : 
  
```bash
az group delete --name your-azure-group-name
```

<a name="next"></a>

## <a name="next-steps"></a>Étapes suivantes

[Azure pour les développeurs Java](/java/azure/)
[Spring Boot](https://spring.io/projects/spring-boot), [Spring Data pour Cosmos DB](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), [Azure Cosmos DB](/azure/cosmos-db/sql-api-introduction) et [App Service Linux ](/azure/app-service/containers/app-service-linux-intro).

Découvrez-en plus sur l’exécution des applications Java sur App Service sur Linux dans le guide du développeur.

> [!div class="nextstepaction"] 
> [Guide du développeur Java dans App Service Linux](/azure/app-service/containers/app-service-linux-java)
