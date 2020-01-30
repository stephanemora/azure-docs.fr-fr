---
title: Considérations sur les navigateurs système Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez les considérations particulières à prendre en compte lors de l’utilisation de navigateurs système sur Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 9346a4d5eaabb2af490afc13d5785a8f8233e53f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695044"
---
#  <a name="xamarin-android-system-browser-considerations-with-msalnet"></a>Considérations relatives aux navigateurs système Xamarin Android avec MSAL.NET

Cet article traite des considérations spécifiques lors de l’utilisation du navigateur système sur Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).

Depuis MSAL.NET 2.4.0-preview, MSAL.NET prend en charge d’autres navigateurs en plus de Chrome, et ne nécessite plus l’installation de Chrome sur l’appareil Android pour s’authentifier.

Nous vous recommandons d’utiliser les navigateurs qui prennent en charge les onglets personnalisés, tels que :

| Navigateurs avec prise en charge des onglets personnalisés | Nom du package |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Brave | com.brave.browser|

En plus des navigateurs avec prise en charge des onglets personnalisés, en fonction de nos tests, certains navigateurs qui ne prennent pas en charge les onglets personnalisés sont également valables pour l’authentification : Opera, Opera Mini, InBrowser et Maxthon. Pour plus d’informations, consultez [Tableau des résultats des tests](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Problèmes connus

- Si l’utilisateur ne dispose d’aucun navigateur sur son appareil, MSAL.NET enverra une exception `AndroidActivityNotFound`. 
  - **Atténuation** : Cette exception informe l’utilisateur qu’il doit disposer d’un navigateur (avec prise en charge des onglets personnalisés, de préférence) sur son appareil.

- En cas d’échec de l’authentification (par ex., l’authentification se lance avec DuckDuckGo), MSAL.NET renvoie un message `AuthenticationCanceled MsalClientException`. 
  - **Problème** : Un navigateur avec prise en charge des onglets personnalisés n’était pas activé sur l’appareil. L’authentification s’est lancée avec un navigateur différent qui n’a pas été en mesure d’accomplir l’authentification. 
  - **Atténuation** : Informez l’utilisateur qu’il doit installer un navigateur (avec prise en charge des onglets personnalisés, de préférence) sur son appareil.

## <a name="devices-and-browsers-tested"></a>Appareils et navigateurs testés
Le tableau suivant répertorie les appareils et les navigateurs qui ont été testés.

| | Navigateur&ast;     |  Résultats  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome&ast; | Réussite|
| Huawei/One+ | Edge&ast; | Réussite|
| Huawei/One+ | Firefox&ast; | Réussite|
| Huawei/One+ | Brave&ast; | Réussite|
| One+ | Ecosia&ast; | Réussite|
| One+ | Kiwi&ast; | Réussite|
| Huawei/One+ | Opera | Réussite|
| Huawei | OperaMini | Réussite|
| Huawei/One+ | InBrowser | Réussite|
| One+ | Maxthon | Réussite|
| Huawei/One+ | DuckDuckGo | Authentification annulée par l’utilisateur|
| Huawei/One+ | UC Browser | Authentification annulée par l’utilisateur|
| One+ | Dolphin | Authentification annulée par l’utilisateur|
| One+ | CM Browser | Authentification annulée par l’utilisateur|
| Huawei/One+ | Aucun installé | AndroidActivityNotFound ex|

&ast; prend en charge des onglets personnalisés

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des extraits de code et des informations supplémentaires concernant l’utilisation de navigateur système avec Xamarin Android, consultez ce [guide](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  