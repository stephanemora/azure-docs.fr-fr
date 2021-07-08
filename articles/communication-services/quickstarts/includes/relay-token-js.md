---
title: Fichier include
description: Fichier include
services: Communication Services
author: shahen
manager: anvalent
ms.service: Communication Services
ms.subservice: Communication Services
ms.date: 05/20/2021
ms.topic: include
ms.custom: include file
ms.author: shahen
ms.openlocfilehash: 118aa3bfcc35b0250b13ea3446e41e8f4b9f06d8
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025919"
---
## <a name="setting-up"></a>Configuration

### <a name="create-a-new-nodejs-application"></a>Créer une application Node.js

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir relay-token-quickstart && cd relay-token-quickstart
```
Exécutez `npm init -y` pour créer un fichier **package.json** avec les paramètres par défaut.

```console
npm init -y
```

### <a name="install-the-package"></a>Installer le package

Utilisez la commande `npm install` pour installer la bibliothèque de client Azure Communication Services Identity et la bibliothèque Network Traversal pour JavaScript.

```console
npm install @azure/communication-identity --save
npm install @azure/communication-network-traversal --save
```

L’option `--save` liste la bibliothèque comme dépendance dans votre fichier **package.json**.

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Ouvrez un nouveau fichier texte dans votre éditeur de code
2. Ajoutez les directives `require` en haut du fichier pour utiliser le SDK Azure Communication Identity et le SDK Network Traversal
3. Créer la structure du programme, y compris la gestion des exceptions de base

   Utilisez le code suivant pour commencer :

   ```javascript
   const { CommunicationIdentityClient } = require("@azure/communication-identity");
   const { CommunicationRelayClient } = require("@azure/communication-network-traversal");;

   const main = async () => {
     console.log("Azure Communication Services - Relay Token Quickstart")
  
     // Quickstart code goes here
   };

   main().catch((error) => {
     console.log("Encountered and error");
     console.log(error);
   })
   ```

4. Enregistrez le nouveau fichier sous le nom **relay-token.js** dans le répertoire *user-tokens-quickstart*.

### <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un `CommunicationIdentityClient` avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion de la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string).

Ajoutez le code suivant à la méthode `main` :

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>Créer une identité

Azure Communication Services gère un répertoire LID (Lightweight Identity Directory). Utilisez la méthode `createUser` pour créer une entrée, avec une valeur `Id` unique, dans le répertoire. Stockez l’identité reçue avec un mappage aux utilisateurs de votre application. Par exemple, en les stockant dans la base de données de votre serveur d’applications. L’identité sera demandée ultérieurement pour émettre des jetons d’accès.

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

### <a name="exchange-the-user-access-token-for-a-relay-token"></a>Échanger le jeton d’accès utilisateur pour un jeton de relais

Appeler le service de jeton Azure Communication pour échanger le jeton d’accès utilisateur pour un jeton de service TURN

```javascript
    const relayClient = new CommunicationRelayClient(connectionString);
    console.log("Getting relay configuration");

    const config = await relayClient.getRelayConfiguration(identityResponse);
    console.log("RelayConfig", config);
```

### <a name="use-the-token-on-the-client-as-an-ice-candidate"></a>Utiliser le jeton sur le client comme candidat ICE

Le jeton peut maintenant être désérialisé et ajouté comme candidat ICE avec WebRTC dans le navigateur client

```javascript  
var configuration = { iceServers: iceServers };
var peerConnection = new RTCPeerConnection(configuration);
```

## <a name="run-the-code"></a>Exécuter le code

À partir d’une invite de console, accédez au répertoire contenant le fichier *issue-token.js*, puis exécutez la commande `node` suivante pour exécuter l’application.

```console
node ./relay-token.js
```
