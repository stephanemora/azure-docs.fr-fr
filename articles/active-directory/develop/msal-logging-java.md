---
title: Journalisation des erreurs et des exceptions dans MSAL pour Java
titleSuffix: Microsoft identity platform
description: En savoir plus sur la journalisation des erreurs et des exceptions dans MSAL pour Java
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
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954916"
---
# <a name="logging-in-msal-for-java"></a>Journalisation dans MSAL pour Java

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-java-logging"></a>Journalisation MSAL pour Java

MSAL pour Java vous permet d’utiliser la bibliothèque de journalisation que vous utilisez déjà avec votre application, à condition qu’elle soit compatible avec SLF4J. MSAL pour Java utilise la [façade de journalisation simple pour Java](http://www.slf4j.org/) (SLF4J) comme façade ou abstraction simple pour différents frameworks de journalisation tels que [java.util.logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) et [log4j](https://logging.apache.org/log4j/2.x/). SLF4J permet à l’utilisateur de brancher le framework de journalisation souhaité au moment du déploiement.

Par exemple, pour utiliser Logback comme framework de journalisation dans votre application, ajoutez la dépendance Logback au fichier pom Maven pour votre application :

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Ajoutez ensuite le fichier de configuration Logback :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J est automatiquement lié à Logback au moment du déploiement. Les journaux MSAL seront écrits dans la console.

Pour obtenir des instructions sur la liaison à d’autres frameworks de journalisation, consultez le [Manuel SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Informations personnelles et propres à l’organisation

Par défaut, la journalisation MSAL ne capture et ne consigne dans un journal aucune donnée personnelle ou propres à une organisation. Dans l’exemple suivant, la journalisation des données personnelles ou organisationnelles est désactivée par défaut :

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Activez la journalisation des données personnelles et organisationnelles en définissant `logPii()` sur le générateur d’applications clientes. Si vous activez la journalisation des informations d’identification personnelles ou propres à l’organisation, votre application doit garantir la gestion de façon sécurisée des données hautement sensibles, et respecter les exigences réglementaires.

Dans l’exemple suivant, la journalisation des informations d’identification personnelles ou organisationnelles est activée :

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples de code, consultez [Exemples de code de la plateforme d’identités Microsoft](sample-v2-code.md).