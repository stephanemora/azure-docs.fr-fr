---
title: Configurer des applications Java Linux - Azure App Service | Microsoft Docs
description: Découvrez comment configurer des applications Java s’exécutant dans Azure App Service sur Linux.
keywords: azure app service, application web, linux, oss, java, java ee, jee, javaee
services: app-service
author: bmitchell287
manager: barbkess
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/26/2019
ms.author: brendm
ms.custom: seodec18
ms.openlocfilehash: 9625870132d088bf1de6df06f05f0cac41a1e7fa
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144217"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configurer une application Java Linux pour Azure App Service

Azure App Service sur Linux permet aux développeurs Java de rapidement générer, déployer et mettre à l’échelle leurs applications web empaquetées Tomcat ou Java Standard Edition (SE) sur un service basé sur Linux entièrement managé. Déployez des applications avec les plug-ins Maven à partir de la ligne de commande ou dans des éditeurs comme IntelliJ, Eclipse ou Visual Studio Code.

Ce guide fournit des concepts et des instructions clés aux développeurs Java qui utilisent un conteneur Linux intégré dans App Service. Si vous n’avez jamais utilisé Azure App Service, suivez d’abord le [guide de démarrage rapide Java](quickstart-java.md) et le [tutoriel sur l’utilisation de Java avec PostgreSQL](tutorial-java-enterprise-postgresql-app.md).

## <a name="deploying-your-app"></a>Déploiement de votre application

