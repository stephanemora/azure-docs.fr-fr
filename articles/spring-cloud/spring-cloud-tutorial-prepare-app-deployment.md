---
title: Tutoriel - Préparer le déploiement d’une application Spring dans Azure Spring Cloud
description: Dans ce tutoriel, vous préparez le déploiement d’une application Spring Java.
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 9918c7866b21cd2a9e021a355fb43977c91a89cf
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277443"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Préparer une application Spring Java pour le déploiement dans Azure Spring Cloud

Ce guide de démarrage rapide vous montre comment préparer une application Spring Cloud Java existante afin de la déployer sur Azure Spring Cloud. S’il est correctement configuré, Azure Spring Cloud fournit des services robustes pour superviser, mettre à l’échelle et mettre à jour votre application Java Spring Cloud.

## <a name="java-runtime-version"></a>Version du runtime Java

Seules les applications Spring/Java peuvent s’exécuter dans Azure Spring Cloud.

Azure Spring Cloud prend en charge Java 8 et Java 11. L’environnement d’hébergement contient la dernière version d’Azul Zulu OpenJDK pour Azure. Pour plus d’informations sur Azul Zulu OpenJDK pour Azure, consultez [Installer le JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versions de Spring Boot et de Spring Cloud

Seules les applications Spring Boot sont prises en charge dans Azure Spring Cloud. Les versions 2.0 et 2.1 de Spring Boot sont prises en charge. Le tableau ci-dessous liste les combinaisons prises en charges de Spring Boot et Spring Cloud :

Version de Spring Boot | Version de Spring Cloud
---|---
2 | Finchley.RELEASE
2.1 | Greenwich.RELEASE

Vérifiez que votre fichier pom.xml comprend les dépendances Spring Boot et Spring Cloud qui correspondent à votre version de Spring Boot.

### <a name="dependencies-for-spring-boot-version-20"></a>Dépendances pour Spring Boot version 2.0

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

### <a name="dependencies-for-spring-boot-version-21"></a>Dépendances pour Spring Boot version 2.1

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

Azure Spring Cloud héberge et gère pour vous les composants Spring Cloud. Ces composants sont notamment Spring Cloud Service Registry et Spring Cloud Config Server. Ajoutez la bibliothèque cliente d’Azure Spring Cloud à vos dépendances pour permettre la communication avec votre instance du service Azure Spring Cloud.

Le tableau suivant liste les versions d’Azure Spring Cloud qui sont compatibles avec les applications qui utilisent Spring Boot et Spring Cloud.

Version de Spring Boot | Version de Spring Cloud | Version d’Azure Spring Cloud
---|---|---
2 | Finchley.RELEASE | 2
2.1 | Greenwich.RELEASE | 2.1

Ajoutez l’une des dépendances suivantes à votre fichier pom.xml. Sélectionnez la dépendance dont la version d’Azure Spring Cloud correspond à la vôtre.

### <a name="dependency-for-azure-spring-cloud-version-20"></a>Dépendance pour Azure Spring Cloud version 2.0

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Dépendance pour Azure Spring Cloud version 2.1

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Autres dépendances nécessaires

Pour activer les fonctionnalités intégrées d’Azure Spring Cloud, votre application doit inclure les dépendances suivantes. Ceci garantit que votre application se configure elle-même correctement avec chaque composant.  

### <a name="service-registry-dependency"></a>Dépendance Service Registry

Pour utiliser le service managé Azure Service Registry, ajoutez la dépendance `spring-cloud-starter-netflix-eureka-client` dans le fichier pom.xml, comme indiqué ici :

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Le point de terminaison du serveur Service Registry est injecté automatiquement sous la forme de variables d’environnement avec votre application. Les applications peuvent alors s’inscrire automatiquement auprès du serveur Service Registry et découvrir d’autres microservices dépendants.

### <a name="distributed-configuration-dependency"></a>Dépendance de la configuration distribuée

Pour activer la configuration distribuée, ajoutez la dépendance `spring-cloud-config-client` suivante dans la section des dépendances de votre fichier pom.xml :

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ne spécifiez pas `spring.cloud.config.enabled=false` dans votre configuration de démarrage. Si vous le faites, votre application cessera de fonctionner avec le serveur de configuration.

### <a name="metrics-dependency"></a>Dépendance des métriques

Ajoutez la dépendance `spring-boot-starter-actuator` dans la section des dépendances de votre fichier pom.xml, comme indiqué ici :

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Les métriques sont tirées (pull) périodiquement des points de terminaison JMX. Vous pouvez visualiser les métriques dans le portail Azure.

### <a name="distributed-tracing-dependency"></a>Dépendance du suivi distribué

Ajoutez les dépendances `spring-cloud-starter-sleuth` et `spring-cloud-starter-zipkin` suivantes dans la section des dépendances de votre fichier pom.xml :

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

 Vous devez également permettre à une instance Azure Application Insights de fonctionner avec votre instance du service Azure Spring Cloud. Pour savoir comment utiliser Application Insights avec Azure Spring Cloud, lisez le [tutoriel sur le suivi distribué](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment configurer votre application Spring Java pour la déployer sur Azure Spring Cloud. Pour savoir comment configurer une instance Config Server, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [En savoir plus sur la configuration d’une instance Config Server](spring-cloud-tutorial-config-server.md)

D’autres exemples sont disponibles sur GitHub : [Exemples Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
