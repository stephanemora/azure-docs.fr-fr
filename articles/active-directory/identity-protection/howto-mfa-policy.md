---
title: Comment configurer la stratégie d’inscription d’authentification multifacteur dans Azure Active Directory Identity Protection | Microsoft Docs
description: Découvrez comment configurer la stratégie d’inscription d’authentification multifacteur Azure AD Identity Protection.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 47c742e486c01d2861889419a56c252e74f1e316
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54469829"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Procédure : Configurer la stratégie d’inscription d’authentification multifacteur

Azure AD Identity Protection vous permet de gérer le déploiement de l’inscription de l’authentification multifacteur (MFA) en configurant une stratégie. Cet article décrit ce que vous pouvez faire avec cette stratégie et comment la configurer.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>Qu’est-ce que la stratégie d’inscription d’authentification multifacteur ?

Azure Multi-Factor Authentication est une méthode permettant de vérifier votre identité qui requiert l’utilisation d’autres méthodes que le nom d’utilisateur et le mot de passe. Ce service fournit une deuxième couche de sécurité pour les connexions et les transactions de l'utilisateur.  

Nous vous recommandons d’exiger l’authentification multifacteur d’Azure des connexions des utilisateurs pour les raisons suivantes :

- Elle fournit une authentification renforcée avec un éventail d’options de vérification simples.

- Elle joue un rôle clé dans la préparation de votre organisation pour protéger et récupérer les comptes compromis.


Pour plus d’informations, consultez [Qu’est-ce qu’Azure Multi-Factor Authentication ?](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>Comment faire pour accéder à la stratégie d’inscription MFA ?
   
La stratégie d’inscription MFA se trouve dans la section **Configurer** de la [page Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Stratégie MFA](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>Paramètres de stratégie

Lorsque vous configurez la stratégie de connexion à risque, vous devez définir les paramètres suivants :

- Les utilisateurs et les groupes auxquels la stratégie s’applique :

    ![Utilisateurs et groupes](./media/howto-mfa-policy/11.png)

- Le type d’accès que vous souhaitez appliquer :  

    ![Utilisateurs et groupes](./media/howto-mfa-policy/12.png)

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
