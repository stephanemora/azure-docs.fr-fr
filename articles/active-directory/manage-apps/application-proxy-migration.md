---
title: Mise à niveau vers le proxy d’application Azure AD | Microsoft Docs
description: Choisissez la meilleure solution de proxy si vous effectuez une mise à niveau à partir de Microsoft Forefront ou de Unified Access Gateway.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: cf1c39d0270dcb7de3ae8ff487b21874b23db74a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
---
# <a name="compare-remote-access-solutions"></a>Comparer les solutions d’accès à distance

Le proxy d’application Azure Active Directory est l’une des deux solutions d’accès à distance proposées par Microsoft. L’autre solution est le proxy d’application Web, la version locale. Ces deux solutions remplacent d’anciens produits proposés par Microsoft : Microsoft Forefront Threat Management Gateway (TMG) et Unified Access Gateway (UAG). Cet article vous permettra de comparer ces quatre solutions entre elles. Pour ceux qui utilisent toujours les solutions TMG ou UAG déconseillées, utilisez cet article pour planifier votre migration vers l’un des proxys d’application. 


## <a name="feature-comparison"></a>Comparaison des fonctionnalités

Ce tableau vous permettra de comparer les solutions Threat Management Gateway (TMG), Unified Access Gateway (UAG), de proxy d’application Web (WAP) et de proxy d’application Azure AD (AP) entre elles.

| Fonctionnalité | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Authentification par certificat | OUI | OUI | - | - |
| Publier de manière sélective les applications de navigateur | OUI | OUI | OUI | OUI |
| Préauthentification et authentification unique | OUI | OUI | OUI | OUI | 
| Pare-feu de couche 2/3 | OUI | OUI | - | - |
| Transférer des fonctionnalités de proxy | OUI | - | - | - |
| Fonctionnalités VPN | OUI | OUI | - | - |
| Prise en charge du protocole riche | - | OUI | Oui, en cas d’exécution sur HTTP | Oui, en cas d’exécution sur HTTP ou via la passerelle des services Bureau à distance |
| Sert de serveur proxy AD FS | - | OUI | OUI | - |
| Un portail pour l’accès de l’application | - | OUI | - | OUI |
| Traduction du lien de corps de réponse | OUI | OUI | - | OUI | 
| Authentification avec des en-têtes | - | OUI | - | Oui, avec PingAccess | 
| Sécurité à l’échelle du cloud | - | - | - | OUI | 
| Accès conditionnel | - | OUI | - | OUI |
| Aucun composant dans la zone démilitarisée (DMZ) | - | - | - | OUI |
| Aucune connexion entrante | - | - | - | OUI |

Pour la plupart des scénarios, nous vous recommandons d’utiliser la solution moderne de proxy d’application Azure AD. Le proxy d’application Web est uniquement recommandé dans les scénarios qui requièrent un serveur proxy pour AD FS, et que vous ne pouvez pas utiliser de domaines personnalisés dans Azure Active Directory. 

Le proxy d’application Azure AD offre des avantages uniques par rapport à des produits similaires, notamment :

- L’extension d’Azure AD à des ressources locales
   - La protection et la sécurité à l’échelle du cloud
   - Des fonctionnalités telles que l’accès conditionnel et Multi-Factor Authentication faciles à activer
- Aucun composant dans la zone démilitarisée
- Aucune connexion entrante nécessaire
- Un panneau d’accès dans lequel vos utilisateurs peuvent se rendre pour toutes leurs applications, y compris les applications SaaS intégrées à Azure AD, les applications O365 et vos applications web locales. 


## <a name="next-steps"></a>Étapes suivantes

- [Utiliser le proxy d’application Azure AD pour offrir un accès à distance sécurisé aux applications locales](application-proxy.md)
- [Passer de Forefront TMG et UAG au proxy d’application](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).
