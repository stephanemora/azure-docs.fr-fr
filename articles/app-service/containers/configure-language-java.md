---
title: Configurer des applications Linux Java - Azure App Service | Microsoft Docs
description: Découvrez comment configurer des applications Java s’exécutant dans Azure App Service sur Linux.
keywords: service d’application Azure, application web, linux, oss, java, java ee, jee, javaee
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 883042e7c8abb43338c55a76bba3d64844ce1c56
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604349"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configurer une application Linux Java pour Azure App Service

Azure App Service sur Linux permet aux développeurs Java de rapidement générer, déployer et mettre à l’échelle leurs applications web empaquetées Tomcat ou Java Standard Edition (SE) sur un service basé sur Linux entièrement managé. Déployez des applications avec les plug-ins Maven à partir de la ligne de commande ou dans des éditeurs comme IntelliJ, Eclipse ou Visual Studio Code.

Ce guide fournit les principaux concepts et des instructions pour les développeurs Java qui utilisent un conteneur Linux intégré dans App Service. Si vous n’avez jamais utilisé Azure App Service, suivez les [démarrage rapide de Java](quickstart-java.md) et [Java avec PostgreSQL didacticiel](tutorial-java-enterprise-postgresql-app.md) première.

## <a name="deploying-your-app"></a>Déploiement de votre application

