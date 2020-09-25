---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 1235cb299fe887f20dd3f7e47c22eed2b9df6dc9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944571"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) version 8 ou ultérieure.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Une ressource Communication Services déployée et la chaîne de connexion. [Créez une ressource Communication Services](../create-communication-resource.md).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-java-application"></a>Créer une application Java

Ouvrez votre fenêtre de terminal ou de commande, puis accédez au répertoire dans lequel vous souhaitez créer votre application Java. Exécutez la commande ci-dessous pour générer le projet Java à partir du modèle maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Vous remarquerez que la tâche « generate » a créé un répertoire portant le même nom que l’`artifactId`. Sous ce répertoire, le répertoire src/main/java contient le code source du projet, `src/test/java directory` contient la source de test, et le fichier `pom.xml` est le modèle objet du projet (le POM).

### <a name="install-the-package"></a>Installer le package

Ouvrez le fichier **pom.xml** dans votre éditeur de texte. Ajoutez l’élément dépendance suivant au groupe de dépendances.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-administration</artifactId>
    <version>1.0.0-beta.1</version> 
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Accédez au répertoire */src/main/java/com/communication/quickstart*
1. Ouvrez le fichier *App.java* dans votre éditeur
1. Remplacez l’instruction `System.out.println("Hello world!");`
1. Ajoutez des directives `import`.

Utilisez le code suivant pour commencer :

```java
import com.azure.communication.common.CommunicationUser;
import com.azure.communication.administration.models.CommunicationIdentityToken;
import com.azure.communication.administration.CommunicationIdentityClient;
import com.azure.communication.administration.CommunicationIdentityClientBuilder;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - User Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un `CommunicationIdentityClient` avec la clé d’accès et le point de terminaison de votre ressource. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string).

Ajoutez le code suivant à la méthode `main` :

```java
// Your can find your endpoint and access token from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessToken = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationClientCredential(accessToken))
    .httpClient(httpClient)
    .buildClient();
```

Vous pouvez initialiser le client avec n’importe quel client HTTP personnalisé qui implémente l’interface `com.azure.core.http.HttpClient`. Le code ci-dessus illustre l’utilisation du [client HTTP Netty Azure Core](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true) qui est fourni par `azure-core`.

## <a name="create-a-user"></a>Créer un utilisateur

Azure Communication Services gère un répertoire LID (Lightweight Identity Directory). Utilisez la méthode `createUser` pour créer une entrée, avec une valeur `Id` unique, dans le répertoire. Vous devez tenir à jour un mappage entre les utilisateurs de votre application et les identités générées par Communication Services (par exemple, en les stockant dans la base de données de votre serveur d’applications).

```java
CommunicationUser user = communicationIdentityClient.createUser();
System.out.println("\nCreated a user with ID: " + user.getId());
```

## <a name="issue-user-access-tokens"></a>Émettre des jetons d’accès utilisateur

Avec la méthode `issueToken`, émettez un jeton d’accès pour un utilisateur de Communication Services. Si vous ne spécifiez pas le paramètre facultatif `user`, un nouvel utilisateur sera créé et retourné avec le jeton.

```java
// Issue an access token with the "voip" scope for a new user
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(user, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String userId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + userId + ": " + token);
System.out.println(token);
```

Les jetons d’accès utilisateur sont des informations d’identification de courte durée qui doivent être réémises afin d’éviter que les utilisateurs rencontrent des interruptions de service. La propriété de réponse `expiresAt` indique la durée de vie du jeton.

## <a name="revoke-user-access-tokens"></a>Révoquer des jetons d’accès utilisateur

Dans certains cas, il est nécessaire de révoquer explicitement des jetons d’accès utilisateur, par exemple, quand des utilisateurs changent leur mot de passe pour s’authentifier auprès de votre service. Utilisez la méthode `revokeTokens` pour invalider tous les jetons d’accès d’un utilisateur.

```java  
communicationIdentityClient.revokeTokens(user, OffsetDateTime.now());
System.out.println("\nRevoked tokens for the user with ID: " + user.getId());
```

## <a name="delete-a-user"></a>Supprimer un utilisateur

Quand vous supprimez un utilisateur, vous supprimez aussi tous les jetons actifs et vous ne pouvez plus ensuite émettre de jetons pour les identités. De plus, tout le contenu persistant associé à l’utilisateur est également supprimé.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nSuccessfully deleted the identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>Exécuter le code

Accédez au répertoire contenant le fichier *pom.xml*, puis compilez le projet à l’aide de la commande `mvn` suivante.

```console
mvn compile
```

Ensuite, générez le package.

```console
mvn package
```

Exécutez la commande `mvn` suivante pour exécuter l’application.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
