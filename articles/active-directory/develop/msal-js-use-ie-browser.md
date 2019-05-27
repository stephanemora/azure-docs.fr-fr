---
title: Utiliser Internet Explorer (bibliothèque d’authentification Microsoft pour JavaScript) | Azure
description: En savoir plus sur l’utilisation de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js) avec le navigateur Internet Explorer.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf8c84120f4c90d3943cfc31ffbf9aafcec0ba3
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873914"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Problèmes connus dans les navigateurs Internet Explorer et Microsoft Edge avec MSAL.js

Bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js) est généré pour [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) afin qu’il peut s’exécuter dans Internet Explorer. Toutefois, il existe quelques éléments à connaître.

## <a name="run-an-app-in-internet-explorer"></a>Exécuter une application dans Internet Explorer
Si vous avez l’intention d’utiliser MSAL.js dans les applications qui peuvent s’exécuter dans Internet Explorer, vous devrez ajouter une référence à un polyfill promesse avant de le référencer le script MSAL.js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Il s’agit, car Internet Explorer ne prend pas en charge les promesses de JavaScript en mode natif.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Débogage d’une application en cours d’exécution dans Internet Explorer

### <a name="running-in-production"></a>En cours d’exécution en production
Déploiement de votre application en production (par exemple dans Azure Web apps) normalement fonctionne correctement, fournie à que l’utilisateur final a accepté les fenêtres contextuelles. Nous l’avons testée avec Internet Explorer 11.

### <a name="running-locally"></a>Exécution locale
Si vous souhaitez exécuter et déboguer localement votre application en cours d’exécution dans Internet Explorer, vous devez connaître les considérations suivantes (supposent que vous souhaitez exécuter votre application en tant que *http://localhost:1234*) :

- Internet Explorer possède un mécanisme de sécurité nommé « mode protégé », ce qui empêche que MSAL.js fonctionne correctement. Parmi les symptômes, après vous être connecté, la page peut être redirigée vers http://localhost:1234/null.

- Pour exécuter et déboguer votre application localement, vous devez désactiver ce « mode protégé ». Pour cela :

    1. Cliquez sur Internet Explorer **outils** (l’icône d’engrenage).
    1. Sélectionnez **Options Internet** , puis le **sécurité** onglet.
    1. Cliquez sur le **Internet** de zone, puis décochez **activer le Mode protégé (nécessite le redémarrage d’Internet Explorer)**. Internet Explorer avertit que votre ordinateur n’est plus protégé. Cliquez sur **OK**.
    1. Redémarrez Internet Explorer.
    1. Exécuter et déboguer votre application.

Lorsque vous avez terminé, restaurez les paramètres de sécurité d’Internet Explorer.  Sélectionnez **paramètres** -> **Options Internet** -> **sécurité** -> **rétablir toutes les zones au niveau par défaut**.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [problèmes connus lors de l’utilisation de MSAL.js dans Internet Explorer](msal-js-use-ie-browser.md).