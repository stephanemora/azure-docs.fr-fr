---
title: Journalisation des erreurs et des exceptions dans MSAL pour iOS/macOS
titleSuffix: Microsoft identity platform
description: En savoir plus sur la journalisation des erreurs et des exceptions dans MSAL pour iOS/macOS
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
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763240"
---
# <a name="logging-in-msal-for-iosmacos"></a>Journalisation dans MSAL pour iOS/macOS

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>Journalisation MSAL pour iOS et macOS - ObjC

Définissez un rappel pour capturer la journalisation MSAL et l’incorporer dans la journalisation de votre propre application. La signature du rappel se présente comme suit :

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Par exemple :

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>Données à caractère personnel

Par défaut, MSAL ne capture et n’enregistre aucune information d’identification personnelle. La bibliothèque permet aux développeurs d’applications d’activer cette fonctionnalité via une propriété de la classe MSALLogger. Lorsque vous activez `pii.Enabled`, l’application devient responsable de la gestion de façon sécurisé des données hautement sensibles et du suivi des exigences réglementaires.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Niveaux de journalisation

Pour définir le niveau de journalisation lorsque vous utilisez MSAL pour iOS et macOS, optez pour l’une des valeurs suivantes :

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| Désactiver la journalisation |
| `MSALLogLevelError` | Niveau par défaut, affiche uniquement les informations lorsque des erreurs se produisent |
| `MSALLogLevelWarning` | Avertissements |
| `MSALLogLevelInfo` |  Points d’entrée de bibliothèque, avec paramètres et diverses opérations de trousseau |
|`MSALLogLevelVerbose`     |  Suivi API |

Par exemple :

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Format de message de journal

La partie message des messages de journal MSAL se présente au format `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Par exemple :

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Les ID de corrélation et les timestamps facilitent le suivi des problèmes. Les informations de timestamp et d’ID de corrélation sont disponibles dans le message de journal. Seuls les messages de journalisation MSAL permettent de les récupérer de manière fiable.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>Journalisation MSAL pour iOS et macOS - Swift

Définissez un rappel pour capturer la journalisation MSAL et l’incorporer dans la journalisation de votre propre application. La signature (représentée en Objective-C) pour le rappel ressemble à ceci :

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Par exemple :

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>Données à caractère personnel

Par défaut, MSAL ne capture et n’enregistre aucune information d’identification personnelle. La bibliothèque permet aux développeurs d’applications d’activer cette fonctionnalité via une propriété de la classe MSALLogger. Lorsque vous activez `pii.Enabled`, l’application devient responsable de la gestion de façon sécurisé des données hautement sensibles et du suivi des exigences réglementaires.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Niveaux de journalisation

Pour définir le niveau de journalisation lorsque vous utilisez MSAL pour iOS et macOS, optez pour l’une des valeurs suivantes :

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| Désactiver la journalisation |
| `MSALLogLevelError` | Niveau par défaut, affiche uniquement les informations lorsque des erreurs se produisent |
| `MSALLogLevelWarning` | Avertissements |
| `MSALLogLevelInfo` |  Points d’entrée de bibliothèque, avec paramètres et diverses opérations de trousseau |
|`MSALLogLevelVerbose`     |  Suivi API |

Par exemple :

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Format de message de journal

La partie message des messages de journal MSAL se présente au format `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Par exemple :

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Les ID de corrélation et les timestamps facilitent le suivi des problèmes. Les informations de timestamp et d’ID de corrélation sont disponibles dans le message de journal. Seuls les messages de journalisation MSAL permettent de les récupérer de manière fiable.

---

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples de code, consultez [Exemples de code de la plateforme d’identités Microsoft](sample-v2-code.md).