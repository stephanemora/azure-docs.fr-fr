---
title: Comment configurer la stratégie d’inscription d’authentification multifacteur dans Azure Active Directory Identity Protection | Microsoft Docs
description: Découvrez comment configurer la stratégie d’inscription d’authentification multifacteur Azure AD Identity Protection.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd1049551d7dbc4823636dfdc00f64afab72cdf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60294848"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Procédure : Configurer la stratégie d’inscription d’authentification multifacteur

Azure AD Identity Protection vous permet de gérer le déploiement de l’inscription de l’authentification multifacteur (MFA) en configurant une stratégie pour exiger l’inscription MFA. Cet article explique ce que la stratégie peut être utilisée pour et comment le configurer.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>Qu’est-ce que la stratégie d’inscription d’authentification multifacteur ?

Azure Multi-Factor Authentication est une méthode permettant de vérifier votre identité qui requiert l’utilisation d’autres méthodes que le nom d’utilisateur et le mot de passe. Ce service fournit une deuxième couche de sécurité pour les connexions et les transactions de l'utilisateur. Pour que les utilisateurs à être en mesure de répondre aux invites d’authentification Multifacteur, ils doivent s’enregistrer pour l’authentification Multifacteur. 

Nous vous recommandons d’exiger l’authentification multifacteur d’Azure des connexions des utilisateurs pour les raisons suivantes :

- Elle fournit une authentification renforcée avec un éventail d’options de vérification simples.

- Elle joue un rôle clé dans la préparation de votre organisation pour protéger et récupérer les comptes compromis.


Pour plus d’informations sur l’authentification Multifacteur, consultez [What ' s Azure multi-Factor Authentication ?](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>Comment faire pour accéder à la stratégie d’inscription MFA ?
   
La stratégie d’inscription MFA se trouve dans la section **Configurer** de la [page Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Stratégie MFA](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>Paramètres de stratégie

Lorsque vous configurez la stratégie d’inscription MFA, vous devez définir :

- Les utilisateurs et les groupes auxquels la stratégie s’applique :

    ![Utilisateurs et groupes](./media/howto-mfa-policy/11.png)

- Le contrôle que vous souhaitez appliquer exigence d’inscription MFA :  

    ![Access](./media/howto-mfa-policy/12.png)

- L’état de votre stratégie :

    ![Appliquer la stratégie](./media/howto-mfa-policy/14.png)


La boîte de dialogue de configuration de stratégie vous offre une option permettant d’évaluer l’impact de votre configuration.

![Impact estimé](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>Expérience utilisateur


Pour une obtenir une vue d’ensemble de l’expérience utilisateur, consultez :

* [Processus d’inscription à l’authentification multifacteur](flows.md#multi-factor-authentication-registration).  
* [Expériences de connexion avec Azure AD Identity Protection](flows.md).  



## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble du service Azure AD Identity Protection, consultez l’article de [présentation d’Azure AD Identity Protection](overview.md).
