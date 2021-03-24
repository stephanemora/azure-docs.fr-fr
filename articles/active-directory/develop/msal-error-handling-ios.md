---
title: Gérer les erreurs et les exceptions dans MSAL pour iOS/macOS
titleSuffix: Microsoft identity platform
description: Découvrez comment gérer les erreurs et exceptions, les défis relatifs aux revendications d’accès conditionnel et les nouvelles tentatives dans MSAL pour les applications iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, oldalton
ms.custom: aaddev
ms.openlocfilehash: f7f2abadb7586e1b19168eb46a54bad53caa05cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761112"
---
# <a name="handle-errors-and-exceptions-in-msal-for-iosmacos"></a>Gérer les erreurs et les exceptions dans MSAL pour iOS/macOS

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-iosmacos"></a>Gestion des erreurs dans MSAL pour iOS/macOS

La liste complète des erreurs MSAL pour iOS et macOS est répertoriée dans l’[énumération MSALError](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Toutes les erreurs générées par MSAL sont retournées avec le domaine `MSALErrorDomain`.

Pour les erreurs système, MSAL retourne l’erreur `NSError` originale de l’API système. Par exemple, si l’acquisition de jeton échoue en raison d’une absence de connectivité réseau, MSAL retourne une erreur avec le domaine `NSURLErrorDomain` et le code `NSURLErrorNotConnectedToInternet`.

Nous vous recommandons de gérer au moins les deux erreurs MSAL suivantes côté client :

- `MSALErrorInteractionRequired`: l’utilisateur doit effectuer une requête interactive. De nombreuses conditions peuvent entraîner cette erreur, par exemple une session d’authentification arrivée à expiration ou le besoin d’autres exigences d’authentification. Appelez l’API d’acquisition interactive de jetons MSAL pour effectuer la récupération. 

- `MSALErrorServerDeclinedScopes`: Toutes les étendues ou certaines d’entre elles ont été refusées. Décidez si vous souhaitez continuer avec uniquement les étendues accordées ou arrêter le processus de connexion.

> [!NOTE]
> L’énumération `MSALInternalError` doit être utilisée uniquement pour référence et débogage. N’essayez pas de gérer automatiquement ces erreurs au moment de l’exécution. Si votre application rencontre l’une des erreurs se trouvant sous `MSALInternalError`, vous pouvez afficher un message d’utilisateur générique expliquant ce qui s’est passé.

Par exemple, `MSALInternalErrorBrokerResponseNotReceived` signifie que l’utilisateur n’a pas effectué l’authentification et est retourné manuellement à l’application. Dans ce cas, votre application doit afficher un message d’erreur générique expliquant que l’authentification n’est pas terminée et suggérant qu’elle essaie de s’authentifier à nouveau.

L’exemple de code Objective-C suivant illustre les meilleures pratiques pour gérer certaines conditions d’erreur courantes.

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

MSAL pour iOS et macOS vous permet de demander des revendications spécifiques dans des scénarios d’acquisition de jetons en mode interactif et en mode silencieux.

Pour demander des revendications personnalisées, spécifiez `claimsRequest` dans `MSALSilentTokenParameters` ou `MSALInteractiveTokenParameters`.

Pour plus d’informations, consultez [Demander des revendications personnalisées à l’aide de MSAL pour iOS et macOS](request-custom-claims.md).

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Étapes suivantes

Envisagez l’activation de la [journalisation dans MSAL pour iOS/macOS](msal-logging-ios.md) pour vous aider à diagnostiquer et à déboguer les problèmes.
