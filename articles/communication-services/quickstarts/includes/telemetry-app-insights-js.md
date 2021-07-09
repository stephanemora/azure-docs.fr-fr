---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: orwatson
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/01/2021
ms.topic: include
ms.custom: include file
ms.author: orwatson
ms.openlocfilehash: f044b23d26bee71fc3f6a28f96a42c42a01f4d02
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111593088"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Versions de [Node.js](https://nodejs.org/), d’Active LTS et de Maintenance LTS.
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../create-communication-resource.md).
- Créez une [ressource Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) dans le portail Azure.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-nodejs-app"></a>Créer une application Node.js

Si vous disposez déjà d’une application utilisant une bibliothèque ACS, et êtes familiarisé avec JavaScript, n’hésitez pas à passer à la [configuration du traceur](#setup-tracer).

Pour commencer, vous aurez besoin d’une application JS utilisant l’une de nos bibliothèques de client. Nous allons utiliser la bibliothèque [@azure/communication-identity](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/communication/communication-identity) pour créer notre application. Suivez les étapes ci-dessous pour configurer votre exemple d’application :

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire, puis accédez-y.

```console
mkdir export-js-telemetry && cd export-js-telemetry
```
Exécutez `npm init -y` pour créer un fichier **package.json** avec les paramètres par défaut.

```console
npm init -y
```

### <a name="install-dependencies"></a>Installer des dépendances

Utilisez la commande `npm install` pour installer les dépendances que notre application utilisera.

```bash
npm install @azure/communication-identity @azure/monitor-opentelemetry-exporter @opentelemetry/node@^0.14.0 @opentelemetry/plugins-node-core --save
```

L’option `--save` ajoute les bibliothèques en tant que dépendances dans le fichier **package.json** créé à l’étape précédente.

Il est important que vous n’oubliiez pas la version spécifique de `@opentelemetry/node@^0.14.0`. Au moment de la rédaction de ce guide, il s’agit de la dernière version du package entièrement compatible avec `@azure/monitor-opentelemetry-exporter`.

### <a name="add-main-app-file"></a>Ajouter un fichier d’application principale

Créez un fichier nommé **token.js** à la racine du répertoire. Ajoutez-lui le code suivant :

```javascript
// token.js

const { CommunicationIdentityClient } = require("@azure/communication-identity");

async function main() {
  const ACS_CONNECTION_STRING = "<your-acs-connection-string>"
  const client = new CommunicationIdentityClient(ACS_CONNECTION_STRING);

  console.log("Creating user...")
  const user = await client.createUser();
  console.log(`User ${user.communicationUserId} was created successfully.\n`);

  console.log(`Issuing token for ${user.communicationUserId}...`);
  const { token } = await client.getToken(user, ["chat"]);
  console.log("Token issued successfully.\n")
  console.log(`${token}\n`);

  console.log(`Revoking token for ${user.communicationUserId}...`);
  await client.revokeTokens(user);
  console.log("Token revoked successfully.\n");

  console.log(`Deleting user ${user.communicationUserId}...`);
  await client.deleteUser(user);
  console.log("User deleted successfully.\n");
}

main().catch((error) => {
  console.log("Encountered an error:", error);
  process.exit(1);
});
```
## <a name="setup-tracer"></a>Configurer le traceur

Créez un fichier nommé **tracing.js** à la racine du répertoire. Ajoutez-lui le code suivant :

```javascript
// tracing.js

const azureSdkTracing = require("@azure/core-tracing");
const { AzureMonitorTraceExporter } = require("@azure/monitor-opentelemetry-exporter");
const { NodeTracerProvider } = require("@opentelemetry/node");
const { BatchSpanProcessor } = require("@opentelemetry/tracing");

const AI_CONNECTION_STRING = "<your-application-insights-connection-string>";
const provider = new NodeTracerProvider();
const azExporter = new AzureMonitorTraceExporter({
  connectionString: AI_CONNECTION_STRING
});

provider.addSpanProcessor(
  new BatchSpanProcessor(azExporter, {
    bufferSize: 1000, // 1000 spans
    bufferTimeout: 5000 // 5 seconds
  })
);

provider.register();

const tracer = provider.getTracer("sample tracer");
azureSdkTracing.setTracer(tracer);

exports.default = tracer;
```

### <a name="add-tracer-to-main-app"></a>Ajouter le traceur à l’application principale

Effectuez les mises à jour suivantes du fichier **token.js** :

Exiger **tracing.js** :

```javascript
const { CommunicationIdentityClient } = require("@azure/communication-identity");
const tracer = require("./tracing.js");

async function main() {
```

Créer une étendue racine et exporter des données

```javascript
  console.log("User deleted successfully.\n");
}

const rootSpan = tracer.startSpan("Root Identity Span");
tracer.withSpan(rootSpan, async function() {
  try {
    await main();
  } catch (error) {
    console.error("Error running sample:", error);
  } finally {
    // End the optional root span on completion
    rootSpan.end();
  }
}).then(function() {
  console.log("Awaiting batched span processor to export batched spans...");

  setTimeout(function() {
    console.log("Spans exported.");
  }, 6000);
});
```

## <a name="run-the-code"></a>Exécuter le code

Veillez à remplacer les textes d’espace réservé par des valeurs de chaîne de connexion valides.

Utilisez la commande node pour exécuter le code que vous avez ajouté au fichier **token.js**.

```console
node token.js
```