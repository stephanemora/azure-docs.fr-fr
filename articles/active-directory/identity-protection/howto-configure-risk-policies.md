---
title: Comment configurer les stratégies en matière de risque dans Azure Active Directory Identity Protection (version actualisée) | Microsoft Docs
description: Comment configurer les stratégies en matière de risque dans Azure Active Directory Identity Protection (version actualisée)
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f244c28b99c429fef5641bb4fc399e09fd451069
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126552"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>Procédure : Configurer les stratégies en matière de risque dans Azure Active Directory Identity Protection (version actualisée)

Azure AD détecte les détections de risques indiquant des identités potentiellement compromises. La configuration de stratégies en matière de risque vous permet de définir les réponses automatiques à apporter aux résultats de la détection :

- Avec la stratégie de risque à la connexion, vous pouvez configurer une réponse à apporter à des détections de risques en temps réel détectées lors d’une connexion de l’utilisateur. 
- Avec la stratégie de risque de l'utilisateur, vous pouvez configurer une réponse à apporter à tous les risques de l'utilisateur actif détectés au fil du temps.  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="what-is-the-sign-in-risk-policy"></a>Qu’est-ce que la stratégie de connexion à risque ?

Azure AD analyse chaque connexion d’un utilisateur. L’objectif de l’analyse est de détecter les actions suspectes se produisant dans le cadre de la connexion. Par exemple, la connexion est-elle effectuée à l’aide d’une adresse IP anonyme, ou est-elle lancée à partir d’un emplacement inconnu ? Dans Azure AD, une action suspecte pouvant être détectée par le système est également appelée une détection de risque. Azure AD calcule une valeur en fonction des détections de risques détectées au cours d’une connexion. Cette valeur représente la probabilité (faible, moyenne ou élevée) avec laquelle la connexion n’est pas effectuée par un utilisateur légitime. La probabilité est appelée **niveau de risque de connexion**.

La stratégie de connexion à risque est une réponse automatisée que vous pouvez configurer pour un niveau de risque de connexion spécifique. Dans votre réponse, vous pouvez bloquer l’accès à vos ressources ou exiger la résolution d’un test de l’authentification multifacteur pour obtenir l’accès.

Lorsqu’un utilisateur exécute une invite MFA déclenchée par la stratégie de connexion à risque, il fournit des commentaires à Identity Protection et indique que la connexion provient de l’utilisateur légitime. Ainsi, la détection de connexion à risque qui a déclenché l’invite MFA est automatiquement fermée, et Identity Protection empêche cet événement de contribuer à l’élévation des risques utilisateur. L’activation de la stratégie de connexion à risque peut réduire la bruyance dans la vue des connexions à risque en permettant aux utilisateurs d’effectuer des corrections automatiques lors des invites MFA, puis en fermant automatiquement la connexion à risque associée.

## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Comment faire pour accéder à la stratégie de connexion à risque ?
   
La stratégie de risque de connexion est disponible dans la section **Configurer** de la [page Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Stratégie en matière de risque à la connexion](./media/howto-configure-risk-policies/1014.png "Stratégie en matière de risque à la connexion")

## <a name="sign-in-risk-policy-settings"></a>Paramètres de stratégie en matière de risque à la connexion

Lorsque vous configurez la stratégie de connexion à risque, vous devez définir les paramètres suivants :

- Les utilisateurs et les groupes auxquels la stratégie s’applique :

   ![Utilisateurs et groupes](./media/howto-configure-risk-policies/11.png)

- Le niveau de risque de connexion qui déclenche la stratégie :

   ![Niveau de risque d’une connexion](./media/howto-configure-risk-policies/12.png)

- Le type d’accès que vous souhaitez appliquer lorsque votre niveau de risque de connexion est atteint :  

   ![Access](./media/howto-configure-risk-policies/13.png)

- L’état de votre stratégie :

   ![Appliquer la stratégie](./media/howto-configure-risk-policies/14.png)

La boîte de dialogue de configuration de stratégie vous offre une option permettant d’évaluer l’impact de la reconfiguration.

