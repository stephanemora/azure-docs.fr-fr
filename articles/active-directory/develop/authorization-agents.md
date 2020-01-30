---
title: Agents d’autorisation et comment les activer | Azure
description: Découvrez les différents agents d’autorisation que Microsoft Authentication Library (MSAL) permet à votre application Android d’utiliser et comment les activer.
services: active-directory
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: c2d0c8a877ba856aa7a93bfc960c44b63cbe8f9c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697997"
---
# <a name="authorization-agents-android"></a>Agents d’autorisation (Android)

Cet article décrit les différents agents d’autorisation que Microsoft Authentication Library (MSAL) permet à votre application d’utiliser et comment les activer.

Le choix d’une stratégie spécifique pour les agents d’autorisation est facultatif et représente des fonctionnalités supplémentaires que les applications peuvent personnaliser. La plupart des applications vont utiliser les valeurs par défaut de MSAL (consultez [Comprendre le fichier de configuration MSAL Android](msal-configuration.md) pour voir les différentes valeurs par défaut).

MSAL prend en charge l’autorisation à l’aide d’un `WebView` ou du navigateur système.  L’image ci-dessous montre son aspect avec `WebView`, ou le navigateur système avec CustomTabs ou sans CustomTabs :

![Exemples de connexion MSAL](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implications de l’authentification unique

Par défaut, les applications intégrées à MSAL utilisent les onglets personnalisés du navigateur système pour l’autorisation. À la différence des vues web, les onglets personnalisés partagent un fichier jar de cookie avec le navigateur système par défaut, ce qui permet de réduire le nombre de connexions avec d’autres applications web ou natives qui ont été intégrées à des onglets personnalisés.

Si l’application utilise une stratégie `WebView` sans intégrer Microsoft Authenticator ou la prise en charge d’un portail d’entreprise, les utilisateurs ne disposent pas d’une expérience d’authentification unique (SSO) sur l’appareil ou entre des applications natives et des applications web.

Si l’application utilise MSAL avec Microsoft Authenticator ou la prise en charge d’un portail d’entreprise, les utilisateurs peuvent bénéficier d’une expérience SSO entre les applications s’ils sont activement connectés à l’une des applications.

## <a name="webview"></a>Vue web

Pour utiliser la vue web dans l’application, insérez la ligne suivante dans le fichier JSON de configuration de l’application passé à MSAL :

```json
"authorization_user_agent" : "WEBVIEW"
```

Lorsqu’il utilise la `WebView` dans l’application, l’utilisateur se connecte directement à l’application. Les jetons sont conservés à l’intérieur du bac à sable de l’application et ne sont pas disponibles en dehors du fichier jar du cookie de l’application. Ainsi, l’utilisateur ne peut pas bénéficier d’une expérience SSO entre les applications, sauf si les applications s’intègrent à Authenticator ou au portail d’entreprise.

Toutefois, `WebView` offre la possibilité de personnaliser l’apparence de l’interface utilisateur de connexion. Pour plus d’informations sur la façon de procéder à cette personnalisation, consultez [Vues web Android](https://developer.android.com/reference/android/webkit/WebView).

## <a name="default-browser-plus-custom-tabs"></a>Navigateur par défaut et onglets personnalisés

Par défaut, MSAL utilise le navigateur et une stratégie avec des [onglets personnalisés](https://developer.chrome.com/multidevice/android/customtabs). Vous pouvez indiquer explicitement cette stratégie pour empêcher des changements dans les versions ultérieures de `DEFAULT` en utilisant la configuration JSON suivante dans le fichier de configuration personnalisé :

```json
"authorization_user_agent" : "BROWSER"
```

Utilisez cette approche pour fournir une expérience SSO par le biais du navigateur de l’appareil. MSAL utilise un fichier jar de cookie partagé, qui permet à d’autres applications natives ou web de bénéficier de l’authentification unique sur l’appareil à l’aide du cookie de session persistent défini par MSAL.

## <a name="browser-selection-heuristic"></a>Heuristique de sélection du navigateur

Étant donné qu’il est impossible pour MSAL de spécifier le package de navigateur exact à utiliser sur chaque gamme de téléphones Android, MSAL implémente une heuristique de sélection du navigateur qui tente de fournir la meilleure expérience SSO entre les appareils.

MSAL récupère la liste complète des navigateurs installés sur l’appareil pour sélectionner celui à utiliser. La liste est dans l’ordre retourné par le gestionnaire de package, qui reflète indirectement les préférences de l’utilisateur. Par exemple, le navigateur par défaut, s’il est défini, est la première entrée de la liste. Le _premier_ navigateur dans la liste est choisi, qu’il prenne en charge les onglets personnalisés ou non. Si le navigateur prend en charge les onglets personnalisés, MSAL lance l’onglet personnalisé. Les onglets personnalisés ont une apparence plus proche de celle d’une `WebView` dans l’application et permettent une personnalisation de l’interface utilisateur de base. Pour plus d’informations, consultez [Onglets personnalisés dans Android](https://developer.chrome.com/multidevice/android/customtabs).

S’il n’existe aucun package de navigateur sur l’appareil, MSAL utilise la `WebView` dans l’application.

L’ordre des navigateurs dans la liste des navigateurs est déterminé par le système d’exploitation. Il va de la préférence la plus élevée à la moins élevée. Si vous ne changez pas le paramètre par défaut de l’appareil, le même navigateur est lancé pour chaque connexion afin de garantir une expérience d’authentification unique (SSO).

> [!NOTE]
> MSAL ne préfère plus toujours Chrome si un autre navigateur est défini par défaut. Par exemple, sur un appareil sur lequel sont à la fois préinstallés Chrome et un autre navigateur, MSAL utilise le navigateur que l’utilisateur a défini comme navigateur par défaut.

### <a name="tested-browsers"></a>Navigateurs testés

Les navigateurs suivants ont été testés pour voir s’ils redirigent correctement vers le `"redirect_uri"` spécifié dans le fichier de configuration :

| | Navigateur intégré | Chrome | Opera  | Microsoft Edge | UC Browser | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | Réussite | Réussite |Non applicable |Non applicable |Non applicable |Non applicable |
| Samsung S7 (API 25) | Réussite* | Réussite | Réussite | Réussite | Échec |Réussite |
| Huawei (API 26) |Réussite** | Réussite | Échec | Réussite | Réussite |Réussite |
| Vivo (API 26) |Réussite|Réussite|Réussite|Réussite|Réussite|Échec|
| Pixel 2 (API 26) |Réussite | Réussite | Réussite | Réussite | Échec |Réussite |
| Oppo | Réussite | Non applicable*** |Non applicable  |Non applicable |Non applicable | Non applicable|
| OnePlus (API 25) |Réussite | Réussite | Réussite | Réussite | Échec |Réussite |
| Nexus (API 28) |Réussite | Réussite | Réussite | Réussite | Échec |Réussite |
|MI | Réussite | Réussite | Réussite | Réussite | Échec |Réussite |

*Le navigateur intégré de Samsung est Samsung Internet.  
**Le navigateur intégré de Huawei est Huawei Browser.  
***Le navigateur par défaut ne peut pas être changé dans le paramètre de l’appareil Oppo.
