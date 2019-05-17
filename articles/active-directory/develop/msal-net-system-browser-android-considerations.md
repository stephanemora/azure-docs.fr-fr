---
title: Considérations de Xamarin Android (bibliothèque d’authentification Microsoft pour .NET) | Azure
description: En savoir plus sur les considérations spécifiques lorsque vous utilisez Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57feb33967732481d78e0ddaba5e90f4f82f327
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544438"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Considérations spécifiques à Xamarin Android avec MSAL.NET
Cet article traite des considérations spécifiques lors de l’utilisation du navigateur du système sur Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).

À compter de MSAL.NET 2.4.0-preview, MSAL.NET prend en charge des navigateurs autres que Chrome et ne requiert plus Chrome être installé sur l’appareil Android pour l’authentification.

Nous vous recommandons de qu'utiliser les navigateurs qui prennent en charge les onglets personnalisés, telles que celles-ci :

| Navigateurs avec prise en charge des onglets personnalisés | Nom du package |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Courageux | com.brave.browser|

En plus de navigateurs avec prise en charge des onglets personnalisés, en fonction de nos tests, certains navigateurs qui ne prennent pas en charge les onglets personnalisés sont également valables pour l’authentification : Opera, Opera Mini, InBrowser et Maxthon. Pour plus d’informations, consultez [table pour les résultats de test](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Problèmes connus

- Si l’utilisateur ne dispose d’aucun navigateur n’est activée sur l’appareil, MSAL.NET lèvera une `AndroidActivityNotFound` Exception. 
  - **Atténuation** : Informer l’utilisateur qu’ils doivent activer un navigateur (de préférence un avec prise en charge des onglets personnalisés) sur leur appareil.

- Si l’authentification échoue (par ex. authentification lancée avec DuckDuckGo), MSAL.NET retournera un `AuthenticationCanceled MsalClientException`. 
  - **Racine du problème**: Un navigateur avec prise en charge des onglets personnalisés n’était pas activé sur l’appareil. L’authentification est lancé avec un autre navigateur, ce qui n’a pas été en mesure d’effectuer l’authentification. 
  - **Atténuation** : Informer l’utilisateur qu’ils doivent installer un navigateur (de préférence un avec prise en charge de l’onglet personnalisé) sur leur appareil.

## <a name="devices-and-browsers-tested"></a>Appareils et navigateurs testés
Le tableau suivant répertorie les appareils et navigateurs qui ont été testés.

| | Navigateur&ast;     |  Résultat  | 
| ------------- |:-------------:|:-----:|
| Huawei / un + | Chrome&ast; | Réussite|
| Huawei / un + | Edge&ast; | Réussite|
| Huawei / un + | Firefox&ast; | Réussite|
| Huawei / un + | Courageux&ast; | Réussite|
| Un seul + | Ecosia&ast; | Réussite|
| Un seul + | Kiwi&ast; | Réussite|
| Huawei / un + | Opera | Réussite|
| Huawei | OperaMini | Réussite|
| Huawei / un + | InBrowser | Réussite|
| Un seul + | Maxthon | Réussite|
| Huawei / un + | DuckDuckGo | Authentification de l’utilisateur a annulé|
| Huawei / un + | Navigateur de communications unifiées | Authentification de l’utilisateur a annulé|
| Un seul + | Dolphin | Authentification de l’utilisateur a annulé|
| Un seul + | Navigateur de CM | Authentification de l’utilisateur a annulé|
| Huawei / un + | Aucune installation | AndroidActivityNotFound ex|

&ast; Prend en charge les onglets personnalisés

## <a name="next-steps"></a>Étapes suivantes
Pour le code des extraits de code et des informations supplémentaires sur l’utilisation de navigateur du système avec Xamarin Android, lisez ce [guide](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  