---
title: Journalisation des erreurs et des exceptions dans MSAL.js
titleSuffix: Microsoft identity platform
description: En savoir plus sur la journalisation des erreurs et des exceptions dans MSAL.js
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d7463e3376847a219750254548c25ca79f4cdfb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954815"
---
# <a name="logging-in-msaljs"></a>Journalisation dans MSAL.js

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>Configurer la journalisation dans MSAL.js

Activez la journalisation dans MSAL.js (JavaScript) en passant un objet enregistreur d’événements lors de la configuration pour la création d’une instance `UserAgentApplication`. Cet objet enregistreur d'événements a les propriétés suivantes :

- `localCallback` : une instance de rappel qui peut être fournie par le développeur pour consommer et publier des journaux de manière personnalisée. Implémentez la méthode localCallback en fonction de la façon dont vous souhaitez rediriger les journaux.
- `level` (facultatif) : le niveau de journalisation configurable. Les niveaux de journalisation pris en charge sont les suivants : `Error`, `Warning`, `Info` et `Verbose`. Par défaut, il s’agit de `Info`.
- `piiLoggingEnabled` (facultatif) : s'il est défini sur true, vous permet de journaliser des données personnelles et organisationnelles. Par défaut, il est défini sur false. Votre application n’enregistre donc pas de données personnelles. Les journaux de données personnelles ne sont jamais écrits dans les sorties par défaut telles que Console, Logcat ou NSLog.
- `correlationId` (facultatif) : un identificateur unique utilisé pour mapper la requête avec la réponse à des fins de débogage. Guide des valeurs par défaut de RFC4122, version 4 (128 bits).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
    system: {
        logger: new Msal.Logger(
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples de code, consultez [Exemples de code de la plateforme d’identités Microsoft](sample-v2-code.md).