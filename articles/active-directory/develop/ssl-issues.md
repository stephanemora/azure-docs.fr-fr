---
title: Résoudre les problèmes liés à SSL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment résoudre différents problèmes lors de l’utilisation de certificats SSL avec la bibliothèque MSAL.Objective-C.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: e278b928cc7f1e7f830ba246545ea52944a2e252
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084364"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-ssl-issues"></a>Procédure : résoudre les problèmes SSL liés à MSAL pour iOS et macOS

Cet article fournit des informations pour vous aider à résoudre les problèmes que vous pouvez rencontrer lors de l’utilisation de la [bibliothèque MSAL (Microsoft Authentication Library) pour iOS et macOS](reference-v2-libraries.md).

## <a name="network-issues"></a>Problèmes de réseau

**Erreur -1200** : « Une erreur SSL s’est produite et une connexion sécurisée au serveur ne peut pas être établie. »

Cette erreur signifie que la connexion n’est pas sécurisée. Elle se produit quand un certificat n’est pas valide. Pour plus d’informations, notamment sur le serveur qui fait l’objet d’un échec de la vérification SSL, reportez-vous à `NSURLErrorFailingURLErrorKey` dans le dictionnaire `userInfo` de l’objet d’erreur.

Cette erreur provient de la bibliothèque réseau d’Apple. La liste complète des codes d’erreur NSURL se trouve dans NSURLError.h dans les kits SDK macOS et iOS. Pour plus d’informations sur cette erreur, consultez [URL Loading System Error Codes](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Problèmes de certificat

Si l’URL qui fournit un certificat non valide se connecte au serveur que vous avez l’intention d’utiliser dans le cadre du processus d’authentification, un bon point de départ pour le diagnostic du problème consiste à tester l’URL avec un service de validation SSL, par exemple [Qualys SSL Labs Analyzer](https://www.ssllabs.com/ssltest/analyze.html). Il teste le serveur sur un vaste éventail de scénarios et de navigateurs, et vérifie la plupart des vulnérabilités connues.

Par défaut, la nouvelle fonctionnalité d’Apple [App Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) applique des stratégies de sécurité plus strictes aux applications qui utilisent des certificats SSL. Certains systèmes d’exploitation et navigateurs web ont commencé à appliquer certaines de ces stratégies par défaut. Pour des raisons de sécurité, nous vous recommandons de ne pas désactiver ATS.

Les certificats utilisant des hachages SHA-1 ont des vulnérabilités connues. La plupart des navigateurs web modernes n’autorisent pas les certificats avec des hachages SHA-1.

## <a name="captive-portals"></a>Portails captifs

Un portail captif présente une page web à un utilisateur quand il accède pour la première fois à un réseau Wi-Fi et ne s’est pas encore vu accorder l’accès à ce réseau. Il intercepte le trafic Internet jusqu’à ce que l’utilisateur réponde aux exigences du portail. Des erreurs réseau dues au fait que l’utilisateur ne peut pas se connecter aux ressources réseau sont attendues jusqu’à ce que l’utilisateur se connecte par le biais du portail.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les [portails captifs](https://en.wikipedia.org/wiki/Captive_portal) et sur la nouvelle fonctionnalité [App Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) d’Apple.
