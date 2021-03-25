---
title: Gérer les erreurs et les exceptions dans MSAL4J
titleSuffix: Microsoft identity platform
description: Découvrez comment gérer les erreurs et exceptions, les défis relatifs aux revendications d’accès conditionnel et les nouvelles tentatives dans les applications MSAL4J.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760676"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>Gérer les erreurs et les exceptions dans MSAL pour Java

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>Gestion des erreurs dans MSAL pour Java

Dans MSAL pour Java, il existe trois types d’exceptions : `MsalClientException`, `MsalServiceException` et `MsalInteractionRequiredException` ; tous héritant de `MsalException`.

- `MsalClientException` est levée lorsqu’une erreur se produit localement dans la bibliothèque ou l’appareil.
- `MsalServiceException` est levée lorsque le service d’émission de jeton de sécurité (STS) renvoie une réponse d’erreur ou qu’une autre erreur de mise en réseau se produit.
- `MsalInteractionRequiredException` est levée lorsque l’interaction avec l’interface utilisateur est requise pour que l’authentification aboutisse.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` expose les en-têtes HTTP retournés dans les demandes au STS. Accédez-y via `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

L’un des codes d’état courants renvoyés par MSAL pour Java lors de l’appel à `AcquireTokenSilently()` est `InvalidGrantError`. Cela signifie que l’interaction de l’utilisateur supplémentaire est nécessaire pour qu’un jeton d’authentification puisse être émis. Votre application doit rappeler la bibliothèque d’authentification, mais en mode interactif en envoyant `AuthorizationCodeParameters` ou `DeviceCodeParameters` pour les applications clientes publiques.

La plupart du temps, lorsque `AcquireTokenSilently` échoue, cela est dû au fait que le cache du jeton n’a pas de jeton correspondant à votre demande. Les jetons d’accès expirent dans une heure et `AcquireTokenSilently` tente d’en obtenir un nouveau en fonction d’un jeton d’actualisation. En termes d’OAuth2, il s’agit du flux de jeton d’actualisation. Ce flux peut également échouer pour diverses raisons, comme lorsqu’un administrateur de locataire configure des stratégies de connexion plus strictes.

Certaines conditions qui provoquent cette erreur sont faciles à résoudre par les utilisateurs. Par exemple, ils peuvent être amenés à accepter les conditions d’utilisation ou la demande ne peut pas être satisfaite avec la configuration actuelle, car l’ordinateur doit se connecter à un réseau d’entreprise spécifique.

MSAL expose un champ `reason`, que vous pouvez utiliser pour fournir une meilleure expérience utilisateur. Par exemple, le champ `reason` peut vous amener à indiquer à l’utilisateur que son mot de passe a expiré ou qu’il devra fournir son consentement pour utiliser certaines ressources. Les valeurs prises en charge font partie de l’énumération `InteractionRequiredExceptionReason` :

| Motif | Signification | Gestion recommandée |
|---------|-----------|-----------------------------|
| `BasicAction` | La condition peut être résolue par l’interaction de l’utilisateur pendant le flux d’authentification interactive. | Appelez `acquireToken` avec des paramètres interactifs. |
| `AdditionalAction` | La condition peut être résolue par une interaction corrective supplémentaire avec le système, en dehors du flux d’authentification interactive. | Appelez `acquireToken` avec des paramètres interactifs pour afficher un message expliquant l’action corrective à prendre. L’appel de l’application peut choisir de masquer les flux qui requièrent une action supplémentaire si l’utilisateur est peu susceptible d’effectuer l’action corrective. |
| `MessageOnly` | La condition ne peut pas être résolue pour l’instant. Lancez le flux d’authentification interactive pour afficher un message qui explique la condition. | Appelez `acquireToken` avec des paramètres interactifs pour afficher un message expliquant la condition. `acquireToken` renvoie une erreur `UserCanceled` lorsque l’utilisateur lit le message et ferme la fenêtre. L’application peut choisir de masquer les flux qui résultent en un message si l’utilisateur est peu susceptible de tirer parti du message. |
| `ConsentRequired`| Le consentement de l’utilisateur est manquant ou a été révoqué. |Appelez `acquireToken` avec des paramètres interactifs afin que l’utilisateur puisse donner son consentement. |
| `UserPasswordExpired` | Le mot de passe de l’utilisateur a expiré. | Appelez `acquireToken` avec un paramètre interactif pour permettre à l’utilisateur de réinitialiser son mot de passe. |
| `None` |  D’autres détails sont fournis. La condition peut être résolue par l’interaction de l’utilisateur pendant le flux d’authentification interactive. | Appelez `acquireToken` avec des paramètres interactifs. |

### <a name="code-example"></a>Exemple de code

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Étapes suivantes

Envisagez l’activation de la [journalisation dans MSAL pour Java](msal-logging-java.md) pour vous aider à diagnostiquer et à déboguer les problèmes.
