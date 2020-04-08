---
title: Azure Multi-Factor Authentication pour votre organisation – Azure Active Directory
description: Découvrez les fonctionnalités d’Azure Multi-Factor Authentication que peut utiliser votre organisation selon son modèle de licence.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530391"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Présentation d’Azure Multi-Factor Authentication pour votre organisation

Il existe plusieurs façons d’activer Azure Multi-Factor Authentication pour vos utilisateurs Azure Active Directory (AD) en fonction des licences que détient votre organisation. 

![Examiner les signaux et appliquer l’authentification multifacteur si nécessaire](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Nos études révèlent que votre compte court 99,9 % moins de risque d’être compromis si vous utilisez l’authentification multifacteur (MFA).

Comment votre organisation peut-elle activer l’authentification multifacteur même gratuitement, avant de devenir une statistique ?

## <a name="free-option"></a>Option gratuite

Les clients qui utilisent les avantages gratuits d’Azure AD peuvent utiliser les [paramètres de sécurité par défaut](../fundamentals/concept-fundamentals-security-defaults.md) pour activer l’authentification multifacteur dans leur environnement.

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 Business Premium, E3 ou E5

Les clients disposant d’Office 365 ont deux options :

* Azure Multi-Factor Authentication est activé ou désactivé pour tous les utilisateurs, pour tous les événements de connexion. Il n’est pas possible d’activer l’authentification multifacteur uniquement pour un sous-ensemble d’utilisateurs ou pour certains scénarios. La gestion s’effectue via le portail Office 365. 
* Pour une expérience utilisateur améliorée, effectuez une mise à niveau vers Azure AD Premium P1 ou P2, et utilisez un accès conditionnel. Pour plus d’informations, découvrez les ressources Office 365 sécurisées à l’aide de l’authentification multifacteur.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Pour les clients disposant d’Azure AD Premium P1 ou de licences similaires incluant cette fonctionnalité, comme Enterprise Mobility + Security E3, Microsoft 365 F1 ou Microsoft 365 E3 : 

Utilisez l’[accès conditionnel Azure AD](../conditional-access/overview.md) pour inviter les utilisateurs à opter pour une authentification multifacteur dans le cadre de certains scénarios ou événements en fonction des besoins de votre entreprise.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Pour les clients disposant d’Azure AD Premium P2 ou de licences similaires incluant cette fonctionnalité, comme Enterprise Mobility + Security E5 ou Microsoft 365 E5 : 

Fournit la sécurité la plus forte et une expérience utilisateur améliorée. Ajoute l’[accès conditionnel en fonction des risques](../conditional-access/howto-conditional-access-policy-risk.md) aux fonctionnalités d’Azure AD Premium P1, qui s’adapte aux profils d’utilisation et minimise les invites d’authentification multifacteur.

## <a name="authentication-methods"></a>Méthodes d’authentification

|   | Paramètres de sécurité par défaut | Toutes les autres méthodes |
| --- | --- | --- |
| Notification via une application mobile | X | X |
| Code de vérification provenant d’une application mobile ou d’un jeton matériel |   | X |
| Message texte vers le téléphone |   | X |
| Appel vers le téléphone |   | X |

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer, suivez le tutoriel expliquant comment [sécuriser les événements de connexion utilisateur avec Azure Multi-Factor Authentication](../authentication/tutorial-enable-azure-mfa.md).

Pour plus d’informations sur les licences, consultez [Fonctionnalités et licences pour Azure Multi-Factor Authentication](../authentication/concept-mfa-licensing.md).
