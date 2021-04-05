---
title: Gérer les erreurs et les exceptions dans MSAL pour Python
titleSuffix: Microsoft identity platform
description: Découvrez comment gérer les erreurs et exceptions, les défis relatifs aux revendications d’accès conditionnel et les nouvelles tentatives dans MSAL pour les applications Python.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761082"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Gérer les erreurs et les exceptions dans MSAL pour Python

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Gestion des erreurs dans MSAL pour Python

Dans MSAL pour Python, la plupart des erreurs sont transmises sous la forme d’une valeur renvoyée de l’appel d’API. L’erreur est représentée sous la forme d’un dictionnaire contenant la réponse JSON de la plateforme d’identité Microsoft.

* Une réponse correcte contient la clé `"access_token"`. Le format de la réponse est défini par le protocole OAuth2. Pour plus d’informations, consultez [5.1 Réponse réussie](https://tools.ietf.org/html/rfc6749#section-5.1)
* Une réponse d’erreur contient `"error"` et généralement `"error_description"`. Le format de la réponse est défini par le protocole OAuth2. Pour plus d’informations, consultez [5.2 Réponse d’erreur](https://tools.ietf.org/html/rfc6749#section-5.2)

Lorsqu’une erreur est retournée, la clé `"error_description"` contient un message explicite qui, à son tour, contient un code d’erreur de la plateforme d’identité Microsoft. Pour obtenir les divers codes d’erreur, consultez [Codes d’erreur d’authentification et d’autorisation](./reference-aadsts-error-codes.md).

Dans MSAL pour Python, les exceptions sont rares car la plupart des erreurs sont gérées en renvoyant une valeur d’erreur. L’exception `ValueError` est levée uniquement en cas de problème lié à la façon dont vous essayez d’utiliser la bibliothèque, par exemple lorsque des paramètres d’API sont malformés.

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Étapes suivantes

Envisagez l’activation de la [journalisation dans MSAL pour Python](msal-logging-python.md) pour vous aider à diagnostiquer et à déboguer les problèmes.
