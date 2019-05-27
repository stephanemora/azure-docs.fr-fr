---
title: Problèmes connus sur les navigateurs (bibliothèque d’authentification Microsoft pour JavaScript) | Azure
description: Découvrez les problèmes connus lors de l’utilisation de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js) avec le navigateur Safari.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb89b1ef4dbbef234fba3152d7f85bbadfbdc64a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873884"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Problèmes connus sur le navigateur Safari avec MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Renouvellement des jetons en mode silencieux sur Safari 12 et ITP 2.0

Apple iOS 12 et les systèmes d’exploitation MacOS 10.14 inclus une version de la [Safari 12 navigateur](https://developer.apple.com/safari/whats-new/). Pour des raisons de sécurité et de confidentialité, Safari 12 inclut le [2.0 de prévention de suivi Intelligent](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Cela génère essentiellement le navigateur supprimer les cookies tiers qui est définies. ITP 2.0 traite également les cookies définis par le fournisseur d’identité en tant que les cookies tiers.

### <a name="impact-on-msaljs"></a>Impact sur MSAL.js

MSAL.js utilise un Iframe masqué pour effectuer l’acquisition du jeton en mode silencieux et le renouvellement de la `acquireTokenSilent` appels. Les demandes de jeton sans assistance s’appuient sur l’Iframe ayant un accès à la session de l’utilisateur authentifié représenté par les cookies définis par Azure AD. 2.0 ITP empêche l’accès à ces cookies MSAL.js ne parvient pas à acquérir et renouveler les jetons en mode silencieux et il en résulte `acquireTokenSilent` échecs.

Il n’existe aucune solution à ce problème à ce stade et que nous évaluons options avec la Communauté de normes.

### <a name="work-around"></a>Solution de contournement

Par défaut, le paramètre de ITP est activé sur le navigateur Safari. Vous pouvez désactiver ce paramètre en accédant à **préférences** -> **confidentialité** puis en décochant la **empêcher intersites suivi** option.

![paramètre de Safari](./media/msal-js-known-issue-safari-browser/safari.png)

Vous devrez gérer le `acquireTokenSilent` échecs avec interactif acquièrent le jeton appel, ce qui invite l’utilisateur à se connecter.
Pour éviter des connexions répétées, est une approche que vous pouvez implémenter pour gérer les `acquireTokenSilent` échec et fournir à l’utilisateur une option pour désactiver le paramètre ITP dans Safari avant de procéder à l’appel interactive. Une fois que le paramètre est désactivé, les renouvellements de jetons en mode silencieux suivants doivent réussir.
