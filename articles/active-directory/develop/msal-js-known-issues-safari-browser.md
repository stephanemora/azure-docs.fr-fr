---
title: Problèmes connus du navigateur Safari (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: En savoir plus sur les problèmes connus d’utilisation de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js) avec le navigateur Safari.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1fda1471d629ab8d84d06a3cc5bcfae0a6d9ec5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916330"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Problèmes connus sur le navigateur Safari avec MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Renouvellement silencieux des jetons sur Safari 12 et ITP 2.0

Les systèmes d’exploitation Apple iOS 12 et MacOS 10.14 incluaient une version de [navigateur Safari 12](https://developer.apple.com/safari/whats-new/). Pour des raisons de sécurité et de confidentialité, Safari 12 inclut [Intelligent Tracking Prevention 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Cela amène essentiellement le navigateur à supprimer les cookies tiers définis. ITP 2.0 traite également les cookies définis par les fournisseurs d’identité en tant que cookies tiers.

### <a name="impact-on-msaljs"></a>Impact sur MSAL.js

MSAL.js utilise un Iframe masqué pour effectuer l’acquisition et le renouvellement silencieux des jetons dans le cadre des appels `acquireTokenSilent`. Les requêtes silencieuses de jeton s’appuient sur l’Iframe disposant d’un accès à la session utilisateur authentifiée, représentée par les cookies définis par Azure AD. Lorsque ITP 2.0 empêche l’accès à ces cookies, MSAL.js ne parvient pas à acquérir et renouveler silencieusement les jetons et cela entraîne des échecs de `acquireTokenSilent`.

Il n’existe aucune solution à ce stade et nous sommes en train d’évaluer les différentes options avec la communauté de normalisation.

### <a name="work-around"></a>Solution de contournement

Par défaut, le paramètre ITP est activé sur le navigateur Safari. Vous pouvez désactiver ce paramètre en accédant à **Préférences** -> **Confidentialité**, puis en décochant l’option **Empêcher le suivi sur plusieurs domaines**.

![paramètre safari](./media/msal-js-known-issue-safari-browser/safari.png)

Vous devrez gérer les échecs de `acquireTokenSilent` avec un appel de jeton d’acquisition interactif qui invite l’utilisateur à se connecter.
Pour éviter les connexions répétées, vous pouvez gérer l’échec de `acquireTokenSilent` et permettre à l’utilisateur de désactiver le paramètre ITP dans Safari avant de procéder à l’appel interactif. Une fois le paramètre désactivé, les renouvellements silencieux de jetons doivent réussir.
