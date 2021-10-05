---
title: Journalisation des erreurs et des exceptions dans MSAL.NET
titleSuffix: Microsoft identity platform
description: En savoir plus sur la journalisation des erreurs et des exceptions dans MSAL.NET
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
ms.openlocfilehash: 518fc85aff920ffece511e383b2322d8e81266ee
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091860"
---
# <a name="logging-in-msalnet"></a>Journalisation dans MSAL.NET

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>Configurer la journalisation dans MSAL.NET

Dans la MSAL, la journalisation est définie lors de la création de l’application à l’aide du modificateur de générateur `.WithLogging`. Cette méthode accepte des paramètres optionnels :

- `Level` vous permet de définir le niveau de journalisation souhaité. Si vous le définissez sur Erreurs, vous obtiendrez seulement les erreurs.
- `PiiLoggingEnabled` vous permet de journaliser des données personnelles et d’organisation (informations d’identification personnelle) s’il est défini sur true. Par défaut, il est défini sur false. Votre application n’enregistre donc pas de données personnelles.
- `LogCallback` est défini sur un délégué qui effectue la journalisation. Si `PiiLoggingEnabled` a la valeur true, cette méthode recevra les messages qui peuvent inclure des informations d’identification personnelle, auquel cas l’indicateur `containsPii` aura la valeur true.
- `DefaultLoggingEnabled` active la journalisation par défaut pour la plateforme. La valeur par défaut est false. Si vous la définissez sur true elle utilise le suivi d’événements dans les applications de bureau/plateforme Windows universelle, NSLog sur iOS et logcat sur Android.

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

> [!TIP]
 > Pour des exemples de journalisation MSAL.NET et bien plus, consultez le [wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples de code, consultez [Exemples de code de la plateforme d’identités Microsoft](sample-v2-code.md).
