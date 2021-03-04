---
ms.openlocfilehash: 0fd97fe0eebb23130513409698b75d2ee7b98a6f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657633"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Ajouter une identité managée à votre solution Communication Services (JS)

### <a name="install-the-client-library-packages"></a>Installer les packages de bibliothèques de client

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-client-library-packages"></a>Utiliser les packages de bibliothèque de client

Ajoutez les directives `import` suivantes à votre code pour utiliser les bibliothèques de client Azure Identity et Stockage Azure.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { SmsClient } from "@azure/communication-sms";
```

Les exemples ci-dessous utilisent [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential). Ces informations d’identification sont appropriées pour les environnements de production et de développement.

Pour inscrire une application dans l’environnement de développement et configurer des variables d’environnement, consultez [Autoriser l’accès avec une identité managée](../managed-identity-from-cli.md).  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Créer une identité et émettre un jeton avec une identité managée

L’exemple de code suivant montre comment créer un objet client de service avec une identité managée, puis comment utiliser le client afin d’émettre un jeton pour un nouvel utilisateur :

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserWithToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Envoyer un SMS avec une identité managée

L’exemple de code suivant montre comment créer un objet client de service avec une identité managée, puis comment utiliser le client pour envoyer un SMS :

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SendRequest = { 
          from: fromNumber, 
          to: [toNumber], 
          message: message 
     };

     const response = await smsClient.send(
          sendRequest, 
          {} //Optional SendOptions
          );
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’authentification](../concepts/authentication.md)

Vous souhaiterez peut-être également :

- [En savoir plus sur le contrôle d’accès en fonction du rôle dans Azure](../../../../articles/role-based-access-control/index.yml)
- [En savoir plus sur la bibliothèque Azure Identity pour JS](/javascript/api/overview/azure/identity-readme)
- [Créer des jetons d’accès utilisateur](../../quickstarts/access-tokens.md)
- [Envoyer un message SMS](../../quickstarts/telephony-sms/send.md)
- [En savoir plus sur les SMS](../../concepts/telephony-sms/concepts.md)

