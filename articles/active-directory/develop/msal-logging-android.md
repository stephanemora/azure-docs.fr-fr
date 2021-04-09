---
title: Journalisation des erreurs et des exceptions dans MSAL pour Android
titleSuffix: Microsoft identity platform
description: En savoir plus sur la journalisation des erreurs et des exceptions dans MSAL pour Android.
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
ms.openlocfilehash: ce0929adbb2b0213cfd4ee61fe795a2d5f33c648
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954882"
---
# <a name="logging-in-msal-for-android"></a>Journalisation dans MSAL pour Android

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>Journalisation dans MSAL pour Android à l’aide de Java

Activez la journalisation lors de la création de l’application en créant un rappel de journalisation. Le rappel utilise les paramètres suivants :

- `tag` est une chaîne transmise au rappel par la bibliothèque. Il est associé à l’entrée de journal et peut être utilisé pour trier les messages de journalisation.
- `logLevel` vous permet de définir le niveau de journalisation souhaité. Les niveaux de journalisation pris en charge sont les suivants : `Error`, `Warning`, `Info` et `Verbose`.
- `message` correspond au contenu de l’entrée de journal.
- `containsPII` indique si les messages contenant des données personnelles ou organisationnelles sont journalisés. Par défaut, il est défini sur false. Votre application n’enregistre donc pas de données personnelles. Si `containsPII` a la valeur `true`, cette méthode reçoit les messages à deux reprises : une fois avec le paramètre `containsPII` défini sur `false` et le `message` sans données personnelles, et une deuxième fois avec le paramètre `containsPii` défini sur `true` et le message pouvant contenir des données personnelles. Dans certains cas (lorsque le message ne contient pas de données personnelles), le message doit être le même.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Par défaut, l’enregistreur d’événements MSAL ne capture pas les informations d’identification personnelles ou informations d’identification organisationnelles.
Pour activer la journalisation des informations d’identification personnelles et des informations d’identification organisationnelles :

```java
Logger.getInstance().setEnablePII(true);
```

Pour désactiver la journalisation des données personnelles et organisationnelles :

```java
Logger.getInstance().setEnablePII(false);
```

Par défaut, la journalisation dans logcat est désactivée. Pour activer :

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples de code, consultez [Exemples de code de la plateforme d’identités Microsoft](sample-v2-code.md).