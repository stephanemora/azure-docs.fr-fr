---
title: 'Démarrage rapide : Ajouter l’appel VoIP à une application web avec Azure Communication Services'
description: Dans ce tutoriel, vous allez découvrir comment utiliser la bibliothèque de client Azure Communication Services Calling pour JavaScript.
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: b66ee8117e5326a8ed8c1a1ad973fb13e942e0c7
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761967"
---
Dans ce guide de démarrage rapide, vous allez découvrir comment démarrer un appel à l’aide de la bibliothèque de client Azure Communication Services Calling pour Javascript.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) versions Active LTS et Maintenance LTS (8.11.1 et 10.14.1 recommandées).
- Une ressource Communication Services active. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un jeton d’accès utilisateur pour instancier le client d’appel. Découvrez comment [créer et gérer des jetons d’accès utilisateur](../../access-tokens.md)

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Exécutez `npm init -y` pour créer un fichier **package.json** avec les paramètres par défaut.

```console
npm init -y
```

### <a name="install-the-package"></a>Installer le package

Utilisez la commande `npm install` pour installer la bibliothèque de client Azure Communication Services Calling pour JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

L’option `--save` liste la bibliothèque comme dépendance dans votre fichier **package.json**.

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

Ce guide de démarrage rapide utilise webpack pour regrouper les ressources de l’application. Exécutez la commande suivante pour installer les packages npm webpack, webpack-cli et webpack-dev-server, puis listez-les comme dépendances de développement dans votre fichier **package.json** :

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Créez un fichier **index.html** dans le répertoire racine de votre projet. Nous utiliserons ce fichier pour configurer une disposition de base qui permettra à l’utilisateur d’effectuer un appel à un bot Azure Communications.

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
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
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
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");

// quickstart code goes here
```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque de client Azure Communication Services Calling :

| Nom                             | Description                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient est le point d’entrée principal de la bibliothèque de client Appel.                                                                       |
| CallAgent                        | CallAgent sert à démarrer et à gérer les appels.                                                                                            |
| AzureCommunicationUserCredential | La classe AzureCommunicationUserCredential implémente l’interface CommunicationUserCredential qui est utilisée pour instancier CallAgent. |


## <a name="authenticate-the-client"></a>Authentifier le client

Vous devez remplacer `<USER_ACCESS_TOKEN>` par un jeton d’accès utilisateur valide pour votre ressource. Consultez la documentation sur les [jetons d’accès utilisateur](../../access-tokens.md) si vous n’avez pas encore de jeton disponible. À l’aide du `CallClient`, initialisez une instance de `CallAgent` avec un `CommunicationUserCredential` qui nous permettra d’établir et de recevoir des appels. Ajoutez le code suivant à **client.js** :

```javascript
const callClient = new CallClient();
const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
let callAgent;

callAgent = await callClient.createCallAgent(tokenCredential);
callButton.disabled = false;
```

## <a name="start-a-call"></a>Démarrer un appel

Ajoutez un gestionnaire d’événements pour lancer un appel en cas de clic sur `callButton` :

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.call(
        [{ communicationUserId: userToCall }],
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
});
```

La propriété `forEveryone` met fin à l’appel pour tous les participants.

## <a name="run-the-code"></a>Exécuter le code

Utilisez `webpack-dev-server` pour créer et exécuter votre application. Exécutez la commande suivante pour regrouper l’hôte d’application sur un serveur web local :

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Ouvrez votre navigateur et accédez à http://localhost:8080/. Les éléments suivants doivent s’afficher :

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Capture d’écran de l’application JavaScript terminée.":::

Vous pouvez établir un appel VoIP sortant en fournissant un ID d’utilisateur dans le champ de texte et en cliquant sur le bouton **Start Call**. L’appel de `8:echo123` vous connecte à un bot d’écho, ce qui est parfait pour démarrer et vérifier que vos périphériques audio fonctionnent.
