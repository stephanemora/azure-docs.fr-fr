---
title: Tutoriel - Préparer le déploiement d’une application Spring dans Azure Spring Cloud
description: Dans ce tutoriel, vous préparez le déploiement d’une application Spring Java.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e112fdc9e6f518e2ea3c72161e8978118cf19335
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890312"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Didacticiel : Préparer une application Spring Java pour le déploiement dans Azure Spring Cloud

Ce guide de démarrage rapide vous montre comment préparer une application Spring Cloud Java existante pour la déployer sur Azure Spring Cloud.  Correctement configuré, Azure Spring Cloud fournit des services robustes pour superviser, mettre à l’échelle et mettre à jour votre application Spring Cloud. 

## <a name="java-runtime-version"></a>Version du runtime Java

Seules les applications Spring/Java peuvent s’exécuter dans Azure Spring Cloud.

Java 8 et Java 11 sont tous deux pris en charge. L’environnement d’hébergement contient la dernière version d’Azul Zulu OpenJDK pour Azure. Pour plus d’informations sur Azul Zulu OpenJDK pour Azure, consultez [cet article](https://docs.microsoft.com/azure/java/jdk/java-jdk-install). 

## <a name="spring-boot-and-spring-cloud-versions"></a>Versions de Spring Boot et de Spring Cloud

Seules les applications Spring Boot sont prises en charge dans Azure Spring Cloud. Spring Boot 2.0 et 2.1 sont tous deux pris en charge. Les combinaisons de Spring Boot et de Spring Cloud prises en charge sont listées dans le tableau ci-dessous.

Version de Spring Boot | Version de Spring Cloud
---|---
2.0.x | Finchley.RELEASE
2.1.x | Greenwich.RELEASE

Vérifiez que votre fichier `pom.xml` a les dépendances de Spring Boot et de Spring Cloud, en fonction de votre version.

### <a name="version-20"></a>Version 2.0 :

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>Version 2.1 :

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Dépendance du client Azure Spring Cloud

Azure Spring Cloud héberge et gère les composants Spring Cloud pour vous, comme le registre du service Spring Cloud et le serveur de configuration Spring Cloud. Ajoutez la bibliothèque cliente d’Azure Spring Cloud dans vos dépendances pour permettre la communication avec votre instance du service Azure Spring Cloud.

Le tableau ci-dessous liste les versions correctes pour votre application Spring Boot/Spring Cloud.

Version de Spring Boot | Version de Spring Cloud | Version d’Azure Spring Cloud
---|---|---
2.0.x | Finchley.RELEASE | 2.0.x
2.1.x | Greenwich.RELEASE | 2.1.x

Incluez l'un des extraits de code ci-dessous dans votre `pom.xml`.  Sélectionnez l'extrait de code dont la version correspond à la vôtre.

### <a name="version-20x"></a>Version 2.0.x :
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>Version 2.1.x :
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Autres dépendances nécessaires

Pour activer les fonctionnalités intégrées d’Azure Spring Cloud, votre application doit inclure les dépendances suivantes. Ceci garantit que votre application se configure elle-même correctement avec chaque composant.  

### <a name="service-registry"></a>Registre de service

Pour utiliser le service Azure Service Registry managé, ajoutez `spring-cloud-starter-netflix-eureka-client` dans `POM.xml`, comme indiqué ci-dessous.

Le point de terminaison du serveur Service Registry sera injecté automatiquement en tant que variables d’environnement avec votre application. Les applications peuvent alors s’inscrire elles-mêmes auprès du serveur Service Registry et découvrir d’autres microservices dépendants.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Configuration distribuée

Pour activer la configuration distribuée, ajoutez `spring-cloud-config-client` dans la section des dépendances de votre fichier `pom.xml`.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ne spécifiez pas `spring.cloud.config.enabled=false` dans la configuration de démarrage, car votre application cessera de fonctionner avec le serveur de configuration.

### <a name="metrics"></a>Mesures

Ajoutez `spring-boot-starter-actuator` dans la section des dépendances de votre fichier pom.xml. Les métriques sont extraites régulièrement à partir des points de terminaison JMX et peuvent être visualisées dans le portail Azure.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Suivi distribué

Ajoutez `spring-cloud-starter-sleuth` et `spring-cloud-starter-zipkin` dans la section des dépendances de votre fichier pom.xml, comme ci-dessous. Vous devez aussi activer une instance Azure Application Insights pour fonctionner avec votre instance du service Azure Spring Cloud. Découvrez plus d’informations sur l’activation d’Application Insights avec Azure Spring Cloud [ici](spring-cloud-tutorial-distributed-tracing.md)

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment configurer votre application Spring Java pour la déployer sur Azure Spring Cloud.  Pour découvrir comment activer le serveur de configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Découvrez comment configurer votre serveur de configuration](spring-cloud-tutorial-config-server.md).

D’autres exemples sont disponibles sur GitHub : [Exemples Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
