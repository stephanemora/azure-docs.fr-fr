---
title: Utilisation de Micrometer avec le Kit de développement logiciel (SDK) Azure Application Insights | Microsoft Docs
description: 'Guide pas à pas sur l’utilisation de Micrometer avec vos applications Application Insights Spring Boot et autres que Spring Boot. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: lagayhar
ms.openlocfilehash: 80c87de21baffb5d3152eff631c07aba1fae0a7b
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019795"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Utilisation de Micrometer avec le Kit de développement logiciel (SDK) Java Azure Application Insights
La supervision d’application Micrometer mesure les métriques pour le code d’application basé sur la JVM et vous permet d’exporter les données vers vos systèmes de supervision préférés. Cet article vous montrera comment utiliser Micrometer avec Application Insights pour les applications Spring Boot et les autres.

## <a name="using-spring-boot-15x"></a>Utilisation de Spring Boot 1.5x
Ajoutez les dépendances suivantes à votre fichier pom.xml ou build.gradle : 
* [Application Insights spring-boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter)1.1.0-BETA ou version ultérieure
* Micrometer Azure Registry version 1.1.0 ou ultérieure
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) version 1.1.0 ou ultérieure (ce qui revient à porter le code autoconfig dans le framework Spring).
* [Ressource ApplicationInsights](app-insights-create-new-resource.md)

Étapes

1. Mettez à jour le fichier pom.xml de votre application Spring Boot et ajoutez-y les dépendances suivantes :

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>1.1.0-BETA</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Mettez à jour le fichier application.properties ou yml avec la clé d’instrumentation Application Insights en utilisant la propriété suivante :

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Créer votre application et l’exécuter
2. L’approche ci-dessus devrait vous permettre d’être opérationnel avec des métriques préagrégées collectées automatiquement pour Azure Monitor. Pour plus de détails sur la façon d’ajuster Application Insights Spring Boot Starter, reportez-vous au [fichier Lisez-moi sur GitHub](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Utilisation de Spring 2.x

Ajoutez les dépendances suivantes à votre fichier pom.xml ou build.gradle :

* Application Insights Spring-boot-starter version 2.1.2 ou ultérieure
* Azure-spring-boot-metrics-starters version 2.0.7 ou ultérieure  
* [Ressource Application Insights](app-insights-create-new-resource.md)

Étapes :

1. Mettez à jour le fichier pom.xml de votre application Spring Boot et ajoutez-y la dépendance suivante :

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Mettez à jour le fichier application.properties ou yml avec la clé d’instrumentation Application Insights en utilisant la propriété suivante :

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Créer votre application et l’exécuter
4. L’approche ci-dessus devrait vous permettre d’être opérationnel avec des métriques préagrégées collectées automatiquement pour Azure Monitor. Pour plus de détails sur la façon d’ajuster Application Insights Spring Boot Starter, reportez-vous au [fichier Lisez-moi sur GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Métriques par défaut :

*    Métriques configurées automatiquement pour Tomcat, machine virtuelle Java, métriques Logback, métriques Log4J, métriques de disponibilité, métriques de processeur, FileDescriptorMetrics.
*    Par exemple, si l’API Netflix Hystrix est présente dans le chemin d’accès des classes, nous obtenons également ces métriques. 
*    Les métriques suivantes peuvent être obtenues par l’ajout de leurs beans respectifs. 
        - CacheMetrics (CaffineCache, EhCache2, GuavaCache, HazelcaseCache, Jcache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - Mesures OKHttp3 
        - Mesures Kafka 

 

Comment désactiver la collecte automatique de métriques : 
 
- Métriques de JVM : 
    - management.metrics.binders.jvm.enabled=false 
- Mesures Logback : 
    - management.metrics.binders.logback.enabled=false
- Mesures sur le fonctionnement : 
    - management.metrics.binders.uptime.enabled=false 
- Mesures sur le processeur :
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics :
    - management.metrics.binders.files.enabled=false 
- Métriques Hystrix si la bibliothèque est enregistrée dans le chemin de classes : 
    - management.metrics.binders.hystrix.enabled=false 
- Métriques AspectJ si la bibliothèque est enregistrée dans le chemin de classes : 
    - Spring.AOP.Enabled=false 

> [!NOTE]
> Spécifiez les propriétés ci-dessus dans le fichier application.properties ou application.yml de votre application Spring Boot

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Utilisez Micrometer avec les applications web non Spring Boot

Ajoutez les dépendances suivantes à votre fichier pom.xml ou build.gradle :
 
* [Application Insight Core 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.2.0) ou version ultérieure
* [Application Insight Web 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/2.2.0) ou version ultérieure
* [Inscrire un filtre web](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* Micrometer Azure Registry version 1.1.0 ou ultérieure
* [Ressource Application Insights](app-insights-create-new-resource.md)

Étapes :

1. Ajoutez les dépendances suivantes à votre fichier pom.xml ou build.gradle :

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web</artifactId>
            <version>2.2.0</version>
        </dependency
     ```

2. Placez le fichier ApplicationInsights.xml dans le dossier de ressources

    Exemple de classe de servlet (émet une métrique de minuteur) :

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest reqest, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

      Exemple de classe de configuration :

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

Pour en savoir plus sur les métriques, consultez la [Documentation Micrometer](https://micrometer.io/docs/).

D’autres exemples de code sur la création de différents types de métriques sont disponibles dans le [dépôt Github officiel de Micrometer](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Comment lier la collecte de métriques supplémentaires

### <a name="springbootspring"></a>SpringBoot/Spring

Créez un bean de la catégorie de métrique appropriée. Par exemple, supposons que nous ayons besoin de métriques du cache Guava :

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Il existe plusieurs métriques qui ne sont pas activées par défaut mais qui peuvent être liées de la manière décrite ci-dessus. Pour obtenir la liste complète, reportez-vous au [dépôt Github officiel de Micrometer](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Applications non Spring
Ajoutez le code de liaison suivant au fichier de configuration :
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur Micrometer, consultez la [Documentation Micrometer](https://micrometer.io/docs) officielle.
* Pour en savoir plus sur Spring sur Azure, consultez la [documentation officielle Spring sur Azure](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).
