---
title: Collecter les métriques du disjoncteur Spring Cloud Resilience4J
description: Comment collecter les métriques du disjoncteur Spring Cloud Resilience4J.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9fc8ccc5ba21f02885b2002d5040051baac92068
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877335"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-preview"></a>Collecter les métriques du disjoncteur Spring Cloud Resilience4J (préversion)

Ce document explique comment collecter les métriques de disjoncteur Spring Cloud Resilience4J avec l’agent In-process Java Application Insights.  Grâce à cette fonctionnalité, vous pouvez surveiller les métriques du disjoncteur Resilience4J à partir d’Application Insights.

Pour en illustrer le fonctionnement, nous allons utiliser [spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo).

## <a name="prerequisites"></a>Prérequis

* Activez l’agent In-process Java à partir du [guide de l’agent In-process Java pour Application Insights](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights). 

* Activez la collecte de dimensions pour les métriques Resilience4J à partir du [guide Application Insights](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

* S’ils ne sont pas déjà utilisés par l’ordinateur de développement, installez git, Maven et Java.

## <a name="build-and-deploy-apps"></a>Créer et déployer des applications

La procédure suivante génère et déploie des applications.

1. Clonez et générez le référentiel de démonstration.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Créer des applications avec des points de terminaison

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. Déployez des applications.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Incluez la dépendance requise pour Resilience4J :
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * Le code client doit utiliser l’API de `CircuitBreakerFactory`, qui est implémentée en tant que `bean` automatiquement créé lorsque vous incluez un démarrage de disjoncteur Spring Cloud. Pour plus d’informations, consultez [Disjoncteur Spring Cloud](https://spring.io/projects/spring-cloud-circuitbreaker#overview).
>
> * Les 2 dépendances suivantes sont en conflit avec les packages Resilient4J ci-dessus.  Assurez-vous que le client ne les inclue pas.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> Accédez à l’URL fournie par les applications de passerelle, puis au point de terminaison à partir de [spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) comme suit :
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Localiser les métriques Resilence4J à partir du portail

1. Sélectionnez le panneau **Application Insights** à partir du portail Azure Spring Cloud, puis cliquez sur **Application Insights**.

   [ ![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Sur la page **Application Insights**, sélectionnez **Métriques**.  Sélectionnez **azure.applicationinsights** dans **Espace de noms de métrique**.  Sélectionnez également les métriques **resilience4j_circuitbreaker_buffered_calls** et **Moyenne**.

   [ ![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Sélectionnez les métriques **resilience4j_circuitbreaker_calls** et **Moyenne**.

   [ ![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Sélectionnez les métriques **resilience4j_circuitbreaker_calls** et **Moyenne**.  Cliquez sur **Ajouter un filtre**, puis sélectionnez le nom **createNewAccount**.

   [ ![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Sélectionnez les métriques **resilience4j_circuitbreaker_calls** et **Moyenne**.  Cliquez sur **Appliquer la division** et sélectionnez **genre**.

   [ ![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Sélectionnez les métriques **resilience4j_circuitbreaker_calls**, **resilience4j_circuitbreaker_buffered_calls** et **resilience4j_circuitbreaker_slow_calls**, ainsi que **Moyenne**.

   [ ![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Voir aussi

* [Application Insights](spring-cloud-howto-application-insights.md)
* [Traçage distribué](spring-cloud-howto-distributed-tracing.md)
* [Tableau de bord Disjoncteur](spring-cloud-tutorial-circuit-breaker.md)