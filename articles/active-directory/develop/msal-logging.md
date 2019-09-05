---
title: Journalisation dans les applications MSAL | Azure
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
ms.date: 04/22/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02c093375ba2dc5c851a2deb35bdea28338ee982
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135779"
---
# <a name="logging"></a>Journalisation
Applications Microsoft Authentication Library (MSAL) pour générer des messages de journal qui peuvent aider à diagnostiquer des problèmes et fournissent des détails. Une application peut configurer la journalisation avec quelques lignes de code et contrôler de façon personnalisée le niveau de détail, ainsi que permettre de savoir si des données personnelles et d’organisation sont consignées ou non. Il est recommandé de définir un rappel de journalisation et de donner aux utilisateurs le moyen d’envoyer des journaux lorsqu’ils rencontrent des problèmes d’authentification.

## <a name="logging-levels"></a>Niveaux de journalisation

L’enregistreur d’événements de MSAL autorise la capture de plusieurs niveaux de détail :

- Error: indique qu’un problème est survenu et qu’une erreur a été générée. À utiliser pour le débogage et l’identification des problèmes.
- Avertissement : événements suscitant des interrogations et sur lesquels l’application a besoin de plus d’informations. Il n’y a pas nécessairement eu une erreur ou une défaillance, mais l’objectif est d’effectuer un diagnostic et de mettre en avant des problèmes éventuels.
- Information : MSAL enregistrera des événements destinés à fournir des informations et pas nécessairement destinés à être débogués.
- Verbose : Par défaut. MSAL va enregistrer une grande quantité d’informations et donner des détails complets sur le comportement de la bibliothèque.

## <a name="personal-and-organizational-data"></a>Données personnelles et d’organisation
Par défaut, l’enregistreur d’événements MSAL ne capture pas de données personnelles ou d’organisation hautement sensibles. La bibliothèque vous offre la possibilité d’activer la journalisation de données personnelles et d’organisation si vous décidez de le faire.

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

- *localCallback* : une instance de rappel qui peut être fournie par le développeur pour consommer et publier des journaux de manière personnalisée. Implémentez la méthode localCallback en fonction de la façon dont vous souhaitez rediriger les journaux.

- *niveau* (facultatif) : le niveau de journalisation configurable. Les niveaux de journalisation pris en charge sont les suivants : Erreur, Avertissement, Info, Verbose. La valeur par défaut est Info.

- *PiiLoggingEnabled* (facultatif) : vous permet de journaliser des données personnelles et d’organisation s’il est défini sur true. Par défaut, il est défini sur false. Votre application n’enregistre donc pas de données personnelles. Les journaux de données personnelles ne sont jamais écrits dans les sorties par défaut telles que Console, Logcat ou NSLog. La valeur par défaut est false.

- *correlationId* (facultatif) : un identificateur unique utilisé pour mapper la demande avec la réponse à des fins de débogage. Guide des valeurs par défaut de RFC4122, version 4 (128 bits).

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
