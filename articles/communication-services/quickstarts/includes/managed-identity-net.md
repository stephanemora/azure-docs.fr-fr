---
ms.openlocfilehash: c1b74b43c6ef884c68282dcaaae8dfc9a5541453
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622326"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Ajouter une identité managée à votre solution Communication Services (.NET)

### <a name="install-the-client-library-packages"></a>Installer les packages de bibliothèques de client

```console
dotnet add package Azure.Communication.Identity  --version 1.0.0-beta.5
dotnet add package Azure.Communication.Sms  --version 1.0.0-beta.4
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Utiliser les packages de bibliothèque de client

Ajoutez les directives `using` suivantes à votre code pour utiliser les bibliothèques de client Azure Identity et Stockage Azure.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
```

Les exemples ci-dessous utilisent [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Ces informations d’identification sont appropriées pour les environnements de production et de développement.

Les variables d’environnement `AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` et `AZURE_TENANT_ID` sont nécessaires pour créer un objet `DefaultAzureCredential`. Pour créer une application inscrite dans l’environnement de développement et configurer des variables d’environnement, consultez [Autoriser l’accès avec une identité managée](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Créer une identité et émettre un jeton avec une identité managée

L’exemple de code suivant montre comment créer un objet client de service avec des jetons Azure Active Directory.

Ensuite, utilisez le client afin d’émettre un jeton pour un nouvel utilisateur :

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Envoyer un SMS avec une identité managée

L’exemple de code suivant montre comment créer un objet client de service SMS avec une identité managée, puis comment utiliser le client pour envoyer un SMS :

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
      }
```

