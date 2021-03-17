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
ms.openlocfilehash: 3f90edd5729ff5229be09bc3798082c33bdeead2
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632099"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Prise en charge par navigateur de l’authentification FIDO2 sans mot de passe

Azure Active Directory permet d’utiliser des [clés de sécurité FIDO2](./concept-authentication-passwordless.md#fido2-security-keys) en tant qu’appareil sans mot de passe. La disponibilité de l’authentification FIDO2 pour les comptes Microsoft a été [annoncée en 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Comme indiqué dans l’annonce, certaines fonctionnalités et extensions facultatives de la spécification FIDO2 CTAP doivent être implémentées pour prendre en charge l’authentification sécurisée avec des comptes Microsoft et Azure Active Directory. Le diagramme suivant montre les navigateurs et les combinaisons de systèmes d’exploitation qui prennent en charge l’authentification sans mot de passe à l’aide des clés d’authentification FIDO2 avec Azure Active Directory.

## <a name="supported-browsers"></a>Navigateurs pris en charge

Ce tableau indique la prise en charge de l’authentification sur Azure Active Directory (Azure AD) et sur des comptes Microsoft (MSA). Les comptes Microsoft sont créés par les consommateurs pour des services tels que Xbox, Skype ou Outlook.com. Les types d’appareils pris en charge sont **USB**, **NFC** (Near-Field communication) et **BLE** (Bluetooth Low Energy).

| Système d''exploitation | Chrome | Chrome  | Chrome | Edge | Edge | Edge | Firefox | Firefox | Firefox |
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
[Activer la connexion par clé de sécurité sans mot de passe (préversion)](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
