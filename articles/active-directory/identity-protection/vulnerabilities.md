---
title: Vulnérabilités détectées par Azure Active Directory Identity Protection
description: Présentation des vulnérabilités détectées par Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e792551f4cac857f56454c67d527e01cb9c4281
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113121"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnérabilités détectées par Azure Active Directory Identity Protection

Des vulnérabilités sont des failles dans un environnement qui peut être exploitée par un attaquant. Nous recommandons aux administrateurs résolvent ces vulnérabilités pour améliorer la sécurité de leur organisation.

![Vulnérabilités signalées par Identity Protection](./media/vulnerabilities/identity-protection-vulnerabilities.png)

Les sections suivantes fournissent une vue d’ensemble des vulnérabilités signalées par Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Inscription de l’authentification multifacteur non configurée

Cette vulnérabilité vous aide à évaluer le déploiement de l’authentification multifacteur Azure dans votre organisation.

Azure multi-Factor Authentication fournit une deuxième couche de sécurité à l’authentification utilisateur. Elle contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Azure multi-Factor Authentication fournit des options de vérification facile à utiliser comme :

* appel téléphonique
* SMS
* Notificaiton d'application mobile
* Code de vérification du secret à usage unique

Nous vous recommandons d’exiger l’authentification multifacteur d’Azure pour les connexions des utilisateurs. L’authentification multifacteur joue un rôle clé dans les stratégies d’accès conditionnel en fonction des risques disponibles via Identity Protection.

Pour plus d’informations, consultez [Présentation d’Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md).

## <a name="unmanaged-cloud-apps"></a>Applications cloud non managées

Cette vulnérabilité vous permet d’identifier les applications cloud non gérées au sein de votre organisation.

Personnel informatique est souvent pas conscients de toutes les applications cloud dans leur organisation. Il est facile de comprendre pourquoi les administrateurs s’inquiètent d’un accès non autorisé aux données d’entreprise, de possibles fuites de données et d’autres risques de sécurité.

Nous vous recommandons de déployer Cloud Discovery pour découvrir les applications cloud non gérées et pour gérer ces applications à l’aide d’Azure Active Directory.

Pour plus d’informations, consultez [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Alertes de sécurité de Privileged Identity Management

Cette vulnérabilité vous aide à détecter et à résoudre les alertes relatives aux identités privilégiées dans votre organisation.  

Pour permettre aux utilisateurs d’effectuer des opérations privilégiées, les organisations doivent leur accorder un accès privilégié temporaire ou permanent à des ressources Azure AD, Azure ou Office 365 ou à d’autres applications SaaS. Chacun de ces utilisateurs privilégiés augmente la surface d’attaque de votre organisation. Cette vulnérabilité vous permet d’identifier les utilisateurs disposant d’un accès privilégié inutile et de prendre les mesures qui s’imposent pour réduire ou éliminer le risque associé.

Nous recommandons aux organisations d’utiliser Azure AD Privileged Identity Management pour gérer, contrôler et surveiller les identités privilégiées dans Azure AD ainsi que d’autres services en ligne Microsoft comme Office 365 ou Microsoft Intune.

Pour plus d’informations, consultez [Qu’est-ce qu’Azure AD Privileged Identity Management ?](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Voir aussi

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
