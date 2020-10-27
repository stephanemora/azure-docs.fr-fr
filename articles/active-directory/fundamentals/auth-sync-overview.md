---
title: Vue d’ensemble du protocole d’authentification et de synchronisation Azure Active Directory
description: Guide architectural pour atteindre ce modèle d’authentification
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d881dc3fe3e3caa1058cf97834735910b0de1d9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113942"
---
# <a name="azure-active-directory-integrations-with-legacy-authentication-and-synchronization-protocols"></a>Intégration des Azure Active Directory avec les protocoles d’authentification et de synchronisation hérités

Microsoft Azure Active Directory (Azure AD) permet l’intégration avec de nombreux protocoles d’authentification et de synchronisation. Les intégrations d’authentification vous permettent d’utiliser Azure AD et ses fonctionnalités de sécurité et de gestion avec peu ou pas de modifications apportées à vos applications qui utilisent des méthodes d’authentification héritées. Les intégrations de la synchronisation vous permettent de synchroniser les données des utilisateurs et des groupes avec Azure AD, puis les fonctionnalités de gestion des Azure AD des utilisateurs. Certains modèles de synchronisation activent également l’approvisionnement automatique.

## <a name="authentication-patterns"></a>Modèles d’authentification

Le tableau suivant présente les modèles d’authentification et leurs fonctionnalités. Sélectionner le nom d’un modèle d’authentification à afficher

* Description détaillée

* Quand utiliser cette fonctionnalité ?

* Diagramme architectural

* Explication des composants système

* Liens pour l’implémentation de l’intégration

 

| Modèles d’authentification| Authentification| Autorisation| Multi-Factor Authentication| Accès conditionnel |
| - |- | - | - | - |
| [Authentification basée sur l’en-tête](auth-header-based.md)|![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png) |
| [Authentification LDAP](auth-ldap.md)| ![coche](./media/authentication-patterns/check.png)| | |  |
| [Authentification OAuth 2.0](auth-oauth2.md)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png) |
| [Authentification OIDC](auth-oidc.md)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png) |
| [Authentification unique basée sur un mot de passe](auth-password-based-sso.md )| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png) |
| [Authentification RADIUS]( auth-radius.md)| ![coche](./media/authentication-patterns/check.png)| | ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png) |
| [Services de passerelle Bureau à distance](auth-remote-desktop-gateway.md)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png) |
| [Authentification SAML](auth-saml.md)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png) |
| [Authentification Windows - délégation Kerberos contrainte](auth-kcd.md)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Modèles de synchronisation

Le tableau suivant présente les modèles de synchronisation et leurs fonctionnalités. Sélectionner le nom d’un modèle à afficher

* Description détaillée

* Quand utiliser cette fonctionnalité ?

* Diagramme architectural

* Explication des composants système

* Liens pour l’implémentation de l’intégration



| Modèle de synchronisation| Synchronisation de répertoires| Approvisionnement d'utilisateurs |
| - | - | - |
| [Synchronisation de répertoires](sync-directory.md)| ![coche](./media/authentication-patterns/check.png)|  |
| [Synchronisation LDAP](sync-ldap.md)| ![coche](./media/authentication-patterns/check.png)|  |
| [Synchronisation SCIM](sync-scim.md)| ![coche](./media/authentication-patterns/check.png)| ![coche](./media/authentication-patterns/check.png) |

