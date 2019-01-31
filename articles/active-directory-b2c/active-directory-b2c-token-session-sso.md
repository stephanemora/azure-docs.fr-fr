---
title: Configuration du jeton, de la session et de l’authentification unique dans Azure Active Directory B2C | Microsoft Docs
description: Configuration du jeton, de la session et de l’authentification unique dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d1acdb8b5d0054f1dffd1014a350540b6de40d75
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171505"
---
# <a name="token-session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Configuration du jeton, de la session et de l’authentification unique dans Azure Active Directory B2C

Cette fonctionnalité vous donne un contrôle précis, [par flux d’utilisateur](active-directory-b2c-reference-policies.md), de ce qui suit :

- Durées de vie des jetons de sécurité émis par Azure Active Directory (Azure AD) B2C.
- Durées de vie des sessions d’applications web gérées par Azure AD B2C.
- Formats des revendications importantes dans les jetons de sécurité émis par Azure AD B2C.
- Comportement de l’authentification unique (SSO) entre plusieurs applications et flux d’utilisateur dans votre locataire Azure AD B2C.

Vous pouvez utiliser cette fonctionnalité sur n’importe quel type de stratégie, mais cet exemple montre comment utiliser la fonctionnalité avec un flux d’utilisateur d’inscription ou de connexion. Pour les flux d’utilisateur, vous pouvez utiliser cette fonctionnalité dans votre annuaire Azure AD B2C de la façon suivante :

1. Cliquez sur **flux d’utilisateur**.
2. Ouvrez un flux d’utilisateur en cliquant dessus. Par exemple, cliquez sur **B2C_1_SiUpIn**.
3. Cliquez sur **Propriétés**.
4. Sous **Paramètres de compatibilité de jeton**, apportez les modifications voulues. Apprenez-en plus sur les propriétés disponibles dans les sections suivantes.
5. Cliquez sur **Enregistrer** en haut du menu.

## <a name="token-lifetimes-configuration"></a>Configuration de la durée de vie des jetons

Azure AD B2C prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-b2c-reference-protocols.md) pour activer l’accès sécurisé aux ressources protégées. Pour implémenter cette prise en charge, Azure AD B2C émet divers [jetons de sécurité](active-directory-b2c-reference-tokens.md). 

Les propriétés suivantes servent à gérer les durées de vie des jetons de sécurité émis par Azure AD B2C :

- **Durées de vie du jeton d’accès et d’ID (minutes)** : la durée de vie du jeton du porteur OAuth 2.0 utilisé pour accéder à une ressource protégée.
    - Par défaut : 60 minutes.
    - Valeur minimale (inclusive) : 5 minutes.
    - Valeur maximale (inclusive) : 1 440 minutes.
- **Durée de vie du jeton d’actualisation (jours)** : la durée maximale avant laquelle un jeton d’actualisation peut être utilisé pour acquérir un nouveau jeton d’accès ou d’ID (et éventuellement, un nouveau jeton d’actualisation, si l’étendue `offline_access` a été accordée à votre application).
    - Par défaut : 14 jours.
    - Valeur minimale (inclusive) : 1 jour.
    - Valeur maximale (inclusive) : 90 jours.
- **Durée de vie fenêtre glissante du jeton d’actualisation (jours)**: une fois cette période écoulée, l’utilisateur est obligé de s’authentifier de nouveau, quelle que soit la période de validité de dernier jeton d’actualisation obtenu par l’application. Cette valeur ne peut être fournie que si le commutateur est défini sur **Délimité**. Elle doit être supérieure ou égale à la valeur **Durée de vie du jeton d’actualisation (jours)** . Si le commutateur est défini sur **Non délimité**, vous ne pouvez pas fournir une valeur spécifique.
    - Par défaut : 90 jours.
    - Valeur minimale (inclusive) : 1 jour.
    - Valeur maximale (inclusive) : 365 jours.

Les cas d’usage suivants sont activés à l’aide de ces propriétés :

- Autoriser un utilisateur à rester connecté à une application mobile indéfiniment, tant qu’il est actif en permanence sur l’application. Vous pouvez définir le paramètre **Durée de vie fenêtre glissante du jeton d’actualisation (jours)** sur **Non délimité** dans votre flux d’utilisateur de connexion.
- Respectez les exigences de conformité et de sécurité de votre secteur en définissant les durées de vie correctes du jeton d’accès.

Ces paramètres ne sont pas disponibles pour les flux d’utilisateur de réinitialisation de mot de passe. 

## <a name="token-compatibility-settings"></a>Paramètres de conformité de jeton

Les propriétés suivantes permettent aux clients de valider les options selon les besoins :

- **Revendication de l’émetteur (iss)**  : cette propriété identifie le client Azure AD B2C qui a émis le jeton.
    - `https://<domain>/{B2C tenant GUID}/v2.0/` : il s’agit de la valeur par défaut.
    - `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` : cette valeur inclut l’ID pour le locataire B2C et le flux d’utilisateur utilisé dans la demande de jeton. Si votre application ou bibliothèque a besoin qu’Azure AD B2C soit conforme à [la spécification OpenId Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html), utilisez cette valeur.
- **Revendication d’objet (obj)**  : cette propriété identifie l’entité pour laquelle le jeton indique des informations.
    - **ObjectID** : cette propriété est la valeur par défaut. Elle renseigne l’ID d’objet de l’utilisateur du répertoire dans la revendication `sub` du jeton.
    - **Non pris en charge** : cette propriété est uniquement fournie pour la compatibilité descendante, nous vous recommandons de passer à **ObjectID** dès que vous le pouvez.
- **ID de stratégie représentant la revendication** : cette propriété identifie le type de revendication dans lequel l’ID de stratégie utilisé dans la demande de jeton est renseigné.
    - **tfp** : cette propriété est la valeur par défaut.
    - **ACR** : cette propriété est fournie uniquement pour la compatibilité descendante.

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
- **Désactivé** : ce paramètre oblige l’utilisateur à réexécuter toute la procédure pour chaque exécution du flux d’utilisateur. Par exemple, cela permet à plusieurs utilisateurs de s’inscrire à votre application (dans un scénario de type bureau partagé), même si un seul utilisateur reste connecté pendant toute la durée.

Ces paramètres ne sont pas disponibles pour les flux d’utilisateur de réinitialisation de mot de passe. 

