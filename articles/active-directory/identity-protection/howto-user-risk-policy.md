---
title: Comment configurer la stratégie de risque utilisateur dans Azure Active Directory Identity Protection | Microsoft Docs
description: Découvrez comment configurer la stratégie de risque utilisateur Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc7ea05497d69a7ca833cc783e7a2bc6bf1a8b07
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335441"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Procédure : Configurer la stratégie en matière de risque à l’utilisateur

Avec le risque utilisateur, Azure AD détecte la probabilité selon laquelle un compte utilisateur a été compromis. En tant qu’administrateur, vous pouvez configurer une stratégie d’accès conditionnel de risque utilisateur pour répondre automatiquement à un niveau de risque utilisateur spécifique.
 
Cet article vous fournit les informations dont vous avez besoin pour configurer une stratégie de risque utilisateur.

## <a name="what-is-a-user-risk-policy"></a>Qu’est-ce qu’une stratégie de risque utilisateur ?

Azure AD analyse chaque connexion d’un utilisateur. L’objectif de l’analyse est de détecter les actions suspectes se produisant dans le cadre de la connexion. Dans Azure AD, une action suspecte pouvant être détectée par le système est également appelée un événement à risque. Bien que certains événements à risque puissent être détectés en temps réel, pour d’autres, la détection peut prendre plus de temps. Par exemple, pour détecter une navigation impossible vers des emplacements inhabituels, le système nécessite une période d’apprentissage initiale de 14 jours pour en savoir plus sur le comportement normal d’un utilisateur. Il existe plusieurs options pour résoudre les événements à risque détectés. Par exemple, vous pouvez résoudre manuellement des événements à risque ou vous pouvez utiliser une stratégie d’accès conditionnel de risque utilisateur ou de connexion à risque pour le faire.

Tous les événements à risque qui ont été détectés pour un utilisateur et qui n’ont pas été résolus sont appelés événements à risque actifs. Les événements à risque actifs qui sont associés à un utilisateur sont appelés risque utilisateur. En fonction du risque utilisateur, Azure AD calcule la probabilité (faible, moyenne, élevée) selon laquelle un utilisateur a été compromis. Cette probabilité est appelée niveau de risque utilisateur.

![Risque des utilisateurs](./media/howto-user-risk-policy/1031.png)

La stratégie de risque utilisateur est une réponse automatisée que vous pouvez configurer pour un niveau de risque utilisateur spécifique. Avec une stratégie de risque utilisateur, vous pouvez bloquer l’accès à vos ressources ou demander une modification du mot de passe pour récupérer un compte utilisateur dans un état propre.

## <a name="how-do-i-access-the-user-risk-policy"></a>Comment faire pour accéder à la stratégie d’utilisateur à risque ?
   
La stratégie de risque de connexion est disponible dans la section **Configurer** de la [page Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Stratégie de risque d’utilisateur](./media/howto-user-risk-policy/1014.png)

## <a name="policy-settings"></a>Paramètres de stratégie

Lorsque vous configurez la stratégie de connexion à risque, vous devez définir les paramètres suivants :

- Les utilisateurs et les groupes auxquels la stratégie s’applique :

    ![Utilisateurs et groupes](./media/howto-user-risk-policy/11.png)

- Le niveau de risque de connexion qui déclenche la stratégie :

    ![Niveau de risque d’un utilisateur](./media/howto-user-risk-policy/12.png)

- Le type d’accès que vous souhaitez appliquer lorsque votre niveau de risque de connexion est atteint :  

    ![Access](./media/howto-user-risk-policy/13.png)

- L’état de votre stratégie :

    ![Appliquer la stratégie](./media/howto-user-risk-policy/14.png)

La boîte de dialogue de configuration de stratégie vous offre une option permettant d’évaluer l’impact de votre configuration.

![Impact estimé](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>Ce que vous devez savoir

Vous pouvez définir une stratégie de sécurité de risque utilisateur pour bloquer les utilisateurs lors de la connexion selon le niveau de risque.

![Blocage](./media/howto-user-risk-policy/16.png)

Le blocage d’une connexion :

* empêche la génération de nouveaux événements à risque pour l’utilisateur concerné ;
* permet aux administrateurs de corriger manuellement les événements à risques affectant l’identité de l’utilisateur pour sécuriser à nouveau cette dernière.

## <a name="best-practices"></a>Bonnes pratiques

La sélection d’un niveau de risque **Élevé** réduit la fréquence de déclenchement d’une stratégie et minimise l’impact sur les utilisateurs.
Cependant, cela a pour effet d’exclure les utilisateurs associés à un indicateur de risque **Faible** et **Moyen**. Par conséquent, il se peut que des identités ou des appareils déjà identifiés comme potentiellement ou effectivement compromis ne soient pas sécurisés.

Pour définir la stratégie

* Excluez les utilisateurs susceptibles de générer un grand nombre de faux positifs (développeurs, analystes de sécurité).
* Excluez les utilisateurs situés dans des régions où l’activation de la stratégie n’est pas adaptée (par exemple, aucun accès au support technique).
* Utilisez un niveau de risque **Élevé** pendant le déploiement initial de la stratégie ou si vous devez minimiser la complexité pour les utilisateurs finaux.
* Utilisez un niveau de risque **Faible** si votre organisation nécessite une sécurité accrue. La sélection d’un niveau de risque **Faible** complique la connexion pour les utilisateurs, mais renforce la sécurité.

Pour la plupart des organisations, nous recommandons de configurer un niveau de risque **Moyen** afin d’établir un juste équilibre entre facilité d’utilisation et sécurité.

Pour une obtenir une vue d’ensemble de l’expérience utilisateur, consultez :

* [Flux de récupération de compte compromis](flows.md#compromised-account-recovery).  
* [Flux de compte compromis bloqué](flows.md#compromised-account-blocked).  

**Pour ouvrir la boîte de dialogue de configuration connexe**:

- Dans le panneau **Azure AD Identity Protection**, dans la section **Configurer**, cliquez sur **Stratégie de risque d’utilisateur**.

    ![Stratégie de risque d’utilisateur](./media/howto-user-risk-policy/1009.png "Stratégie de risque d’utilisateur")

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble du service Azure AD Identity Protection, consultez l’article de [présentation d’Azure AD Identity Protection](overview.md).