Vous pouvez utiliser le [plug-in Maven pour Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) pour déployer des fichiers .jar et .war. Le déploiement avec des IDE populaires est également pris en charge avec [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) ou [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Sinon, votre méthode de déploiement dépend du type de votre archive :

- Pour déployer des fichiers .war sur Tomcat, utilisez le point de terminaison `/api/wardeploy/` pour effectuer un POST de votre fichier d’archive. Pour plus d’informations sur cette API, voir [cette documentation](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Pour déployer des fichiers .jar sur les images Java SE, utilisez le point de terminaison `/api/zipdeploy/` du site Kudu. Pour plus d’informations sur cette API, voir [cette documentation](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Ne déployez pas votre fichier .war ou .jar en utilisant un FTP. L’outil FTP est conçu pour charger des scripts de démarrage, des dépendances ou d’autres fichiers de runtime. Il ne s’agit pas du meilleur choix pour le déploiement d’applications web.

## <a name="logging-and-debugging-apps"></a>Journalisation et débogage des applications

Vous trouverez des rapports de performances, des visualisations de trafic et des contrôles d’intégrité pour chaque application dans le portail Azure. Pour plus d’informations, consultez [Présentation des diagnostics Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Accès à la console SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Diffuser les journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Pour plus d’informations, consultez [Diffuser des journaux dans Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Journalisation des applications

Activez [Journal des applications](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enable-application-logging-windows) via le portail Azure ou [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) pour configurer App Service de sorte à écrire la sortie de console standard de votre application et les flux d’erreur de console standard dans le système de fichiers local ou le service Stockage Blob Azure. La journalisation sur l’instance du système de fichiers App Service locale est désactivée 12 heures après avoir été configurée. Si vous en avez besoin plus longtemps, configurez l’application pour écrire la sortie sur un conteneur de stockage d’objets blob. Vous trouverez vos journaux d’application Java et Tomcat dans le répertoire */home/LogFiles/Application/* .

Si votre application utilise [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) pour le traçage, vous pouvez transférer ces traces pour révision vers Azure Application Insights en suivant les instructions de configuration des frameworks de journalisation dans [Exploration des journaux d’activité de traces Java dans Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Outils de résolution des problèmes

Les images Java intégrées sont basées sur le système d’exploitation [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html). Utilisez le gestionnaire de package `apk` pour installer des outils ou commandes de résolution des problèmes.

### <a name="flight-recorder"></a>Boîte noire SQL

La boîte noire SQL Zulu est installée sur toutes les images Java Linux sur App Service pour que vous puissiez vous connecter facilement à la machine virtuelle Java et démarrer un enregistrement de profileur ou générer un dump de tas.

#### <a name="timed-recording"></a>Enregistrement chronométré

Pour commencer, connectez-vous avec SSH à votre App Service et exécutez la commande `jcmd` pour afficher la liste de tous les processus Java en cours d’exécution. En plus de jcmd lui-même, vous devez voir votre application Java en cours d’exécution avec un numéro d’identification de processus (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Exécutez la commande suivante pour démarrer un enregistrement de 30 secondes de la machine virtuelle Java. La machine virtuelle Java est profilée et un fichier JFR nommé *jfr_example.jfr* est créé dans le répertoire d’accueil. (Remplacez 116 par le pid de votre application Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Pendant l’intervalle de 30 secondes, vous pouvez vérifier le déroulement de l’enregistrement en exécutant `jcmd 116 JFR.check`. Cette commande affiche tous les enregistrements du processus Java donné.

#### <a name="continuous-recording"></a>Enregistrement continu

Vous pouvez utiliser la boîte noire SQL Zulu pour profiler en continu votre application Java avec un impact minimal sur les performances du runtime ([source](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Pour ce faire, exécutez la commande d’Azure CLI suivante pour créer un paramètre d’application nommé JAVA_OPTS avec la configuration nécessaire. Le contenu du paramètre d’application JAVA_OPTS est transmis à la commande `java` au démarrage de votre application.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Une fois l’enregistrement démarré, vous pouvez vider les données d’enregistrement à tout moment à l’aide de la commande `JFR.dump`.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Pour plus d’informations, consultez les [informations de référence sur la commande Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analyse des enregistrements

Utilisez [FTPS](../deploy-ftp.md) pour télécharger votre fichier JFR sur votre ordinateur local. Pour analyser le fichier JFR, téléchargez et installez [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Pour obtenir des instructions sur Zulu Mission Control, consultez la [documentation d’Azul](https://docs.azul.com/zmc/) et les [instructions d’installation](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Personnalisation et réglage

Azure App Service pour Linux prend en charge le réglage et la personnalisation prêts à l’emploi par le biais du portail Azure et de l’interface CLI. Consultez les articles suivants pour configurer des applications web spécifiques non-Java :

- [Configurer les paramètres d’application](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Configurer un nom de domaine personnalisé](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurer des liaisons SSL](../configure-ssl-bindings.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Ajouter un CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurer le site Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Définir les options de runtime Java

Pour définir la mémoire allouée ou d’autres options de runtime JVM dans les environnements Tomcat et Java SE, créez un [paramètre d’application](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) nommé `JAVA_OPTS` avec les options. App Service Linux transmet ce paramètre comme variable d’environnement au runtime Java quand celui-ci démarre.

Dans le portail Azure, sous **Paramètres d’application** de l’application web, créez un paramètre d’application appelé `JAVA_OPTS` qui contient les paramètres supplémentaires tels que `-Xms512m -Xmx1204m`.

Pour configurer le paramètre d’application à partir du plug-in Maven, ajoutez des étiquettes paramètre/valeur dans la section du plug-in Azure. L’exemple suivant définit une taille de segment de mémoire Java minimale et maximale spécifique :

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Les développeurs exécutant une seule application avec un seul emplacement de déploiement dans leur plan App Service peuvent utiliser les options suivantes :

- Instances B1 et S1 : `-Xms1024m -Xmx1024m`
- Instances B2 et S2 : `-Xms3072m -Xmx3072m`
- Instances B3 et S3 : `-Xms6144m -Xmx6144m`

Lors du réglage des paramètres de segment de mémoire de l’application, consultez les détails de votre plan App Service et prenez en compte qu’avec plusieurs applications et emplacements de déploiement, vous devez trouver l’allocation de mémoire optimale.

Si vous déployez une application JAR, elle doit être nommée *app.jar* pour que l’image intégrée puisse identifier correctement votre application. (Le plug-in Maven la renomme automatiquement.) Si vous ne souhaitez pas renommer votre JAR en *app.jar*, vous pouvez charger un script d’interpréteur de commandes avec la commande pour exécuter votre JAR. Collez ensuite le chemin d’accès complet à ce script dans la zone de texte [Fichier de démarrage](app-service-linux-faq.md#built-in-images), dans la section Configuration du portail. Le script de démarrage ne s’exécute pas dans le répertoire dans lequel il est placé. Par conséquent, utilisez toujours des chemins d’accès absolus pour référencer les fichiers dans votre script de démarrage (par exemple : `java -jar /home/myapp/myapp.jar`).

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

Si votre application Java est particulièrement volumineuse, vous devez augmenter la limite de durée de démarrage. Pour ce faire, créez un paramètre d’application, `WEBSITES_CONTAINER_START_TIME_LIMIT`, et définissez-le sur le nombre de secondes d’attente d’App Service avant d’arriver à expiration. La valeur maximale est `1800` secondes.

### <a name="pre-compile-jsp-files"></a>Précompiler les fichiers JSP

Pour améliorer les performances des applications Tomcat, vous pouvez compiler vos fichiers JSP avant le déploiement sur App Service. Vous pouvez utiliser le [plug-in Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fourni par Apache Sling, ou ce [fichier de build Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Sécuriser les applications

Les applications Java exécutées dans App Service pour Linux présentent les mêmes [bonnes pratiques de sécurité](/azure/security/security-paas-applications-using-app-services) que les autres applications.

### <a name="authenticate-users-easy-auth"></a>Authentifier les utilisateurs (authentification facile)

Configurez l’authentification de l’application dans le portail Azure avec l’option **Authentification et autorisation**. À partir de là, vous pouvez activer l’authentification en utilisant Azure Active Directory ou des identifiants de réseaux sociaux tels que Facebook, Google ou GitHub. La configuration du portail Azure fonctionne seulement si vous configurez un seul fournisseur d’authentification. Pour plus d’informations, consultez [Configurer votre application App Service pour utiliser une connexion Azure Active Directory](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) et les articles connexes sur d’autres fournisseurs d’identités. Si vous devez activer plusieurs fournisseurs de connexion, suivez les instructions de l’article [Personnaliser l’authentification App Service](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

#### <a name="tomcat-and-wildfly"></a>Tomcat et Wildfly

Votre application Tomcat ou Wildfly peut accéder directement aux revendications de l’utilisateur à partir du servlet en castant l’objet Principal en objet Map. L’objet Map mappe chaque type de revendication avec une collection des revendications de ce type. Dans le code ci-dessous, `request` est une instance de `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Vous pouvez maintenant inspecter l’objet `Map` pour une revendication spécifique. Par exemple, l’extrait de code suivant effectue une itération dans tous les types de revendications et imprime le contenu de chaque collection.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Pour déconnecter les utilisateurs, utilisez le chemin `/.auth/ext/logout`. Pour effectuer d’autres actions, consultez la documentation sur l’[authentification et les autorisations App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Il existe également une documentation officielle sur l’[interface HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) de Tomcat et ses méthodes. Les méthodes servlet suivantes sont également alimentées en fonction de la configuration de votre App Service :

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Pour désactiver cette fonctionnalité, créez un paramètre d’application nommé `WEBSITE_AUTH_SKIP_PRINCIPAL` avec la valeur `1`. Pour désactiver tous les filtres de servlet ajoutés par App Service, créez un paramètre nommé `WEBSITE_SKIP_FILTERS` avec la valeur `1`.

#### <a name="spring-boot"></a>Spring Boot

Les développeurs Spring Boot peuvent utiliser le [démarreur Spring Boot Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) pour sécuriser les applications à l’aide d’API et d’annotations Spring Security familières. Veillez à augmenter la taille d’en-tête maximale dans votre fichier *application.properties*. Nous vous suggérons une valeur de `16384`.

### <a name="configure-tlsssl"></a>Configurer TLS/SSL

Suivez les instructions dans [Sécuriser un nom DNS personnalisé avec une liaison SSL dans Azure App Service](../configure-ssl-bindings.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) pour télécharger un certificat SSL existant et le lier au nom de domaine de votre application. Par défaut, votre application autorisera toujours les connexions HTTP. Suivez les étapes spécifiques du tutoriel pour appliquer SSL et TLS.

### <a name="use-keyvault-references"></a>Utiliser des références KeyVault

[Azure Key Vault](../../key-vault/key-vault-overview.md) fournit une gestion des secrets centralisée avec des stratégies d’accès et un historique d’audit. Vous pouvez stocker des secrets (tels que des mots de passe ou chaînes de connexion) dans KeyVault et accéder à ces secrets dans votre application via des variables d’environnement.

Tout d’abord, suivez les instructions de [Autoriser votre application à accéder à Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) et de [création d’une référence KeyVault à votre secret dans un paramètre d’application](../app-service-key-vault-references.md#reference-syntax). Vous pouvez vérifier la résolution de la référence en secret en imprimant la variable d’environnement tout en accédant à distance au terminal App Service.

Pour injecter ces secrets dans votre fichier de configuration Spring ou Tomcat, utilisez la syntaxe d’injection de variable d’environnement (`${MY_ENV_VAR}`). Pour les fichiers de configuration Spring, consultez cette documentation sur les [configurations externalisées](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Utilisation du Java Key Store

Par défaut, tous les certificats publics ou privés [chargés sur App Service Linux](../configure-ssl-certificate.md) seront chargés dans le Java Key Store au démarrage du conteneur. Cela signifie que vos certificats chargés seront disponibles dans le contexte de connexion lors de l'établissement de connexions TLS sortantes. Après avoir chargé votre certificat, vous devrez redémarrer votre App Service afin de le charger dans le Java Key Store.

Vous pouvez interagir ou déboguer l'outil Java Key Tool en [ouvrant une connexion SSH](app-service-linux-ssh-support.md) sur votre App Service et en exécutant la commande `keytool`. Consultez la [documentation de Key Tool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) pour obtenir une liste des commandes. Les certificats sont stockés dans l'emplacement par défaut du magasin de clés Java, `$JAVA_HOME/jre/lib/security/cacerts`.

Une configuration supplémentaire peut être nécessaire pour chiffrer votre connexion JDBC. Reportez-vous à la documentation du pilote JDBC que vous avez choisi.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)

#### <a name="manually-initialize-and-load-the-key-store"></a>Initialiser et charger manuellement le magasin de clés

Vous pouvez manuellement initialiser le magasin de clés et ajouter des certificats. Créez un paramètre d'application, `SKIP_JAVA_KEYSTORE_LOAD`, avec une valeur de `1` pour désactiver le chargement automatique des certificats dans le magasin de clés. Tous les certificats publics chargés sur App Service via le portail Azure sont stockés sous `/var/ssl/certs/`. Les certificats privés sont stockés sous `/var/ssl/private/`.

Pour plus d'informations sur l'API KeyStore, reportez-vous à la [documentation officielle](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Configurer des plateformes d’APM

Cette section explique comment connecter des applications Java déployées sur Azure App Service sur Linux avec les plateformes de supervision des performances d’application (APM) NewRelic et AppDynamics.

### <a name="configure-new-relic"></a>Configurer New Relic

1. Créez un compte NewRelic sur [NewRelic.com](https://newrelic.com/signup)
2. Téléchargez l’agent Java à partir de NewRelic, son nom doit ressembler à *newrelic-java-x.x.x.zip*.
3. Copiez votre clé de licence, vous en aurez besoin pour configurer l’agent par la suite.
4. [Connectez-vous avec SSH à votre instance App Service](app-service-linux-ssh-support.md) et créez un répertoire */home/site/wwwroot/apm*.
5. Chargez les fichiers de l’agent Java de NewRelic décompressés dans un répertoire sous */home/site/wwwroot/apm*. Les fichiers de votre agent doivent se trouver dans */home/site/wwwroot/apm/newrelic*.
6. Modifiez le fichier YAML dans */home/site/wwwroot/apm/newrelic/newrelic.yml* et remplacez la valeur de la licence d’espace réservé par votre propre clé de licence.
7. Dans le portail Azure, accédez à votre application dans App Service et créez un paramètre d’application.
    - Si votre application utilise **Java SE**, créez une variable d’environnement nommée `JAVA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Si vous utilisez **Tomcat**, créez une variable d’environnement nommée `CATALINA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Si vous utilisez **WildFly**, consultez la documentation de New Relic [ici](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) pour obtenir des conseils sur l’installation de l’agent Java et la configuration de JBoss.

### <a name="configure-appdynamics"></a>Configurer AppDynamics

1. Créez un compte AppDynamics sur [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Téléchargez l’agent Java à partir du site web AppDynamics, le nom de fichier ressemble à *AppServerAgent-x.x.x.xxxxx.zip*
3. [Connectez-vous avec SSH à votre instance App Service](app-service-linux-ssh-support.md) et créez un répertoire */home/site/wwwroot/apm*.
4. Chargez les fichiers de l’agent Java dans un répertoire sous */home/site/wwwroot/apm*. Les fichiers de votre agent doivent se trouver dans */home/site/wwwroot/apm/appdynamics*.
5. Dans le portail Azure, accédez à votre application dans App Service et créez un paramètre d’application.
    - Si vous utilisez **Java SE**, créez une variable d’environnement nommée `JAVA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` où `<app-name>` est votre nom App Service.
    - Si vous utilisez **Tomcat**, créez une variable d’environnement nommée `CATALINA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` où `<app-name>` est votre nom App Service.
    - Si vous utilisez **WildFly**, consultez la documentation d’AppDynamics [ici](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) pour obtenir des conseils sur l’installation de l’agent Java et la configuration de JBoss.

>  Si vous avez déjà une variable d’environnement pour `JAVA_OPTS` ou `CATALINA_OPTS`, ajoutez l’option `-javaagent:/...` à la fin de la valeur actuelle.

## <a name="configure-jar-applications"></a>Configurer des applications JAR

### <a name="starting-jar-apps"></a>Démarrage d’applications JAR

Par défaut, App Service suppose que votre application JAR se nomme *app.jar*. Si elle porte ce nom, elle est exécutée automatiquement. Pour les utilisateurs de Maven, vous pouvez définir le nom JAR en incluant `<finalName>app</finalName>` dans la section `<build>` de votre fichier *pom.xml*. [Vous pouvez faire de même dans Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) en définissant la propriété `archiveFileName`.

Si vous souhaitez utiliser un autre nom pour votre JAR, vous devez également fournir la [commande de démarrage](app-service-linux-faq.md#built-in-images) qui exécute votre fichier JAR. Par exemple : `java -jar my-jar-app.jar`. Vous pouvez définir la valeur de votre commande de démarrage dans le portail, sous Configuration > Paramètres généraux, ou avec un paramètre d’application nommé `STARTUP_COMMAND`.

### <a name="server-port"></a>Port du serveur

App Service Linux achemine les requêtes entrantes vers le port 80, votre application doit donc également écouter sur le port 80. Vous pouvez le faire dans la configuration de votre application (par exemple, le fichier *application.properties* de Spring), ou dans votre commande de démarrage (par exemple, `java -jar spring-app.jar --server.port=80`). Consultez la documentation suivante pour les infrastructures Java courantes :

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

Pour configurer Tomcat afin d’utiliser Java Database Connectivity (JDBC) ou l’API Java Persistence (JPA), commencez par personnaliser la variable d’environnement `CATALINA_OPTS` lue par Tomcat au démarrage. Définissez ces valeurs via un paramètre d’application dans le [plug-in Maven App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) :

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou définissez les variables d’environnement dans la page **Configuration** > **Paramètres d’application** du portail Azure.

Ensuite, déterminez si la source de données doit être mise à la disposition d’une seule application ou de toutes les applications exécutées sur le servlet Tomcat.

#### <a name="application-level-data-sources"></a>Sources de données au niveau de l’application

1. Créez un fichier *context.xml* dans le répertoire *META-INF /* de votre projet. Créez le répertoire *META-INF/* s’il n’existe pas.

2. Dans *context.xml*, ajoutez un élément `Context` pour lier la source de données à une adresse JNDI. Remplacez l’espace réservé `driverClassName` par le nom de la classe de votre pilote à l’aide du tableau ci-dessus.

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

3. Mettez à jour le fichier *web.xml* de votre application pour utiliser la source de données dans votre application.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Ressources au niveau du serveur partagées

Pour ajouter une source de données partagée au niveau du serveur, vous devez modifier le fichier server.xml de Tomcat. Tout d’abord, téléchargez un [script de démarrage](app-service-linux-faq.md#built-in-images) et définissez le chemin d’accès au script dans **Configuration** > **Commande de démarrage**. Vous pouvez télécharger le script de démarrage à l’aide de [FTP](../deploy-ftp.md).

Votre script de démarrage crée une [transformation xsl](https://www.w3schools.com/xml/xsl_intro.asp) sur le fichier server.xml et génère le fichier xml résultant sur `/usr/local/tomcat/conf/server.xml`. Le script de démarrage doit installer libxslt via apk. Votre fichier xsl et votre script de démarrage peuvent être téléchargés via FTP. Vous trouverez ci-dessous un exemple de script de démarrage.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /usr/local/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /home/tomcat/conf/server.xml
```

Un exemple de fichier xsl est fourni ci-dessous. L’exemple de fichier xsl ajoute un nouveau nœud de connecteur au fichier server.xml Tomcat.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Finaliser la configuration

Enfin, placez les fichiers du pilote JAR dans le classpath Tomcat puis redémarrez votre App Service.

1. Veillez à ce que les fichiers du pilote JDBC soient disponibles pour le chargeur de classes Tomcat en les mettant dans le répertoire */home/tomcat/lib*. (S’il n’existe pas déjà, créez ce répertoire.) Pour charger ces fichiers sur votre instance App Service, procédez comme suit :

    1. Dans le [Cloud Shell](https://shell.azure.com), installez l’extension d’application web :

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Exécutez la commande CLI suivante pour créer un tunnel SSH de votre système local vers App Service :

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Connectez-vous au port de tunneling local avec votre client SFTP et chargez les fichiers dans le dossier */home/tomcat/lib*.

    Vous pouvez également utiliser un client FTP pour charger le pilote JDBC. Suivez ces [instructions pour obtenir vos informations d’identification FTP](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Si vous avez créé une source de données de niveau serveur, redémarrez l’application App Service Linux. Tomcat rétablit `CATALINA_BASE` sur `/home/tomcat` et utilise la configuration mise à jour.

### <a name="spring-boot"></a>Spring Boot

Pour vous connecter à des sources de données dans des applications Spring Boot, nous vous suggérons de créer des chaînes de connexion et de les injecter dans votre fichier *application.properties*.

1. Dans la section « Configuration » de la page App Service, définissez un nom pour la chaîne, collez votre chaîne de connexion JDBC dans le champ de valeur, puis définissez le type sur « Personnalisé ». Vous pouvez éventuellement définir cette chaîne de connexion en tant que paramètre d’emplacement.

    Cette chaîne de connexion est accessible à notre application en tant que variable d’environnement nommée `CUSTOMCONNSTR_<your-string-name>`. Par exemple, la chaîne de connexion que nous avons créée ci-dessus sera nommée `CUSTOMCONNSTR_exampledb`.

2. Dans votre fichier *application.properties*, référencez cette chaîne de connexion avec le nom de variable d’environnement. Dans notre exemple, nous utiliserions ce qui suit.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Consultez la [documentation Spring Boot sur l’accès aux données](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) et les [configurations externalisées](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) pour plus d’informations à ce sujet.

## <a name="configure-java-ee-wildfly"></a>Configurer Java EE (WildFly)

> [!NOTE]
> Java Enterprise Edition sur App Service Linux est actuellement en préversion. Cette pile n’est **pas** recommandée pour une utilisation en production.

Azure App Service sur Linux permet aux développeurs Java de générer, déployer et mettre à l’échelle des applications Java EE (Java Enterprise) sur un service Linux entièrement managé.  L’environnement d’exécution Java Enterprise sous-jacent est le serveur d’applications open source [WildFly](https://wildfly.org/).

Cette section comporte les sous-sections suivantes :

- [Mettre à l’échelle avec App Service](#scale-with-app-service)
- [Personnaliser la configuration du serveur d’applications](#customize-application-server-configuration)
- [Installer des modules et dépendances](#install-modules-and-dependencies)
- [Configurer des sources de données](#configure-data-sources)
- [Activer des fournisseurs de messagerie](#enable-messaging-providers)

### <a name="scale-with-app-service"></a>Mettre à l’échelle avec App Service

Le serveur d’applications WildFly s’exécutant dans App Service sur Linux s’exécute en mode autonome, et non pas dans une configuration de domaine. Quand vous effectuez un scale-out du plan App Service, chaque instance WildFly est configurée comme un serveur autonome.

Mettez à l’échelle votre application verticalement ou horizontalement, avec des [règles de mise à l’échelle](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) et en [augmentant le nombre de vos instances](../manage-scale-up.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Personnaliser la configuration du serveur d’applications

Les instances d’application web sont sans état, chaque nouvelle instance démarrée doit donc être configurée au démarrage pour prendre en charge la configuration WildFly nécessaire par application.
Vous pouvez écrire un script Bash de démarrage pour appeler l’interface CLI WildFly afin de :

- Configurer les sources de données
- Configurer les fournisseurs de messagerie
- Ajouter d’autres modules et dépendances à la configuration de serveur WildFly.

Le script s’exécute une fois que WildFly est opérationnel, mais avant le démarrage de l’application. Le script doit utiliser [l’interface CLI JBOSS](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) appelée depuis */opt/jboss/wildfly/bin/jboss-cli.sh* pour configurer le serveur d’applications avec une certaine configuration ou avec les modifications nécessaires après le démarrage du serveur.

N’utilisez pas le mode interactif de l’interface CLI pour configurer WildFly. Au lieu de cela, vous pouvez fournir un script de commandes à l’interface CLI JBoss avec la commande `--file`, par exemple :

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Utilisez FTP pour charger le script de démarrage à un emplacement de votre instance App Service sous votre répertoire */home*, par exemple, */home/site/deployments/tools*. Pour plus d’informations, consultez [Déploiement de votre application dans Azure App Service](https://docs.microsoft.com/azure/app-service/deploy-ftp).

Définissez le champ **Script de démarrage** dans le portail Azure sur l’emplacement de votre script shell de démarrage, par exemple */home/site/deployments/tools/your-startup-script.sh*.

Fournissez les [paramètres d’application](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) dans la configuration d’application pour passer les variables d’environnement à utiliser dans le script. Les paramètres d’application conservent les chaînes de connexion et les autres secrets nécessaires pour configurer votre application en dehors de la gestion de version.

### <a name="install-modules-and-dependencies"></a>Installer des modules et dépendances

Pour installer des modules et leurs dépendances dans le classpath WildFly via l’interface CLI JBoss, vous devez créer les fichiers suivants dans leur propre répertoire. Certains modules et certaines dépendances peuvent nécessiter une configuration supplémentaire, comme le nommage JNDI ou d’autres configurations spécifiques à l’API : cette liste est donc un ensemble minimal de ce vous devez configurer dans la plupart des cas pour une dépendance.

- [Descripteur de module XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Ce fichier XML définit le nom, les attributs et les dépendances de votre module. Cet [exemple de fichier module.xml](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) définit un module Postgres, sa dépendance JDBC du fichier JAR et d’autres dépendances nécessaires du module.
- Dépendances de fichier JAR nécessaires pour votre module.
- Script avec vos commandes d’interface CLI JBoss pour configurer le nouveau module. Ce fichier contient vos commandes à exécuter par l’interface CLI JBoss pour configurer le serveur de façon à ce qu’il utilise la dépendance. Pour une documentation sur les commandes pour ajouter des modules, des sources de données et des fournisseurs de messagerie, reportez-vous à [ce document](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Script de démarrage Bash pour appeler l’interface CLI JBoss et exécuter le script de l’étape précédente. Ce fichier est exécuté quand votre instance App Service est redémarrée ou quand de nouvelles instances sont provisionnées lors d’un scale-out. C’est dans ce script de démarrage que vous pouvez effectuer les autres configurations pour votre application, car les commandes JBoss sont passées à l’interface CLI JBoss. Au minimum, ce fichier peut être une commande unique pour passer votre script de commandes d’interface CLI JBoss à l’interface CLI JBoss :

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Une fois que vous avez les fichiers et le contenu pour votre module, suivez les étapes ci-dessous pour ajouter le module au serveur d’applications WildFly.

1. Utilisez FTP pour charger vos fichiers à un emplacement de votre instance App Service sous votre répertoire */home*, par exemple, */home/site/deployments/tools*. Pour plus d’informations, consultez [Déploiement de votre application dans Azure App Service](../deploy-ftp.md).
2. Dans la page **Configuration** > **Paramètres généraux** du portail Azure, définissez le champ **Script de démarrage** sur l’emplacement de votre script shell de démarrage, par exemple */home/site/deployments/tools/startup.sh*.
3. Redémarrez votre instance App Service en cliquant sur le bouton **Redémarrer** situé dans la section **Vue d’ensemble** du portail ou avec l’interface de ligne de commande Azure.

### <a name="configure-data-sources"></a>Configurer des sources de données

Pour configurer l’accès à une source de données de WildFly/JBoss, utilisez le processus général ci-dessus dans la section « Installer des modules et dépendances ». La section suivante fournit des détails spécifiques sur ce processus pour les sources de données PostgreSQL, MySQL et SQL Server.

Cette section suppose que vous disposez déjà d’une application, d’une instance d’App Service et d’une instance de service de base de données Azure. Les instructions ci-dessous font référence au nom de votre App Service, son groupe de ressources et vos informations de connexion de base de données. Ces informations sont disponibles sur le portail Azure.

Si vous préférez suivre l’intégralité du processus depuis le début à l’aide d’un exemple d’application, consultez [Didacticiel : Créer une application web Java EE et Postgres dans Azure](tutorial-java-enterprise-postgresql-app.md).

Les étapes suivantes expliquent les exigences de connexion de votre App Service et base de données existants.

1. Téléchargez le pilote JDBC pour [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/) ou [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). Décompressez l’archive téléchargée pour obtenir le fichier .jar de pilote.

2. Créez un fichier avec un nom tel que *module.xml* et ajoutez le balisage suivant. Remplacez l’espace réservé `<module name>` (y compris les accolades) par `org.postgres` pour PostgreSQL, `com.mysql` pour MySQL ou `com.microsoft` pour SQL Server. Remplacez `<JDBC .jar file path>` par le nom du fichier .jar de l’étape précédente, y compris le chemin d’accès complet à l’emplacement où le fichier sera placé dans votre instance d’App Service. Il peut s’agir de n’importe quel emplacement sous le répertoire */home*.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. Créez un fichier avec un nom tel que *datasource-commands.cli* et ajoutez le code suivant. Remplacez `<JDBC .jar file path>` par la valeur que vous avez utilisée à l’étape précédente. Remplacez `<module file path>` par le nom de fichier et le chemin d’accès d’App Service de l’étape précédente, par exemple */home/module.xml*.

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    Ce fichier est exécuté par le script de démarrage décrit à l’étape suivante. Il installe le pilote JDBC en tant que module WildFly, crée la source de données WildFly correspondante et recharge le serveur pour s’assurer que les modifications seront appliquées.

4. Créez un fichier avec un nom tel que *startup.sh* et ajoutez le code suivant. Remplacez `<JBoss CLI script>` par le nom du fichier que vous avez créé à l’étape précédente. Veillez à inclure le chemin d’accès complet à l’emplacement où le fichier sera placé dans votre instance d’App Service, par exemple */home/datasource-commands.cli*.

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. Utilisez FTP pour charger le fichier .jar JDBC, le fichier XML de module, le script de CLI JBoss et le script de démarrage sur votre instance d’App Service. Placez ces fichiers dans l’emplacement spécifié dans les étapes précédentes, tel que */home*. Pour plus d’informations sur FTP, consultez [Déployer votre application dans Azure App Service avec FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Utilisez Azure CLI pour ajouter des paramètres à votre App Service qui contiennent vos informations de connexion de base de données. Remplacez `<resource group>` et `<webapp name>` par les valeurs utilisées par votre App Service. Remplacez `<database server name>`, `<database name>`, `<admin name>` et `<admin password>` par vos informations de connexion de base de données. Vous pouvez obtenir vos informations d’App Service et de base de données sur le portail Azure.

    **PostgreSQL :**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL :**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server :**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    Les valeurs DATABASE_CONNECTION_URL sont différentes pour chaque serveur de base de données, ainsi que des valeurs sur le portail Azure. Les formats d’URL indiqués ici (et dans les extraits de code ci-dessus) sont requis pour une utilisation par WildFly :

    * **PostgreSQL :** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL :** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server :** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. Dans le portail Azure, accédez à votre App Service et recherchez la page **Configuration** > **Paramètres généraux**. Définissez le champ **Script de démarrage** sur le nom et l’emplacement de votre script de démarrage, par exemple */home/startup.sh*.

Lorsque votre App Service redémarre, il exécute le script de démarrage et effectue les étapes de configuration nécessaires. Pour vérifier que cette configuration s’effectue correctement, vous pouvez accéder à votre App Service à l’aide de SSH et exécuter vous-même le script de démarrage à partir de l’invite Bash. Vous pouvez également examiner les journaux d’activité App Service. Pour plus d’informations sur ces options, consultez [Journalisation et débogage des applications](#logging-and-debugging-apps).

Vous devez ensuite mettre à jour la configuration WildFly pour votre application et la redéployer. Procédez comme suit :

1. Ouvrez le fichier *src/main/resources/META-INF/persistence.xml* de votre application et recherchez l’élément `<jta-data-source>`. Remplacez son contenu comme indiqué ici :

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. Régénérez et redéployez votre application à l’aide de la commande suivante à l’invite de commandes Bash :

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Redémarrez votre instance App Service en cliquant sur le bouton **Redémarrer** situé dans la section **Vue d’ensemble** du portail Azure ou à l’aide de l’interface de ligne de commande Azure.

Votre instance d’App Service est maintenant configurée pour accéder à votre base de données.

Pour plus d’informations sur la configuration de la connectivité de base de données avec WildFly, consultez [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) ou [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

### <a name="enable-messaging-providers"></a>Activer des fournisseurs de messagerie

Pour activer des beans pilotés par les messages avec Service Bus comme mécanisme de messagerie :

1. Utilisez la [bibliothèque de messagerie Apache QPId JMS](https://qpid.apache.org/proton/index.html). Incluez cette dépendance dans votre fichier pom.xml (ou dans un autre fichier de build) pour l’application.

2. Créez des [ressources Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Créez un espace de noms Azure Service Bus et une file d’attente dans cet espace de noms ainsi qu’une stratégie d’accès partagé avec des fonctionnalités d’envoi et de réception.

3. Passez la clé de la stratégie d’accès partagé à votre code, en encodant la clé principale de votre stratégie dans une URL ou en [utilisant le SDK Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Suivez les étapes décrites dans la section Installation de modules et de dépendances avec le descripteur XML de votre module, les dépendances .jar, les commandes CLI JBoss et le script de démarrage pour le fournisseur JMS. En plus des quatre fichiers, vous devez aussi créer un fichier XML qui définit le nom JNDI pour la file d’attente et la rubrique JMS. Pour les fichiers de configuration de référence, consultez [ce dépôt](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig).

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Utiliser Redis comme cache de session avec Tomcat

Vous pouvez configurer Tomcat de façon à utiliser un magasin de sessions externe comme le [Cache Azure pour Redis](/azure/azure-cache-for-redis/). Cela vous permet de conserver l’état de la session utilisateur (par exemple, les données du panier des achats) lorsqu’un utilisateur est transféré vers une autre instance de votre application, par exemple lorsqu’une mise à l’échelle automatique, un redémarrage ou un basculement se produisent.

Pour utiliser Tomcat avec Redis, vous devez configurer votre application pour qu’elle utilise une implémentation de [PersistentManager](http://tomcat.apache.org/tomcat-8.5-doc/config/manager.html). Les étapes suivantes expliquent ce processus à l’aide de [Pivotal Session Manager : redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) en guise d’exemple.

1. Ouvrez un terminal Bash et utilisez `export <variable>=<value>` pour définir chacune des variables d’environnement suivantes.

    | Variable                 | Valeur                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Nom du groupe de ressources contenant votre instance App Service.       |
    | WEBAPP_NAME              | Nom de votre service instance App Service.                                     |
    | WEBAPP_PLAN_NAME         | Nom de votre service plan App Service.                                          |
    | RÉGION                   | Nom de la région dans laquelle votre application est hébergée.                           |
    | REDIS_CACHE_NAME         | Nom de votre instance Cache Azure pour Redis.                           |
    | REDIS_PORT               | Port SSL sur lequel votre cache Redis écoute.                             |
    | REDIS_PASSWORD           | Clé d’accès primaire de votre instance.                                  |
    | REDIS_SESSION_KEY_PREFIX | Valeur que vous spécifiez pour identifier les clés de session provenant de votre application. |

    Vous pouvez trouver le nom, le port et les informations de clé d’accès sur le portail Azure en consultant les sections **Propriétés** ou **Clés d’accès** de votre instance de service.

2. Créez ou mettez à jour le fichier *src/main/webapp/META-INF/Context.xml* de votre application avec le contenu suivant :

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Ce fichier spécifie et configure l’implémentation du gestionnaire de sessions pour votre application. Il utilise les variables d’environnement que vous avez définies lors de l’étape précédente pour conserver les informations de votre compte dans vos fichiers sources.

3. Utilisez le FTP pour télécharger le fichier JAR du gestionnaire de session sur votre instance App Service, en le plaçant dans le répertoire */home/tomcat/lib*. Pour plus d’informations, consultez [Déploiement de votre application dans Azure App Service](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Désactivez le [cookie d’affinité de session](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) pour votre instance App Service. Vous pouvez le faire à partir du portail Azure en accédant à votre application, puis en définissant **Configuration > Paramètres généraux > Affinité ARR** sur **Désactivé**. Vous pouvez aussi utiliser la commande suivante :

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Par défaut, App Service utilise des cookies d’affinité de session pour garantir que les demandes des clients avec des sessions existantes soient acheminées vers la même instance de votre application. Ce comportement par défaut ne nécessite aucune configuration, mais il ne peut pas conserver l’état de session utilisateur lorsque votre instance d’application est redémarrée ou lorsque le trafic est redirigé vers une autre instance. Lorsque vous désactivez la [configuration de l’affinité d’instance ARR existante](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) pour arrêter le routage de session basé sur les cookies, vous autorisez le magasin de sessions configuré pour un fonctionnement sans interférence.

5. Accédez à la section **Propriétés** de votre instance App Service et recherchez **Autres adresses IP sortantes**. Celles-ci représentent toutes les adresses IP sortantes possibles pour votre application. Copiez-les pour les utiliser à l’étape suivante.

6. Pour chaque adresse IP, créez une règle de pare-feu dans votre instance Cache Azure pour Redis. Vous pouvez le faire sur le portail Azure, dans la section **Pare-feu** de votre instance Redis. Fournissez un nom unique pour chaque règle et définissez les valeurs **Adresse IP de début** et **Adresse IP de fin** sur la même adresse IP.

7. Accédez à la section **Paramètres avancés** de votre instance Redis et définissez **Autoriser l’accès uniquement via SSL** sur **Non**. Cela permet à votre instance App Service de communiquer avec votre cache Redis via l’infrastructure Azure.

8. Mettez à jour la configuration `azure-webapp-maven-plugin` dans le fichier *pom.xml* de votre application pour faire référence à vos informations de compte Redis. Ce fichier utilise les variables d’environnement que vous avez définies précédemment pour conserver les informations de votre compte dans vos fichiers sources.

    Si nécessaire, replacez `1.7.0` par la version actuelle du [plug-in Maven pour Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Procédez maintenant à la régénération et au redéploiement de votre application.

    ```bash
    mvn package
    mvn azure-webapp:deploy
    ```

Votre application utilisera désormais votre cache Redis pour la gestion des sessions.

Pour obtenir un exemple que vous pouvez utiliser pour tester ces instructions, consultez le référentiel [scaling-stateful-java-web-app-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) sur GitHub.

## <a name="docker-containers"></a>Conteneurs Docker

Pour utiliser le kit JDK Zulu pris en charge par Azure dans vos conteneurs, assurez-vous d’extraire et d’utiliser les images précompilées documentées dans la [page de téléchargement Azul Zulu Enterprise for Azure](https://www.azul.com/downloads/azure-only/zulu/), ou utilisez les exemples `Dockerfile` du [référentiel Microsoft Java GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Instruction de prise en charge

### <a name="runtime-availability"></a>Disponibilité du runtime

App Service pour Linux prend en charge deux runtimes pour l’hébergement managé des application web Java :

- Le [conteneur servlet Tomcat](https://tomcat.apache.org/) pour exécuter les applications empaquetées sous forme de fichiers WAR (web archive). Les versions prises en charge sont les versions 8.5 et 9.0.
- L’environnement de runtime Java SE pour exécuter les applications empaquetées sous forme de fichiers JAR (Java archive). Les versions prises en charge sont Java 8 et 11.

### <a name="jdk-versions-and-maintenance"></a>Versions JDK et maintenance

Les builds Azul Zulu Enterprise d’OpenJDK sont une distribution gratuite, multiplateforme et prête pour la production d’OpenJDK pour Azure et Azure Stack pris en charge par Microsoft et Azul Systems. Elles contiennent tous les composants nécessaires pour générer et exécuter des applications Java SE. Vous pouvez installer le JDK à partir de l’[installation du JDK Java](https://aka.ms/azure-jdks).

Les kits JDK pris en charge sont automatiquement mis à jour tous les trimestres, en janvier, avril, juillet et octobre de chaque année.

### <a name="security-updates"></a>Mises à jour de sécurité

Les correctifs des principales vulnérabilités de sécurité seront publiés dès qu’Azul Systems les mettra à disposition. Une vulnérabilité « majeure » est définie par un score de base de 9.0 ou supérieur dans le système [NIST Common Vulnerability Scoring System, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Dépréciation et retrait

Si un runtime Java pris en charge est retiré, les développeurs Azure utilisant le runtime affecté reçoivent un avis de dépréciation au moins six mois avant son retrait.

## <a name="next-steps"></a>Étapes suivantes

Visitez le centre [Azure pour les développeurs Java](/java/azure/) pour trouver des guides de démarrage rapide Azure, des tutoriels et la documentation de référence Java.

Des questions générales sur l’utilisation d’App Service pour Linux qui ne sont pas spécifiques au développement Java sont traitées dans [Questions fréquentes (FAQ) sur App Service Linux](app-service-linux-faq.md).
