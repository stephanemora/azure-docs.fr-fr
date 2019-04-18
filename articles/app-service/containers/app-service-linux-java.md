---
title: Guide du développeur Java pour App Service sur Linux - Azure | Microsoft Docs
description: Découvrez comment configurer des applications Java s’exécutant dans Azure App Service sur Linux.
keywords: azure app service, application web, linux, oss, java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 5c9f70650f518c72a75d9a7826e7cbc30a95a00c
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680874"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Guide du développeur Java pour App Service sur Linux

Azure App Service sur Linux permet aux développeurs Java de rapidement générer, déployer et mettre à l’échelle leurs applications web empaquetées Tomcat ou Java Standard Edition (SE) sur un service basé sur Linux entièrement managé. Déployez des applications avec les plug-ins Maven à partir de la ligne de commande ou dans des éditeurs comme IntelliJ, Eclipse ou Visual Studio Code.

Ce guide fournit les concepts et instructions clés aux développeurs Java qui utilisent App Service pour Linux. Si vous n’avez jamais utilisé Azure App Service pour Linux, commencez par lire [Démarrage rapide avec Java](quickstart-java.md). Des questions générales sur l’utilisation d’App Service pour Linux qui ne sont pas spécifiques au développement Java sont traitées dans [Questions fréquentes (FAQ) sur App Service Linux](app-service-linux-faq.md).

## <a name="deploying-your-app"></a>Déploiement de votre application

