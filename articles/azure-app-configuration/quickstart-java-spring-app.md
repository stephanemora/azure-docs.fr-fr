---
title: Démarrage rapide pour apprendre à utiliser Azure App Configuration | Microsoft Docs
description: Guide de démarrage rapide pour utiliser Azure App Configuration avec des applications Java Spring.
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: e8f6f9ca610c515deca6ed1bdbee54f40cacf427
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184935"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Démarrage rapide : Créer une application Java Spring avec Azure App Configuration

Dans ce guide de démarrage rapide, vous intégrez Azure App Configuration dans une application Java Spring pour centraliser le stockage et la gestion des paramètres d’application en dehors de votre code.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- Un [kit de développement Java (JDK)](https://docs.microsoft.com/java/azure/jdk) pris en charge avec version 8
- [Apache Maven](https://maven.apache.org/download.cgi) version 3.0 ou ultérieure

## <a name="create-an-app-configuration-store"></a>Créer un magasin App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Sélectionnez **Explorateur de configurations** >  **+ Créer** pour ajouter les paires clé-valeur suivantes :

    | Clé | Valeur |
    |---|---|
    | /application/config.message | Hello |

    Laissez **Étiquette** et **Type de contenu** vides pour l’instant.

## <a name="create-a-spring-boot-app"></a>Créer une application Spring Boot

Vous utilisez [Spring Initializr](https://start.spring.io/) pour créer un projet Spring Boot.

1. Accédez à <https://start.spring.io/>.

2. Spécifiez les options suivantes :

   * Générez un projet **Maven** avec **Java**.
   * Spécifiez une version de **Spring Boot** égale ou supérieure à 2.0.
   * Indiquez les noms du **Groupe** et de l’**Artefact** de votre application.
   * Ajoutez la dépendance **Web**.

3. Une fois les options précédentes spécifiées, sélectionnez **Générer le projet**. Lorsque vous y êtes invité, téléchargez le projet dans un emplacement défini par un chemin d’accès sur votre ordinateur local.

## <a name="connect-to-an-app-configuration-store"></a>Se connecter à un magasin App Configuration

1. Après avoir extrait les fichiers sur votre système local, votre application Spring Boot simple est prête à être modifiée. Localisez le fichier *pom.xml* dans le répertoire racine de votre application.

2. Ouvrez le fichier *pom.xml* dans un éditeur de texte, puis ajoutez le démarrage de Spring Cloud Azure Config à la liste de `<dependencies>` :

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M5</version>
    </dependency>
    ```

3. Créez un fichier Java nommé *MessageProperties.java* dans le répertoire du package de votre application. Ajoutez les lignes suivantes :

    ```java
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

4. Créez un fichier Java nommé *HelloController.java* dans le répertoire du package de votre application. Ajoutez les lignes suivantes :

    ```java
    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

5. Ouvrez le fichier Java de l’application principale et ajoutez `@EnableConfigurationProperties` pour activer cette fonctionnalité.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Créez un fichier nommé `bootstrap.properties` sous le répertoire des ressources de votre application, puis ajoutez les lignes suivantes au fichier. Remplacez les exemples de valeurs par les propriétés appropriées pour votre magasin App Configuration.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Générez votre application Spring Boot avec Maven, puis exécutez-la. Par exemple :

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Lorsque votre application s’exécute, utilisez *curl* pour la tester. Par exemple :

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Vous voyez le message que vous avez entré dans le magasin App Configuration.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

En suivant ce guide de démarrage rapide, vous avez créé un magasin App Configuration et vous l’avez utilisé avec une application Java Spring. Pour plus d’informations, consultez [Spring sur Azure](https://docs.microsoft.com/java/azure/spring-framework/). Pour savoir comment utiliser une identité managée Azure afin de simplifier l’accès à App Configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
