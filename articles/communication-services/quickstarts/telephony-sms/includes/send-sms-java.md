---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/12/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: 2739079b67d80f3e4a9f367aaa58f6dcbbb650ca
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622195"
---
Commencez avec Azure Communication Services en utilisant la bibliothèque de client Communication Services SMS Java pour envoyer des SMS.

Le fait de suivre ce guide de démarrage rapide entraîne une petite dépense de quelques cents USD tout au plus dans votre compte Azure.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) version 8 ou ultérieure.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un numéro de téléphone permettant de recevoir des SMS. [Obtenez un numéro de téléphone](../get-phone-number.md).

### <a name="prerequisite-check"></a>Vérification du prérequis

- Dans une fenêtre de terminal ou de commande, exécutez `mvn -v` pour vérifier que Maven est installé.
- Pour voir les numéros de téléphone associés à votre ressource Communication Services, connectez-vous au [portail Azure](https://portal.azure.com/), localisez votre ressource Communication Services, puis ouvrez l’onglet **Phone Numbers** (Numéros de téléphone) dans le volet de navigation gauche.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-java-application"></a>Créer une application Java

Ouvrez votre terminal ou votre fenêtre Commande, puis accédez au répertoire dans lequel vous souhaitez créer votre application Java. Exécutez la commande ci-dessous pour générer le projet Java à partir du modèle maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

L’objectif « generate » crée un répertoire portant le même nom que l’artifactId. Sous ce répertoire, le répertoire **src/main/java** contient le code source du projet, le répertoire **src/test/java** contient la source de test, et le fichier **pom.xml** est le modèle d’objet du projet, ou modèle POM.

### <a name="install-the-package"></a>Installer le package

Ouvrez le fichier **pom.xml** dans votre éditeur de texte. Ajoutez l’élément dépendance suivant au groupe de dépendances.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.4</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

Ajoutez la dépendance `azure-core-http-netty` à votre fichier **pom.xml**.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.8.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core</artifactId>
    <version>1.13.0</version> <!-- {x-version-update;com.azure:azure-core;dependency} -->
</dependency>
```

Ouvrez **/src/main/java/com/communication/quickstart/App.java** dans un éditeur de texte, ajoutez des directives d’importation, puis supprimez l’instruction `System.out.println("Hello world!");` :

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque de client Azure Communication Services SMS pour Java.

| Nom                                                             | Description                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Cette classe crée le SmsClient. Vous lui fournissez un point de terminaison, des informations d’identification et un client HTTP. |
| SmsClient                    | Cette classe est nécessaire pour toutes les fonctionnalités SMS. Vous l’utilisez pour envoyer des SMS.                |
| SmsSendResult                | Cette classe contient le résultat du service SMS.                                          |
| SmsSendOptions               | Cette classe fournit des options pour ajouter des balises personnalisées et configurer la remise de rapports. Si deliveryReportEnabled est défini sur true, un événement est émis quand la remise a réussi|                           |

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un `SmsClient` avec votre chaîne de connexion. (Les informations d’identification sont la `Key` du portail Azure. Découvrez comment [gérer la chaîne de connexion de la ressource](../../create-communication-resource.md#store-your-connection-string).

Ajoutez le code suivant à la méthode `main` :

```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .httpClient(httpClient)
                .buildClient();
```

Vous pouvez initialiser le client avec n’importe quel client HTTP personnalisé qui implémente l’interface `com.azure.core.http.HttpClient`. Le code ci-dessus illustre l’utilisation du [client HTTP Netty Azure Core](/java/api/overview/azure/core-http-netty-readme) qui est fourni par `azure-core`.

Vous pouvez également fournir la chaîne de connexion entière à l’aide de la fonction connectionString() au lieu de fournir le point de terminaison et la clé d’accès. 
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .httpClient(httpClient)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>Envoyer un message SMS 1:1

Pour envoyer un message SMS à un seul destinataire, appelez la méthode `send` à partir du SmsClient avec un numéro de téléphone de destinataire unique. Vous pouvez également transmettre des paramètres facultatifs pour spécifier si le rapport de remise doit être activé et pour définir des balises personnalisées.

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```
## <a name="send-a-1n-sms-message-with-options"></a>Envoyer un message SMS 1:N avec des options
Pour envoyer un message SMS à une liste de destinataires, appelez la méthode `send` avec une liste de numéros de téléphone de destinataire. Vous pouvez également transmettre des paramètres facultatifs pour spécifier si le rapport de remise doit être activé et pour définir des balises personnalisées.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.send(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE);

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

Vous devez remplacer `<from-phone-number>` par un numéro de téléphone permettant de recevoir des SMS et associé à votre ressource Communication Services, et `<to-phone-number>` par le numéro de téléphone ou une liste de numéros auxquels vous souhaitez envoyer un message.

## <a name="optional-parameters"></a>Paramètres facultatifs

Le paramètre `deliveryReportEnabled` est un paramètre facultatif que vous pouvez utiliser pour configurer la création de rapports de remise. C’est utile pour les scénarios où vous souhaitez émettre des événements quand des SMS sont remis. Consultez le guide de démarrage rapide [Gérer les événements SMS](../handle-sms-events.md) pour configurer la création de rapports de remise pour vos SMS.

Le paramètre `tag` est un paramètre facultatif que vous pouvez utiliser pour appliquer une balise au rapport de remise.

## <a name="run-the-code"></a>Exécuter le code

Accédez au répertoire contenant le fichier **pom.xml**, puis compilez le projet à l’aide de la commande `mvn`.

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