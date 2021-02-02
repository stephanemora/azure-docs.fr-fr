---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760664"
---
## <a name="conditional-access-and-claims-challenges"></a>Accès conditionnel et revendications

Quand vous obtenez des jetons silencieusement, votre application risque de recevoir des erreurs quand une [revendication d’accès conditionnel](../articles/active-directory/develop/v2-conditional-access-dev-guide.md), comme une stratégie d’authentification multifacteur, est exigée par une API à laquelle vous essayez d’accéder.

Le modèle de gestion de cette erreur consiste à acquérir de manière interactive un jeton à l’aide de MSAL. Cela adresse une invite à l’utilisateur et lui donne la possibilité de satisfaire à la stratégie d’accès conditionnel exigée.

Dans certains cas, lors de l’appel d’une API qui exige un accès conditionnel, vous pouvez recevoir une revendication dans l’erreur de la part de l’API. Par exemple, si la stratégie d’accès conditionnel consiste à utiliser un appareil managé (Intune), l’erreur est de type [AADSTS53000 : Votre appareil doit être managé pour accéder à cette ressource](../articles/active-directory/develop/reference-aadsts-error-codes.md) ou quelque chose de semblable. Dans ce cas, vous pouvez transmettre la revendication dans l’appel d’acquisition du jeton, afin que l’utilisateur soit invité à satisfaire à la stratégie appropriée.
