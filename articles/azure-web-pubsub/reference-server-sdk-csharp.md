---
title: Informations de référence - Kit de développement logiciel (SDK) de serveur .NET pour le service Azure Web PubSub
description: Les informations de référence décrivent le kit de développement logiciel (SDK) de serveur .NET pour le service Azure Web PubSub
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 4bb4c5d90958a31e26c39ce9d0fe7e22bec769e2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116245"
---
# <a name="net-server-sdk-for-azure-web-pubsub-service"></a>Kit de développement logiciel (SDK) de serveur .NET pour le service Azure Web PubSub

Cette bibliothèque peut servir à effectuer les actions suivantes. Les détails des termes utilisés ici sont décrits dans la section [Concepts clés](#key-concepts).

- Envoyer des messages à des hubs et des groupes. 
- Envoyer des messages à des utilisateurs et des connexions particuliers.
- Organiser des utilisateurs et des connexions en groupes.
- Fermer les connexions
- Accorder, révoquer et vérifier des autorisations pour une connexion existante

[Code source][code] |
[Package][package] |
[Documentation de référence de l’API][api] |
[Documentation du produit](https://aka.ms/awps/doc) |
[Exemples][samples_ref]

## <a name="getting-started"></a>Prise en main
### <a name="install-the-package"></a>Installer le package

Installer la bibliothèque de client pour [NuGet](https://www.nuget.org/) :

```PowerShell
dotnet add package Azure.Messaging.WebPubSub --prerelease
```

### <a name="prerequisites"></a>Prérequis

- Un [abonnement Azure][azure_sub].
- Une instance de service Azure Web PubSub existante.

### <a name="authenticate-the-client"></a>Authentifier le client

Pour interagir avec le service, vous devez créer une instance de la classe WebPubSubServiceClient. Pour que cela soit possible, vous avez besoin de la chaîne de connexion ou d’une clé, à laquelle vous pouvez accéder dans le portail Azure.

### <a name="create-a-webpubsubserviceclient"></a>Créez une classe `WebPubSubServiceClient`

```csharp
var serviceClient = new WebPubSubServiceClient(new Uri("<endpoint>"), "<hub>", new AzureKeyCredential("<access-key>"));
```

## <a name="key-concepts"></a>Concepts clés

[!INCLUDE [Termsc](includes/terms.md)]

## <a name="examples"></a>Exemples

### <a name="broadcast-a-text-message-to-all-clients"></a>Diffuser un message texte à tous les clients

```C# Snippet:WebPubSubHelloWorld
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll("Hello World!");
```

### <a name="broadcast-a-json-message-to-all-clients"></a>Diffuser un message JSON à tous les clients

```C# Snippet:WebPubSubSendJson
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll(RequestContent.Create(
        new
        {
            Foo = "Hello World!",
            Bar = 42
        }),
        ContentType.ApplicationJson);
```

### <a name="broadcast-a-binary-message-to-all-clients"></a>Diffuser un message binaire à tous les clients

```C# Snippet:WebPubSubSendBinary
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

Stream stream = BinaryData.FromString("Hello World!").ToStream();
serviceClient.SendToAll(RequestContent.Create(stream), ContentType.ApplicationOctetStream);
```

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="setting-up-console-logging"></a>Configuration de la journalisation de la console
Vous pouvez également [activer la journalisation de la console](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging) facilement si vous souhaitez approfondir les requêtes que vous formulez sur le service.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp
[code]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/Azure.Messaging.WebPubSub/src
[package]: https://www.nuget.org/packages/Azure.Messaging.WebPubSub
[api]: /dotnet/api/azure.messaging.webpubsub

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]