![Impact estimé](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>Ce que vous devez savoir sur les stratégies en matière de risque à la connexion

Vous pouvez configurer une stratégie de sécurité en matière de connexion à risque pour exiger l’authentification multifacteur :

![Exiger une authentification multifacteur](./media/howto-configure-risk-policies/16.png)

Toutefois, pour des raisons de sécurité, ce paramètre s’applique uniquement aux utilisateurs qui ont déjà été inscrits pour l’authentification multifacteur. Identity Protection bloque les utilisateurs avec une exigence d’authentification multifacteur s’ils ne sont pas encore inscrits pour l’authentification multifacteur.

Si vous souhaitez exiger l’authentification multifacteur pour les connexions à risque, vous devez :

1. Activer la stratégie d'inscription à l'authentification multifacteur pour les utilisateurs concernés, et
2. Demander aux utilisateurs concernés de se connecter à une session ne présentant aucun risque pour s’inscrire à une authentification MFA.

Suivre ces étapes permet de s’assurer que l’authentification multifacteur est requise pour une connexion à risque.

La stratégie en matière de risque à la connexion :

- est appliquée à l’ensemble du trafic de navigateur et des connexions utilisant une authentification moderne ;
- n’est pas appliquée aux applications utilisant des protocoles de sécurité plus anciens en désactivant le point de terminaison WS-Trust sur le fournisseur d’identité fédérée, tels qu’ADFS.

Pour une obtenir une vue d’ensemble de l’expérience utilisateur, consultez :

* [Récupération de connexion à risque](flows.md#risky-sign-in-recovery)
* [Connexion à risque bloquée](flows.md#risky-sign-in-blocked)  
* [Expériences de connexion avec Azure AD Identity Protection](flows.md)  

## <a name="what-is-a-user-risk-policy"></a>Qu’est-ce qu’une stratégie de risque utilisateur ?

Azure AD analyse chaque connexion d’un utilisateur. L’objectif de l’analyse est de détecter les actions suspectes se produisant dans le cadre de la connexion. Dans Azure AD, une action suspecte pouvant être détectée par le système est également appelée une détection de risque. Bien que certaines détections de risques puissent être détectées en temps réel, pour d’autres, la détection peut prendre plus de temps. Par exemple, pour détecter une navigation impossible vers des emplacements inhabituels, le système nécessite une période d’apprentissage initiale de 14 jours pour en savoir plus sur le comportement normal d’un utilisateur. Il existe plusieurs options pour résoudre les détections de risques détectées. Par exemple, vous pouvez résoudre manuellement des détections de risques ou vous pouvez utiliser une stratégie d’accès conditionnel de risque utilisateur ou de connexion à risque pour le faire.

Toutes les détections de risques qui ont été détectées pour un utilisateur et qui n’ont pas été résolues sont appelées détections de risques actives. Les détections de risques actives qui sont associées à un utilisateur sont appelées risques utilisateur. En fonction du risque utilisateur, Azure AD calcule la probabilité (faible, moyenne, élevée) selon laquelle un utilisateur a été compromis. Cette probabilité est appelée niveau de risque utilisateur.

![Risque des utilisateurs](./media/howto-configure-risk-policies/11031.png)

La stratégie de risque utilisateur est une réponse automatisée que vous pouvez configurer pour un niveau de risque utilisateur spécifique. Avec une stratégie de risque utilisateur, vous pouvez bloquer l’accès à vos ressources ou demander une modification du mot de passe pour récupérer un compte utilisateur dans un état propre.

## <a name="how-do-i-access-the-user-risk-policy"></a>Comment faire pour accéder à la stratégie d’utilisateur à risque ?
   
La stratégie de risque d'utilisateur est disponible dans la section **Configurer** de la [page Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Stratégie de risque d’utilisateur](./media/howto-configure-risk-policies/11014.png)

## <a name="user-risk-policy-settings"></a>Paramètres de stratégie de risque d’utilisateur

Lorsque vous configurez la stratégie de risque utilisateur, vous devez définir les paramètres suivants :

- Les utilisateurs et les groupes auxquels la stratégie s’applique :

   ![Utilisateurs et groupes](./media/howto-configure-risk-policies/111.png)

- Le niveau de risque de connexion qui déclenche la stratégie :

   ![Niveau de risque d’un utilisateur](./media/howto-configure-risk-policies/112.png)

- Le type d’accès que vous souhaitez appliquer lorsque votre niveau de risque de connexion est atteint :  

   ![Access](./media/howto-configure-risk-policies/113.png)

- L’état de votre stratégie :

   ![Appliquer la stratégie](./media/howto-configure-risk-policies/114.png)

La boîte de dialogue de configuration de stratégie vous offre une option permettant d’évaluer l’impact de votre configuration.

![Impact estimé](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>Ce que vous devez savoir sur les stratégies de risque d'utilisateur

Vous pouvez définir une stratégie de sécurité de risque utilisateur pour bloquer les utilisateurs lors de la connexion selon le niveau de risque.

![Blocage](./media/howto-configure-risk-policies/116.png)

Le blocage d’une connexion :

* empêche la génération de nouvelles détections de risques pour l’utilisateur concerné ;
* permet aux administrateurs de corriger manuellement les détections de risques affectant l’identité de l’utilisateur pour sécuriser à nouveau cette dernière.

## <a name="best-practices"></a>Bonnes pratiques

La sélection d’un niveau de risque **Élevé** réduit la fréquence de déclenchement d’une stratégie et minimise l’impact sur les utilisateurs.  

Cependant, cela a pour effet d’exclure les connexions associées à un indicateur de risque **Faible** et **Moyen**. Par conséquent, il se peut qu’un cybercriminel soit en mesure d’exploiter une identité compromise.

Pour définir la stratégie

- Excluez les utilisateurs qui ne sont pas inscrits/ne peuvent pas s’inscrire à l’authentification multifacteur.
- Excluez les utilisateurs situés dans des régions où l’activation de la stratégie n’est pas adaptée (par exemple, aucun accès au support technique).
- Excluez les utilisateurs susceptibles de générer un grand nombre de faux positifs (développeurs, analystes de sécurité).
- Utilisez un niveau de risque **Élevé** pendant le déploiement initial de la stratégie ou si vous devez réduire la complexité pour les utilisateurs finaux.
- Utilisez un niveau de risque **Faible** si votre organisation nécessite une sécurité accrue. La sélection d’un niveau de risque **Faible** complique la connexion pour les utilisateurs, mais renforce la sécurité.

Pour la plupart des organisations, nous recommandons de configurer un niveau de risque **Moyen** afin d’établir un juste équilibre entre facilité d’utilisation et sécurité.

## <a name="next-steps"></a>Étapes suivantes

 [Channel 9 : Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
