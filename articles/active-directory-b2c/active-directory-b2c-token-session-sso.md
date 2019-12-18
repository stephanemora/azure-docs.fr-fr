---
title: Configuration de la session et de l’authentification unique
titleSuffix: Azure AD B2C
description: Configuration de la session et de l’authentification unique (SSO) dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 736dd1f0490c2c9c7c4f526df96dd5ace6a1f819
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950066"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Configuration de la session et de l’authentification unique dans Azure Active Directory B2C

Cette fonctionnalité vous donne un contrôle précis, [par flux d’utilisateur](active-directory-b2c-reference-policies.md), de ce qui suit :

- Durées de vie des sessions d’applications web gérées par Azure AD B2C.
- Comportement de l’authentification unique (SSO) entre plusieurs applications et flux d’utilisateur dans votre locataire Azure AD B2C.

## <a name="session-behavior"></a>Comportement de la session

Azure AD B2C prend en charge le [protocole d’authentification OpenID Connect](active-directory-b2c-reference-oidc.md) pour activer l’authentification sécurisée dans les applications web. Vous pouvez utiliser les propriétés suivantes pour gérer les sessions d’application web :

- **Durée de vie de session d’application web (minutes)** : la durée de vie du cookie de session Azure AD B2C stocké dans le navigateur de l’utilisateur après une authentification réussie.
    - Par défaut : 1 440 minutes.
    - Valeur minimale (inclusive) : 15 minutes.
    - Valeur maximale (inclusive) : 1 440 minutes.
- **Expiration de la session d’application web** : si ce commutateur est défini sur **Absolu**, l’utilisateur est obligé de s’authentifier de nouveau lorsque le délai spécifié dans **Durée de vie de la session d’application web (minutes)** est écoulé. Si ce commutateur est défini sur **Cumulé** (paramètre par défaut), l’utilisateur reste connecté tant qu’il est actif en permanence dans votre application web.

Les cas d’usage suivants sont activés à l’aide de ces propriétés :

- Respectez les exigences de conformité et de sécurité de votre secteur en définissant les durées de vie correctes de la session d’application web.
- Forcez l’authentification après une période donnée pendant une interaction utilisateur avec une partie haute sécurité de votre application web.

Ces paramètres ne sont pas disponibles pour les flux d’utilisateur de réinitialisation de mot de passe.

## <a name="single-sign-on-sso-configuration"></a>Configuration de l’authentification unique

Si vous avez plusieurs applications et flux d’utilisateur dans votre locataire B2C, vous pouvez gérer les interactions utilisateur à l’aide de la propriété **Configuration de l’authentification unique**. Vous pouvez définir la propriété sur l’un des paramètres suivants :

- **Client** : il s’agit du paramètre par défaut. L’utilisation de ce paramètre permet à plusieurs applications et flux d’utilisateur dans votre locataire B2C de partager la même session utilisateur. Par exemple, lorsqu’un utilisateur se connecte à une application, il peut également se connecter de façon transparente à une autre application, Contoso Pharmacy, lorsqu’il y accède.
- **Application**: ce paramètre vous permet de maintenir une session utilisateur exclusivement pour une application, indépendamment des autres applications. Par exemple, si vous souhaitez que l’utilisateur se connecte à Contoso Pharmacy (avec les mêmes informations d’identification), même s’il est déjà connecté à Contoso Shopping, une autre application sur le même client B2C.
- **Stratégie** : ce paramètre vous permet de maintenir une session utilisateur exclusivement pour un flux d’utilisateur, indépendamment des applications qui l’utilisent. Par exemple, si l’utilisateur s’est déjà connecté et a effectué une étape d’authentification multifacteur, il peut obtenir l’accès à des parties plus sécurisées de plusieurs applications tant que la session liée au flux d’utilisateur n’expire pas.
- **Désactivé** : ce paramètre oblige l’utilisateur à réexécuter toute la procédure pour chaque exécution du flux d’utilisateur.

Ces paramètres ne sont pas disponibles pour les flux d’utilisateur de réinitialisation de mot de passe.

