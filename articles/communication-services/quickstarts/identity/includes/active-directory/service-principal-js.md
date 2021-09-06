---
ms.openlocfilehash: 80522448fa77cb8f07ae89a28f78ce6417edf17d
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656952"
---
> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/use-managed-Identity).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir active-directory-authentication-quickstart && cd active-directory-authentication-quickstart
```

Exécutez `npm init -y` pour créer un fichier **package.json** avec les paramètres par défaut.

```console
npm init -y
```

### <a name="install-the-sdk-packages"></a>Installer les packages du kit SDK

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="create-a-new-file"></a>Créer un fichier

Ouvrez un nouveau fichier avec un éditeur de texte et enregistrez-le sous `index.js`. Nous placerons notre code dans ce fichier.

### <a name="use-the-sdk-packages"></a>Utiliser les packages du kit SDK

Ajoutez les directives `require` suivantes au début de `index.js` pour utiliser les Kits de développement logiciel (SDK) Azure Identity et Stockage Azure.

```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");
```
## <a name="create-a-defaultazurecredential"></a>Créer un DefaultAzureCredential

Nous utiliserons le [DefaultAzureCredential](/javascript/api/@azure/identity/defaultazurecredential) pour ce démarrage rapide. Ces informations d’identification sont appropriées pour les environnements de production et de développement. Comme elles sont nécessaires pour chaque opération, créons-les au début de notre fichier `index.js`. 

```JavaScript
    const credential = new DefaultAzureCredential();
```

## <a name="create-an-identity-and-issue-a-token-with-service-principals"></a>Créer une identité et émettre un jeton avec des principaux de service

Ensuite, nous allons écrire une fonction qui crée une nouvelle identité et émet un jeton pour cette identité. Nous l’utiliserons ultérieurement pour tester la configuration de notre principal de service.

```JavaScript
async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}
```

## <a name="send-an-sms-with-service-principals"></a>Envoyer un SMS avec des principaux de service

À présent, nous allons écrire une fonction qui utilise des principaux de service pour envoyer un SMS :

```JavaScript
async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}
```

## <a name="write-the-main-function"></a>Écrire la fonction main

Après avoir créé nos fonctions, nous pouvons maintenant écrire une fonction principale pour les appeler et démontrer l’utilisation des principaux de service :
```JavaScript
async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Service Principals");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Service Principals");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Service Principals");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

Le fichier `index.js` final doit ressembler à ce qui suit :
```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");

const credential = new DefaultAzureCredential();

async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}

async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}

async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Service Principals");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Service Principals");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Service Principals");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

## <a name="run-the-program"></a>Exécuter le programme

Lorsque tout est terminé, vous pouvez exécuter le fichier en entrant `node index.js` dans le répertoire de votre projet. Si tout s’est bien passé, vous devez obtenir un résultat similaire à ce qui suit.

```Bash
    $ node index.js
    Retrieving new Access Token, using Service Principals
    Retrieved Access Token: ey...Q
    Sending SMS using Service Principals
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
