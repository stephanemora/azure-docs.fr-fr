---
title: Disponibilité et limitations de Microsoft Authenticator pour Android en Chine | Microsoft Docs
description: Découvrez obtenir l’application Microsoft Authenticator - Disponibilité en Chine
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 05/06/2020
ms.author: curtand
ms.openlocfilehash: 3ba1b41859a543ab5e0393c5715dc49d452ea73c
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82892235"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>Microsoft Authenticator pour Android dans le cloud public en Chine

L’application Microsoft Authenticator pour Android est disponible en téléchargement en Chine. Le Google Play Store n’étant pas disponible en Chine, l’application doit être téléchargée à partir d’autres places de marché d’applications chinoises. L’application Microsoft Authenticator pour Android est actuellement disponible dans les boutiques suivantes en Chine :

- [Baidu](https://shouji.baidu.com/software/26638379.html)
- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy Store](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

La build la plus récente de l’application se trouve dans le Google Play Store, mais nous mettons à jour l’application dans toutes les autres boutiques le plus rapidement possible. Étant donné qu’aucun paquet d’application Android (APK) personnalisé n’est déployé dans aucune boutique d’applications, l’application peut être mise à jour sans interruption à partir de l’un des emplacements suivants :

- La boutique à partir de laquelle elle a été téléchargée
- Le Google Play Store si l’utilisateur change de région

## <a name="limitations"></a>Limitations

L’application Microsoft Authenticator pour Android utilise le système Firebase Cloud Messaging de Google et Google Play Services pour recevoir des notifications Push. Aucun de ces services n’étant disponible en Chine, les fonctionnalités de l’application présentent certaines limitations :

- L’inscription de l’application Authenticator en tant que méthode d’authentification multifacteur (MFA, Multi-Factor Authentication) à l’aide de notifications Push ne fonctionne pas

- La [connexion par téléphone](../authentication/howto-authentication-sms-signin.md) ne peut pas être configurée. Elle exige de l’utilisateur qu’il configure l’application en tant que méthode d’authentification multifacteur à l’aide de notifications Push, ce qui ne fonctionne pas actuellement

Si un utilisateur est déjà parvenu à configurer la connexion par téléphone ou l’authentification multifacteur à l’aide de l’application, il peut effectuer une vérification manuelle pour les demandes de notifications dans l’application et l’utiliser pour la vérification de l’identité.

## <a name="multi-factor-authentication-workaround"></a>Solution de contournement pour l’authentification multifacteur

Au lieu d’utiliser des notifications Push pour l’authentification multifacteur, les utilisateurs peuvent configurer leur authentification multifacteur de façon à recevoir des codes de vérification sur leur appareil, qu’ils peuvent ensuite utiliser pour vérifier leur identité. Ces codes de vérification sont valides pendant 30 secondes, et pour les utiliser les administrateurs doivent faire en sorte que leur locataire effectue la vérification à l’aide de codes de vérification TOTP (Time-based One-Time Password).

## <a name="availability"></a>Disponibilité

Fonctionnalité de Microsoft Authenticator | Disponibilité en Chine
------------------------------- | ---------------------
Inscription MFA à l’aide de notifications Push | Non 
Compte MFA préexistant vérifiant l’identité à l’aide de notifications Push | Non 
Compte MFA préexistant effectuant une vérification manuelle pour les notifications | Oui
Inscription/authentification MFA à l’aide de codes de vérification/TOTP uniquement | Oui
Inscription par connexion par téléphone | Non 
Connexion par téléphone existante à l’aide de notifications Push | Non 
Vérification de connexion par téléphone existante en effectuant une vérification manuelle pour les demandes d’authentification | Oui

## <a name="next-steps"></a>Étapes suivantes

- [Télécharger et installer l’application Microsoft Authenticator](user-help-auth-app-download-install.md)
