---
ms.openlocfilehash: a72147e84383a461c86339483d7e621c97fb5680
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111429815"
---
## <a name="additional-prerequisites-for-java"></a>Autres composants requis pour Java
Pour Java, vous aurez également besoin des éléments suivants :
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install) version 8 ou ultérieure.
- [Apache Maven](https://maven.apache.org/download.cgi).

> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/use-managed-Identity).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-java-application"></a>Créer une application Java

Ouvrez votre fenêtre de terminal ou de commande. Accédez au répertoire dans lequel vous souhaitez créer votre application Java. Exécutez la commande ci-dessous pour générer le projet Java à partir du modèle maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Vous remarquerez que la tâche « generate » a créé un répertoire portant le même nom que l’`artifactId`. Sous ce répertoire, le répertoire src/main/java contient le code source du projet, `src/test/java directory` contient la source de test, et le fichier `pom.xml` est le modèle objet du projet (le POM).

### <a name="install-the-package"></a>Installer le package

Ouvrez le fichier **pom.xml** dans votre éditeur de texte. Ajoutez l’élément dépendance suivant au groupe de dépendances.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="use-the-sdk-packages"></a>Utiliser les packages du kit SDK

Ajoutez les directives `import` suivantes à votre code pour utiliser les kits SDK Azure Identity et Azure Communication.

```java
import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.communication.sms.models.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.util.*;
```

## <a name="create-a-defaultazurecredential"></a>Créer un DefaultAzureCredential

Nous utiliserons le [DefaultAzureCredential](/java/api/com.azure.identity.defaultazurecredential) pour ce démarrage rapide. Ces informations d’identification sont appropriées pour les environnements de production et de développement. Comme elles sont nécessaires pour chaque opération, créons-les dans la classe `App.java`. Ajoutez le code suivant au début de la classe `App.java`.

```java
private TokenCredential credential = new DefaultAzureCredentialBuilder().build();
```

## <a name="issue-a-token-with-managed-identities"></a>Émettre un jeton avec des identités managées

Nous allons maintenant ajouter du code qui utilise les informations d’identification créées pour émettre un jeton d’accès VoIP. Nous appellerons ce code plus tard :

```java
    public AccessToken createIdentityAndGetTokenAsync(String endpoint) {
          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          return communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
    }
```

## <a name="send-an-sms-with-managed-identities"></a>Envoyer un SMS avec des identités managées

Comme autre exemple d’utilisation des identités managées, nous allons ajouter ce code qui utilise les mêmes informations d’identification pour envoyer un SMS :

```java
     public SmsSendResult sendSms(String endpoint, String from, String to, String message) {
          SmsClient smsClient = new SmsClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          // Send the message and check the response for a message id
          return smsClient.send(from, to, message);
     }
```
## <a name="write-the-main-method"></a>Écrire la méthode main

Votre `App.java` doit déjà disposer d’une méthode main. Ajoutons du code qui appellera notre code précédemment créé pour démontrer l’utilisation des identités managées :
```java
    public static void main(String[] args) {
          App instance = new App();
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com/";

          System.out.println("Retrieving new Access Token, using Managed Identities");
          AccessToken token = instance.createIdentityAndGetTokenAsync(endpoint);
          System.out.println("Retrieved Access Token: "+ token.getToken());

          System.out.println("Sending SMS using Managed Identities");
          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
          System.out.println("Sms id: "+ result.getMessageId());
          System.out.println("Send Result Successful: "+ result.isSuccessful());
    }
```

Votre `App.java` finale doit ressembler à ceci :

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.communication.sms.models.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.util.*;

public class App 
{

    private TokenCredential credential = new DefaultAzureCredentialBuilder().build();

    public SmsSendResult sendSms(String endpoint, String from, String to, String message) {
          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(this.credential)
               .buildClient();

          // Send the message and check the response for a message id
          return smsClient.send(from, to, message);
    }
    public AccessToken createIdentityAndGetTokenAsync(String endpoint) {
          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          return communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
    }

    public static void main(String[] args) {
          App instance = new App();
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com/";

          System.out.println("Retrieving new Access Token, using Managed Identities");
          AccessToken token = instance.createIdentityAndGetTokenAsync(endpoint);
          System.out.println("Retrieved Access Token: "+ token.getToken());

          System.out.println("Sending SMS using Managed Identities");
          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
          System.out.println("Sms id: "+ result.getMessageId());
          System.out.println("Send Result Successful: "+ result.isSuccessful());
    }
}
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

La sortie finale doit ressembler à ce qui suit :
```
Retrieving new Access Token, using Managed Identities
Retrieved Access Token: ey..A
Sending SMS using Managed Identities
Sms id: Outgoing_202104...33f8ae1f_noam
Send Result Successful: true
```