Vous pouvez utiliser [plug-in Maven pour Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) pour déployer les fichiers .jar et .war. Déploiement avec des IDE populaires est également pris en charge avec [Kit de ressources Azure pour IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) ou [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Sinon, votre méthode de déploiement dépendra de votre type d’archive :

- Pour déployer des fichiers .war sur Tomcat, utilisez le point de terminaison `/api/wardeploy/` pour effectuer un POST de votre fichier d’archive. Pour plus d’informations sur cette API, voir [cette documentation](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Pour déployer des fichiers .jar sur les images Java SE, utilisez le point de terminaison `/api/zipdeploy/` du site Kudu. Pour plus d’informations sur cette API, voir [cette documentation](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Ne déployez pas votre fichier .war ou .jar en utilisant un FTP. L’outil FTP est conçu pour charger des scripts de démarrage, des dépendances ou d’autres fichiers de runtime. Il ne s’agit pas du meilleur choix pour le déploiement d’applications web.

## <a name="logging-and-debugging-apps"></a>Journalisation et débogage des applications

Vous trouverez des rapports de performances, des visualisations de trafic et des contrôles d’intégrité pour chaque application dans le portail Azure. Pour plus d’informations, consultez [présentation des diagnostics Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Accès à la console SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Diffuser les journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Pour plus d’informations, consultez [Envoi de journaux d’activité avec l’interface Azure CLI](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Journalisation des applications

Activez [Journal des applications](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) via le portail Azure ou [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) pour configurer App Service de sorte à écrire la sortie de console standard de votre application et les flux d’erreur de console standard dans le système de fichiers local ou le service Stockage Blob Azure. La journalisation sur l’instance du système de fichiers App Service locale est désactivée 12 heures après avoir été configurée. Si vous en avez besoin plus longtemps, configurez l’application pour écrire la sortie sur un conteneur de stockage d’objets blob. Vous trouverez vos journaux d’application Java et Tomcat dans le `/home/LogFiles/Application/` directory.

Si votre application utilise [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) pour le traçage, vous pouvez transférer ces traces pour révision vers Azure Application Insights en suivant les instructions de configuration des frameworks de journalisation dans [Exploration des journaux d’activité de traces Java dans Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Outils de résolution des problèmes

Les images de Java intégrées sont basées sur le [Linux Alpine](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) système d’exploitation. Utilisez le `apk` Gestionnaire de package pour installer des procédures de dépannage des outils ou commandes.

## <a name="customization-and-tuning"></a>Personnalisation et réglage

Azure App Service sur Linux prend en charge en dehors de la zone paramétrage et la personnalisation via le portail Azure et CLI. Passez en revue les articles suivants pour la configuration d’application spécifiques de Java web :

- [Configurer les paramètres App Service](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurer un nom de domaine personnalisé](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Activer le protocole SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Ajouter un CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurer le site Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Définir les options de runtime Java

Pour définir la mémoire allouée ou autres options de runtime JVM dans les environnements de Tomcat et de Java SE, créez un [paramètre d’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) nommé `JAVA_OPTS` avec les options. App Service Linux transmet ce paramètre comme variable d’environnement au runtime Java quand celui-ci démarre.

Dans le portail Azure, sous **Paramètres d’application** de l’application web, créez un paramètre d’application appelé `JAVA_OPTS` qui contient les paramètres supplémentaires tels que `-Xms512m -Xmx1204m`.

Pour configurer le paramètre d’application à partir du plug-in Maven, ajouter des balises de paramètre/valeur dans la section plug-in Azure. L’exemple suivant définit une taille du tas Java minimale et maximale spécifique :

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Les développeurs exécutant une seule application avec un seul emplacement de déploiement dans leur plan App Service peuvent utiliser les options suivantes :

- Instances B1 et S1 : `-Xms1024m -Xmx1024m`
- Instances B2 et S2 : `-Xms3072m -Xmx3072m`
- Instances B3 et S3 : `-Xms6144m -Xmx6144m`

Lors du réglage des paramètres de segment de mémoire de l’application, consultez les détails de votre plan App Service et prenez en compte qu’avec plusieurs applications et emplacements de déploiement, vous devez trouver l’allocation de mémoire optimale.

Si vous déployez une application du fichier JAR, il doit être nommé `app.jar` afin que l’image intégrée peut identifier correctement votre application. (Le plug-in Maven effectue automatiquement cette modification du nom). Si vous ne souhaitez pas renommer votre fichier JAR à `app.jar`, vous pouvez télécharger un script d’interpréteur de commandes avec la commande pour exécuter votre fichier JAR. Puis collez le chemin d’accès complet à ce script dans le [fichier de démarrage](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#startup-file) zone de texte dans la section de Configuration du portail.

### <a name="turn-on-web-sockets"></a>Activer les sockets web

Activez la prise en charge des sockets web dans le portail Azure dans les **Paramètres d’application** de l’application. Vous devrez redémarrer l’application pour que le paramètre prenne effet.

Activez la prise en charge des sockets web via l’interface Azure CLI avec la commande suivante :

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Redémarrez ensuite votre application :

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Définir l’encodage de caractères par défaut

Dans le portail Azure, sous **Paramètres d’application** de l’application web, créez un paramètre d’application appelé `JAVA_OPTS` avec la valeur `-Dfile.encoding=UTF-8`.

Vous pouvez aussi configurer le paramètre d’application à l’aide du plug-in Maven App Service. Ajoutez le nom du paramètre et les étiquettes des valeurs dans la configuration du plug-in :

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Ajuster le délai de démarrage

Si votre application Java est particulièrement volumineux, vous devez augmenter la limite de temps de démarrage. Pour ce faire, créez un paramètre d’application, `WEBSITES_CONTAINER_START_TIME_LIMIT` et définissez-le sur le nombre de secondes pendant lesquelles le Service de l’application doit attendre avant l’expiration. La valeur maximale est `1800` secondes.

## <a name="secure-applications"></a>Sécuriser les applications

Les applications Java exécutées dans App Service pour Linux présentent les mêmes [bonnes pratiques de sécurité](/azure/security/security-paas-applications-using-app-services) que les autres applications. 

### <a name="authenticate-users"></a>Authentification des utilisateurs

Configurer l’authentification d’application dans le portail Azure avec le **authentification et autorisation** option. À partir de là, vous pouvez activer l’authentification en utilisant Azure Active Directory ou des identifiants de réseaux sociaux tels que Facebook, Google ou GitHub. La configuration du portail Azure fonctionne seulement si vous configurez un seul fournisseur d’authentification. Pour plus d’informations, consultez [Configurer votre application App Service pour utiliser une connexion Azure Active Directory](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) et les articles connexes sur d’autres fournisseurs d’identités.

Si vous devez activer plusieurs fournisseurs de connexion, suivez les instructions de l’article [Personnaliser l’authentification App Service](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

 Les développeurs Spring Boot peuvent utiliser le [démarreur Spring Boot Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) pour sécuriser les applications à l’aide d’API et d’annotations Spring Security familières. Veillez à augmenter la taille d’en-tête maximale dans votre fichier `application.properties`. Nous vous suggérons une valeur de `16384`.

### <a name="configure-tlsssl"></a>Configurer TLS/SSL

Suivez les instructions dans [Lier un certificat SSL personnalisé existant](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) pour charger un certificat SSL existant et le lier au nom de domaine de votre application. Par défaut, votre application autorisera toujours les connexions HTTP. Suivez les étapes spécifiques du tutoriel pour appliquer SSL et TLS.

### <a name="use-keyvault-references"></a>Utilisation de références de coffre de clés

[Azure Key Vault](../../key-vault/key-vault-overview.md) fournit une gestion centralisée secret avec l’historique des stratégies et d’audit d’accès. Vous pouvez stocker des secrets (tels que les mots de passe ou les chaînes de connexion) dans le coffre de clés et accéder à ces secrets dans votre application via des variables d’environnement.

Tout d’abord, suivez les instructions pour [lui accordant l’accès de votre application au coffre de clés](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) et [apportant une référence Key Vault à votre clé secrète dans un paramètre d’Application](../app-service-key-vault-references.md#reference-syntax). Vous pouvez valider que la référence est résolue à la clé secrète à l’impression de la variable d’environnement tout en accédant à distance au terminal App Service.

Pour injecter ces secrets dans votre fichier de configuration Spring ou Tomcat, utilisez la syntaxe de l’injection de variable d’environnement (`${MY_ENV_VAR}`). Pour les fichiers de configuration Spring, consultez cette documentation sur [externalisées configurations](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="configure-apm-platforms"></a>Configurer des plates-formes d’APM

Cette section montre comment connecter des applications Java déployées sur Azure App Service sur Linux avec les performances des applications NewRelic et AppDynamics analyse les plateformes (APM).

[Configurer New Relic](#configure-new-relic)
[configurer AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>Configurer New Relic

1. Créez un compte NewRelic sur [NewRelic.com](https://newrelic.com/signup)
2. Téléchargez l’agent Java à partir de NewRelic, son nom doit ressembler à `newrelic-java-x.x.x.zip`.
3. Copiez votre clé de licence, vous en aurez besoin pour configurer l’agent par la suite.
4. [Connectez-vous avec SSH à votre instance App Service](app-service-linux-ssh-support.md) et créez un répertoire `/home/site/wwwroot/apm`.
5. Chargez les fichiers de l’agent Java de NewRelic décompressés dans un répertoire sous `/home/site/wwwroot/apm`. Les fichiers de votre agent doivent être dans `/home/site/wwwroot/apm/newrelic`.
6. Modifiez le fichier YAML dans `/home/site/wwwroot/apm/newrelic/newrelic.yml` et remplacez la valeur de la licence d’espace réservé par votre propre clé de licence.
7. Dans le portail Azure, accédez à votre application dans App Service et créez un paramètre d’application.
    - Si votre application utilise **Java SE**, créez une variable d’environnement nommée `JAVA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Si vous utilisez **Tomcat**, créez une variable d’environnement nommée `CATALINA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Si vous utilisez **WildFly**, consultez la documentation de New Relic [ici](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) pour obtenir des conseils sur l’installation de l’agent Java et la configuration de JBoss.
    - Si vous avez déjà une variable d’environnement pour `JAVA_OPTS` ou `CATALINA_OPTS`, ajoutez l’option `javaagent` à la fin de la valeur actuelle.

### <a name="configure-appdynamics"></a>Configurer AppDynamics

1. Créez un compte AppDynamics sur [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Téléchargez l’agent Java à partir du site web AppDynamics, le nom de fichier ressemble à `AppServerAgent-x.x.x.xxxxx.zip`
1. [Connectez-vous avec SSH à votre instance App Service](app-service-linux-ssh-support.md) et créez un répertoire `/home/site/wwwroot/apm`.
1. Chargez les fichiers de l’agent Java dans un répertoire sous `/home/site/wwwroot/apm`. Les fichiers de votre agent doivent être dans `/home/site/wwwroot/apm/appdynamics`.
1. Dans le portail Azure, accédez à votre application dans App Service et créez un paramètre d’application.
    - Si vous utilisez **Java SE**, créez une variable d’environnement nommée `JAVA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` où `<app-name>` est votre nom App Service.
    - Si vous utilisez **Tomcat**, créez une variable d’environnement nommée `CATALINA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` où `<app-name>` est votre nom App Service.
    - Si vous utilisez **WildFly**, consultez la documentation AppDynamics [ici](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) pour obtenir des conseils sur l’installation de l’agent Java et la configuration de JBoss.
    
## <a name="configure-jar-applications"></a>Configurer les Applications du fichier JAR

### <a name="starting-jar-apps"></a>Démarrage des applications du fichier JAR

Par défaut, App Service suppose que votre application JAR se nomme `app.jar`. S’il possède ce nom, il s’exécutera automatiquement. Pour les utilisateurs de Maven, vous pouvez définir le nom de fichier JAR en incluant `<finalName>app</finalName>` dans le `<build>` section de votre `pom.xml`. [Vous pouvez faire de même dans Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) en définissant le `archiveFileName` propriété.

Si vous souhaitez utiliser un autre nom pour votre fichier JAR, vous devez également fournir le [commande de démarrage](app-service-linux-faq.md#built-in-images) qui exécute votre fichier JAR. Par exemple : `java -jar my-jar-app.jar`. Vous pouvez définir la valeur de votre commande de démarrage dans le portail, sous Configuration > Paramètres généraux, ou avec un paramètre d’Application nommé `STARTUP_COMMAND`.

### <a name="server-port"></a>Port du serveur

App Service Linux achemine les demandes entrantes vers le port 80, votre application doit écouter sur le port 80. Vous pouvez le faire dans votre configuration d’application (tels que du ressort `application.properties` fichier), ou dans votre commande de démarrage (par exemple, `java -jar spring-app.jar --server.port=80`). Consultez la documentation suivante pour les infrastructures Java courantes :

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Sources de données

### <a name="tomcat"></a>Tomcat

Ces instructions s’appliquent à toutes les connexions de base de données. Vous devrez indiquer dans les espaces réservés le nom de la classe de pilote de la base de données que vous avez choisie ainsi que le fichier JAR. Vous disposez d’une table contenant des noms de classes et de téléchargements de pilotes pour les bases de données courantes.

| Base de données   | Nom de la classe du pilote                             | Pilote JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Télécharger](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Télécharger](https://dev.mysql.com/downloads/connector/j/) (sélectionnez « Indépendant de la plateforme ») |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Télécharger](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Pour configurer Tomcat pour utiliser Java Database Connectivity (JDBC) ou l’API de persistance Java (JPA), tout d’abord personnaliser le `CATALINA_OPTS` variable d’environnement qui est lu par Tomcat au démarrage. Définissez ces valeurs via un paramètre d’application dans le [plug-in Maven App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) :

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou définissez les variables d’environnement dans le panneau « Paramètres d’application » du portail Azure.

Ensuite, déterminez si la source de données doit être mise à la disposition d’une seule application ou de toutes les applications exécutées sur le servlet Tomcat.

#### <a name="application-level-data-sources"></a>Sources de données de niveau application

1. Créez un fichier `context.xml` dans le répertoire `META-INF/` de votre projet. Créez le répertoire `META-INF/` s’il n’existe pas.

2. Dans `context.xml`, ajoutez un élément `Context` pour lier la source de données à une adresse JNDI. Remplacez l’espace réservé `driverClassName` par le nom de la classe de votre pilote à l’aide du tableau ci-dessus.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ```

3. Mettez à jour le fichier `web.xml` de votre application pour utiliser la source de données dans votre application.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Ressources partagées au niveau du serveur

1. Copiez le contenu de `/usr/local/tomcat/conf` dans `/home/tomcat/conf` sur votre instance App Service Linux à l’aide de SSH, si vous n’y avez pas déjà une configuration.
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Ajoutez un élément de contexte dans votre `server.xml` au sein d’élément `<Server>`.

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. Mettez à jour le fichier `web.xml` de votre application pour utiliser la source de données dans votre application.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Finaliser la configuration

Enfin, placez les fichiers de pilote JAR dans classpath Tomcat et redémarrer votre App Service.

1. Veillez à ce que les fichiers du pilote JDBC soient disponibles pour le chargeur de classes Tomcat en les mettant dans le répertoire `/home/tomcat/lib`. (S’il n’existe pas déjà, créez ce répertoire.) Pour charger ces fichiers sur votre instance App Service, procédez comme suit :
    1. Dans le [Cloud Shell](https://shell.azure.com), installez l’extension de l’application Web :

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Exécutez la commande CLI suivante pour créer un tunnel SSH à partir de votre système local vers App Service :

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Connectez-vous au port de tunneling local avec votre client SFTP et chargez les fichiers dans le dossier `/home/tomcat/lib`.

    Vous pouvez également utiliser un client FTP pour charger le pilote JDBC. Suivez ces [instructions pour obtenir vos informations d’identification FTP](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Si vous avez créé une source de données de niveau serveur, redémarrez l’application App Service Linux. Tomcat rétablit `CATALINA_HOME` sur `/home/tomcat/conf` et utilise la configuration mise à jour.

### <a name="spring-boot"></a>Spring Boot

Pour vous connecter aux sources de données dans des applications Spring Boot, nous vous suggérons de créer des chaînes de connexion et de les injecter dans vos `application.properties` fichier.

1. Dans la section « Paramètres de l’Application » du panneau App Service, définir un nom pour la chaîne, collez votre chaîne de connexion JDBC dans le champ de valeur et définissez le type « Personnalisé ». Vous pouvez éventuellement définir cette chaîne de connexion en tant que paramètre d’emplacement.

    ! [Création d’une chaîne de connexion dans le portail.]
    

    Cette chaîne de connexion est accessible à notre application comme une variable d’environnement nommée `CUSTOMCONNSTR_<your-string-name>`. Par exemple, la chaîne de connexion que nous avons créé ci-dessus sera nommée `CUSTOMCONNSTR_exampledb`.

2. Dans votre `application.properties` de fichiers, cette chaîne de connexion avec le nom de variable d’environnement de référence. Dans notre exemple, nous utiliserions ce qui suit.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Consultez le [documentation Spring Boot sur l’accès aux données](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) et [externalisées configurations](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) pour plus d’informations sur cette rubrique.

## <a name="configure-java-ee-wildfly"></a>Configurer Java EE (WildFly)

> [!NOTE]
> Java Enterprise Edition sur App Service Linux est actuellement en version préliminaire. Cette pile est **pas** recommandé pour le travail de production côté. plus d’informations sur nos piles Java SE et Tomcat.

Azure App Service sur Linux permet aux développeurs de Java pour générer, déployer et mettre à l’échelle des applications d’entreprise de Java (EE Java) sur un service entièrement géré basé sur Linux.  L’environnement d’exécution Java Enterprise sous-jacent est le serveur d’applications open source [WildFly](https://wildfly.org/).

[Mise à l’échelle avec App Service](#scale-with-app-service)
[configuration de serveur d’applications personnaliser](#customize-application-server-configuration)
[Modules et les dépendances](#modules-and-dependencies)
[données sources](#data-sources)
[activer les fournisseurs de messagerie](#enable-messaging-providers)
[configurer la mise en cache de gestion de session](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Mettre à l’échelle avec App Service

Le serveur d’applications WildFly s’exécutant dans App Service sur Linux s’exécute en mode autonome, et non pas dans une configuration de domaine. Quand vous effectuez un scale-out du plan App Service, chaque instance WildFly est configurée comme un serveur autonome.

 Mettez à l’échelle votre application verticalement ou horizontalement, avec des [règles de mise à l’échelle](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) et en [augmentant le nombre de vos instances](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Personnaliser la configuration du serveur d’applications

Les instances d’application web sont sans état, chaque nouvelle instance démarrée doit donc être configurée au démarrage pour prendre en charge la configuration Wildfly nécessaire par application.
Vous pouvez écrire un script Bash de démarrage pour appeler l’interface CLI WildFly afin de :

- Configurer les sources de données
- Configurer les fournisseurs de messagerie
- Ajouter d’autres modules et dépendances à la configuration de serveur WildFly.

 Le script s’exécute une fois que WildFly est opérationnel, mais avant le démarrage de l’application. Le script doit utiliser l’[interface CLI JBOSS](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) appelée depuis `/opt/jboss/wildfly/bin/jboss-cli.sh` pour configurer le serveur d’applications avec une certaine configuration ou avec les modifications nécessaires après le démarrage du serveur.

N’utilisez pas le mode interactif de l’interface CLI pour configurer WildFly. Au lieu de cela, vous pouvez fournir un script de commandes à l’interface CLI JBoss avec la commande `--file`, par exemple :

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Chargez le script de démarrage sur `/home/site/deployments/tools` dans votre instance App Service. Consultez [ce document](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope) pour obtenir des instructions sur l’obtention de vos informations d’identification FTP.

Définissez le champ **Script de démarrage** champ dans le portail Azure sur l’emplacement de votre script shell de démarrage, par exemple `/home/site/deployments/tools/your-startup-script.sh`.

Fournir [paramètres de l’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) dans la configuration d’application pour passer les variables d’environnement pour une utilisation dans le script. Les paramètres d’application conservent les chaînes de connexion et les autres secrets nécessaires pour configurer votre application en dehors de la gestion de version.

### <a name="modules-and-dependencies"></a>Modules et dépendances

Pour installer des modules et leurs dépendances dans le classpath WildFly via l’interface CLI JBoss, vous devez créer les fichiers suivants dans leur propre répertoire. Certains modules et certaines dépendances peuvent nécessiter une configuration supplémentaire, comme le nommage JNDI ou d’autres configurations spécifiques à l’API : cette liste est donc un ensemble minimal de ce vous devez configurer dans la plupart des cas pour une dépendance.

- [Descripteur de module XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Ce fichier XML définit le nom, les attributs et les dépendances de votre module. Cet [exemple de fichier module.xml](https://access.redhat.com/documentation/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) définit un module Postgres, sa dépendance JDBC du fichier JAR et d’autres dépendances nécessaires du module.
- Dépendances de fichier JAR nécessaires pour votre module.
- Script avec vos commandes d’interface CLI JBoss pour configurer le nouveau module. Ce fichier contient vos commandes à exécuter par l’interface CLI JBoss pour configurer le serveur de façon à ce qu’il utilise la dépendance. Pour une documentation sur les commandes pour ajouter des modules, des sources de données et des fournisseurs de messagerie, reportez-vous à [ce document](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Script de démarrage Bash pour appeler l’interface CLI JBoss et exécuter le script de l’étape précédente. Ce fichier est exécuté quand votre instance App Service est redémarrée ou quand de nouvelles instances sont provisionnées lors d’un scale-out. C’est dans ce script de démarrage que vous pouvez effectuer les autres configurations pour votre application, car les commandes JBoss sont passées à l’interface CLI JBoss. Au minimum, ce fichier peut être une commande unique pour passer votre script de commandes d’interface CLI JBoss à l’interface CLI JBoss :

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

Une fois que vous avez les fichiers et le contenu pour votre module, suivez les étapes ci-dessous pour ajouter le module au serveur d’applications WildFly.

1. Chargez vos fichiers via FTP dans `/home/site/deployments/tools` sur votre instance App Service. Consultez ce document pour obtenir des instructions sur l’obtention de vos informations d’identification FTP.
2. Dans le panneau Paramètres de l’application du portail Azure, définissez le champ « Script de démarrage » sur l’emplacement de votre script shell de démarrage, par exemple `/home/site/deployments/tools/your-startup-script.sh`.
3. Redémarrez votre instance App Service en appuyant sur la **redémarrer** situé dans le **vue d’ensemble** section du portail ou à l’aide de l’interface CLI.

### <a name="configure-data-source-connections"></a>Configurer des connexions de source de données

Pour configurer une connexion de source de données WildFly, suivez le même processus que ci-dessus dans la section Installation de modules et de dépendances. Vous pouvez effectuer les mêmes étapes pour n’importe quel service Azure Database.

1. Téléchargez le pilote JDBC pour votre base de données. Pour des raisons pratiques, voici les pilotes pour [Postgres](https://jdbc.postgresql.org/download.html) et [MySQL](https://dev.mysql.com/downloads/connector/j/). Décompressez le téléchargement pour obtenir le fichier .jar.
2. Suivez le plan des étapes de « Modules et dépendances » pour créer et charger votre descripteur de module XML, le script d’interface CLI JBoss, le script de démarrage et la dépendance .jar JDBC.

Des informations plus détaillées sur la configuration de WildFly avec [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) et [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) sont disponibles. Vous pouvez utiliser ces instructions personnalisées ainsi que l’approche généralisée ci-dessus pour ajouter des définitions de source de données à votre serveur.

### <a name="enable-messaging-providers"></a>Activer les fournisseurs de messagerie

Pour activer des beans pilotés par les messages avec Service Bus comme mécanisme de messagerie :

1. Utilisez la [bibliothèque de messagerie Apache QPId JMS](https://qpid.apache.org/proton/index.html). Incluez cette dépendance dans votre fichier pom.xml (ou dans un autre fichier de build) pour l’application.

2. Créez des [ressources Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Créez un espace de noms Azure Service Bus et une file d’attente dans cet espace de noms ainsi qu’une stratégie d’accès partagé avec des fonctionnalités d’envoi et de réception.

3. Passez la clé de la stratégie d’accès partagé à votre code, en encodant la clé principale de votre stratégie dans une URL ou en [utilisant le SDK Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Suivez les étapes décrites dans la section Installation de modules et de dépendances avec le descripteur XML de votre module, les dépendances .jar, les commandes CLI JBoss et le script de démarrage pour le fournisseur JMS. En plus des quatre fichiers, vous devez aussi créer un fichier XML qui définit le nom JNDI pour la file d’attente et la rubrique JMS. Pour les fichiers de configuration de référence, consultez [ce dépôt](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig).

### <a name="configure-session-management-caching"></a>Configurer la mise en cache de la gestion de session

Par défaut, App Service sur Linux utilise des cookies de session d’affinités pour garantir que les demandes des clients ayant des sessions existantes sont routées vers la même instance de votre application. Ce comportement par défaut ne nécessite aucune configuration, mais il présente certaines limitations :

- Si une instance d’application est redémarrée ou fait l’objet d’un scale-down, l’état de la session utilisateur dans le serveur d’applications est perdu.
- Si les applications ont des paramètres de délai d’expiration de session avec une durée longue ou bien un nombre fixe d’utilisateurs, la réception de la charge par les instances mises à l’échelle automatiquement peut prendre un certain temps, car seules les nouvelles sessions sont routées vers les instances nouvellement démarrées.

Vous pouvez configurer WildFly de façon à utiliser un magasin de sessions externe comme le [Cache Azure pour Redis](/azure/azure-cache-for-redis/). Vous devez alors [désactiver la configuration de l’affinité d’instance ARR existante](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) pour arrêter le routage de session basé sur les cookies et autoriser le magasin de sessions WildFly configuré pour un fonctionnement sans interférence.

## <a name="docker-containers"></a>Conteneurs Docker

Pour utiliser le kit JDK Zulu pris en charge par Azure dans vos conteneurs, assurez-vous d’extraire et d’utiliser les images précompilées documentées dans la [page de téléchargement Azul Zulu Enterprise for Azure](https://www.azul.com/downloads/azure-only/zulu/), ou utilisez les exemples `Dockerfile` du [référentiel Microsoft Java GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Instruction de prise en charge

### <a name="runtime-availability"></a>Disponibilité de l’exécution

App Service pour Linux prend en charge deux runtimes pour l’hébergement managé des application web Java :

- Le [conteneur servlet Tomcat](https://tomcat.apache.org/) pour exécuter les applications empaquetées sous forme de fichiers WAR (web archive). Les versions prises en charge sont les versions 8.5 et 9.0.
- L’environnement de runtime Java SE pour exécuter les applications empaquetées sous forme de fichiers JAR (Java archive). Versions prises en charge sont Java 8 et 11.

### <a name="jdk-versions-and-maintenance"></a>Versions JDK et maintenance

Le kit de développement Java (JDK) pris en charge d’Azure est [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fourni par [Azul Systems](https://www.azul.com/).

Les mises à jour de la version majeure sont fournies via de nouvelles options de runtime dans Azure App Service pour Linux. Les clients effectuent une mise à jour avec ces versions plus récentes de Java en configurant leur déploiement App Service et doivent s’occuper des tests et de s’assurer que la mise à jour majeure répond à leurs besoins.

Les kits JDK pris en charge sont automatiquement mis à jour tous les trimestres, en janvier, avril, juillet et octobre de chaque année.

### <a name="security-updates"></a>Mises à jour de sécurité

Les correctifs des principales vulnérabilités de sécurité seront publiés dès qu’Azul Systems les mettra à disposition. Une vulnérabilité « majeure » est définie par un score de base de 9.0 ou supérieur dans le système [NIST Common Vulnerability Scoring System, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Dépréciation et retrait

Si un runtime Java pris en charge est retiré, les développeurs Azure utilisant le runtime affecté reçoivent un avis de dépréciation au moins six mois avant son retrait.

### <a name="local-development"></a>Développement local

Les développeurs peuvent télécharger l’édition Production du kit Azul Zulu Enterprise JDK à partir du [site de téléchargement d’Azul](https://www.azul.com/downloads/azure-only/zulu/) pour développer en local.

### <a name="development-support"></a>Prise en charge du développement

La prise en charge produit du [kit JDK Zulu d’Azul pris en charge par Azure](https://www.azul.com/downloads/azure-only/zulu/) est disponible si vous développez pour Azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) avec un [plan de support Azure éligible](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Prise en charge du runtime

Les développeurs peuvent [signaler un problème](/azure/azure-supportability/how-to-create-azure-support-request) avec les kits JDK Zulu d’Azul via le support Azure s’ils ont un [plan de support éligible](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Étapes suivantes

Visitez le centre [Azure pour les développeurs Java](/java/azure/) pour trouver des guides de démarrage rapide Azure, des tutoriels et la documentation de référence Java.

Des questions générales sur l’utilisation d’App Service pour Linux qui ne sont pas spécifiques au développement Java sont traitées dans [Questions fréquentes (FAQ) sur App Service Linux](app-service-linux-faq.md).

