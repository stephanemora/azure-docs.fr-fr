---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 0a3e3d80e5b669884d19d5eb60260a31b1836de3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700481"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du SDK web Appel ACS

Le transfert d’appels est une fonctionnalité étendue de l’API `Call` principale. Vous devez d’abord obtenir l’objet d’API de la fonctionnalité de transfert :

```js
const callTransferApi = call.api(Features.Transfer);
```

Les transferts d’appels impliquent trois parties :

- *Transférant*  : personne qui lance la demande de transfert.
- *Transféré* : personne qui est transférée.
- *Cible de transfert* : personne vers laquelle le transfert est effectué.

Les transferts suivent les étapes ci-dessous :

1. Il existe déjà un appel connecté entre le *transférant* et le *transféré*. Le *transférant* décide de transférer l’appel depuis le *transféré* vers la *cible de transfert*.
1. Le *transférant* appelle l’API `transfer`.
1. Le *transféré* décide de faire un `accept` ou un `reject` de la demande de transfert vers la *cible de transfert* en utilisant un événement `transferRequested`.
1. La *cible de transfert* reçoit un appel entrant seulement si le *transféré* accepte la demande de transfert.

Pour transférer un appel en cours, vous pouvez utiliser l’API `transfer`. `transfer` prend les `transferCallOptions` facultatives, ce qui vous permet de définir un indicateur `disableForwardingAndUnanswered` :

- `disableForwardingAndUnanswered = false` : si la *cible de transfert* ne répond pas à l’appel transféré, le transfert suit les paramètres de transfert et de non-réponse de la *cible de transfert*.
- `disableForwardingAndUnanswered = true` : si la *cible de transfert* ne répond pas à l’appel transféré, la tentative de transfert se termine.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

L’API `transfer` vous permet de vous abonner aux événements `transferStateChanged` et `transferRequested`. Un événement `transferRequested` provient d’une instance `call` ; un événement `transferStateChanged` et les informations `state` et `error` du transfert proviennent d’une instance `transfer`.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

Le *transféré* peut accepter ou rejeter la demande de transfert lancée par le *transférant* dans l’événement `transferRequested` en utilisant `accept()` ou `reject()` dans `transferRequestedEventArgs`. Vous pouvez accéder aux informations de `targetParticipant` et aux méthodes `accept` ou `reject` dans `transferRequestedEventArgs`.

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
    args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
    args.reject();
});
```