---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 77b1e9ab245f668ab81741451a5e032f37bc3625
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944594"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) versions Active LTS et Maintenance LTS (8.11.1 et 10.14.1 recommandées).
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../create-communication-resource.md).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-nodejs-application"></a>Créer une application Node.js

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
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
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Enregistrez le nouveau fichier sous le nom **issue-token.js** dans le répertoire *user-tokens-quickstart*.

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un `CommunicationIdentityClient` avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion de la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string).

Ajoutez le code suivant à la méthode `main` :

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>Créer un utilisateur

Azure Communication Services gère un répertoire LID (Lightweight Identity Directory). Utilisez la méthode `createUser` pour créer une entrée, avec une valeur `Id` unique, dans le répertoire. Vous devez tenir à jour un mappage entre les utilisateurs de votre application et les identités générées par Communication Services (par exemple, en les stockant dans la base de données de votre serveur d’applications).

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>Émettre des jetons d’accès utilisateur

Avec la méthode `issueToken`, émettez un jeton d’accès pour un utilisateur de Communication Services. Si vous ne spécifiez pas le paramètre facultatif `user`, un nouvel utilisateur sera créé et retourné avec le jeton.

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Les jetons d’accès utilisateur sont des informations d’identification de courte durée qui doivent être réémises afin d’éviter que les utilisateurs rencontrent des interruptions de service. La propriété de réponse `expiresOn` indique la durée de vie du jeton.

## <a name="revoke-user-access-tokens"></a>Révoquer des jetons d’accès utilisateur

Dans certains cas, il est nécessaire de révoquer explicitement des jetons d’accès utilisateur, par exemple, quand des utilisateurs changent leur mot de passe pour s’authentifier auprès de votre service. Utilisez la méthode `revokeTokens` pour invalider tous les jetons d’accès d’un utilisateur.

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="delete-a-user"></a>Supprimer un utilisateur

Quand vous supprimez un utilisateur, vous supprimez aussi tous les jetons actifs et vous ne pouvez plus ensuite émettre de jetons pour les identités. De plus, tout le contenu persistant associé à l’utilisateur est également supprimé.

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Exécuter le code

À partir d’une invite de console, accédez au répertoire contenant le fichier *issue-token.js*, puis exécutez la commande `node` suivante pour exécuter l’application.

```console
node ./issue-token.js
```
