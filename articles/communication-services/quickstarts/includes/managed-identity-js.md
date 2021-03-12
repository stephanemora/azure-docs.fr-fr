---
ms.openlocfilehash: a51121b9dd9c7dcb894399fd9ad5f49cc5e07f3a
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486607"
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

