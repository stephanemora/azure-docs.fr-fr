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
ms.openlocfilehash: d607d6cd813b23051e1676153cbb134261bcf5bc
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960607"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Démarrage rapide : Créer une application Java Spring avec App Configuration

Azure App Configuration est un service de configuration managée dans Azure. Il vous permet de stocker et gérer facilement tous les paramètres de votre application à un seul endroit, indépendamment de votre code. Ce guide de démarrage rapide vous montre comment intégrer le service à une application Java Spring.

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce démarrage rapide. Toutefois, [Visual Studio Code](https://code.visualstudio.com/) est une excellente option disponible sur les plateformes Windows, macOS et Linux.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, installez [Java Development Kit (JDK)](https://aka.ms/azure-jdks) version 8 et [Apache Maven](http://maven.apache.org/) version 3.0 ou ultérieure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Créer un magasin de configuration d’application

1. Pour créer un magasin de configuration d’application, commencez par vous connecter au [portail Azure](https://aka.ms/azconfig/portal). Dans le coin supérieur gauche de la page, cliquez sur **+ Créer une ressource**. Dans la zone de texte **Rechercher dans la Place de marché**, tapez **App Configuration** et appuyez sur **Entrée**.

    ![Rechercher App Configuration](./media/quickstarts/azure-app-configuration-new.png)

2. Cliquez sur **App Configuration** dans les résultats de la recherche, puis sur **Créer**.

3. Dans la page **App Configuration** > **Créer**, entrez les paramètres suivants :

    | Paramètre | Valeur suggérée | Description |
    |---|---|---|
    | **Nom de la ressource** | Nom globalement unique | Entrez un nom de ressource unique à utiliser pour la ressource du magasin de configuration d’application. Le nom doit être une chaîne de 1 à 63 caractères et contenir uniquement des chiffres, des lettres et le caractère `-`. Le nom ne peut ni commencer ni se terminer par le caractère `-` et il n’accepte pas de caractères `-` consécutifs.  |
    | **Abonnement** | Votre abonnement | Sélectionnez l’abonnement Azure à utiliser pour tester App Configuration. Si votre compte a un seul abonnement, il est automatiquement sélectionné et la liste déroulante **Abonnement** ne s’affiche pas. |
    | **Groupe de ressources** | *AppConfigTestResources* | Sélectionnez ou créez un groupe de ressources pour votre ressource du magasin de configuration d’application. Ce groupe est utile pour organiser plusieurs ressources que vous pouvez supprimer en même temps en supprimant le groupe de ressources. Pour plus d'informations, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
    | **Lieu** | *USA Centre* | Utilisez **Emplacement** pour indiquer l’emplacement géographique de l’hébergement de votre ressource SignalR. Pour des performances optimales, nous vous recommandons de créer la ressource dans la même région que les autres composants de votre application. |

    ![Créer un magasin de configuration d’application](./media/quickstarts/azure-app-configuration-create.png)

4. Cliquez sur **Créer**. Le déploiement peut prendre quelques minutes.

5. Une fois le déploiement terminé, cliquez sur **Paramètres** > **Clés d’accès**. Notez la chaîne de connexion de la clé primaire en lecture seule et en lecture-écriture. Vous allez l’utiliser plus tard pour configurer votre application de sorte qu’elle communique avec le magasin de configuration d’application que vous venez de créer. La chaîne de connexion prend la forme suivante :

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    Vous devez utiliser la chaîne entière dans votre application.

6. Cliquez sur **Explorateur de paires clé-valeur** et sur **+ Créer** pour ajouter les paires clé-valeur suivantes :

    | Clé | Valeur |
    |---|---|
    | /application/config.message | Hello |

    Laissez **Étiquette** et **Type de contenu** vides pour l’instant.

## <a name="create-a-spring-boot-app"></a>Créer une application Spring Boot

Vous allez utiliser [Spring Initializr](https://start.spring.io/) pour créer un projet Spring Boot.

1. Accédez à <https://start.spring.io/>.

2. Spécifiez les options suivantes :

   * Générez un projet **Maven** avec **Java**.
   * Spécifiez une version de **Spring Boot** égale ou supérieure à 2.0.
   * Indiquez les noms du **Groupe** et de l’**Artefact** de votre application.
   * Ajoutez la dépendance **Web**.

3. Une fois les options ci-dessus définies, cliquez sur **Générer le projet**. Lorsque vous y êtes invité, téléchargez le projet dans un emplacement défini par un chemin d’accès sur votre ordinateur local.

## <a name="connect-to-app-configuration-store"></a>Se connecter au magasin de configuration d’application

1. Après avoir extrait les fichiers sur votre système local, votre application Spring Boot simple est prête à être modifiée. Localisez le fichier *pom.xml* dans le répertoire racine de votre application.

2. Ouvrez le fichier *pom.xml* dans un éditeur de texte, puis ajoutez le démarrage de Spring Cloud Azure Config à la liste de `<dependencies>` :

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.RC2</version>
    </dependency>
    ```

3. Créez un fichier Java nommé *MessageProperties.java* dans le répertoire du package de votre application. Ajoutez les lignes suivantes.

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

4. Créez un fichier Java nommé *HelloController.java* dans le répertoire du package de votre application. Ajoutez les lignes suivantes.

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

6. Créez un fichier nommé `bootstrap.yaml` sous le répertoire des ressources de votre application, puis ajoutez les lignes suivantes au fichier et remplacez les exemples de valeurs par les propriétés qui conviennent à votre magasin de configuration d’application.

    ```yaml
    spring:
        cloud:
            azure:
                config:
                    stores:
                        - connection-string: [your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Générez votre application Spring Boot avec Maven, puis exécutez-la. Par exemple :

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Une fois votre application exécutée, vous pouvez utiliser *curl* pour tester votre application. Par exemple :

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Vous devez voir le message que vous avez entré dans le magasin de configuration d’application.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

En suivant ce guide de démarrage rapide, vous avez créé un magasin de configuration d’application et vous l’avez utilisé avec une application Java Spring. Visitez [Spring dans la page d’accueil Azure](https://docs.microsoft.com/java/azure/spring-framework/) pour plus d’informations.

Pour en savoir plus sur l’utilisation d’App Configuration, passez au tutoriel suivant pour découvrir l’authentification.

> [!div class="nextstepaction"]
> [Identités managées pour l’intégration des ressources Azure](./integrate-azure-managed-service-identity.md)