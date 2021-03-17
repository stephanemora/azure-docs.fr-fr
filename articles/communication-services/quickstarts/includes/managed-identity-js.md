---
ms.openlocfilehash: 3626ca4cc3e7377f1c6778bc77e5e48ef0dcad0c
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103439193"
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
import { CommunicationIdentityClient, CommunicationUserToken } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

Les exemples ci-dessous utilisent [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential). Ces informations d’identification sont appropriées pour les environnements de production et de développement.

Pour inscrire une application dans l’environnement de développement et configurer des variables d’environnement, consultez [Autoriser l’accès avec une identité managée](../managed-identity-from-cli.md).  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Créer une identité et émettre un jeton avec une identité managée

L’exemple de code suivant montre comment créer un objet client de service avec une identité managée, puis comment utiliser le client afin d’émettre un jeton pour un nouvel utilisateur :

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserAndToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Envoyer un SMS avec une identité managée

L’exemple de code suivant montre comment créer un objet client de service avec une identité managée, puis comment utiliser le client pour envoyer un SMS :

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SmsSendRequest = { 
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

