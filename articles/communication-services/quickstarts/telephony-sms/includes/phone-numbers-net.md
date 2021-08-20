---
ms.openlocfilehash: 6e470aaaad9879116460180b15f45b1419f51418
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201149"
---
> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- La dernière version de la [bibliothèque de client .NET Core](https://dotnet.microsoft.com/download/dotnet-core) pour votre système d’exploitation.
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Vérification du prérequis

- Dans une fenêtre de terminal ou de commande, exécutez la commande `dotnet` pour vérifier que la bibliothèque de client .NET est installée.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-c-application"></a>Créer une application C#

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `PhoneNumbersQuickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : **Program.cs**.

```console
dotnet new console -o PhoneNumbersQuickstart
```

Remplacez votre répertoire par le dossier d’application que vous venez de créer, puis utilisez la commande `dotnet build` pour compiler votre application.

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installer le package

Toujours dans le répertoire de l’application, installez le package de la bibliothèque de client Communication Services PhoneNumbers pour .NET à l’aide de la commande `dotnet add package`.

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0
```

Ajoutez une directive `using` en haut du fichier **Program.cs** pour inclure les espaces de noms.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
```

Mettez à jour la signature de fonction `Main` pour qu’elle soit asynchrone.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>Authentifier le client

Les clients de numéro de téléphone peuvent être authentifiés à l’aide de la chaîne de connexion obtenue à partir d’une ressource Azure Communication Services dans le [portail Azure][azure_portal].

```csharp
// Get a connection string to our Azure Communication Services resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

Les clients de numéro de téléphone ont également la possibilité de s’authentifier à l’aide de l’authentification Azure Active Directory. Avec cette option, les variables d’environnement `AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` et `AZURE_TENANT_ID` doivent être configurées pour l’authentification.

```csharp
// Get an endpoint to our Azure Communication Services resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>Gérer les numéros de téléphone

### <a name="search-for-available-phone-numbers"></a>Rechercher les numéros de téléphone disponibles

Pour pouvoir acheter des numéros de téléphone, vous devez d’abord rechercher les numéros de téléphone disponibles. Pour rechercher des numéros de téléphone, fournissez l'indicatif régional, le type d'affectation, les [fonctionnalités de numéro de téléphone](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), le [type de numéro de téléphone](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services) et la quantité. Notez que pour le type de numéro de téléphone gratuit, l’indicatif régional est facultatif.

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>Acheter des numéros de téléphone

Le résultat de la recherche de numéros de téléphone correspond à `PhoneNumberSearchResult`. Ce résultat contient un élément `SearchId` qui peut être transmis à l'API Acheter des numéros pour acquérir les numéros de la recherche. Notez que l'appel de l'API Acheter des numéros de téléphone sera facturé sur votre compte Azure.

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionResponseAsync();
```

### <a name="get-phone-numbers"></a>Obtenir le ou les numéros de téléphone

Après avoir acheté un numéro, vous pouvez le récupérer à partir du client.
```csharp
var getPhoneNumberResponse = await client.GetPurchasedPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

Vous pouvez également récupérer tous les numéros de téléphone achetés.
``` csharp
var purchasedPhoneNumbers = client.GetPurchasedPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>Mettre à jour les fonctionnalités de numéro de téléphone

Avec un numéro acheté, vous pouvez mettre à jour les fonctionnalités.

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>Libérer un numéro de téléphone

Vous pouvez libérer un numéro de téléphone acheté.

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionResponseAsync();
````

## <a name="run-the-code"></a>Exécuter le code

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```console
dotnet run
```

## <a name="sample-code"></a>Exemple de code

Vous pouvez télécharger l’exemple d’application à partir de [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers).
