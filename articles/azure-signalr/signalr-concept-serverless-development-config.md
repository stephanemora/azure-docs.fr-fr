---
title: Développer et de configurer des applications de Service Azure Functions SignalR
description: Pour plus d’informations sur la façon de développer et de configurer des applications en temps réel sans serveur à l’aide d’Azure Functions et Service Azure SignalR
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569148"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Développement de fonctions Azure et la configuration avec le Service Azure SignalR

Les applications de fonctions Azure peuvent exploiter le [les liaisons de Service Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) pour ajouter des fonctionnalités en temps réel. Applications clientes utilisent disponibles dans plusieurs langues kits de développement logiciel client pour se connecter au Service Azure SignalR et de recevoir des messages en temps réel.

Cet article décrit les concepts pour le développement et la configuration d’une application de fonction Azure qui est intégrée avec le SignalR Service.

## <a name="signalr-service-configuration"></a>Configuration du Service de SignalR

Service Azure SignalR peut être configuré dans des modes différents. Lorsqu’il est utilisé avec Azure Functions, le service doit être configuré dans *Serverless* mode.

Dans le portail Azure, recherchez le *paramètres* page de votre ressource de SignalR Service. Définir le *mode Service* à *Serverless*.

![Mode de Service SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Développement de fonctions Azure

Une application en temps réel sans serveur intégrée avec Azure Functions et de Service Azure SignalR généralement nécessite deux fonctions Azure :

* Une fonction « negotiate » que le client appelle pour obtenir un Service de SignalR valide un jeton d’accès et les URL de point de terminaison du service
* Une ou plusieurs fonctions qui envoient des messages ou gérer l’appartenance au groupe

### <a name="negotiate-function"></a>négocier (fonction)

Une application cliente nécessite un jeton d’accès valide pour vous connecter à Service Azure SignalR. Un jeton d’accès permettre être anonymes ou authentifiés à un ID d’utilisateur donné. Les applications de SignalR Service sans serveur nécessitent qu'un point de terminaison HTTP nommé « negotiate » pour obtenir un jeton et autres informations de connexion, telles que l’URL de point de terminaison de SignalR Service.

Utilisez HTTP a déclenché la fonction Azure et le *SignalRConnectionInfo* liaison pour générer l’objet d’informations de connexion d’entrée. La fonction doit avoir un itinéraire HTTP qui se termine par `/negotiate`.

Pour plus d’informations sur la création de la fonction negotiate, consultez le [ *SignalRConnectionInfo* d’entrée de référence de liaison](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Pour en savoir plus sur la création d’un jeton authentifié, reportez-vous à [à l’aide de l’authentification App Service](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Envoi de messages et la gestion de l’appartenance au groupe

Utilisez le *SignalR* liaison de sortie pour envoyer des messages aux clients connectés au Service Azure SignalR. Vous pouvez diffuser des messages à tous les clients, ou vous pouvez les envoyer à un sous-ensemble de clients qui sont authentifiés avec un ID utilisateur spécifique ou qui ont été ajoutés à un groupe spécifique.

Les utilisateurs peuvent être ajoutés à un ou plusieurs groupes. Vous pouvez également utiliser le *SignalR* liaison pour ajouter ou supprimer des utilisateurs vers/à partir de groupes de sortie.

Pour plus d’informations, consultez le [ *SignalR* la référence de liaison de sortie](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>Concentrateurs SignalR

SignalR a un concept de « hubs ». Chaque connexion client et chaque message envoyé à partir d’Azure Functions sont limitée à un hub spécifique. Vous pouvez utiliser des hubs comme un moyen de séparer vos connexions et les messages dans les espaces de noms logiques.

## <a name="client-development"></a>Développement client

Applications de client de SignalR peuvent exploiter le client SignalR SDK dans un des langages pour se connecter à et recevoir des messages de Service Azure SignalR.

### <a name="configuring-a-client-connection"></a>Configurez une connexion cliente

Pour vous connecter au Service de SignalR, un client doit effectuer une négociation de connexion réussie qui se compose des étapes suivantes :

1. Effectuer une requête pour le *négocier* point de terminaison HTTP décrite ci-dessus pour obtenir des informations de connexion valide
1. Se connecter au Service de SignalR à l’aide de l’URL de point de terminaison de service et le jeton d’accès obtenu à partir de la *négocier* point de terminaison

Kits SDK de client SignalR contienne déjà la logique nécessaire pour effectuer la négociation de négociation. Passez l’URL du point de terminaison negotiate, moins le `negotiate` segment, le Kit de développement SDK `HubConnectionBuilder`. Voici un exemple dans JavaScript :

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Par convention, le Kit de développement logiciel ajoute automatiquement `/negotiate` à l’URL et l’utilise pour commencer la négociation.

> [!NOTE]
> Si vous utilisez le SDK JavaScript/TypeScript dans un navigateur, vous devez [activer des ressources cross-origin CORS (partage)](#enabling-cors) sur votre application de fonction.

Pour plus d’informations sur la façon d’utiliser le Kit de développement logiciel du client SignalR, consultez la documentation de votre langage :

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Envoi de messages à partir d’un client au service

Bien que le SDK SignalR aux applications clientes d’appeler la logique de serveur principal dans un concentrateur SignalR, cette fonctionnalité n'est pas encore pris en charge lorsque vous utilisez le SignalR Service avec Azure Functions. Demandes d’utiliser HTTP pour appeler Azure Functions.

## <a name="azure-functions-configuration"></a>Configuration des fonctions Azure

Les applications de fonction Azure qui s’intègrent à Service Azure SignalR peuvent être déployées comme n’importe quelle application de fonction Azure classique, à l’aide de techniques telles que [déploiement en continu](../azure-functions/functions-continuous-deployment.md), [zip déploiement](../azure-functions/deployment-zip-push.md)et [exécuter à partir du package](../azure-functions/run-functions-from-deployment-package.md).

Toutefois, il existe quelques considérations spéciales pour les applications qui utilisent des liaisons de SignalR Service. Si le client s’exécute dans un navigateur, CORS doit être activé. Et si l’application requiert une authentification, vous pouvez intégrer le point de terminaison de négocier l’authentification App Service.

### <a name="enabling-cors"></a>Activation de CORS

Le client JavaScript/TypeScript rend les requêtes HTTP à la fonction de négocier la négociation de connexion. Lorsque l’application cliente est hébergée sur un autre domaine que l’application de fonction Azure, des ressources cross-origin CORS (partage) doivent être activée sur l’application de fonction ou le navigateur bloque les demandes.

#### <a name="localhost"></a>localhost

Lorsque vous exécutez l’application de fonction sur votre ordinateur local, vous pouvez ajouter un `Host` section à *local.settings.json* pour activer CORS. Dans le `Host` , ajoutez deux propriétés :

* `CORS` -Entrez l’URL de base qui est l’origine de l’application cliente
* `CORSCredentials` -Affectez-lui la valeur `true` pour autoriser les demandes de « withCredentials »

Exemple :

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="azure"></a>Azure

Pour activer CORS sur une Function app Azure, accédez à l’écran de configuration de CORS sous le *fonctionnalités de la plateforme* onglet de votre Function app dans le portail Azure.

CORS avec Access-Control-Allow-Credentials doit être activé pour le client SignalR appeler la fonction negotiate. Sélectionnez la case à cocher pour l’activer.

Dans le *origines autorisées* , ajoutez une entrée avec l’URL d’origine de la base de votre application web.

![Configuration de CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>À l’aide de l’authentification App Service

Azure Functions offre une authentification intégrée, prenant en charge les fournisseurs les plus courants tels que Facebook, Twitter, Account Microsoft, Google et Azure Active Directory. Cette fonctionnalité peut être intégrée à la *SignalRConnectionInfo* liaison pour créer des connexions au Service Azure SignalR qui ont été authentifiées à un ID d’utilisateur. Votre application peut envoyer des messages à l’aide de la *SignalR* de sortie de liaison qui sont destinés à cet ID d’utilisateur.

Dans le portail Azure, dans votre application de fonction *fonctionnalités de la plateforme* onglet, ouvrez le *d’authentification/autorisation* fenêtre de paramètres. Consultez la documentation de [l’authentification App Service](../app-service/overview-authentication-authorization.md) pour configurer l’authentification à l’aide d’un fournisseur d’identité de votre choix.

Une fois configuré, les requêtes HTTP authentifiées inclura `x-ms-client-principal-name` et `x-ms-client-principal-id` en-têtes contenant les nom d’utilisateur de l’identité authentifiée et ID utilisateur, respectivement.

Vous pouvez utiliser ces en-têtes dans votre *SignalRConnectionInfo* configuration de liaison à créer des connexions authentifiées. Voici un exemple C# négocier la fonction qui utilise le `x-ms-client-principal-id` en-tête.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

Vous pouvez ensuite envoyer des messages à cet utilisateur en définissant le `UserId` propriété d’un message de SignalR.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Pour plus d’informations sur les autres langages, consultez le [les liaisons de Service Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) pour faire référence à Azure Functions.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à développer et de configurer des applications de SignalR Service sans serveur à l’aide d’Azure Functions. Essayez de créer une application vous-même à l’aide d’un des Démarrages rapides ou des didacticiels sur la [page de vue d’ensemble de SignalR Service](index.yml).