---
ms.openlocfilehash: 956c92c5c020f892b8148e9d43d403b1099fbdba
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113276"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) versions Active LTS et Maintenance LTS (8.11.1 et 10.14.1 recommandées).
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Vérification du prérequis

- Dans une fenêtre de terminal ou de commande, exécutez `node --version` pour vérifier que Node.js est installé.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-nodejs-application"></a>Créer une application Node.js

Pour commencer, ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Exécutez `npm init -y` pour créer un fichier **package.json** avec les paramètres par défaut.

```console
npm init -y
```

Créez un fichier appelé **phone-numbers-quickstart.js** à la racine du répertoire que vous venez de créer. Ajoutez-lui l’extrait de code suivant :

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>Installer le package

Utilisez la commande `npm install` pour installer la bibliothèque de client Azure Communication Services Phone Numbers pour JavaScript.

```console
npm install @azure/communication-phone-numbers --save
```

L’option `--save` ajoute la bibliothèque en tant que dépendance dans votre fichier **package.json**.

## <a name="authenticate-the-client"></a>Authentifier le client

Importez le **PhoneNumbersClient** à partir de la bibliothèque de client et instanciez-le avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion pour la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../../create-communication-resource.md#store-your-connection-string).

Ajoutez le code suivant au début du fichier **phone-numbers-quickstart.js** :

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>Gérer les numéros de téléphone

### <a name="search-for-available-phone-numbers"></a>Rechercher les numéros de téléphone disponibles

Pour pouvoir acheter des numéros de téléphone, vous devez d’abord rechercher les numéros de téléphone disponibles. Pour rechercher des numéros de téléphone, fournissez l'indicatif régional, le type d'affectation, les [fonctionnalités de numéro de téléphone](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), le [type de numéro de téléphone](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services) et la quantité. Notez que pour le type de numéro de téléphone gratuit, l’indicatif régional est facultatif.

Ajoutez l’extrait de code suivant à votre fonction `main`.

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>Acheter un numéro de téléphone

Le résultat de la recherche de numéros de téléphone correspond à `PhoneNumberSearchResult`. Ce résultat contient un élément `searchId` qui peut être transmis à l'API Acheter des numéros pour acquérir les numéros de la recherche. Notez que l'appel de l'API Acheter des numéros de téléphone sera facturé sur votre compte Azure.

Ajoutez l’extrait de code suivant à votre fonction `main`.

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>Mettre à jour les fonctionnalités de numéro de téléphone

Après avoir acheté un numéro de téléphone, ajoutez le code suivant pour mettre à jour ses fonctionnalités :

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>Obtenir le ou les numéros de téléphone achetés

Après avoir acheté un numéro, vous pouvez le récupérer à partir du client. Ajoutez le code suivant à votre fonciton `main` pour obtenir le numéro de téléphone que vous venez d’acheter :

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log(`These capabilities: ${capabilities}, should be the same as these: ${updateRequest}.`);
```

Vous pouvez également récupérer tous les numéros de téléphone achetés.

```javascript
const phoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of phoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>Libérer un numéro de téléphone

Vous pouvez maintenant libérer le numéro de téléphone acheté. Ajoutez l’extrait de code ci-dessous à votre fonction `main` :

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await client.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>Exécuter le code

Utilisez la commande `node` pour exécuter le code que vous avez ajouté au fichier **phone-numbers-quickstart.js**.

```console
node phone-numbers-quickstart.js
```