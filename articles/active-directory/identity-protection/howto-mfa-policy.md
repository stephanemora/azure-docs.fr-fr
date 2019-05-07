---
title: Comment configurer la stratégie d’inscription d’authentification multifacteur dans Azure Active Directory Identity Protection | Microsoft Docs
description: Découvrez comment configurer la stratégie d’inscription d’authentification multifacteur Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f4083ddf849842358f7699badca6598e56e4dee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139379"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Procédure : Configurer la stratégie d’inscription de l’authentification multifacteur Azure

Azure AD Identity Protection vous permet de gérer le déploiement de l’inscription de l’authentification multifacteur (MFA) en configurant une stratégie d’accès conditionnel pour exiger l’inscription MFA indépendamment de l’application vous vous connectez. Cet article explique ce que la stratégie peut être utilisée pour et comment le configurer.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Quelle est la stratégie d’inscription Azure multi-Factor Authentication ?

Azure multi-Factor Authentication fournit un moyen de vérifier qui vous utilisez plus que simplement un nom d’utilisateur et un mot de passe. Il fournit une deuxième couche de sécurité à l’utilisateur se connecte. Pour que les utilisateurs à être en mesure de répondre aux invites d’authentification Multifacteur, ils doivent s’enregistrer pour l’authentification multifacteur Azure.

Nous vous recommandons d’exiger Azure multi-Factor Authentication pour les connexions utilisateur, car elle :

- Elle fournit une authentification renforcée avec un éventail d’options de vérification simples.
- Joue un rôle clé dans la préparation de votre organisation pour protéger et récupérer à partir d’événements à risque dans Identity Protection

Pour plus d’informations sur l’authentification Multifacteur, consultez [What ' s Azure multi-Factor Authentication ?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Comment accéder à la stratégie d’inscription ?

La stratégie d’inscription MFA se trouve dans la section **Configurer** de la [page Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Stratégie MFA](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Paramètres de stratégie

Lorsque vous configurez la stratégie d’inscription MFA, vous devez apporter les modifications de configuration suivantes :

- Les utilisateurs et les groupes concernés par la stratégie. Pensez à exclure de votre organisation [comptes d’accès d’urgence](../users-groups-roles/directory-emergency-access.md).

    ![Utilisateurs et groupes](./media/howto-mfa-policy/11.png)

- Le contrôle que vous souhaitez appliquer - **inscription nécessite Azure MFA**

    ![Access](./media/howto-mfa-policy/12.png)

- Appliquer la stratégie doit être définie sur **sur**.

    ![Appliquer la stratégie](./media/howto-mfa-policy/14.png)

- **Enregistrer** votre stratégie

## <a name="user-experience"></a>Expérience utilisateur

Pour une obtenir une vue d’ensemble de l’expérience utilisateur, consultez :

- [Processus d’inscription à l’authentification multifacteur](flows.md#multi-factor-authentication-registration).  
- [Expériences de connexion avec Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble du service Azure AD Identity Protection, consultez l’article de [présentation d’Azure AD Identity Protection](overview.md).
