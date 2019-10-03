---
title: Journalisation dans les applications MSAL | Plateforme d’identité Microsoft
description: En savoir plus sur la journalisation dans les applications Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dad8a276cd40b1ff04bbced833b5d70cec4fc87
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268581"
---
# <a name="logging-in-msal-applications"></a>Journalisation dans les applications MSAL

Les applications Microsoft Authentication Library (MSAL) génèrent des messages de journal qui contribuent à diagnostiquer des problèmes. Une application peut configurer la journalisation avec quelques lignes de code et contrôler de façon personnalisée le niveau de détail, ainsi que permettre de savoir si des données personnelles et d’organisation sont consignées ou non. Nous vous conseillons de créer un rappel de journalisation et de donner aux utilisateurs le moyen d’envoyer des journaux lorsqu’ils rencontrent des problèmes d’authentification.

## <a name="logging-levels"></a>Niveaux de journalisation

MSAL fournit plusieurs niveaux de détail de journalisation :

- Error: indique qu’un problème est survenu et qu’une erreur a été générée. À utiliser pour le débogage et l’identification des problèmes.
- Avertissement : Il n’y a pas nécessairement eu une erreur ou une défaillance, mais l’objectif est d’effectuer un diagnostic et de mettre en avant des problèmes éventuels.
- Information : MSAL enregistrera des événements destinés à fournir des informations et pas nécessairement destinés à être débogués.
- Verbose : Par défaut. MSAL enregistre les détails complets du comportement de la bibliothèque.

## <a name="personal-and-organizational-data"></a>Données personnelles et d’organisation

Par défaut, l’enregistreur d’événements MSAL ne capture pas de données personnelles ou d’organisation hautement sensibles. La bibliothèque permet d’activer la journalisation de données personnelles et d’organisation si vous décidez de le faire.

## <a name="logging-in-msalnet"></a>Journalisation dans MSAL.NET

 > [!NOTE]
 > Pour plus d’informations sur MSAL.NET, consultez le [wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki). Obtenez des exemples de journalisation MSAL.NET et bien plus encore.
 
Dans la MSAL 3.x, la journalisation est définie par l’application sur l’application lors de la création du modificateur de générateur `.WithLogging`. Cette méthode accepte des paramètres optionnels :

- *Niveau* vous permet de définir le niveau de journalisation souhaité. Si vous le définissez sur Erreurs, vous obtiendrez seulement les erreurs.
- *PiiLoggingEnabled* vous permet de journaliser des données personnelles et d’organisation s’il est défini sur true. Par défaut, il est défini sur false. Votre application n’enregistre donc pas de données personnelles.
- *LogCallback* est défini sur un délégué qui effectue la journalisation. Si *PiiLoggingEnabled* a la valeur true, cette méthode reçoit les messages à deux reprises : une fois avec le paramètre *containsPii* égal à «false» et le message sans données personnelles, et une deuxième fois avec le paramètre *containsPii* égal à la valeur true et le message pouvant contenir des données personnelles. Dans certains cas (lorsque le message ne contient pas de données personnelles), le message doit être le même.
- *DefaultLoggingEnabled* active la journalisation par défaut pour la plateforme. La valeur par défaut est false. Si vous la définissez sur true elle utilise le suivi d’événements dans les applications de bureau/plateforme Windows universelle, NSLog sur iOS et logcat sur Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Journalisation dans MSAL.js

 Vous pouvez activer la journalisation dans MSAL.js en passant un objet enregistreur d’événements lors de la configuration pour la création d’une instance `UserAgentApplication`. Cet objet enregistreur d'événements a les propriétés suivantes :

- `localCallback` : une instance de rappel qui peut être fournie par le développeur pour consommer et publier des journaux de manière personnalisée. Implémentez la méthode localCallback en fonction de la façon dont vous souhaitez rediriger les journaux.

- `level` (facultatif) : le niveau de journalisation configurable. Les niveaux de journalisation pris en charge sont les suivants : Erreur, Avertissement, Info, Verbose. La valeur par défaut est Info.

- `piiLoggingEnabled` (facultatif) : vous permet de journaliser des données personnelles et d’organisation s’il est défini sur true. Par défaut, il est défini sur false. Votre application n’enregistre donc pas de données personnelles. Les journaux de données personnelles ne sont jamais écrits dans les sorties par défaut telles que Console, Logcat ou NSLog. La valeur par défaut est false.

- `correlationId` (facultatif) : un identificateur unique utilisé pour mapper la requête avec la réponse à des fins de débogage. Guide des valeurs par défaut de RFC4122, version 4 (128 bits).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="logging-in-msal-for-ios-and-macos"></a>Journalisation dans MSAL pour iOS et macOS

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

Objective-C
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

Swift
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

### <a name="personal-identifiable-information-pii"></a>Informations d’identification personnelle (PII)

Par défaut, MSAL ne capture ou n’enregistre aucune information d'identification personnelle. La bibliothèque permet aux développeurs d’applications d’activer cette fonctionnalité via une propriété de la classe MSALLogger. Lorsque vous activez la journalisation des informations d’identification personnelle, l’application devient responsable de la gestion des données hautement sensibles et du suivi des exigences réglementaires.

Objective-C
```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

Swift
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Niveaux de journalisation

Pour définir le niveau de journalisation lorsque vous utilisez MSAL pour iOS et macOS, optez pour l’une des valeurs suivantes :

|Niveau  |Description |
|---------|---------|
| `MSALLogLevelNothing`| Désactiver la journalisation |
| `MSALLogLevelError` | Niveau par défaut, affiche uniquement les informations lorsque des erreurs se produisent |
| `MSALLogLevelWarning` | Avertissements |
| `MSALLogLevelInfo` |  Points d’entrée de bibliothèque, avec paramètres et diverses opérations de trousseau |
|`MSALLogLevelVerbose`     |  Suivi API       |

Par exemple :

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 Swift
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Format de message de journal

La partie message des messages de journal MSAL se présente au format `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Par exemple :

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Les ID de corrélation et les timestamps facilitent le suivi des problèmes. Les informations de timestamp et d’ID de corrélation sont disponibles dans le message de journal. Seuls les messages de journalisation MSAL permettent de les récupérer de manière fiable.
