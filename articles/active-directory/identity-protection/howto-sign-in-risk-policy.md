---
title: Comment configurer la stratégie de connexion à risque dans Azure Active Directory Identity Protection | Microsoft Docs
description: Découvrez comment configurer la stratégie de connexion à risque d’Azure AD Identity Protection.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d2ddcb879e9f152fd7f3ab7867d263b88cbbef
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193272"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Procédure : Configurer la stratégie en matière de risque à la connexion

Azure Active Directory détecte les [types d’événements à risque](../reports-monitoring/concept-risk-events.md#risk-event-types) en temps réel et hors connexion. Tous les événements à risque qui sont détectés pendant la connexion d’un utilisateur viennent alimenter un concept logique appelé « connexion risquée ». Une connexion risquée est une tentative de connexion susceptible d’émaner d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur.


## <a name="what-is-the-sign-in-risk-policy"></a>Qu’est-ce que la stratégie de connexion à risque ?

Azure AD analyse chaque connexion d’un utilisateur. L’objectif de l’analyse est de détecter les actions suspectes se produisant dans le cadre de la connexion. Par exemple, la connexion est-elle effectuée à l’aide d’une adresse IP anonyme, ou est-elle lancée à partir d’un emplacement inconnu ? Dans Azure AD, une action suspecte pouvant être détectée par le système est également appelée un événement à risque. Azure AD calcule une valeur en fonction des événements à risque détectés au cours d’une connexion. Cette valeur représente la probabilité (faible, moyenne ou élevée) avec laquelle la connexion n’est pas effectuée par un utilisateur légitime. La probabilité est appelée **niveau de risque de connexion**.

La stratégie de connexion à risque est une réponse automatisée que vous pouvez configurer pour un niveau de risque de connexion spécifique. Dans votre réponse, vous pouvez bloquer l’accès à vos ressources ou exiger la résolution d’un test de l’authentification multifacteur pour obtenir l’accès.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Comment faire pour accéder à la stratégie de connexion à risque ?
   
La stratégie de risque de connexion est disponible dans la section **Configurer** de la [page Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Stratégie en matière de risque à la connexion](./media/howto-sign-in-risk-policy/1014.png "Stratégie en matière de risque à la connexion")


## <a name="policy-settings"></a>Paramètres de stratégie

Lorsque vous configurez la stratégie de connexion à risque, vous devez définir les paramètres suivants :

- Les utilisateurs et les groupes auxquels la stratégie s’applique :

    ![Utilisateurs et groupes](./media/howto-sign-in-risk-policy/11.png)

- Le niveau de risque de connexion qui déclenche la stratégie :

    ![Niveau de risque d’une connexion](./media/howto-sign-in-risk-policy/12.png)

- Le type d’accès que vous souhaitez appliquer lorsque votre niveau de risque de connexion est atteint :  

    ![Access](./media/howto-sign-in-risk-policy/13.png)

- L’état de votre stratégie :

    ![Appliquer la stratégie](./media/howto-sign-in-risk-policy/14.png)


La boîte de dialogue de configuration de stratégie vous offre une option permettant d’évaluer l’impact de la reconfiguration.

![Impact estimé](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>Ce que vous devez savoir

Vous pouvez configurer une stratégie de sécurité en matière de connexion à risque pour exiger l’authentification multifacteur :

![Exiger une authentification multifacteur](./media/howto-sign-in-risk-policy/16.png)

Toutefois, pour des raisons de sécurité, ce paramètre s’applique uniquement aux utilisateurs qui ont déjà été inscrits pour l’authentification multifacteur. Identity Protection bloque les utilisateurs avec une exigence d’authentification multifacteur s’ils ne sont pas encore inscrits pour l’authentification multifacteur.

Si vous souhaitez exiger l’authentification multifacteur pour les connexions à risque, vous devez :

1. Activer la [stratégie d’inscription à l’authentification multifacteur](howto-mfa-policy.md) pour les utilisateurs concernés, et

2. Demander aux utilisateurs concernés de se connecter à une session sécurisée pour s’inscrire à l’authentification MFA.

Suivre ces étapes permet de s’assurer que l’authentification multifacteur est requise pour une connexion à risque.

La stratégie en matière de risque à la connexion :

- est appliquée à l’ensemble du trafic de navigateur et des connexions utilisant une authentification moderne ;

- n’est pas appliquée aux applications utilisant des protocoles de sécurité plus anciens en désactivant le point de terminaison WS-Trust sur le fournisseur d’identité fédérée, tels qu’ADFS.


Pour une obtenir une vue d’ensemble de l’expérience utilisateur, consultez :

* [Récupération de connexion à risque](flows.md#risky-sign-in-recovery)
* [Connexion à risque bloquée](flows.md#risky-sign-in-blocked)  
* [Expériences de connexion avec Azure AD Identity Protection](flows.md)  

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

Pour obtenir une vue d’ensemble du service Azure AD Identity Protection, consultez l’article de [présentation d’Azure AD Identity Protection](overview.md).
