---
title: Authentification SAML avec Azure Active Directory
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
ms.openlocfilehash: 05b13e04db8e83a8a10c2d7fe0aea202dfa3b69c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113943"
---
# <a name="saml-authentication-with-azure-active-directory"></a>Authentification SAML avec Azure Active Directory

Security Assertion Markup Language (SAML) est une norme ouverte pour l’échange de données d’authentification et d’autorisation entre un fournisseur d’identité et un fournisseur de services. SAML est un langage de balisage basé sur XML pour les assertions de sécurité, qui sont des instructions que les fournisseurs de services utilisent pour prendre des décisions de contrôle d’accès. 

La spécification SAML définit trois rôles :

* Le principal, généralement un utilisateur
* Le Fournisseur d’identité (IdP)
* Le fournisseur de services (SP)


## <a name="use-when"></a>Cas d'utilisation

Il est nécessaire de fournir une expérience d’authentification unique (SSO) pour une application SAML d’entreprise.

Bien que SSO soit l’un des cas d’usage les plus importants pris en charge par SAML, notamment par l’extension de l’authentification unique entre les domaines de sécurité, il existe également d’autres cas d’usage (appelés profils). 

![diagramme architectural](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>Composants du système

* **Utilisateur**  : Demande un service à l’application.

* **Navigateur Web** : Composant avec lequel l’utilisateur interagit.

* **Application web**  : Application d’entreprise qui prend en charge SAML et utilise Azure AD en tant que IdP.

* **Jeton**  : Assertion SAML (également appelée jetons SAML) qui transporte des ensembles de requêtes effectuées par le fournisseur d’identité sur le principal (utilisateur). Il contient les informations d’authentification, les attributs et les instructions de décision d’autorisation.

* **Azure AD**  : IdP Cloud d’entreprise qui fournit l’authentification unique et multi-Factor Authentication pour les applications SAML. Il synchronise, gère et contrôle les informations d’identité des utilisateurs tout en fournissant des services d’authentification aux applications de confiance. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Implémenter l’authentification SAML avec Azure AD

* [Didacticiels pour l’intégration d’applications SaaS à l’aide de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Configuration de l’authentification unique basée sur SAML pour les applications qui ne sont pas de la Galerie](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

* [Utilisation du protocole SAML par Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)
