---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: c1c6478948aaf207f0ca1adf367840ca3db34649
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325289"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) versions Active LTS et Maintenance LTS (8.11.1 et 10.14.1 recommandées).
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../create-communication-resource.md).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-nodejs-application"></a>Créer une application Node.js

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir access-tokens-quickstart && cd access-tokens-quickstart
```

Exécutez `npm init -y` pour créer un fichier **package.json** avec les paramètres par défaut.

```console
npm init -y
```

### <a name="install-the-package"></a>Installer le package

Utilisez la commande `npm install` pour installer la bibliothèque de client Azure Communication Services Administration pour JavaScript.

```console

npm install @azure/communication-administration --save

```

L’option `--save` liste la bibliothèque comme dépendance dans votre fichier **package.json**.

## <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Ouvrez un nouveau fichier texte dans votre éditeur de code
1. Ajoutez un appel `require` pour charger `CommunicationIdentityClient`
1. Créer la structure du programme, y compris la gestion des exceptions de base

Utilisez le code suivant pour commencer :

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Enregistrez le nouveau fichier sous le nom **issue-access-token.js** dans le répertoire *access-tokens-quickstart*.

## <a name="authenticate-the-client"></a>Authentifier le client

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

## <a name="issue-access-tokens"></a>Émettre des jetons d’accès

Avec la méthode `issueToken`, émettez un jeton d’accès pour une identité Communication Services existant déjà. Le paramètre `scopes` définit un ensemble de primitives, qui autorise ce jeton d’accès. Consultez [la liste des actions prises en charge](../../concepts/authentication.md). Une nouvelle instance du paramètre `communicationUser` peut être construite en fonction de la représentation sous forme de chaîne de l’identité Azure Communication Service.

```javascript
// Issue an access token with the "voip" scope for an identity
let tokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Les jetons d’accès sont des informations d’identification à durée de vie courte, qui doivent être réémises. Ne pas le faire peut entraîner une interruption expérimentée par les utilisateurs de votre application. La propriété de réponse `expiresOn` indique la durée de vie du jeton d’accès.


## <a name="refresh-access-tokens"></a>Actualiser des jetons d’accès

Pour actualiser un jeton d’accès, utilisez l’objet `CommunicationUser` permettant d’émettre à nouveau :

```javascript  
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identityResponse = new CommunicationUser(existingIdentity);
tokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
```


## <a name="revoke-access-tokens"></a>Révoquer des jetons d’accès

Dans certains cas, vous pouvez révoquer explicitement des jetons d’accès. Par exemple, lorsqu’un utilisateur d’une application modifie le mot de passe qu’il utilise pour s’authentifier auprès de votre service. La méthode `revokeTokens` invalide tous les jetons d’accès actifs qui ont été émis pour l’identité.

```javascript  
await identityClient.revokeTokens(identityResponse);
console.log(`\nSuccessfully revoked all access tokens for identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="delete-an-identity"></a>Supprimer une identité

Quand vous supprimez une identité, vous supprimez aussi tous les jetons d’accès actifs et vous ne pouvez plus émettre de jetons d’accès pour l’identité. Tout le contenu persistant associé à l’identité est également supprimé.

```javascript
await identityClient.deleteUser(identityResponse);
console.log(`\nDeleted the identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Exécuter le code

À partir d’une invite de console, accédez au répertoire contenant le fichier *issue-access-token.js*, puis exécutez la commande `node` suivante pour exécuter l’application.

```console
node ./issue-access-token.js
```
