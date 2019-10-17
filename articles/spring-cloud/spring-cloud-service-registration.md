---
title: Automatiser le registre et la découverte de services
description: Découvrez comment automatiser la découverte et l’inscription de services à l’aide de Spring Cloud Service Registry.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: jeconnoc
ms.openlocfilehash: 72327e116e498ce0f6881a5c585a08e56c8bf8c2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038672"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Découvrir et inscrire vos services Spring Cloud

La découverte de services est une condition essentielle pour une architecture basée sur des microservices.  La configuration manuelle de chaque client prend du temps et introduit le risque d’erreur humaine.  Spring Cloud Service Registry résout ce problème.  Une fois configuré, un serveur Service Registry contrôle l’inscription et la découverte des services pour les microservices de votre application. Le serveur Service Registry gère un registre des microservices déployés, permet l’équilibrage de charge côté client et découple les fournisseurs de services des clients sans dépendre du système DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Inscrire votre application à l’aide de Spring Cloud Service Registry

Pour que votre application puisse gérer l’inscription et la découverte des services à l’aide de Spring Cloud Service Registry, plusieurs dépendances doivent être incluses dans le fichier *pom.xml* de l’application.

Pour commencer, ajoutez un référentiel de captures instantanées à la section *repository* de votre fichier *pom.xml*.

```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
```

## <a name="include-dependencies"></a>Inclure les dépendances

Ensuite, incluez des dépendances pour *spring-cloud-starter-netflix-eureka-client* et *spring-cloud-starter-azure-spring-cloud-client* à votre fichier *pom.xml*.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Mettre à jour la classe de niveau supérieur

Pour finir, ajoutez une annotation à la classe de niveau supérieur de votre application.

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Le point de terminaison de serveur Spring Cloud Service Registry est injecté en tant que variable d’environnement dans votre application.  Les microservices pourront désormais s’inscrire eux-mêmes auprès du serveur Service Registry et découvrir d’autres microservices dépendants.

Notez que quelques minutes peuvent être nécessaires pour que les modifications se propagent du serveur à tous les microservices.
