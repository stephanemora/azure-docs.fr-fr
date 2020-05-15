---
title: Configurer des applications Windows Java
description: Découvrez comment configurer des applications Java pour les exécuter sur des instances de machine virtuelle Windows dans Azure App Service. Cet article présente les tâches de configuration les plus courantes.
keywords: azure app service, application web, windows, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1e42096e7ab950e5d8046ec6140c01b24643cb87
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891468"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configurer une application Windows Java pour Azure App Service

Azure App Service permet aux développeurs Java de rapidement générer, déployer et mettre à l’échelle leurs applications web Tomcat sur un service basé sur Windows entièrement managé. Déployez des applications avec les plug-ins Maven à partir de la ligne de commande ou dans des éditeurs comme IntelliJ, Eclipse ou Visual Studio Code.

Ce guide fournit les concepts et instructions clés aux développeurs Java qui utilisent App Service. Si vous n’avez jamais utilisé Azure App Service, commencez par lire [Démarrage rapide avec Java](app-service-web-get-started-java.md). Des questions générales sur l’utilisation d’App Service qui ne sont pas spécifiques au développement Java sont traitées dans [Questions fréquentes (FAQ) sur App Service Windows](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Déploiement de votre application

Vous pouvez utiliser le [plug-in Azure Web App pour Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) pour déployer vos fichiers .war. Le déploiement avec des IDE populaires est également pris en charge avec [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) ou [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

Sinon, votre méthode de déploiement dépend du type de votre archive :

- Pour déployer des fichiers .war sur Tomcat, utilisez le point de terminaison `/api/wardeploy/` pour effectuer un POST de votre fichier d’archive. Pour plus d’informations sur cette API, voir [cette documentation](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Pour déployer des fichiers .jar dans Java SE, utilisez le point de terminaison `/api/zipdeploy/` du site Kudu. Pour plus d’informations sur cette API, voir [cette documentation](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Ne déployez pas votre fichier .war à l’aide du protocole FTP. L’outil FTP est conçu pour charger des scripts de démarrage, des dépendances ou d’autres fichiers de runtime. Il ne s’agit pas du meilleur choix pour le déploiement d’applications web.

## <a name="logging-and-debugging-apps"></a>Journalisation et débogage des applications

Vous trouverez des rapports de performances, des visualisations de trafic et des contrôles d’intégrité pour chaque application dans le portail Azure. Pour plus d’informations, consultez [Présentation des diagnostics Azure App Service](overview-diagnostics.md).

### <a name="use-flight-recorder"></a>Utiliser une boîte noire

Tous les runtimes Java sur App Service qui utilisent des JVM Azul sont fournis avec la boîte noire Zulu. Vous pouvez l’utiliser pour enregistrer les événements de niveau JVM, système et Java pour surveiller le comportement de vos applications Java et résoudre les problèmes de ces dernières.

Pour effectuer un enregistrement programmé, vous aurez besoin du PID (ID de processus) de l’application Java. Pour rechercher le PID, ouvrez un navigateur sur le site GCL de votre application web à l’adresse https://<votre-nom-de-site>.scm.azurewebsites.net/ProcessExplorer/. Cette page affiche les processus en cours d’exécution dans votre application web. Recherchez le processus nommé « java » dans le tableau et copiez le PID (ID de processus) correspondant.

Ouvrez ensuite la **Console de débogage** dans la barre d’outils supérieure du site GCL et exécutez la commande suivante. Remplacez `<pid>` par l’ID de processus que vous avez copié précédemment. Cette commande démarre un enregistrement de 30 secondes du profileur de votre application Java et génère un fichier nommé `timed_recording_example.jfr` dans le répertoire `D:\home`.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Pour plus d’informations, consultez les [informations de référence sur la commande Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>Analyser les fichiers `.jfr`

Utilisez [FTPS](deploy-ftp.md) pour télécharger votre fichier JFR sur votre ordinateur local. Pour analyser le fichier JFR, téléchargez et installez [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Pour obtenir des instructions sur Zulu Mission Control, consultez la [documentation d’Azul](https://docs.azul.com/zmc/) et les [instructions d’installation](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="stream-diagnostic-logs"></a>Diffuser les journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Pour plus d’informations, consultez [Diffuser des journaux dans Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Journalisation des applications

Activez [Journal des applications](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) via le portail Azure ou [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) pour configurer App Service de sorte à écrire la sortie de console standard de votre application et les flux d’erreur de console standard dans le système de fichiers local ou le service Stockage Blob Azure. La journalisation sur l’instance du système de fichiers App Service locale est désactivée 12 heures après avoir été configurée. Si vous en avez besoin plus longtemps, configurez l’application pour écrire la sortie sur un conteneur de stockage d’objets blob. Vous trouverez vos journaux d’application Java et Tomcat dans le répertoire */LogFiles/Application/* .

Si votre application utilise [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) pour le traçage, vous pouvez transférer ces traces pour révision vers Azure Application Insights en suivant les instructions de configuration des frameworks de journalisation dans [Exploration des journaux d’activité de traces Java dans Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Personnalisation et réglage

Azure App Service prend en charge le réglage et la personnalisation prêts à l’emploi par le biais du portail Azure et de l’interface CLI. Consultez les articles suivants pour configurer des applications web spécifiques non-Java :

- [Configurer les paramètres d’application](configure-common.md#configure-app-settings)
- [Configurer un nom de domaine personnalisé](app-service-web-tutorial-custom-domain.md)
- [Configurer des liaisons TLS](configure-ssl-bindings.md)
- [Ajouter un CDN](../cdn/cdn-add-to-web-app.md)
- [Configurer le site Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Définir les options de runtime Java

Pour définir la mémoire allouée ou d’autres options de runtime JVM, créez un [paramètre d’application](configure-common.md#configure-app-settings) nommé `JAVA_OPTS` avec les options. App Service transmet ce paramètre comme variable d’environnement au runtime Java quand il démarre.

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

### <a name="pre-compile-jsp-files"></a>Précompiler les fichiers JSP

Pour améliorer les performances des applications Tomcat, vous pouvez compiler vos fichiers JSP avant le déploiement sur App Service. Vous pouvez utiliser le [plug-in Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fourni par Apache Sling, ou ce [fichier de build Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Sécuriser les applications

Les applications Java exécutées dans App Service exigent les mêmes [bonnes pratiques de sécurité](/azure/security/security-paas-applications-using-app-services) que les autres applications.

### <a name="authenticate-users-easy-auth"></a>Authentifier les utilisateurs (authentification facile)

Configurez l’authentification de l’application dans le portail Azure avec l’option **Authentification et autorisation**. À partir de là, vous pouvez activer l’authentification en utilisant Azure Active Directory ou des identifiants de réseaux sociaux tels que Facebook, Google ou GitHub. La configuration du portail Azure fonctionne seulement si vous configurez un seul fournisseur d’authentification. Pour plus d’informations, consultez [Configurer votre application App Service pour utiliser une connexion Azure Active Directory](configure-authentication-provider-aad.md) et les articles connexes sur d’autres fournisseurs d’identités. Si vous devez activer plusieurs fournisseurs de connexion, suivez les instructions de l’article [Personnaliser l’authentification App Service](app-service-authentication-how-to.md).

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

### <a name="configure-tlsssl"></a>Configurer TLS/SSL

Suivez les instructions dans [Sécuriser un nom DNS personnalisé avec une liaison TLS dans Azure App Service](configure-ssl-bindings.md) pour charger un certificat TLS/SSL existant et le lier au nom de domaine de votre application. Par défaut, votre application autorisera toujours les connexions HTTP. Suivez les étapes spécifiques du tutoriel pour appliquer SSL et TLS.

### <a name="use-keyvault-references"></a>Utiliser des références KeyVault

[Azure Key Vault](../key-vault/general/overview.md) fournit une gestion des secrets centralisée avec des stratégies d’accès et un historique d’audit. Vous pouvez stocker des secrets (tels que des mots de passe ou chaînes de connexion) dans KeyVault et accéder à ces secrets dans votre application via des variables d’environnement.

Tout d’abord, suivez les instructions de [Autoriser votre application à accéder à Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) et de [création d’une référence KeyVault à votre secret dans un paramètre d’application](app-service-key-vault-references.md#reference-syntax). Vous pouvez vérifier la résolution de la référence en secret en imprimant la variable d’environnement tout en accédant à distance au terminal App Service.

Pour injecter ces secrets dans votre fichier de configuration Spring ou Tomcat, utilisez la syntaxe d’injection de variable d’environnement (`${MY_ENV_VAR}`). Pour les fichiers de configuration Spring, consultez cette documentation sur les [configurations externalisées](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Configurer des plateformes d’APM

Cette section explique comment connecter des applications Java déployées sur Azure App Service sur Linux avec les plateformes de supervision des performances d’application (APM) NewRelic et AppDynamics.

### <a name="configure-new-relic"></a>Configurer New Relic

1. Créez un compte New Relic sur [NewRelic.com](https://newrelic.com/signup).
2. Téléchargez l’agent Java à partir de NewRelic, son nom doit ressembler à *newrelic-java-x.x.x.zip*.
3. Copiez votre clé de licence, vous en aurez besoin pour configurer l’agent par la suite.
4. Utilisez la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) pour créer un répertoire */home/site/wwwroot/apm*.
5. Chargez les fichiers de l’agent Java de New Relic décompressés dans un répertoire sous */home/site/wwwroot/apm*. Les fichiers de votre agent doivent se trouver dans */home/site/wwwroot/apm/newrelic*.
6. Modifiez le fichier YAML dans */home/site/wwwroot/apm/newrelic/newrelic.yml* et remplacez la valeur de la licence d’espace réservé par votre propre clé de licence.
7. Dans le portail Azure, accédez à votre application dans App Service et créez un paramètre d’application.
    - Si votre application utilise **Java SE**, créez une variable d’environnement nommée `JAVA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Si vous utilisez **Tomcat**, créez une variable d’environnement nommée `CATALINA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configurer AppDynamics

1. Créez un compte AppDynamics sur [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Téléchargez l’agent Java à partir du site web AppDynamics, le nom de fichier ressemble à *AppServerAgent-x.x.x.xxxxx.zip*
3. Utilisez la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) pour créer un répertoire */home/site/wwwroot/apm*.
4. Chargez les fichiers de l’agent Java dans un répertoire sous */home/site/wwwroot/apm*. Les fichiers de votre agent doivent se trouver dans */home/site/wwwroot/apm/appdynamics*.
5. Dans le portail Azure, accédez à votre application dans App Service et créez un paramètre d’application.
    - Si vous utilisez **Java SE**, créez une variable d’environnement nommée `JAVA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` où `<app-name>` est votre nom App Service.
    - Si vous utilisez **Tomcat**, créez une variable d’environnement nommée `CATALINA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` où `<app-name>` est votre nom App Service.

>  Si vous avez déjà une variable d’environnement pour `JAVA_OPTS` ou `CATALINA_OPTS`, ajoutez l’option `-javaagent:/...` à la fin de la valeur actuelle.

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

#### <a name="finalize-configuration"></a>Finaliser la configuration

Pour finir, nous allons placer les fichiers du pilote JAR dans le classpath Tomcat et redémarrer votre App Service. Veillez à ce que les fichiers du pilote JDBC soient disponibles pour le chargeur de classes Tomcat en les mettant dans le répertoire */home/tomcat/lib*. (S’il n’existe pas déjà, créez ce répertoire.) Pour charger ces fichiers sur votre instance App Service, procédez comme suit :

1. Dans le [Cloud Shell](https://shell.azure.com), installez l’extension d’application web :

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Exécutez la commande CLI suivante pour créer un tunnel SSH de votre système local vers App Service :

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Connectez-vous au port de tunneling local avec votre client SFTP et chargez les fichiers dans le dossier */home/tomcat/lib*.

Vous pouvez également utiliser un client FTP pour charger le pilote JDBC. Suivez ces [instructions pour obtenir vos informations d’identification FTP](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Configuration de Tomcat

Pour modifier le `server.xml` ou d’autres fichiers de configuration Tomcat, notez tout d’abord votre version principale de Tomcat dans le portail.

1. Recherchez le répertoire de base Tomcat de votre version en exécutant la commande `env`. Recherchez la variable d’environnement qui commence par `AZURE_TOMCAT` et qui correspond à votre version principale. Par exemple, `AZURE_TOMCAT85_HOME` pointe vers le répertoire Tomcat 8.5 de Tomcat.
1. Une fois que vous avez identifié le répertoire de base Tomcat de votre version, copiez le répertoire de configuration dans `D:\home`. Par exemple, si `AZURE_TOMCAT85_HOME` avait une valeur de `D:\Program Files (x86)\apache-tomcat-8.5.37`, le nouveau chemin du répertoire copié serait `D:\home\apache-tomcat-8.5.37`.

Pour terminer, redémarrez votre instance App Service. Vos déploiements doivent atteindre `D:\home\site\wwwroot\webapps` comme avant.

## <a name="configure-java-se"></a>Configurer Java SE

Lors de l’exécution d’une application .JAR sur Java SE sur Windows, `server.port` est transmis comme option de ligne de commande au démarrage de votre application. Vous pouvez résoudre manuellement le port HTTP à partir de la variable d’environnement, `HTTP_PLATFORM_PORT`. La valeur de cette variable d’environnement sera le port HTTP sur lequel votre application doit écouter. 

## <a name="java-runtime-statement-of-support"></a>Informations de prise en charge du runtime Java

### <a name="jdk-versions-and-maintenance"></a>Versions JDK et maintenance

Le kit de développement Java (JDK) pris en charge d’Azure est [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fourni par [Azul Systems](https://www.azul.com/).

Les mises à jour de la version majeure sont fournies via de nouvelles options de runtime dans Azure App Service pour Windows. Les clients effectuent une mise à jour avec ces versions plus récentes de Java en configurant leur déploiement App Service et doivent s’occuper des tests et de s’assurer que la mise à jour majeure répond à leurs besoins.

Les kits JDK pris en charge sont automatiquement mis à jour tous les trimestres, en janvier, avril, juillet et octobre de chaque année. Pour plus d’informations sur Java sur Azure, consultez [ce document de support](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Mises à jour de sécurité

Les correctifs des principales vulnérabilités de sécurité seront publiés dès qu’Azul Systems les mettra à disposition. Une vulnérabilité « majeure » est définie par un score de base de 9.0 ou supérieur dans le système [NIST Common Vulnerability Scoring System, version 2](https://nvd.nist.gov/cvss.cfm).

Tomcat 8.0 a atteint sa [fin de vie (EOL) le 30 septembre 2018](https://tomcat.apache.org/tomcat-80-eol.html). Bien que le runtime soit toujours disponible sur Azure App Service, Azure n’appliquera pas de correctifs de sécurité à Tomcat 8.0. Si possible, migrez vos applications vers Tomcat 8.5 ou 9.0. Tomcat 8.5 et 9.0 sont tous deux disponibles sur Azure App Service. Pour plus d’informations, rendez-vous sur le [site officiel de Tomcat](https://tomcat.apache.org/whichversion.html). 

### <a name="deprecation-and-retirement"></a>Dépréciation et retrait

Si un runtime Java pris en charge est retiré, les développeurs Azure utilisant le runtime affecté reçoivent un avis de dépréciation au moins six mois avant son retrait.

### <a name="local-development"></a>Développement local

Les développeurs peuvent télécharger l’édition Production du kit Azul Zulu Enterprise JDK à partir du [site de téléchargement d’Azul](https://www.azul.com/downloads/azure-only/zulu/) pour développer en local.

### <a name="development-support"></a>Prise en charge du développement

La prise en charge produit du [kit JDK Zulu d’Azul pris en charge par Azure](https://www.azul.com/downloads/azure-only/zulu/) est disponible via Microsoft si vous développez pour Azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) avec un [plan de support Azure éligible](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Prise en charge du runtime

Les développeurs peuvent [signaler un problème](/azure/azure-portal/supportability/how-to-create-azure-support-request) avec les kits JDK Zulu d’Azul via le support Azure s’ils ont un [plan de support éligible](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Étapes suivantes

Cette rubrique fournit les informations de prise en charge du runtime Java pour Azure App Service sur Windows.

- Pour en savoir plus sur l’hébergement d’applications web avec Azure App Service, consultez [Présentation d’Azure App Service](overview.md).
- Pour plus d’informations sur le développement Java sur Azure, consultez le [Centre de développement Azure pour Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
