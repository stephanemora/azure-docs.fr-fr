---
title: 'Démarrage rapide : Ajouter l’appel VoIP à une application web avec Azure Communication Services'
description: Dans ce tutoriel, vous allez découvrir comment utiliser le kit de développement logiciel (SDK) Azure Communication Services Calling pour JavaScript.
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: bd9a607b46c87ca590bcefef310f822d6f23483c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386704"
---
Dans ce guide de démarrage rapide, vous allez découvrir comment démarrer un appel à l’aide du kit de développement logiciel (SDK) Azure Communication Services Calling pour Javascript.

> [!NOTE]
> Ce document utilise la version 1.0.0-beta.10 du kit de développement logiciel (SDK) Calling.


## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) versions Active LTS et Maintenance LTS (8.11.1 et 10.14.1 recommandées).
- Une ressource Communication Services active. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un jeton d’accès utilisateur pour instancier le client d’appel. Découvrez comment [créer et gérer des jetons d’accès utilisateur](../../access-tokens.md)


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

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
      id="token-input"
      type="text"
      placeholder="User access token"
      style="margin-bottom:1em; width: 200px;"
    />
    </div>
    <button id="token-submit" type="button">
        Submit
    </button>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px; display: block;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
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
let userTokenCredential = "";
const userToken = document.getElementById("token-input");
const calleeInput = document.getElementById("callee-id-input");
const submitToken = document.getElementById("token-submit");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du SDK Azure Communication Services Calling :

| Nom                             | Description                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient est le point d’entrée principal du SDK Calling.                                                                       |
| CallAgent                        | CallAgent sert à démarrer et à gérer les appels.                                                                                            |
| AzureCommunicationTokenCredential | La classe AzureCommunicationTokenCredential implémente l’interface CommunicationTokenCredential qui est utilisée pour instancier CallAgent. |


## <a name="authenticate-the-client"></a>Authentifier le client

Vous devez entrer un jeton d’accès utilisateur valide pour votre ressource dans le champ de texte, puis cliquer sur Envoyer. Consultez la documentation sur les [jetons d’accès utilisateur](../../access-tokens.md) si vous n’avez pas encore de jeton disponible. À l’aide du `CallClient`, initialisez une instance de `CallAgent` avec un `CommunicationTokenCredential` qui nous permettra d’établir et de recevoir des appels. Ajoutez le code suivant à **client.js** :

```javascript
submitToken.addEventListener("click", async () => {
  const callClient = new CallClient(); 
  const userTokenCredential = userToken.value;
    try {
      tokenCredential = new AzureCommunicationTokenCredential(userTokenCredential);
      callAgent = await callClient.createCallAgent(tokenCredential);
      callButton.disabled = false;
      submitToken.disabled = true;
    } catch(error) {
      window.alert("Please submit a valid token!");
    }
})
```

## <a name="start-a-call"></a>Démarrer un appel

Ajoutez un gestionnaire d’événements pour lancer un appel en cas de clic sur `callButton` :

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ id: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Terminer un appel

Ajoutez un détecteur d’événements pour terminer l’appel en cours en cas de clic sur `hangUpButton` :

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
  submitToken.disabled = false;
});
```

La propriété `forEveryone` met fin à l’appel pour tous les participants.

## <a name="run-the-code"></a>Exécuter le code

Utilisez `webpack-dev-server` pour créer et exécuter votre application. Exécutez la commande suivante pour regrouper l’hôte d’application sur un serveur web local :

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Ouvrez votre navigateur et accédez à http://localhost:8080/. Les éléments suivants doivent s’afficher :

:::image type="content" source="../media/javascript/calling-javascript-app-2.png" alt-text="Capture d’écran de l’application JavaScript terminée.":::

Vous pouvez établir un appel VoIP sortant en fournissant un jeton d’accès utilisateur et un ID d’utilisateur valides dans les champs de texte correspondant et en cliquant sur le bouton **Start Call** (Démarrer l’appel). L’appel de `8:echo123` vous connecte à un bot d’écho, ce qui est parfait pour bien démarrer et vérifier que vos périphériques audio fonctionnent.

## <a name="sample-code"></a>Exemple de code

Vous pouvez télécharger l’exemple d’application à partir de [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-voice-calling).
