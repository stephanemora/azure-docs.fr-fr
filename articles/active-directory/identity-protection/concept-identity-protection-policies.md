---
title: Stratégies Azure AD Identity Protection
description: Identification des trois stratégies qui sont activées avec Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8776ad915ff215eef143f62aaa9e242ff0354f3e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368789"
---
# <a name="identity-protection-policies"></a>Stratégies Identity Protection

Azure Active Directory Identity Protection comprend trois stratégies par défaut que les administrateurs peuvent choisir d’activer. Ces stratégies incluent une personnalisation limitée, mais s’appliquent à la plupart des organisations. Toutes les stratégies permettent d’exclure des utilisateurs, tels que vos [comptes d’administrateur d’accès en urgence ou d’interruption](../roles/security-emergency-access.md).

![Stratégies Identity Protection](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Stratégie d’inscription Azure MFA

Identity protection peut aider les organisations à déployer Azure Multi-Factor Authentication (MFA) à l’aide d’une stratégie d’accès conditionnel nécessitant une inscription à la connexion. L’activation de cette stratégie est un excellent moyen de s’assurer que les nouveaux utilisateurs de votre organisation se sont inscrits à l’authentification MFA la première fois. L’authentification multifacteur est l’une des méthodes d’auto-restauration pour les événements à risque dans Identity Protection. L’auto-restauration permet à vos utilisateurs d’effectuer leurs propres actions pour réduire le volume d’appels du support technique.

Pour plus d’informations sur l’authentification multifacteur Azure, consultez l’article [Fonctionnement : Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Stratégie en matière de risque à la connexion

Identity Protection analyse les signaux de chaque connexion, en temps réel et hors connexion, et calcule un score de risque en fonction de la probabilité que la connexion n’ait pas été effectuée par l’utilisateur. Les administrateurs peuvent prendre une décision en fonction de ce score de risque pour appliquer les exigences organisationnelles. Les administrateurs peuvent choisir de bloquer l’accès, d’autoriser l’accès ou d’autoriser l’accès, mais nécessitent l’authentification multifacteur.

Si un risque est détecté, les utilisateurs peuvent effectuer l’authentification multifacteur pour résoudre automatiquement et fermer l’événement de connexion risquée afin d’éviter toute perturbation inutile pour les administrateurs.

> [!NOTE] 
> Les utilisateurs doivent avoir déjà été inscrits à Azure Multi-Factor Authentication avant de déclencher la stratégie de connexion à risque.

### <a name="custom-conditional-access-policy"></a>Stratégie d’accès conditionnel personnalisé

Les administrateurs peuvent également choisir de créer une stratégie d’accès conditionnel personnalisée, y compris le risque de connexion en tant que condition d’affectation. Pour plus d’informations sur le risque en tant que condition dans une politique d’accès conditionnel, consultez l’article [Accès conditionnel : Conditions](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

![Stratégie de risque de connexion d’accès conditionnel personnalisé](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Stratégie de risque d’utilisateur

Identity Protection peut calculer ce qui est normal pour le comportement d’un utilisateur et l’utiliser pour prendre des décisions en matière de risque. Le risque de l’utilisateur est un calcul de probabilité qu’une identité ait été compromise. Les administrateurs peuvent prendre une décision en fonction de ce score de risque pour appliquer les exigences organisationnelles. Les administrateurs peuvent choisir de bloquer l’accès, d’autoriser l’accès ou d’autoriser l'accès mais d’exiger le changement du mot de passe à l’aide de la [Réinitialisation du mot de passe libre-service Azure AD](../authentication/howto-sspr-deployment.md).

Si un risque est détecté, les utilisateurs peuvent effectuer la réinitialisation de mot de passe en libre-service pour résoudre automatiquement et fermer l’événement utilisateur à risque afin d’éviter toute perturbation inutile pour les administrateurs.

> [!NOTE] 
> Les utilisateurs doivent avoir déjà été inscrits pour la réinitialisation du mot de passe libre-service avant de déclencher la stratégie de risque utilisateur.

## <a name="next-steps"></a>Étapes suivantes

- [Activer la réinitialisation de mot de passe en libre-service Azure AD](../authentication/howto-sspr-deployment.md)

- [Activation d’Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

- [Activer une stratégie d’inscription d’authentification multifacteur](howto-identity-protection-configure-mfa-policy.md)

- [Activer les stratégies de connexion et de risque utilisateur](howto-identity-protection-configure-risk-policies.md)
