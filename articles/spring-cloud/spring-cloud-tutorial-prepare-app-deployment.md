---
title: Tutoriel - Préparer le déploiement d’une application Java Spring dans Azure Spring Cloud
description: Dans ce tutoriel, vous allez préparer le déploiement d’une application Spring Java sur Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 49ebfec131c8b9fa7b8535163c03eb7cb692790d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200020"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Préparer une application Spring Java pour le déploiement dans Azure Spring Cloud

Ce guide de démarrage rapide vous montre comment préparer une application Java Spring existante afin de la déployer sur Azure Spring Cloud. S’il est correctement configuré, Azure Spring Cloud fournit des services robustes pour superviser, mettre à l’échelle et mettre à jour votre application Java Spring Cloud.

D’autres exemples expliquent comment déployer une application sur Azure Spring Cloud quand le fichier POM est configuré. 
* [Lancer l’application en utilisant le portail Azure](spring-cloud-quickstart-launch-app-portal.md)
* [Lancer l’application en utilisant Azure CLI](spring-cloud-quickstart-launch-app-cli.md)

Cet article explique les dépendances nécessaires et comment les ajouter au fichier POM.

## <a name="java-runtime-version"></a>Version du runtime Java

Seules les applications Spring/Java peuvent s’exécuter dans Azure Spring Cloud.

Azure Spring Cloud prend en charge Java 8 et Java 11. L’environnement d’hébergement contient la dernière version d’Azul Zulu OpenJDK pour Azure. Pour plus d’informations sur Azul Zulu OpenJDK pour Azure, consultez [Installer le JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versions de Spring Boot et de Spring Cloud

Pour préparer une application Spring Boot existante pour un déploiement sur Azure Spring Cloud, incluez les dépendances Spring Boot et Spring Cloud dans le fichier POM de l’application comme indiqué dans les sections suivantes.

Azure Spring Cloud prend en charge seulement les applications Spring Boot version 2.1 et 2.2. Le tableau ci-dessous liste les combinaisons prises en charges de Spring Boot et Spring Cloud :

Version de Spring Boot | Version de Spring Cloud
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Dépendances pour Spring Boot version 2.1

Pour Spring Boot version 2.1, ajoutez les dépendances suivantes au fichier POM de l’application.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Dépendances pour Spring Boot version 2.2

Pour Spring Boot version 2.2, ajoutez les dépendances suivantes au fichier POM de l’application.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Dépendance du client Azure Spring Cloud

Azure Spring Cloud héberge et gère les composants Spring Cloud. Les composants sont notamment Spring Cloud Service Registry et Spring Cloud Config Server. Ajoutez la bibliothèque cliente d’Azure Spring Cloud à vos dépendances pour permettre la communication avec votre instance du service Azure Spring Cloud.

Le tableau suivant liste les versions d’Azure Spring Cloud qui sont compatibles avec les applications qui utilisent Spring Boot et Spring Cloud.

Version de Spring Boot | Version de Spring Cloud | Version d’Azure Spring Cloud
---|---|---
2.1 | Greenwich.RELEASE | 2.1
2.2 | Hoxton.RELEASE | 2.2

Ajoutez l’une des dépendances suivantes à votre fichier pom.xml. Sélectionnez la dépendance dont la version d’Azure Spring Cloud correspond à la vôtre.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Dépendance pour Azure Spring Cloud version 2.1

Pour Spring Boot version 2.1, ajoutez la dépendance suivante au fichier POM de l’application.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Dépendance pour Azure Spring Cloud version 2.2

Pour Spring Boot version 2.2, ajoutez la dépendance suivante au fichier POM de l’application.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Autres dépendances nécessaires

Pour activer les fonctionnalités intégrées d’Azure Spring Cloud, votre application doit inclure les dépendances suivantes. Ceci garantit que votre application se configure elle-même correctement avec chaque composant.

### <a name="enablediscoveryclient-annotation"></a>Annotation EnableDiscoveryClient

Ajoutez l’annotation suivante au code source de l’application.
```java
@EnableDiscoveryClient
```
Par exemple, regardez l’application piggymetrics provenant des exemples précédents :
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

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

## <a name="see-also"></a>Voir aussi
* [Analyser les journaux et les métriques des applications](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Configurer votre serveur de configuration](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Utiliser le suivi distribué avec Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Guide de démarrage rapide Spring](https://spring.io/quickstart)
* [Documentation Spring Boot](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment configurer votre application Spring Java pour la déployer sur Azure Spring Cloud. Pour savoir comment configurer une instance Config Server, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [En savoir plus sur la configuration d’une instance Config Server](spring-cloud-tutorial-config-server.md)

D’autres exemples sont disponibles sur GitHub : [Exemples Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
