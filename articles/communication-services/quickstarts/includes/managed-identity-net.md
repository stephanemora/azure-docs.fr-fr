---
ms.openlocfilehash: 2c816f005dea452c3ffa2889aa7d2742a5762759
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657650"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Ajouter une identité managée à votre solution Communication Services (.NET)

### <a name="install-the-client-library-packages"></a>Installer les packages de bibliothèques de client

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Utiliser les packages de bibliothèque de client

Ajoutez les directives `using` suivantes à votre code pour utiliser les bibliothèques de client Azure Identity et Stockage Azure.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

Les exemples ci-dessous utilisent [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Ces informations d’identification sont appropriées pour les environnements de production et de développement.

Les variables d’environnement `AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` et `AZURE_TENANT_ID` sont nécessaires pour créer un objet `DefaultAzureCredential`. Pour créer une application inscrite dans l’environnement de développement et configurer des variables d’environnement, consultez [Autoriser l’accès avec une identité managée](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Créer une identité et émettre un jeton avec une identité managée

L’exemple de code suivant montre comment créer un objet client de service avec des jetons Azure Active Directory.

Ensuite, utilisez le client afin d’émettre un jeton pour un nouvel utilisateur :

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndGetTokenAsync(Uri resourceEdnpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Envoyer un SMS avec une identité managée

L’exemple de code suivant montre comment créer un objet client de service SMS avec une identité managée, puis comment utiliser le client pour envoyer un SMS :

```csharp
     public async Task SendSms(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’authentification](../concepts/authentication.md)

Vous souhaiterez peut-être également :

- [En savoir plus sur le contrôle d’accès en fonction du rôle dans Azure](../../../../articles/role-based-access-control/index.yml)
- [En savoir plus sur la bibliothèque d’identité Azure pour .NET](/dotnet/api/overview/azure/identity-readme)
- [Créer des jetons d’accès utilisateur](../../quickstarts/access-tokens.md)
- [Envoyer un message SMS](../telephony-sms/send.md)
- [En savoir plus sur les SMS](../../concepts/telephony-sms/concepts.md)
