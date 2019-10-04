---
title: Vulnérabilités détectées par Azure Active Directory Identity Protection
description: Présentation des vulnérabilités détectées par Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 446e2ef33b714afe06f24f3dbfc46bc6a42790b8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335152"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnérabilités détectées par Azure Active Directory Identity Protection

Les vulnérabilités sont des points faibles d’un environnement qu’un cybercriminel peut exploiter. Nous recommandons aux administrateurs de corriger ces vulnérabilités pour améliorer la sécurité de leur organisation.

![Vulnérabilités signalées par Identity Protection](./media/vulnerabilities/identity-protection-vulnerabilities.png)

Les sections suivantes fournissent une vue d’ensemble des vulnérabilités signalées par Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Inscription à l’authentification multifacteur non configurée

Cette vulnérabilité vous permet d’évaluer le déploiement d’Azure Multi-Factor Authentication dans votre organisation.

Azure Multi-Factor Authentication ajoute une deuxième couche de sécurité à l’authentification utilisateur. Elle contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Azure Multi-Factor Authentication fournit des options de vérification faciles à utiliser comme :

* appel téléphonique
* SMS
* Notification sur l’application mobile
* Le code de vérification OTP

Nous vous recommandons d’exiger l’authentification multifacteur d’Azure pour les connexions des utilisateurs. L’authentification multifacteur joue un rôle clé dans les stratégies d’accès conditionnel en fonction des risques disponibles via Identity Protection.

Pour plus d’informations, consultez [Présentation d’Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md).

## <a name="unmanaged-cloud-apps"></a>Applications cloud non gérées

Cette vulnérabilité vous permet d’identifier les applications cloud non gérées au sein de votre organisation.

Le personnel informatique n’est pas toujours au courant de toutes les applications cloud qui sont utilisées au sein de l’organisation. Il est facile de comprendre pourquoi les administrateurs s’inquiètent d’un accès non autorisé aux données d’entreprise, de possibles fuites de données et d’autres risques de sécurité.

Nous vous recommandons de déployer Cloud Discovery pour découvrir les applications cloud non gérées, et gérer ces applications à l’aide d’Azure Active Directory.

Pour plus d’informations, consultez [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Alertes de sécurité du service Privileged Identity Management

Cette vulnérabilité vous aide à détecter et à résoudre les alertes relatives aux identités privilégiées dans votre organisation.  

Pour permettre aux utilisateurs d’effectuer des opérations privilégiées, les organisations doivent leur accorder un accès privilégié temporaire ou permanent à des ressources Azure AD, Azure ou Office 365 ou à d’autres applications SaaS. Chacun de ces utilisateurs privilégiés augmente la surface d’attaque de votre organisation. Cette vulnérabilité vous permet d’identifier les utilisateurs disposant d’un accès privilégié inutile et de prendre les mesures qui s’imposent pour réduire ou éliminer le risque associé.

Nous vous recommandons d’utiliser le service Azure AD Privileged Identity Management dans votre organisation pour gérer, contrôler et superviser les identités privilégiées dans Azure AD et dans d’autres services en ligne Microsoft tels qu’Office 365 ou Microsoft Intune.

Pour plus d’informations, consultez [Qu’est-ce qu’Azure AD Privileged Identity Management ?](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Voir aussi

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
