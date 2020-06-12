---
title: Configurer des applications Linux Java
description: Découvrez comment configurer un conteneur Java prédéfini pour votre application. Cet article présente les tâches de configuration les plus courantes.
keywords: azure app service, application web, linux, oss, java, java ee, jee, javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 63fee90be773f61bfef73e21a272192eea5f789c
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84167483"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configurer une application Java Linux pour Azure App Service

Azure App Service sur Linux permet aux développeurs Java de rapidement générer, déployer et mettre à l’échelle leurs applications web empaquetées Tomcat ou Java Standard Edition (SE) sur un service basé sur Linux complètement managé. Déployez des applications avec les plug-ins Maven à partir de la ligne de commande ou dans des éditeurs comme IntelliJ, Eclipse ou Visual Studio Code.

Ce guide fournit des concepts et des instructions clés aux développeurs Java qui utilisent un conteneur Linux intégré dans App Service. Si vous n’avez jamais utilisé Azure App Service, suivez le [guide de démarrage rapide Java](quickstart-java.md).

## <a name="deploying-your-app"></a>Déploiement de votre application

Vous pouvez utiliser le [plug-in Maven pour Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) pour déployer des fichiers .jar et .war. Le déploiement avec des IDE populaires est également pris en charge avec [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) ou [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

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

Activez [Journal des applications](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) via le portail Azure ou [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) pour configurer App Service de sorte à écrire la sortie de console standard de votre application et les flux d’erreur de console standard dans le système de fichiers local ou le service Stockage Blob Azure. La journalisation sur l’instance du système de fichiers App Service locale est désactivée 12 heures après avoir été configurée. Si vous en avez besoin plus longtemps, configurez l’application pour écrire la sortie sur un conteneur de stockage d’objets blob. Vous trouverez vos journaux d’application Java et Tomcat dans le répertoire */home/LogFiles/Application/* .

>[!NOTE]
>La désactivation au bout de 12 heures de la journalisation dans le système de fichiers App Services local s’applique uniquement aux services App Services Windows. La journalisation du Stockage Blob Azure pour les services App Services Linux ne peut être configurée qu’avec [Azure Monitor (préversion)](/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview). 

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

- [Configurer les paramètres d’application](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Configurer un nom de domaine personnalisé](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Configurer des liaisons SSL](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Ajouter un CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Configurer le site Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Définir les options de runtime Java

Pour définir la mémoire allouée ou d’autres options de runtime JVM dans les environnements Tomcat et Java SE, créez un [paramètre d’application](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) nommé `JAVA_OPTS` avec les options. App Service Linux transmet ce paramètre comme variable d’environnement au runtime Java quand celui-ci démarre.

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

Configurez l’authentification de l’application dans le portail Azure avec l’option **Authentification et autorisation**. À partir de là, vous pouvez activer l’authentification en utilisant Azure Active Directory ou des identifiants de réseaux sociaux tels que Facebook, Google ou GitHub. La configuration du portail Azure fonctionne seulement si vous configurez un seul fournisseur d’authentification. Pour plus d’informations, consultez [Configurer votre application App Service pour utiliser une connexion Azure Active Directory](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) et les articles connexes sur d’autres fournisseurs d’identités. Si vous devez activer plusieurs fournisseurs de connexion, suivez les instructions de l’article [Personnaliser l’authentification App Service](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json).

#### <a name="tomcat"></a>Tomcat

Votre application Tomcat peut accéder directement aux revendications de l’utilisateur à partir du servlet en forçant le type de l’objet Principal en objet Map. L’objet Map mappe chaque type de revendication avec une collection des revendications de ce type. Dans le code ci-dessous, `request` est une instance de `HttpServletRequest`.

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

Suivez les instructions dans [Sécuriser un nom DNS personnalisé avec une liaison SSL dans Azure App Service](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) pour télécharger un certificat SSL existant et le lier au nom de domaine de votre application. Par défaut, votre application autorisera toujours les connexions HTTP. Suivez les étapes spécifiques du tutoriel pour appliquer SSL et TLS.

### <a name="use-keyvault-references"></a>Utiliser des références KeyVault

[Azure Key Vault](../../key-vault/general/overview.md) fournit une gestion des secrets centralisée avec des stratégies d’accès et un historique d’audit. Vous pouvez stocker des secrets (tels que des mots de passe ou chaînes de connexion) dans KeyVault et accéder à ces secrets dans votre application via des variables d’environnement.

Tout d’abord, suivez les instructions de [Autoriser votre application à accéder à Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) et de [création d’une référence KeyVault à votre secret dans un paramètre d’application](../app-service-key-vault-references.md#reference-syntax). Vous pouvez vérifier la résolution de la référence en secret en imprimant la variable d’environnement tout en accédant à distance au terminal App Service.

Pour injecter ces secrets dans votre fichier de configuration Spring ou Tomcat, utilisez la syntaxe d’injection de variable d’environnement (`${MY_ENV_VAR}`). Pour les fichiers de configuration Spring, consultez cette documentation sur les [configurations externalisées](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Utilisation du Java Key Store

Par défaut, tous les certificats publics ou privés [chargés sur App Service Linux](../configure-ssl-certificate.md) seront chargés dans les magasins de clés Java respectifs au démarrage du conteneur. Après avoir chargé votre certificat, vous devrez redémarrer votre App Service afin de le charger dans le Java Key Store. Les certificats publics sont chargés dans le magasin de clés sur `$JAVA_HOME/jre/lib/security/cacerts`, et les certificats privés sont stockés dans `$JAVA_HOME/lib/security/client.jks`.

Une configuration supplémentaire peut être nécessaire pour chiffrer votre connexion JDBC avec des certificats dans le magasin de clés Java. Reportez-vous à la documentation du pilote JDBC que vous avez choisi.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Initialisation du magasin de clés Java

Pour initialiser l’objet `import java.security.KeyStore`, chargez le fichier de magasin de clés avec le mot de passe. Le mot de passe par défaut pour les deux magasins de clés est « changeit ».

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Charger manuellement le magasin de clés

Vous pouvez charger les certificats manuellement dans le magasin de clés. Créez un paramètre d'application, `SKIP_JAVA_KEYSTORE_LOAD`, avec une valeur de `1` pour désactiver le chargement automatique des certificats dans le magasin de clés. Tous les certificats publics chargés sur App Service via le portail Azure sont stockés sous `/var/ssl/certs/`. Les certificats privés sont stockés sous `/var/ssl/private/`.

Vous pouvez interagir ou déboguer l'outil Java Key Tool en [ouvrant une connexion SSH](app-service-linux-ssh-support.md) sur votre App Service et en exécutant la commande `keytool`. Consultez la [documentation de Key Tool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) pour obtenir une liste des commandes. Pour plus d'informations sur l'API KeyStore, reportez-vous à la [documentation officielle](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

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

### <a name="configure-appdynamics"></a>Configurer AppDynamics

1. Créez un compte AppDynamics sur [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Téléchargez l’agent Java à partir du site web AppDynamics, le nom de fichier ressemble à *AppServerAgent-x.x.x.xxxxx.zip*
3. [Connectez-vous avec SSH à votre instance App Service](app-service-linux-ssh-support.md) et créez un répertoire */home/site/wwwroot/apm*.
4. Chargez les fichiers de l’agent Java dans un répertoire sous */home/site/wwwroot/apm*. Les fichiers de votre agent doivent se trouver dans */home/site/wwwroot/apm/appdynamics*.
5. Dans le portail Azure, accédez à votre application dans App Service et créez un paramètre d’application.
    - Si vous utilisez **Java SE**, créez une variable d’environnement nommée `JAVA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` où `<app-name>` est votre nom App Service.
    - Si vous utilisez **Tomcat**, créez une variable d’environnement nommée `CATALINA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` où `<app-name>` est votre nom App Service.

> [!NOTE]
> Si vous avez déjà une variable d’environnement pour `JAVA_OPTS` ou `CATALINA_OPTS`, ajoutez l’option `-javaagent:/...` à la fin de la valeur actuelle.

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
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Télécharger](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

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
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
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

    Vous pouvez également utiliser un client FTP pour charger le pilote JDBC. Suivez ces [instructions pour obtenir vos informations d’identification FTP](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

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

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Utiliser Redis comme cache de session avec Tomcat

Vous pouvez configurer Tomcat de façon à utiliser un magasin de sessions externe comme le [Cache Azure pour Redis](/azure/azure-cache-for-redis/). Cela vous permet de conserver l’état de la session utilisateur (par exemple, les données du panier des achats) lorsqu’un utilisateur est transféré vers une autre instance de votre application, par exemple lorsqu’une mise à l’échelle automatique, un redémarrage ou un basculement se produisent.

Pour utiliser Tomcat avec Redis, vous devez configurer votre application pour qu’elle utilise une implémentation de [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html). Les étapes suivantes expliquent ce processus à l’aide de [Pivotal Session Manager : redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) en guise d’exemple.

1. Ouvrez un terminal Bash et utilisez `<variable>=<value>` pour définir chacune des variables d’environnement suivantes.

    | Variable                 | Valeur                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Nom du groupe de ressources contenant votre instance App Service.       |
    | WEBAPP_NAME              | Nom de votre service instance App Service.                                     |
    | WEBAPP_PLAN_NAME         | Nom de votre plan App Service.                                         |
    | RÉGION                   | Nom de la région dans laquelle votre application est hébergée.                           |
    | REDIS_CACHE_NAME         | Nom de votre instance Cache Azure pour Redis.                           |
    | REDIS_PORT               | Port SSL sur lequel votre cache Redis écoute.                             |
    | REDIS_PASSWORD           | Clé d’accès primaire de votre instance.                                  |
    | REDIS_SESSION_KEY_PREFIX | Valeur que vous spécifiez pour identifier les clés de session provenant de votre application. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

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

    Si nécessaire, replacez `1.9.1` par la version actuelle du [plug-in Maven pour Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.1</version>
        <configuration>            
            <!-- Web App information -->
            <schemaVersion>v2</schemaVersion>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>            
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>

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
    mvn package -DskipTests azure-webapp:deploy
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

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Étapes suivantes

Visitez le centre [Azure pour les développeurs Java](/java/azure/) pour trouver des guides de démarrage rapide Azure, des tutoriels et la documentation de référence Java.

Des questions générales sur l’utilisation d’App Service pour Linux qui ne sont pas spécifiques au développement Java sont traitées dans [Questions fréquentes (FAQ) sur App Service Linux](app-service-linux-faq.md).
