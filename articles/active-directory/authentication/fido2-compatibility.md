---
title: Prise en charge par navigateur de l’authentification FIDO2 sans mot de passe | Azure Active Directory
description: Les navigateurs et les combinaisons de systèmes d’exploitation prennent en charge l’authentification FIDO2 sans mot de passe pour les applications à l’aide d’Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2595acf25b63f89f6e0e29e996548b58767e9fde
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110786150"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Prise en charge par navigateur de l’authentification FIDO2 sans mot de passe

Azure Active Directory permet d’utiliser des [clés de sécurité FIDO2](./concept-authentication-passwordless.md#fido2-security-keys) en tant qu’appareil sans mot de passe. La disponibilité de l’authentification FIDO2 pour les comptes Microsoft a été [annoncée en 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910)et est devenue [disponible publiquement](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/passwordless-authentication-is-now-generally-available/ba-p/1994700) en mars 2021. Le diagramme suivant montre les navigateurs et les combinaisons de systèmes d’exploitation qui prennent en charge l’authentification sans mot de passe à l’aide des clés d’authentification FIDO2 avec Azure Active Directory.

## <a name="supported-browsers"></a>Navigateurs pris en charge

Ce tableau indique la prise en charge de l’authentification sur Azure Active Directory (Azure AD) et sur des comptes Microsoft (MSA). Les comptes Microsoft sont créés par les consommateurs pour des services tels que Xbox, Skype ou Outlook.com. Les types d’appareils pris en charge sont **USB**, **NFC** (Near-Field communication) et **BLE** (Bluetooth Low Energy).

| Système d’exploitation | Chrome | Chrome  | Chrome | Edge | Edge | Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | BLE | USB | NFC | BLE | USB | NFC | BLE |
| **Windows**  | ![Chrome prend en charge USB sur Windows pour les comptes AAD.][y] | ![Chrome prend en charge NFC sur Windows pour les comptes AAD.][y] | ![Chrome prend en charge BLE sur Windows pour les comptes AAD.][y] | ![Edge prend en charge USB sur Windows pour les comptes AAD.][y] | ![Edge prend en charge NFC sur Windows pour les comptes AAD.][y] | ![Edge prend en charge BLE sur Windows pour les comptes AAD.][y] | ![Firefox prend en charge USB sur Windows pour les comptes AAD.][y] | ![Firefox prend en charge NFC sur Windows pour les comptes AAD.][y] | ![Firefox prend en charge BLE sur Windows pour les comptes AAD.][y] |
| **macOS**  | ![Chrome prend en charge USB sur macOS pour les comptes AAD.][y] | ![Chrome ne prend pas en charge NFC sur macOS pour les comptes AAD.][n] | ![Chrome ne prend pas en charge BLE sur macOS pour les comptes AAD.][n] | ![Edge prend en charge USB sur macOS pour les comptes AAD.][y] | ![Edge ne prend pas en charge NFC sur macOS pour les comptes AAD.][n] | ![Edge ne prend pas en charge BLE sur macOS pour les comptes AAD.][n] | ![Firefox ne prend pas en charge USB sur macOS pour les comptes AAD.][n] | ![Firefox ne prend pas en charge NFC sur macOS pour les comptes AAD.][n] | ![Firefox ne prend pas en charge BLE sur macOS pour les comptes AAD.][n] |
| **Linux**  | ![Chrome prend en charge USB sur Linux pour les comptes AAD.][y] | ![Chrome ne prend pas en charge NFC sur Linux pour les comptes AAD.][n] | ![Chrome ne prend pas en charge BLE sur Linux pour les comptes AAD.][n] | ![Edge ne prend pas en charge USB sur Linux pour les comptes AAD.][n] | ![Edge ne prend pas en charge NFC sur Linux pour les comptes AAD.][n] | ![Edge ne prend pas en charge BLE sur Linux pour les comptes AAD.][n] | ![Firefox ne prend pas en charge USB sur Linux pour les comptes AAD.][n] | ![Firefox ne prend pas en charge NFC sur Linux pour les comptes AAD.][n] | ![Firefox ne prend pas en charge BLE sur Linux pour les comptes AAD.][n] |



## <a name="unsupported-browsers"></a>Navigateurs non pris en charge

Les combinaisons suivantes de système d’exploitation et de navigateur ne sont pas prises en charge, mais une prise en charge et des tests futurs sont à l’étude. Si vous souhaitez voir la prise en charge d’autres systèmes d’exploitation et navigateurs, faites-le nous savoir à l’aide de l’outil de commentaire sur le produit au bas de la page.

| Système d’exploitation | Browser |
| ---- | ---- |
| iOS | Safari, Brave |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="minimum-browser-version"></a>Version minimale du navigateur

Voici la configuration minimale requise pour la version du navigateur. 

| Browser | Version minimale |
| ---- | ---- |
| Chrome | 76 |
| Edge | Windows 10 version 1903<sup>1</sup> |
| Firefox | 66 |

<sup>1</sup>Toutes les versions du nouveau Microsoft Edge basé sur Chromium prennent en charge Fido2. La prise en charge sur Microsoft Edge hérité a été ajoutée à la version 1903.

## <a name="next-steps"></a>Étapes suivantes
[Activer la connexion par clé de sécurité sans mot de passe](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
