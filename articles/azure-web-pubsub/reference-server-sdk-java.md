---
title: Référence – Kit de développement logiciel (SDK) Java Server pour le service Azure Web PubSub
description: La référence décrit le kit de développement logiciel (SDK) de serveur Java pour le service Azure Web PubSub
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: b36bf3715458fdf66d8c1a113e96e048bd414d0a
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115860"
---
# <a name="java-server-sdk-for-azure-web-pubsub-service"></a>Kit de développement logiciel (SDK) Java Server pour le service Azure Web PubSub

Utilisez la bibliothèque pour :

- Envoyer des messages à des hubs et des groupes.
- Envoyer des messages à des utilisateurs et des connexions particuliers.
- Organiser des utilisateurs et des connexions en groupes.
- Fermer les connexions
- Accorder/révoquer/vérifier les autorisations pour une connexion existante

[Code source][source_code] | [Documentation de référence de l’API][api] | [Documentation du produit][product_documentation] | [Exemples][samples_readme]

## <a name="getting-started"></a>Prise en main

### <a name="prerequisites"></a>Prérequis

- Un [Kit de développement Java (JDK)][jdk_link], version 8 ou ultérieure.
- [Abonnement Azure][azure_subscription]

### <a name="include-the-package"></a>Inclure le package

[//]: # ({x-version-update-start;com.azure:azure-messaging-webpubsub;current})

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-webpubsub</artifactId>
    <version>1.0.0-beta.2</version>
</dependency>
```

[//]: # ({x-version-update-end})

### <a name="create-a-web-pubsub-client-using-connection-string"></a>Créer un client Web PubSub à l’aide d’une chaîne de connexion

```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .connectionString("{connection-string}")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-web-pubsub-client-using-access-key"></a>Créer un client Web PubSub à l’aide d’une clé d’accès

```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .endpoint("<Insert endpoint from Azure Portal>")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-web-pubsub-group-client"></a>Créer un client de groupe Web PubSub
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .hub("chat")
    .buildClient();
WebPubSubGroup javaGroup = webPubSubServiceClient.getGroup("java");
```

## <a name="key-concepts"></a>Concepts clés

[!INCLUDE [Terms](includes/terms.md)]


## <a name="examples"></a>Exemples

### <a name="broadcast-message-to-entire-hub"></a>Diffuser un message à l’ensemble d’un hub

```java
webPubSubServiceClient.sendToAll("Hello world!");
```

### <a name="broadcast-message-to-a-group"></a>Diffusez un message à un groupe

```java
WebPubSubGroup javaGroup = webPubSubServiceClient.getGroup("Java");
javaGroup.sendToAll("Hello Java!");
```

### <a name="send-message-to-a-connection"></a>Envoyer un message à une connexion

```java
webPubSubServiceClient.sendToConnection("myconnectionid", "Hello connection!");
```

### <a name="send-message-to-a-user"></a>Envoyer un message à un utilisateur
```java
webPubSubServiceClient.sendToUser("Andy", "Hello Andy!");
```

## <a name="troubleshooting"></a>Dépannage

### <a name="enable-client-logging"></a>Activer la journalisation du client
Vous pouvez définir la variable d’environnement `AZURE_LOG_LEVEL` pour afficher les instructions de journalisation figurant dans la bibliothèque de client. Par exemple, le paramètre `AZURE_LOG_LEVEL=2` affiche tous les messages d’information, d’avertissement et de journal des erreurs. Les niveaux de journal se trouvent ici : [niveaux de journal][log_levels].

### <a name="default-http-client"></a>Client HTTP par défaut
Toutes les bibliothèques de client utilisent par défaut le client HTTP Netty. L’ajout de la dépendance ci-dessus configure automatiquement la bibliothèque de client pour utiliser le client HTTP Netty. La configuration ou la modification du client HTTP sont détaillées dans le [wiki pour clients HTTP](/azure/developer/java/sdk/http-client-pipeline).

### <a name="default-ssl-library"></a>Bibliothèque SSL par défaut
Toutes les bibliothèques de client utilisent par défaut la bibliothèque BoringSSL Tomcat native pour permettre des performances de niveau natif pour les opérations SSL. La bibliothèque BoringSSL est un fichier uber jar contenant des bibliothèques natives pour Linux/macOS/Windows. Elle offre de meilleures performances que l’implémentation SSL par défaut au sein du JDK. Pour plus d’informations, notamment sur la réduction de la taille de dépendance, consultez la section [performance tuning][performance_tuning] du wiki.

[azure_subscription]: https://azure.microsoft.com/free
[jdk_link]: /java/azure/jdk
[source_code]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/webpubsub/azure-messaging-webpubsub/src
[product_documentation]: https://aka.ms/awps/doc
[samples_readme]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java
[log_levels]: https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/logging/ClientLogger.java
[performance_tuning]: https://github.com/Azure/azure-sdk-for-java/wiki/Performance-Tuning
[api]: /java/api/com.azure.messaging.webpubsub

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]
