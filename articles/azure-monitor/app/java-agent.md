---
title: Surveillance des performances des applications web Java dans Azure Application Insights | Microsoft Docs
description: Surveillance étendue des performances et de l’utilisation de votre site web Java avec Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: ce5f7ab1e6751a9ce68aa2d9c466a112c9cac182
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900606"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Surveiller les dépendances, les exceptions interceptées et les temps d’exécution des méthodes dans les applications web Java


Si vous avez [instrumenté votre application web Java avec Application Insights][java], vous pouvez utiliser l’agent Java pour obtenir des informations plus détaillées, sans pour autant modifier le code :

* **Dépendances :** données sur les appels passés par votre application à destination d’autres composants, dont :
  * Les **appels REST** passés via HttpClient, OkHttp et RestTemplate (Spring) sont capturés.
  * Les appels **Redis** passés via le client Jedis sont capturés.
  * Les **[ppels JDBC](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** – commandes MySQL, SQL Server et base de données Oracle sont automatiquement capturés. Pour MySQL, si l’appel prend plus de 10 s, l’agent signale le plan de requête.
* **Exceptions interceptées :** informations concernant les exceptions gérées par votre code.
* **Temps d’exécution de la méthode :** données concernant le temps nécessaire pour exécuter des méthodes spécifiques.

Pour utiliser l’agent Java, installez-le sur votre serveur. Vos applications web doivent être instrumentées à l’aide du [Kit de développement logiciel (SDK) Java Application Insights][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Installer l’agent Application Insights pour Java
1. [Téléchargez l'agent](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) sur la machine exécutant votre serveur Java. Veillez à télécharger la même version de Java Agent en tant que packages de noyau et web de SDK Java Application Insights.
2. Modifiez le script de démarrage du serveur d’applications et ajoutez la Machine virtuelle Java (JVM) suivante :
   
    `javaagent:`*chemin d’accès complet au fichier JAR de l’agent*
   
    Par exemple, dans Tomcat sur une machine Linux :
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Redémarrez votre serveur d’applications.

## <a name="configure-the-agent"></a>Configurer l’agent
Créez un fichier nommé `AI-Agent.xml` et placez-le dans le même dossier que le fichier JAR de l’agent.

Définissez le contenu du fichier xml. Modifiez l’exemple suivant pour inclure ou omettre les fonctionnalités souhaitées.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />
           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Vous devez activer l’exception de rapports et le minutage pour les méthodes individuelles.

Par défaut, `reportExecutionTime` est défini sur true, et `reportCaughtExceptions` sur false.

## <a name="additional-config-spring-boot"></a>Configuration supplémentaire (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Pour Azure App Services, procédez comme suit :

* Cliquez sur Paramètres > Paramètres de l’application
* Sous Paramètres de l’application, ajoutez une nouvelle paire clé/valeur :

Clé : Valeur `JAVA_OPTS` : `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.3.1-SNAPSHOT.jar`

Pour connaître la dernière version de l’agent Java, vérifiez les versions [ici](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

L’agent doit être empaqueté sous forme de ressource dans votre projet de sorte qu’il se retrouve dans le répertoire D:/home/site/wwwroot/. Vous pouvez vérifier que votre agent se trouve dans le répertoire App Service en accédant à **Outils de développement** > **Outils avancés** > **Console de débogage**, puis en examinant le contenu du répertoire du site.    

* Enregistrez les paramètres, puis redémarrez votre application. (Ces étapes s’appliquent uniquement à App Services s’exécutant sur Windows.)

> [!NOTE]
> Le fichier AI-agent.XML et le fichier jar de l’agent doivent se trouver dans le même dossier. Ils sont souvent placés ensemble dans le dossier `/resources` du projet.  

### <a name="spring-rest-template"></a>Modèle Rest de Spring

Pour qu’Application Insights instrumente correctement les appels HTTP effectués avec le modèle Rest de Spring, l’utilisation du client HTTP Apache est obligatoire. Par défaut, le modèle Rest de Spring n’est pas configuré pour utiliser le client HTTP Apache. En spécifiant [HttpComponentsClientHttpRequestfactory](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/HttpComponentsClientHttpRequestFactory.html) dans le constructeur d’un modèle Rest de Spring, ce dernier utilise le client HTTP Apache.

Voici un exemple montrant comment procéder avec Spring Beans. Il s’agit d’un exemple très simple qui utilise les paramètres par défaut de la classe de la fabrique.

```java
@bean
public ClientHttpRequestFactory httpRequestFactory() {
return new HttpComponentsClientHttpRequestFactory()
}
@Bean(name = 'myRestTemplate')
public RestTemplate dcrAccessRestTemplate() {
    return new RestTemplate(httpRequestFactory())
}
```

#### <a name="enable-w3c-distributed-tracing"></a>Activer le traçage distribué W3C

Ajoutez le code suivant au fichier AI-Agent.xml :

```xml
<Instrumentation>
        <BuiltIn enabled="true">
            <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
        </BuiltIn>
    </Instrumentation>
```

> [!NOTE]
> Le mode de compatibilité descendante est activé par défaut ; le paramètre enableW3CBackCompat est facultatif et ne doit être utilisé que si vous souhaitez désactiver ce mode. 

Idéalement, ce serait le cas si tous vos services avaient été mis à jour vers une version plus récente des SDK prenant en charge le protocole W3C. Il est vivement recommandé de migrer au plus vite vers une version plus récente des SDK prenant en charge le protocole W3C.

Assurez-vous que les **configurations [entrante](correlation.md#w3c-distributed-tracing) et sortante (agent)** sont totalement identiques.

## <a name="view-the-data"></a>Visualiser les données
Dans la ressource Application Insights, les temps d’exécution cumulés de la méthode et de la dépendance distante apparaissent [dans la vignette Performances][metrics].

Pour rechercher des instances de dépendance et d’exception ainsi que des rapports de méthode, ouvrez [Rechercher][diagnostic].

[En savoir plus sur le diagnostic des problèmes de dépendance](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Des questions ? Des problèmes ?
* Pas de données ? [Définir les exceptions de pare-feu](../../azure-monitor/app/ip-addresses.md)
* [Résolution des problèmes Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
