---
title: Analyse d’une application web Java avec Azure Application Insights | Microsoft Docs
description: 'Analyse des performances des applications pour les applications web Java à l’aide d’Application Insights. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: lagayhar
ms.openlocfilehash: a453e82f47bb9eed25c8d5caf986bc854085e8ac
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061221"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Prise en main d'Application Insights dans un projet web Java

[Application Insights](https://azure.microsoft.com/services/application-insights/) est un service d’analyse extensible pour développeurs web qui vous permet de comprendre les performances et l’utilisation de votre application en direct. Utilisez-le pour [instrumenter automatiquement les requêtes, effectuer le suivi des dépendances et recueillir les compteurs de performances](auto-collect-dependencies.md#java), diagnostiquer les problèmes de performances et les exceptions, et [écrire du code][api] pour suivre l’utilisation de votre application par les utilisateurs. 

![Capture d’écran des exemples de données de vue d’ensemble](./media/java-get-started/overview-graphs.png)

Application Insights prend en charge les applications Java exécutées sur Linux, Unix ou Windows.

Ce dont vous avez besoin :

* JRE version 1.7 ou 1.8
* Un abonnement à [Microsoft Azure](https://azure.microsoft.com/).

Si vous préférez l’infrastructure Spring, consultez le guide [configure a Spring Boot initializer app to use Application Insights guide](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights) (guide de configuration de l’application d’initialiseur Spring Boot afin d’utiliser Application Insights)

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Obtenir une clé d'instrumentation Application Insights
1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).
2. Créez une ressource Application Insights. Définissez le type d’application sur Application web Java.

3. Obtenez la clé d'instrumentation de la nouvelle ressource. Vous devrez la coller rapidement dans le code de votre projet.

    ![Dans la nouvelle vue d'ensemble des ressources, cliquez sur Propriétés et copiez la clé d'instrumentation.](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Ajoutez le Kit de développement logiciel (SDK) Application Insights pour Java à votre projet
*Choisissez la méthode adaptée à votre projet.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Si vous utilisez Maven... <a name="maven-setup" />
Si votre projet est déjà configuré pour être assemblé avec Maven, fusionnez le code suivant dans votre fichier pom.xml.

Actualisez ensuite les dépendances du projet pour télécharger les fichiers binaires.

```XML

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[2.0,)</version>
      </dependency>
    </dependencies>
```

* *Des erreurs de validation de build ou de somme de contrôle ?* Essayez d’utiliser une version spécifique, telle que : `<version>2.0.n</version>`. Vous trouverez la version la plus récente dans les [notes de publication du Kit de développement logiciel (SDK)](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) ou dans les [artefacts Maven](https://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Besoin de mettre à jour vers un nouveau Kit de développement logiciel (SDK) ?* Actualisez les dépendances de votre projet.

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Si vous utilisez Gradle... <a name="gradle-setup" />
Si votre projet est déjà configuré pour être assemblé avec Gradle, fusionnez le code suivant dans votre fichier build.gradle.xml.

Actualisez ensuite les dépendances du projet pour télécharger les fichiers binaires.

```gradle

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '2.+'
      // or applicationinsights-core for bare API
    }
```

#### <a name="if-youre-using-eclipse-to-create-a-dynamic-web-project-"></a>Si vous utilisez Eclipse pour créer un projet Web dynamique...
Utilisez le [Kit de développement logiciel (SDK) Application Insights pour plug-in Java][eclipse]. Remarque : bien que ce plug-in vous permette d’être opérationnel avec Application Insights plus rapidement (en supposant que vous n’utilisez pas Maven ou Gradle), il ne s’agit pas un système de gestion des dépendances. Par conséquent, la mise à jour du plug-in ne mettra pas automatiquement les bibliothèques d’Application Insights à jour dans votre projet.

* *Des erreurs de validation de build ou de somme de contrôle ?* Essayez d’utiliser une version spécifique, telle que : `version:'2.0.n'`. Vous trouverez la version la plus récente dans les [notes de publication du Kit de développement logiciel (SDK)](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) ou dans les [artefacts Maven](https://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Pour effectuer une mise à jour vers un Kit de développement logiciel (SDK)* , actualisez les dépendances de votre projet.

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Sinon, si vous gérez manuellement les dépendances...
Téléchargez la [version la plus récente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) et copiez les fichiers nécessaires dans votre projet, en remplaçant toutes les versions antérieures.

### <a name="questions"></a>Questions...
* *Quelle est la relation entre les composants `-core` et `-web` ?*
  * `applicationinsights-core` vous fournit l’API seule. Ce composant est toujours requis.
  * `applicationinsights-web` fournit des mesures qui permettent d’effectuer le suivi du nombre de requêtes HTTP et des temps de réponse. Vous pouvez omettre ce composant si vous ne souhaitez pas recueillir automatiquement ces données de télémétrie. Par exemple, si vous préférez écrire vos propres mesures.
  
* *Comment dois-je mettre à jour le Kit de développement logiciel (SDK) vers la dernière version ?*
  * Si vous utilisez Gradle ou Maven...
    * Mettez à jour votre fichier de build pour spécifier la version la plus récente ou utilisez la syntaxe de caractère générique Gradle/Maven pour inclure automatiquement la version la plus récente. Ensuite, actualisez les dépendances de votre projet. La syntaxe de caractère générique peut être consultée dans les exemples ci-dessus pour [Gradle](#gradle-setup) ou [Maven](#maven-setup).
  * Si vous gérez manuellement les dépendances...
    * Téléchargez le dernier [Kit de développement logiciel Application Insights pour Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) et remplacez les anciens Kits. Les modifications sont décrites dans le [notes de publication du kit de développement logiciel (SDK)](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. Ajouter un fichier ApplicationInsights.xml
Ajoutez ApplicationInsights.xml dans le dossier de ressources de votre projet, ou vérifiez qu’il est ajouté au chemin de la classe du déploiement de votre projet. Copiez-y le code XML suivant.

Remplacez la clé d'instrumentation que avez obtenue sur le portail Azure.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->
      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->
      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

Si vous le souhaitez, le fichier config peut être hébergé dans n’importe quel emplacement accessible à votre application.  La propriété système `-Dapplicationinsights.configurationDirectory` spécifie le répertoire qui contient le fichier ApplicationInsights.xml. Par exemple, un fichier config situé à l’emplacement `E:\myconfigs\appinsights\ApplicationInsights.xml` doit être configuré avec la propriété `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* La clé d'instrumentation est envoyée avec chaque élément de télémétrie et indique à Application Insights de l'afficher dans votre ressource.
* Le composant de demande HTTP est facultatif. Il envoie automatiquement la télémétrie concernant les demandes et les temps de réponse au portail.
* La corrélation des événements est un complément au composant de requête HTTP. Il assigne un identificateur à chaque demande reçue par le serveur et l'ajoute comme propriété de chaque élément de télémétrie en tant que propriété « Operation.Id ». Il vous permet de mettre en corrélation la télémétrie associée à chaque demande en définissant un filtre dans [recherche de diagnostic][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Autres méthodes pour définir la clé d’instrumentation
Le kit de développement logiciel (SDK) d’Application Insights recherche la clé dans cet ordre :

1. Propriété système : -DAPPLICATION_INSIGHTS_IKEY=votre_ikey
2. Variable d’environnement : APPLICATION_INSIGHTS_IKEY
3. Configuration de l'application : ApplicationInsights.xml

Vous pouvez également [définir la clé dans le code](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-an-http-filter"></a>4. Ajouter un filtre HTTP
La dernière étape de la configuration permet au composant de demande HTTP de consigner toutes les demandes web. (Non requis si vous voulez juste l'API seule.)

### <a name="spring-boot-applications"></a>Applications Spring Boot
Inscrivez l’Application Insights `WebRequestTrackingFilter` dans votre classe de configuration :

```Java
package <yourpackagename>.configurations;

import javax.servlet.Filter;

import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.core.Ordered;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import com.microsoft.applicationinsights.TelemetryConfiguration;
import com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter;

@Configuration
public class AppInsightsConfig {

    @Bean
    public String telemetryConfig() {
        String telemetryKey = System.getenv("<instrumentation key>");
        if (telemetryKey != null) {
            TelemetryConfiguration.getActive().setInstrumentationKey(telemetryKey);
        }
        return telemetryKey;
    }

    /**
     * Programmatically registers a FilterRegistrationBean to register WebRequestTrackingFilter
     * @param webRequestTrackingFilter
     * @return Bean of type {@link FilterRegistrationBean}
     */
    @Bean
    public FilterRegistrationBean webRequestTrackingFilterRegistrationBean(WebRequestTrackingFilter webRequestTrackingFilter) {
        FilterRegistrationBean registration = new FilterRegistrationBean();
        registration.setFilter(webRequestTrackingFilter);
        registration.addUrlPatterns("/*");
        registration.setOrder(Ordered.HIGHEST_PRECEDENCE + 10);
        return registration;
    }


    /**
     * Creates bean of type WebRequestTrackingFilter for request tracking
     * @param applicationName Name of the application to bind filter to
     * @return {@link Bean} of type {@link WebRequestTrackingFilter}
     */
    @Bean
    @ConditionalOnMissingBean

    public WebRequestTrackingFilter webRequestTrackingFilter(@Value("${spring.application.name:application}") String applicationName) {
        return new WebRequestTrackingFilter(applicationName);
    }


}
```

> [!NOTE]
> Si vous utilisez Spring Boot 1.3.8 ou ultérieur, remplacez le paramètre FilterRegistrationBean par la ligne ci-dessous.

```Java
    import org.springframework.boot.context.embedded.FilterRegistrationBean;
```

Cette classe configurera le `WebRequestTrackingFilter` afin qu’il soit le premier filtre de la chaîne de filtre HTTP. Elle extraira également la clé d’instrumentation à partir de la variable d’environnement du système d’exploitation, si elle est disponible.

> Nous utilisons la configuration du filtre Web HTTP plutôt que la configuration Spring MVC, car il s’agit d’une application Spring Boot, et qu’elle a sa propre configuration Spring MVC. Consultez les sections ci-dessous pour une configuration Spring MVC spécifique.

### <a name="applications-using-webxml"></a>Applications utilisant web.xml
Recherchez et ouvrez le fichier web.xml dans votre projet et fusionnez le code suivant sous le nœud de l'application web, où vos filtres d'application sont configurés.

Pour obtenir des résultats plus précis, le filtre doit être mappé avant tous les autres filtres.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

   <!-- This listener handles shutting down the TelemetryClient when an application/servlet is undeployed. -->
    <listener>
      <listener-class>com.microsoft.applicationinsights.web.internal.ApplicationInsightsServletContextListener</listener-class>
    </listener>
```

#### <a name="if-youre-using-spring-web-mvc-31-or-later"></a>Si vous utilisez Spring Web MVC 3.1 ou une version ultérieure
Modifiez ces éléments dans *-servlet.xml pour inclure le package Application Insights :

```XML

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>
```

#### <a name="if-youre-using-struts-2"></a>Si vous utilisez Struts 2
Ajoutez cet élément au fichier de configuration Struts (généralement struts.xml ou struts-default.xml) :

```XML

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />
```

Si vous avez défini des intercepteurs dans une pile par défaut, l’intercepteur peut être ajouté à cette pile.

## <a name="5-run-your-application"></a>5. Exécuter votre application
Exécutez-le en mode débogage sur votre ordinateur de développement, ou publiez-le sur votre serveur.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Voir votre télémétrie dans Application Insights
Revenez à votre ressource Application Insights sur le [portail Microsoft Azure](https://portal.azure.com).

Les données des demandes HTTP apparaissent dans le panneau Vue d’ensemble. (Si elles n’y sont pas, attendez quelques secondes et cliquez sur Actualiser).

![Capture d’écran des exemples de données de vue d’ensemble](./media/java-get-started/overview-graphs.png)

[En savoir plus sur les mesures.][metrics]

Cliquez sur un des graphiques pour afficher des métriques agrégées plus détaillées.

![Volet Échecs d’Application Insights avec des graphiques](./media/java-get-started/006-barcharts.png)

> Application Insights repose sur l’hypothèse que le format des requêtes HTTP pour les applications MVC est le suivant : `VERB controller/action`. Par exemple, `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` et `GET Home/Product/sdf96vws` sont regroupés dans `GET Home/Product`. Ceci permet l’agrégation correcte des demandes, par exemple le nombre de demandes et le temps moyen d’exécution des demandes.
>
>

### <a name="instance-data"></a>Données d’instance
Cliquez sur un type de demande spécifique pour afficher les instances individuelles.

![Explorer un exemple de vue spécifique](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analytics : Tirez parti d’un puissant langage de requête.
En accumulant toujours plus de données, vous pouvez exécuter des requêtes à la fois pour agréger les données et pour rechercher des instances individuelles.  [Analytics](../../azure-monitor/app/analytics.md) est un outil puissant qui permet non seulement de comprendre les performances et l’utilisation, mais également d’effectuer des diagnostics.

![Exemple d’Analytics](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Installer votre application sur le serveur
Publiez maintenant votre application sur le serveur, laissez le temps aux usagers de l’utiliser, puis observez les données de télémétrie qui s’affichent sur le portail.

* Assurez-vous que votre pare-feu autorise votre application à envoyer les données de télémétrie vers ces ports :

  * dc.services.VisualStudio.com:443
  * f5.services.visualstudio.com:443

* Si le trafic sortant doit être acheminé via un pare-feu, définissez les propriétés système `http.proxyHost` et `http.proxyPort`.

* Sur les serveurs Windows, installez :

  * [Redistribuable Microsoft Visual C++](https://www.microsoft.com/download/details.aspx?id=40784)

    (Cette opération active les compteurs de performances.)

## <a name="azure-app-service-config-spring-boot"></a>Configuration d’Azure App Service (Spring Boot)

Les applications Spring Boot qui s’exécutent sur Windows nécessitent une configuration supplémentaire pour s’exécuter sur Azure App Services. Modifiez le fichier **web.config** et ajoutez l’élément suivant :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Exceptions et échecs de requêtes
Les exceptions non prises en charge sont collectées automatiquement.

Pour collecter les données concernant d’autres exceptions, vous disposez de deux options :

* [Insérez des appels à trackException() dans votre code][apiexceptions].
* [Installez l’agent Java sur votre serveur](java-agent.md). Vous spécifiez les méthodes que vous souhaitez surveiller.

## <a name="monitor-method-calls-and-external-dependencies"></a>Surveiller les appels de méthode et les dépendances externes
[Installez l’agent Java](java-agent.md) pour journaliser les méthodes internes spécifiées et les appels effectués via JDBC, avec des données de minutage.

## <a name="w3c-distributed-tracing"></a>Traçage distribué W3C

Le kit de développement logiciel (SDK) Java Application Insights prend désormais en charge le [traçage distribué W3C](https://w3c.github.io/trace-context/).

La configuration entrante du SDK est expliquée plus en détail dans notre article consacré à la [corrélation](correlation.md#w3c-distributed-tracing).

La configuration sortante du SDK est définie dans le fichier [AI-Agent.xml](java-agent.md).

## <a name="performance-counters"></a>Compteurs de performances
Ouvrez **Examiner**, **Métriques** afin d’afficher un ensemble de compteurs de performances.

![Capture d’écran du volet Métriques avec les octets privés du processus sélectionnés](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personnaliser la collecte des compteurs de performances
Pour désactiver la collecte du jeu standard de compteurs de performances, ajoutez le code suivant sous le nœud racine du fichier ApplicationInsights.xml :

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Collecter des compteurs de performances supplémentaires
Vous pouvez spécifier d'autres compteurs de performances à collecter.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Compteurs JMX (exposés par la machine virtuelle Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` : nom affiché sur le portail Application Insights.
* `objectName` : nom de l'objet JMX.
* `attribute` attribut du nom d'objet JMX à récupérer
* `type` (facultatif) : type d'attribut d'objet JMX :
  * Par défaut : un type simple, comme int ou long.
  * `composite`: les données du compteur de performances sont au format « Attribute.Data »
  * `tabular`: les données du compteur de performances sont au format ligne de tableau

#### <a name="windows-performance-counters"></a>Compteurs de performances Windows
Chaque [compteur de performances Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) est un membre d'une catégorie (de la même façon qu'un champ est un membre d'une classe). Les catégories peuvent être globales ou peuvent avoir des instances numérotées ou nommées.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName : nom affiché sur le portail Application Insights.
* categoryName : catégorie du compteur de performances (objet de performances) à laquelle ce compteur de performances est associé.
* counterName : nom du compteur de performances.
* instanceName : nom de l'instance de catégorie de compteur de performances ou une chaîne vide ("") si la catégorie contient une seule instance. Si categoryName est Process et que le compteur de performance que vous souhaitez collecter vient du processus en cours de la JVM sur laquelle votre application s'exécute, spécifiez `"__SELF__"`.

### <a name="unix-performance-counters"></a>Compteurs de performances Unix
* [Installez collectd avec le plug-in Application Insights](java-collectd.md) pour obtenir une grande variété de données sur le système et le réseau.

## <a name="local-forwarder"></a>Redirecteur local

[Redirecteur local](https://docs.microsoft.com/azure/application-insights/local-forwarder) est un agent qui collecte des données de télémétrie Application Insights ou [OpenCensus](https://opencensus.io/) sur une variété de kits de développement logiciel (SDK) et d’infrastructures, et les achemine vers Application Insights. Il peut être exécuté sous Windows et Linux.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<DeveloperMode>false</DeveloperMode>
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>
<!-- The properties below are optional. The values shown are the defaults for each property -->
<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

Si vous utilisez le starter SpringBoot, ajoutez le code suivant à votre fichier de configuration (application.properies) :

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Les valeurs par défaut sont les mêmes pour la configuration du fichier application.properties SpringBoot et du fichier applicationinsights.xml.

## <a name="get-user-and-session-data"></a>Obtenir des données utilisateur et de session
Vous envoyez des données de télémétrie depuis votre serveur web. Vous pouvez désormais ajouter plus de surveillance pour obtenir une vue à 360 degrés de votre application :

* [Ajoutez la télémétrie à vos pages web][usage] pour surveiller les affichages de pages et les mesures relatives à l’utilisateur.
* [Configurez les tests web][availability] pour vous assurer que votre application est bien active.

## <a name="capture-log-traces"></a>Capture le suivi des journaux
Vous pouvez utiliser Application Insights pour traiter les journaux d’activité Log4J, Logback ou autres frameworks de journalisation. Vous pouvez mettre en corrélation les journaux d’activité avec les demandes HTTP et autres informations de télémétrie. [Découvrez comment][javalogs].

## <a name="send-your-own-telemetry"></a>Envoyer votre propre télémétrie
Maintenant que vous avez installé le Kit de développement logiciel (SDK), vous pouvez utiliser l'API pour envoyer votre propre télémétrie.

* [Suivez des événements et des mesures personnalisés][api] pour savoir ce que les utilisateurs font avec votre application.
* [Recherchez les événements et les journaux d’activité][diagnostic] pour diagnostiquer les problèmes.

## <a name="availability-web-tests"></a>Tests web de disponibilité
Application Insights peut tester votre site web à intervalles réguliers pour vérifier qu’il fonctionne et répond correctement.

[En savoir plus sur la configuration des tests web de disponibilité.][availability]

## <a name="questions-problems"></a>Des questions ? Des problèmes ?
[Résolution des problèmes Java](java-troubleshoot.md)

## <a name="next-steps"></a>Étapes suivantes
* [Surveillance des appels de dépendance](java-agent.md)
* [Surveillance des compteurs de performances Unix](java-collectd.md)
* Ajoutez [la surveillance à vos pages web](javascript.md) pour surveiller le temps de chargement des pages, les appels AJAX et les exceptions du navigateur.
* Écrivez [télémétrie personnalisée](../../azure-monitor/app/api-custom-events-metrics.md) pour suivre l’utilisation sur le navigateur ou le serveur.
* Utilisez [Analytics](../../azure-monitor/app/analytics.md) pour des requêtes puissantes sur les données de télémétrie de votre application
* Pour plus d’informations, consultez [Azure pour les développeurs Java](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: ../../azure-monitor/learn/java-quick-start.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