Vous pouvez utiliser [plug-in Maven pour Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) pour déployer les fichiers .jar et .war. Déploiement avec des IDE populaires est également pris en charge avec [Kit de ressources Azure pour IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) ou [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Sinon, votre méthode de déploiement dépendra de votre type d’archive :

- Pour déployer des fichiers .war sur Tomcat, utilisez le point de terminaison `/api/wardeploy/` pour effectuer un POST de votre fichier d’archive. Pour plus d’informations sur cette API, voir [cette documentation](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Pour déployer des fichiers .jar sur les images Java SE, utilisez le point de terminaison `/api/zipdeploy/` du site Kudu. Pour plus d’informations sur cette API, voir [cette documentation](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Ne déployez pas votre fichier .war ou .jar en utilisant un FTP. L’outil FTP est conçu pour charger des scripts de démarrage, des dépendances ou d’autres fichiers de runtime. Il ne s’agit pas du meilleur choix pour le déploiement d’applications web.

## <a name="logging-and-debugging-apps"></a>Journalisation et débogage des applications

Vous trouverez des rapports de performances, des visualisations de trafic et des contrôles d’intégrité pour chaque application dans le portail Azure. Consultez [Vue d’ensemble des diagnostics Azure App Service](/azure/app-service/overview-diagnostics) pour plus d’informations sur la façon d’accéder à ces outils de diagnostic et des les utiliser.

## <a name="application-performance-monitoring"></a>Analyse des performances des applications

Pour des instructions permettant de configurer New Relic et AppDynamics avec des applications Java qui s’exécutent sur App Service sous Linux, voir [Outils d’analyse des performances des applications Java sur Azure App Service sous Linux](how-to-java-apm-monitoring.md).

### <a name="ssh-console-access"></a>Accès à la console SSH

Connectivité SSH à l’environnement Linux exécutant votre application est disponible. Consultez [Prise en charge SSH pour Azure App Service sur Linux](/azure/app-service/containers/app-service-linux-ssh-support) pour obtenir des instructions complètes sur la connexion au système Linux via votre navigateur web ou terminal local.

### <a name="streaming-logs"></a>Diffusion de journaux d’activité

Pour un débogage et un dépannage rapides, vous pouvez envoyer des journaux d’activité à votre console en utilisant l’interface Azure CLI. Configurez l’interface CLI avec `az webapp log config` pour activer la journalisation :

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

Ensuite, envoyez les journaux d’activité à votre console en utilisant `az webapp log tail` :

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

Pour plus d’informations, consultez [Envoi de journaux d’activité avec l’interface Azure CLI](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Journalisation des applications

Activez [Journal des applications](/azure/app-service/troubleshoot-diagnostic-logs#enablediag) via le portail Azure ou [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) pour configurer App Service de sorte à écrire la sortie de console standard de votre application et les flux d’erreur de console standard dans le système de fichiers local ou le service Stockage Blob Azure. La journalisation sur l’instance du système de fichiers App Service locale est désactivée 12 heures après avoir été configurée. Si vous en avez besoin plus longtemps, configurez l’application pour écrire la sortie sur un conteneur de stockage d’objets blob. Vous trouverez vos journaux d’application Java et Tomcat dans le `/home/LogFiles/Application/` directory.

Si votre application utilise [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) pour le traçage, vous pouvez transférer ces traces pour révision vers Azure Application Insights en suivant les instructions de configuration des frameworks de journalisation dans [Exploration des journaux d’activité de traces Java dans Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Outils de dépannage

Les images de Java intégrées sont basées sur le [Linux Alpine](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) système d’exploitation. Utilisez le `apk` Gestionnaire de package pour installer des procédures de dépannage des outils ou commandes.

## <a name="customization-and-tuning"></a>Personnalisation et réglage

Azure App Service sur Linux prend en charge en dehors de la zone paramétrage et la personnalisation via le portail Azure et CLI. Consultez les articles suivants pour configurer des applications web spécifiques non-Java :

- [Configurer les paramètres App Service](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurer un nom de domaine personnalisé](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Activer le protocole SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Ajouter un CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurer le site Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Définir les options de runtime Java

Pour définir la mémoire allouée ou autres options de runtime JVM dans les environnements de Tomcat et de Java SE, créez un [paramètre d’application](/azure/app-service/web-sites-configure#app-settings) nommé `JAVA_OPTS` avec les options. App Service Linux transmet ce paramètre comme variable d’environnement au runtime Java quand celui-ci démarre.

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

Si vous déployez une application du fichier JAR, il doit être nommé `app.jar` afin que l’image intégrée peut identifier correctement votre application. (Le plug-in Maven effectue automatiquement cette modification du nom). Si vous ne souhaitez pas renommer votre fichier JAR à `app.jar`, vous pouvez télécharger un script d’interpréteur de commandes avec la commande pour exécuter votre fichier JAR. Puis collez le chemin d’accès complet à ce script dans le [fichier de démarrage](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-faq#startup-file) zone de texte dans la section de Configuration du portail.

### <a name="turn-on-web-sockets"></a>Activer les sockets web

Activez la prise en charge des sockets web dans le portail Azure dans les **Paramètres d’application** de l’application. Vous devrez redémarrer l’application pour que le paramètre prenne effet.

Activez la prise en charge des sockets web via l’interface Azure CLI avec la commande suivante :

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true
```

Redémarrez ensuite votre application :

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
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

Configurer l’authentification d’application dans le portail Azure avec le **authentification et autorisation** option. À partir de là, vous pouvez activer l’authentification en utilisant Azure Active Directory ou des identifiants de réseaux sociaux tels que Facebook, Google ou GitHub. La configuration du portail Azure fonctionne seulement si vous configurez un seul fournisseur d’authentification.  Pour plus d’informations, consultez [Configurer votre application App Service pour utiliser une connexion Azure Active Directory](/azure/app-service/configure-authentication-provider-aad) et les articles connexes sur d’autres fournisseurs d’identités.

Si vous devez activer plusieurs fournisseurs de connexion, suivez les instructions de l’article [Personnaliser l’authentification App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to).

Les développeurs Spring Boot peuvent utiliser le [démarreur Spring Boot Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) pour sécuriser les applications à l’aide d’API et d’annotations Spring Security familières. Veillez à augmenter la taille d’en-tête maximale dans votre fichier `application.properties`. Nous vous suggérons une valeur de `16384`.

### <a name="configure-tlsssl"></a>Configurer TLS/SSL

Suivez les instructions dans [Lier un certificat SSL personnalisé existant](/azure/app-service/app-service-web-tutorial-custom-ssl) pour charger un certificat SSL existant et le lier au nom de domaine de votre application. Par défaut, votre application autorisera toujours les connexions HTTP. Suivez les étapes spécifiques du tutoriel pour appliquer SSL et TLS.

### <a name="use-keyvault-references"></a>Utilisation de références de coffre de clés

[Azure Key Vault](../../key-vault/key-vault-overview.md) fournit une gestion centralisée secret avec l’historique des stratégies et d’audit d’accès. Vous pouvez stocker des secrets (tels que les mots de passe ou les chaînes de connexion) dans le coffre de clés et accéder à ces secrets dans votre application via des variables d’environnement.

Tout d’abord, suivez les instructions pour [lui accordant l’accès de votre application au coffre de clés](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) et [apportant une référence Key Vault à votre clé secrète dans un paramètre d’Application](../app-service-key-vault-references.md#reference-syntax). Vous pouvez valider que la référence est résolue à la clé secrète à l’impression de la variable d’environnement tout en accédant à distance au terminal App Service.

Pour injecter ces secrets dans votre fichier de configuration Spring ou Tomcat, utilisez la syntaxe de l’injection de variable d’environnement (`${MY_ENV_VAR}`). Pour les fichiers de configuration Spring, consultez cette documentation sur [externalisées configurations](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="data-sources"></a>Sources de données

### <a name="tomcat"></a>Tomcat

Ces instructions s’appliquent à toutes les connexions de base de données. Vous devrez indiquer dans les espaces réservés le nom de la classe de pilote de la base de données que vous avez choisie ainsi que le fichier JAR. Vous disposez d’une table contenant des noms de classes et de téléchargements de pilotes pour les bases de données courantes.

| Base de données   | Nom de la classe du pilote                             | Pilote JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Télécharger](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Télécharger](https://dev.mysql.com/downloads/connector/j/) (sélectionnez « Indépendant de la plateforme ») |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Télécharger](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Pour configurer Tomcat afin d’utiliser Java Database Connectivity (JDBC) ou l’API Java Persistence (JPA), commencez par personnaliser la variable d’environnement `CATALINA_OPTS` lue par Tomcat au démarrage. Définissez ces valeurs via un paramètre d’application dans le [plug-in Maven App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) :

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

#### <a name="shared-server-level-resources"></a>Ressources partagées, au niveau du serveur

1. Copiez le contenu de `/usr/local/tomcat/conf` dans `/home/tomcat/conf` sur votre instance App Service Linux à l’aide de SSH, si vous n’y avez pas déjà une configuration.

    ```bash
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

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>Enfin, placez les fichiers de pilote JAR dans classpath Tomcat et redémarrer votre App Service

1. Veillez à ce que les fichiers du pilote JDBC soient disponibles pour le chargeur de classes Tomcat en les mettant dans le répertoire `/home/tomcat/lib`. (S’il n’existe pas déjà, créez ce répertoire.) Pour charger ces fichiers sur votre instance App Service, procédez comme suit :  
   1. Installez l’extension webpp Azure App Service :

      ```azurecli-interactive
      az extension add –name webapp
      ```

   1. Exécutez la commande CLI suivante pour créer un tunnel SSH de votre système local vers App Service :

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

   1. Connectez-vous au port de tunneling local avec votre client SFTP et chargez les fichiers dans le dossier `/home/tomcat/lib`.

      Vous pouvez également utiliser un client FTP pour charger le pilote JDBC. Suivez ces [instructions pour obtenir vos informations d’identification FTP](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

2. Si vous avez créé une source de données de niveau serveur, redémarrez l’application App Service Linux. Tomcat rétablit `CATALINA_HOME` sur `/home/tomcat/conf` et utilise la configuration mise à jour.

### <a name="spring-boot"></a>Spring Boot

Pour vous connecter aux sources de données dans des applications Spring Boot, nous vous suggérons de créer des chaînes de connexion et de les injecter dans vos `application.properties` fichier.

1. Dans la section « Paramètres de l’Application » du panneau App Service, définir un nom pour la chaîne, collez votre chaîne de connexion JDBC dans le champ de valeur et définissez le type « Personnalisé ». Vous pouvez éventuellement définir cette chaîne de connexion en tant que paramètre d’emplacement.

    ![Création d’une chaîne de connexion dans le portail.][1]

    Cette chaîne de connexion est accessible à notre application comme une variable d’environnement nommée `CUSTOMCONNSTR_<your-string-name>`. Par exemple, la chaîne de connexion que nous avons créé ci-dessus sera nommée `CUSTOMCONNSTR_exampledb`.

2. Dans votre `application.properties` de fichiers, cette chaîne de connexion avec le nom de variable d’environnement de référence. Dans notre exemple, nous utiliserions ce qui suit.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Consultez le [documentation Spring Boot sur l’accès aux données](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html
) et [externalisées configurations](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) pour plus d’informations sur cette rubrique.

## <a name="docker-containers"></a>Conteneurs Docker

Pour utiliser le kit JDK Zulu pris en charge par Azure dans vos conteneurs, assurez-vous d’extraire et d’utiliser les images précompilées documentées dans la [page de téléchargement Azul Zulu Enterprise for Azure](https://www.azul.com/downloads/azure-only/zulu/), ou utilisez les exemples `Dockerfile` du [référentiel Microsoft Java GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Disponibilité des runtimes et informations de prise en charge

App Service pour Linux prend en charge deux runtimes pour l’hébergement managé des application web Java :

- Le [conteneur servlet Tomcat](https://tomcat.apache.org/) pour exécuter les applications empaquetées sous forme de fichiers WAR (web archive). Les versions prises en charge sont les versions 8.5 et 9.0.
- L’environnement de runtime Java SE pour exécuter les applications empaquetées sous forme de fichiers JAR (Java archive). La seule version majeure prise en charge est Java 8.

## <a name="java-runtime-statement-of-support"></a>Informations de prise en charge du runtime Java

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

<!--Image references-->
[1]: ./media/app-service-linux-java/connection-string.png
