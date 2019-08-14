---
title: Comment configurer la stratégie d’inscription d’authentification multifacteur dans Azure Active Directory Identity Protection | Microsoft Docs
description: Découvrez comment configurer la stratégie d’inscription d’authentification multifacteur Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27ff7512bb3f9422ed4c8edd7ab50fce23f0ed07
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499558"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Procédure : Configurer la stratégie d’inscription de l’authentification multifacteur Azure

Azure AD Identity Protection vous permet de gérer le déploiement de l’inscription de l’authentification multifacteur (MFA) en configurant une stratégie d’accès conditionnel dans le but de demander une inscription MFA, quelle que soit l’application avec authentification moderne à laquelle vous vous connectez. Cet article explique ce que cette stratégie permet de faire et comment la configurer.



## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Qu’est-ce qu’une stratégie d’inscription d’authentification multifacteur ?

L’authentification multifacteur Azure permet de vérifier votre identité, à l’aide d’une méthode plus sécurisée que la simple fourniture d’un nom d’utilisateur et d’un mot de passe. Ce service fournit une deuxième couche de sécurité pour les connexions utilisateur. Pour que les utilisateurs puissent répondre aux invites de l’authentification multifacteur, ils doivent s’inscrire pour l’authentification multifacteur Azure.

Voici les raisons pour lesquelles il est conseillé d’exiger l’authentification multifacteur d’Azure pour les connexions utilisateur :

- Elle fournit une authentification renforcée avec un éventail d’options de vérification simples.
- Elle joue un rôle clé dans la protection et la récupération en cas d’événements à risque dans Identity Protection.

Pour plus d’informations sur l’authentification multifacteur, consultez [Qu’est-ce qu’Azure Multi-Factor Authentication ?](../authentication/howto-mfa-getstarted.md).

## <a name="how-do-i-access-the-registration-policy"></a>Comment accéder à la stratégie d’inscription ?

La stratégie d’inscription MFA se trouve dans la section **Configurer** de la [page Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Stratégie MFA](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Paramètres de stratégie

Lorsque vous configurez la stratégie d’inscription MFA, vous devez apporter les modifications suivantes à la configuration :

- Les utilisateurs et les groupes auxquels la stratégie s’applique. N’oubliez pas d’exclure les [comptes d’accès d’urgence](../users-groups-roles/directory-emergency-access.md) de votre organisation.

    ![Utilisateurs et groupes](./media/howto-mfa-policy/11.png)

- Le contrôle que vous souhaitez appliquer - **Exiger l’inscription Azure MFA**

    ![Access](./media/howto-mfa-policy/12.png)

- L’option Appliquer la stratégie doit être définie sur **Activée**.

    ![Appliquer la stratégie](./media/howto-mfa-policy/14.png)

- **Enregistrez** votre stratégie.

## <a name="user-experience"></a>Expérience utilisateur

Azure Active Directory Identity Protection invite vos utilisateurs à s’inscrire la prochaine fois qu’ils se connectent de façon interactive.

Pour une obtenir une vue d’ensemble de l’expérience utilisateur, consultez :

- [Processus d’inscription à l’authentification multifacteur](flows.md#multi-factor-authentication-registration).  
- [Expériences de connexion avec Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble du service Azure AD Identity Protection, consultez l’article de [présentation d’Azure AD Identity Protection](overview.md).
