---
title: Configurer le comportement de session – Azure Active Directory B2C | Microsoft Docs
description: Configurez le comportement de session dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c1d39fdbca9484f47ce0c8537c82247b75b2e3db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186809"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurer le comportement de session dans Azure Active Directory B2C

Cette fonctionnalité vous donne un contrôle précis, [par flux d’utilisateur](user-flow-overview.md), de ce qui suit :

- Durées de vie des sessions d’applications web gérées par Azure AD B2C.
- Comportement de l’authentification unique (SSO) entre plusieurs applications et flux d’utilisateur dans votre locataire Azure AD B2C.

Ces paramètres ne sont pas disponibles pour les flux d’utilisateur de réinitialisation de mot de passe.

Azure AD B2C prend en charge le [protocole d’authentification OpenID Connect](openid-connect.md) pour activer l’authentification sécurisée dans les applications web. Vous pouvez utiliser les propriétés suivantes pour gérer les sessions d’application web :

## <a name="session-behavior-properties"></a>Propriétés de comportement de session

- **Durée de vie de session d’application web (minutes)** : la durée de vie du cookie de session Azure AD B2C stocké dans le navigateur de l’utilisateur après une authentification réussie.
    - Par défaut : 1 440 minutes.
    - Valeur minimale (inclusive) : 15 minutes.
    - Valeur maximale (inclusive) : 1 440 minutes.
- **Expiration de la session d’application web** : si ce commutateur est défini sur **Absolu**, l’utilisateur est obligé de s’authentifier de nouveau lorsque le délai spécifié dans **Durée de vie de la session d’application web (minutes)** est écoulé. Si ce commutateur est défini sur **Cumulé** (paramètre par défaut), l’utilisateur reste connecté tant qu’il est actif en permanence dans votre application web.
- **Configuration de l’authentification unique** : si vous disposez de plusieurs applications et flux utilisateur dans votre locataire B2C, vous pouvez gérer les interactions utilisateur qui s’y produisent à l’aide de la propriété **Configuration de l’authentification unique**. Vous pouvez définir la propriété sur l’un des paramètres suivants :
    - **Client** : il s’agit du paramètre par défaut. L’utilisation de ce paramètre permet à plusieurs applications et flux d’utilisateur dans votre locataire B2C de partager la même session utilisateur. Par exemple, lorsqu’un utilisateur se connecte à une application, il peut également se connecter de façon transparente à une autre application, Contoso Pharmacy, lorsqu’il y accède.
    - **Application**: ce paramètre vous permet de maintenir une session utilisateur exclusivement pour une application, indépendamment des autres applications. Par exemple, si vous souhaitez que l’utilisateur se connecte à Contoso Pharmacy (avec les mêmes informations d’identification), même s’il est déjà connecté à Contoso Shopping, une autre application sur le même client B2C.
    - **Stratégie** : ce paramètre vous permet de maintenir une session utilisateur exclusivement pour un flux d’utilisateur, indépendamment des applications qui l’utilisent. Par exemple, si l’utilisateur s’est déjà connecté et a effectué une étape d’authentification multifacteur, il peut obtenir l’accès à des parties plus sécurisées de plusieurs applications tant que la session liée au flux d’utilisateur n’expire pas.
    - **Désactivé** : ce paramètre oblige l’utilisateur à réexécuter toute la procédure pour chaque exécution du flux d’utilisateur.

Les cas d’usage suivants sont activés à l’aide de ces propriétés :

- Respectez les exigences de conformité et de sécurité de votre secteur en définissant les durées de vie correctes de la session d’application web.
- Forcez l’authentification après une période donnée pendant une interaction utilisateur avec une partie haute sécurité de votre application web.

## <a name="configure-the-properties"></a>Configurer les propriétés

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire et abonnement** dans le menu supérieur et en choisissant l’annuaire qui contient votre locataire Azure AD B2C.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Flux utilisateur (stratégies)** .
5. Ouvrez le flux utilisateur que vous avez créé précédemment.
6. Sélectionner **Propriétés**.
7. Configurez **Durée de vie de la session de l’application web (minutes)** , **Délai d’expiration de la session de l’application web** , **Configuration de l’authentification unique** et **Exiger un jeton d’ID dans les demandes de déconnexion**, selon les besoins.

    ![Paramètres de propriété du comportement de la session sur le Portail Azure](./media/session-behavior/session-behavior.png)

8. Cliquez sur **Enregistrer**.
