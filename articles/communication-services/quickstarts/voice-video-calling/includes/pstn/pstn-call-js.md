---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: 7506f2c42066cb26532f815f2b77b240caea1993
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112535605"
---
[!INCLUDE [Emergency Calling Notice](../../../../includes/emergency-calling-notice-include.md)]

## <a name="sample-code"></a>Exemple de code
Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-phone-calling).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../../create-communication-resource.md).
- Numéro de téléphone acquis dans votre ressource Communication Services. [Comment obtenir un numéro de téléphone](../../../telephony-sms/get-phone-number.md).
- Un `User Access Token` pour activer le client d’appel. Pour en savoir plus, consultez [Comment obtenir un `User Access Token`](../../../access-tokens.md)

[!INCLUDE [Calling with JavaScript](../get-started/get-started-javascript-setup.md)]

Voici le code :

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-phone-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 230px;"
    />
    <div>
      <button id="call-phone-button" type="button">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-phone-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Créez un fichier dans le répertoire racine de votre projet sous le nom **client.js** qui contiendra la logique d’application pour ce guide de démarrage rapide. Ajoutez le code suivant pour importer le client appelant et obtenir des références aux éléments DOM afin que nous puissions attacher notre logique métier.

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;

const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential('<USER ACCESS TOKEN with PSTN scope>');
    callAgent = await callClient.createCallAgent(tokenCredential);
  //  callPhoneButton.disabled = false;
}

init();

```

## <a name="start-a-call-to-phone"></a>Démarrer un appel téléphonique

Spécifiez le numéro de téléphone que vous avez acquis dans la ressource Communication Services, qui sera utilisé pour démarrer l’appel :
> [!WARNING]
> Notez que les numéros de téléphone doivent être fournis au format standard international E.164. (par exemple +12223334444)

Ajoutez un gestionnaire d’événements pour lancer un appel au numéro de téléphone que vous avez fourni quand l’utilisateur clique sur `callPhoneButton` :


```javascript
callPhoneButton.addEventListener("click", () => {
  // start a call to phone
  const phoneToCall = calleePhoneInput.value;
  call = callAgent.startCall(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: 'YOUR AZURE REGISTERED PHONE NUMBER HERE: +12223334444'}
  });
  // toggle button states
  hangUpPhoneButton.disabled = false;
  callPhoneButton.disabled = true;
});
```

## <a name="end-a-call-to-phone"></a>Mettre fin à un appel téléphonique

Ajoutez un détecteur d’événements pour terminer l’appel en cours en cas de clic sur `hangUpPhoneButton` :

```javascript
hangUpPhoneButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({
    forEveryone: true
  });

  // toggle button states
  hangUpPhoneButton.disabled = true;
  callPhoneButton.disabled = false;
});
```

La propriété `forEveryone` met fin à l’appel pour tous les participants.

## <a name="run-the-code"></a>Exécuter le code

Utilisez `webpack-dev-server` pour créer et exécuter votre application. Exécutez la commande suivante pour créer un bundle de l’application hôte sur un serveur web local :

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Ouvrez votre navigateur et accédez à `http://localhost:8080/`. Les éléments suivants doivent s’afficher :

:::image type="content" source="../../media/javascript/pstn-calling-javascript-app.png" alt-text="Capture d’écran de l’application JavaScript terminée.":::

Vous pouvez passer un appel à un numéro de téléphone réel en fournissant un numéro de téléphone dans le champ texte ajouté et en cliquant sur le bouton **Démarrer l’appel téléphonique**.

> [!WARNING]
> Notez que les numéros de téléphone doivent être fournis au format standard international E.164. (par exemple +12223334444)