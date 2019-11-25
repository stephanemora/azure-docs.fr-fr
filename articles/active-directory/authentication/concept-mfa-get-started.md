---
title: Activer l’authentification multifacteur pour votre organisation – Azure Active Directory
description: Activer Azure MFA pour votre organisation en fonction de votre licence
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98bf62dff3e6c642f5de775f2d5bf8682ecea169
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164131"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>Activer l’authentification multifacteur pour votre organisation

Il existe plusieurs façons d’activer Azure Multi-Factor Authentication (MFA) pour vos utilisateurs d’Azure Active Directory (AD) en fonction des licences que votre organisation possède. 

![Examiner les signaux et appliquer l’authentification multifacteur si nécessaire](./media/concept-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Nos études révèlent que votre compte court 99,9 % moins de risque d’être compromis si vous utilisez l’authentification multifacteur.

Comment votre organisation active-t-elle l’authentification multifacteur même gratuitement, avant de devenir une statistique ?

## <a name="free-option"></a>Option gratuite

Les clients qui utilisent les avantages gratuits d’Azure AD peuvent utiliser les [paramètres de sécurité par défaut](../conditional-access/concept-conditional-access-security-defaults.md) pour activer l’authentification multifacteur dans leur environnement.

## <a name="office-365"></a>Office 365

Les clients disposant d’Office 365 ont deux options :

- Les [paramètres de sécurité par défaut](../conditional-access/concept-conditional-access-security-defaults.md) peuvent être activés via Azure AD pour protéger tous vos utilisateurs avec Azure Multi-Factor Authentication.
- Si votre organisation a besoin d’une plus grande précision pour fournir l’authentification multifacteur, vos licences Office incluent des fonctionnalités [MFA par utilisateur](../authentication/howto-mfa-userstates.md). L’authentification multifacteur par utilisateur est activée et appliquée individuellement à chaque utilisateur par les administrateurs.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Pour les clients disposant d’Azure AD Premium P1 ou de licences similaires incluant cette fonctionnalité, comme Enterprise Mobility + Security E3, Microsoft 365 F1 ou Microsoft 365 E3 : 

Il est recommandé d’utiliser des [stratégies d’accès conditionnel](../conditional-access/concept-conditional-access-policy-common.md) pour une expérience utilisateur optimale.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Pour les clients disposant d’Azure AD Premium P2 ou de licences similaires incluant cette fonctionnalité, comme Enterprise Mobility + Security E5 ou Microsoft 365 E5 : 

Il est recommandé d’utiliser des [stratégies d’accès conditionnel](../conditional-access/concept-conditional-access-policy-common.md) avec des stratégies en matière de risque [Identity Protection](../identity-protection/overview-v2.md) pour une expérience utilisateur et une flexibilité d’application optimales.

## <a name="authentication-methods"></a>Méthodes d’authentification

|   | Paramètres de sécurité par défaut | Toutes les autres méthodes |
| --- | --- | --- |
| Notification via une application mobile | X | X |
| Code de vérification provenant d’une application mobile ou d’un jeton matériel |   | X |
| Message texte vers le téléphone |   | X |
| Appel vers le téléphone |   | X |
| Mots de passe d'application |   | X** |

** Les mots de passe d’application sont uniquement disponibles dans l’authentification multifacteur par utilisateur avec des scénarios d’authentification hérités uniquement s’ils sont activés par des administrateurs.

## <a name="next-steps"></a>Étapes suivantes

[Page de tarification Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
