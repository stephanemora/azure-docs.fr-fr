---
title: Démarrage rapide pour ajouter des indicateurs de fonctionnalité à Spring Boot - Azure App Configuration | Microsoft Docs
description: Guide de démarrage rapide pour ajouter des indicateurs de fonctionnalité à des applications Spring Boot et leur gestion dans Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Spring Boot
ms.workload: tbd
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 3e82354116969b01743700485b5c2dd75b4887e4
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310048"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Démarrage rapide : Ajouter des indicateurs de fonctionnalité à une application Spring Boot

Dans ce guide de démarrage rapide, vous incorporez Azure App Configuration à une application web Spring Boot pour créer une implémentation de bout en bout de la gestion des fonctionnalités. Vous pouvez utiliser le service App Configuration pour stocker de manière centralisée tous vos indicateurs de fonctionnalités et contrôler leur état.

Les bibliothèques de gestion des fonctionnalités Spring Boot étendent le framework avec une prise en charge complète des indicateurs de fonctionnalités. Ces bibliothèques **n’ont pas** de dépendance envers les bibliothèques Azure. Elles s’intègrent de manière fluide à App Configuration par le biais de son fournisseur de configuration Spring Boot.

## <a name="prerequisites"></a>Conditions préalables requises

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- Un [kit SDK de développement Java](https://docs.microsoft.com/java/azure/jdk) pris en charge avec version 8
- [Apache Maven](https://maven.apache.org/download.cgi) version 3.0 ou ultérieure

## <a name="create-an-app-configuration-store"></a>Créer un magasin App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Sélectionnez **Gestionnaire de fonctionnalités** >  **+ Créer** pour ajouter les indicateurs de fonctionnalités suivants :

    | Clé | State |
    |---|---|
    | Bêta | Off |

## <a name="create-a-spring-boot-app"></a>Créer une application Spring Boot

Vous utilisez [Spring Initializr](https://start.spring.io/) pour créer un projet Spring Boot.

1. Accédez à <https://start.spring.io/>.

2. Spécifiez les options suivantes :

   - Générez un projet **Maven** avec **Java**.
   - Spécifiez une version de **Spring Boot** égale ou supérieure à 2.0.
   - Indiquez les noms du **Groupe** et de l’**Artefact** de votre application.
   - Ajoutez la dépendance **Spring Web**.

3. Une fois les options précédentes spécifiées, sélectionnez **Générer le projet**. Lorsque vous y êtes invité, téléchargez le projet dans un emplacement défini par un chemin d’accès sur votre ordinateur local.

## <a name="add-feature-management"></a>Ajouter la gestion des fonctionnalités

1. Après avoir extrait les fichiers sur votre système local, votre application Spring Boot simple est prête à être modifiée. Localisez le fichier *pom.xml* dans le répertoire racine de votre application.

2. Ouvrez le fichier *pom.xml* dans un éditeur de texte, puis ajoutez le démarrage de Spring Cloud Azure Config et la gestion des fonctionnalités à la liste de `<dependencies>` :

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.0</version>
    </dependency>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Il existe une bibliothèque de gestion des fonctionnalités non web qui n’a pas de dépendance envers spring-web. Consultez la [documentation](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management) supplémentaire pour connaître les différences. En outre, si vous n’utilisez pas App Configuration, consultez [Déclaration d’indicateur de fonctionnalité](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management#feature-flag-declaration).

## <a name="connect-to-an-app-configuration-store"></a>Se connecter à un magasin App Configuration

1. Ouvrez _bootstrap.properties_ sous le répertoire _resources_ de votre application. Si _bootstrap.properties_ n’existe pas, créez-le. Ajoutez la ligne suivante au fichier.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Dans le portail App Configuration pour votre magasin de configuration, accédez à Clés d’accès. Sélectionnez l’onglet Clés en lecture seule. Sous cet onglet, copiez la valeur de l’une des chaînes de connexion et ajoutez-la en tant que nouvelle variable d’environnement avec le nom de variable `APP_CONFIGURATION_CONNECTION_STRING`.

1. Ouvrez le fichier Java de l’application principale et ajoutez `@EnableConfigurationProperties` pour activer cette fonctionnalité.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Créez un fichier Java nommé *MessageProperties.java* dans le répertoire du package de votre application. Ajoutez les lignes suivantes :

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

1. Créez un fichier Java nommé *HelloController.java* dans le répertoire du package de votre application. Ajoutez les lignes suivantes :

    ```java
    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabled("Beta"));
            return "welcome";
        }
    }
    ```

1. Créez un fichier HTML nommé *welcome.html* dans le répertoire de modèles de votre application. Ajoutez les lignes suivantes :

    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Feature Management with Spring Cloud Azure</title>

        <link rel="stylesheet" href="/css/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    </head>
    <body>
        <header>
        <!-- Fixed navbar -->
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <a class="navbar-brand" href="#">TestFeatureFlags</a>
            <button class="navbar-toggler" aria-expanded="false" aria-controls="navbarCollapse" aria-label="Toggle navigation" type="button" data-target="#navbarCollapse" data-toggle="collapse">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
                </li>
                <li class="nav-item" th:if="${Beta}">
                <a class="nav-link" href="#">Beta</a>
                </li>
                <li class="nav-item">
                <a class="nav-link" href="#">Privacy</a>
                </li>
            </ul>
            </div>
        </nav>
        </header>
        <div class="container body-content">
            <h1 class="mt-5">Welcome</h1>
            <p>Learn more about <a href="https://github.com/microsoft/spring-cloud-azure/blob/master/spring-cloud-azure-feature-management/README.md">Feature Management with Spring Cloud Azure</a></p>

        </div>
        <footer class="footer">
            <div class="container">
            <span class="text-muted">&copy; 2019 - Projects</span>
        </div>

        </footer>
    </body>
    </html>

    ```

1. Créez un dossier nommé CSS sous « static », puis dans ce dossier, créez un fichier CSS nommé *main.css*. Ajoutez les lignes suivantes :

    ```css
    html {
    position: relative;
    min-height: 100%;
    }
    body {
    margin-bottom: 60px;
    }
    .footer {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 60px;
    line-height: 60px;
    background-color: #f5f5f5;
    }

    body > .container {
    padding: 60px 15px 0;
    }

    .footer > .container {
    padding-right: 15px;
    padding-left: 15px;
    }

    code {
    font-size: 80%;
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Générez votre application Spring Boot avec Maven, puis exécutez-la. Par exemple :

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

2. Ouvrez une fenêtre de navigateur, puis accédez à `https://localhost:8080`, qui est l’URL par défaut de l’application web hébergée localement.

    ![Démarrage rapide du lancement d’application local](./media/quickstarts/spring-boot-feature-flag-local-before.png)

3. Dans le portail App Configuration, sélectionnez **Gestionnaire de fonctionnalités**, puis changez la valeur de la clé **Bêta** en **On** :

    | Clé | State |
    |---|---|
    | Bêta | Il en va |

4. Actualisez la page de navigateur pour afficher les nouveaux paramètres de configuration.

    ![Démarrage rapide du lancement d’application local](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un magasin App Configuration et vous l’avez utilisé pour gérer les fonctionnalités dans une application web Spring Boot via les [bibliothèques de gestion des fonctionnalités](https://go.microsoft.com/fwlink/?linkid=2074664).

- Découvrez plus d’informations sur la [gestion des fonctionnalités](./concept-feature-management.md).
- [Gérer les indicateurs de fonctionnalité](./manage-feature-flags.md).
- [Utiliser des indicateurs de fonctionnalité dans une application Spring Boot](./use-feature-flags-spring-boot.md).
