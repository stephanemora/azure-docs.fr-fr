---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: a671eb00f2108b96220e72a32f8dd9f5654a4224
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700469"
---
## <a name="install-the-sdk"></a>Installer le SDK

Utilisez la commande `npm install` pour installer les kits SDK Appel Azure Communication Services et communs pour JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

## <a name="initialize-required-objects"></a>Initialiser les objets nécessaires

Une instance de CallClient est nécessaire pour la plupart des opérations d’appel. Créons une instance de `CallClient`. Vous pouvez le configurer à l’aide d’options personnalisées comme une instance d’enregistreur d’événements.

Quand vous avez une instance `CallClient`, vous pouvez créer une instance `CallAgent` en appelant la méthode `createCallAgent` sur l’instance `CallClient`. Cette méthode renvoie un objet d’instance `CallAgent` de manière asynchrone.

La méthode `createCallAgent` utilise `CommunicationTokenCredential` comme argument. Elle accepte un [jeton d’accès utilisateur](../../../../quickstarts/access-tokens.md).

Vous pouvez utiliser la méthode `getDeviceManager` sur l’instance `CallClient` pour accéder à `deviceManager`.

```js
const { CallClient } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential} = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");

// Set the logger's log level
setLogLevel('verbose');

// Redirect log output to wherever desired. To console, file, buffer, REST API, etc...
AzureLogger.log = (...args) => {
    console.log(...args); // Redirect log output to console
};

const userToken = '<USER_TOKEN>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```