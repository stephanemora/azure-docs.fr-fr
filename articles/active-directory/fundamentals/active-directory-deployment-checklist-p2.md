---
title: Liste de contrôle pour la fonctionnalité de licence Premium P2 | Microsoft Docs
description: Liste de contrôle pour le déploiement des fonctionnalités d’Azure Active Directory Premium P2 à 30 jours, 90 jours et au-delà.
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.custom: it-pro, seodec18
ms.openlocfilehash: 61cefe762b639dcc58c2e10fc845dd9729cb2cb2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078230"
---
# <a name="azure-active-directory-premium-p2-licensing-feature-checklist"></a>Liste de contrôle pour la fonctionnalité de licence Azure Active Directory Premium P2

Il peut sembler trop lourd de déployer Azure Active Directory (Azure AD) pour votre entreprise et d’en assurer la sécurité. Cet article identifie quelques tâches courantes que les clients trouvent pratiques. En règle générale, les clients effectuent ces tâches au bout de 30 jours, de 90 jours ou au-delà pour améliorer leur sécurité. Même les organisations ayant déjà déployé Azure AD peuvent utiliser cette liste de contrôle pour vérifier qu’elles rentabilisent au mieux leur investissement.

Une infrastructure d’identité correctement planifiée et exécutée offre un accès plus sécurisé à vos charges de travail et vos données de productivité en authentifiant uniquement les utilisateurs et les appareils.

## <a name="prerequisites"></a>Prérequis

Ce guide suppose que vous avez des licences Azure AD Premium P2, Enterprise Mobility + Security E5, Microsoft 365 E5 ou un bundle de licences similaires.

[Gestion des licences Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)

[Microsoft 365 Entreprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)

[Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)

## <a name="plan-and-deploy-day-1-30"></a>Planifier et déployer : Jour 1-30

- Désigner plus d’un administrateur global (compte de secours)
   - [Gérer les comptes d’administration de l’accès d’urgence dans Azure AD](../users-groups-roles/directory-emergency-access.md)
- Activer Azure AD Privileged Identity Management pour consulter les rapports
   - [Commencer à utiliser PIM](../privileged-identity-management/pim-getting-started.md)
- Utilisez des rôles d’administration non globaux lorsque c’est possible.
   - [Attribution de rôles d’administrateur dans Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
- Authentification
   - [Déployer la réinitialisation du mot de passe en libre-service](../authentication/howto-sspr-deployment.md)
   - Déployer la protection de mot de passe Azure AD (préversion)
      - [Éliminer les mots de passe incorrects de votre organisation](../authentication/concept-password-ban-bad.md)
      - [Appliquer la protection de mot de passe Azure AD pour Windows Server Active Directory](../authentication/concept-password-ban-bad-on-premises.md)
   - Configurer des stratégies de verrouillage de compte
      - [Verrouillage intelligent Azure Active Directory](../authentication/howto-password-smart-lockout.md)
      - [Verrouillage extranet et verrouillage intelligent extranet AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)
   - [Déployer l’authentification multifacteur Azure AD à l’aide de stratégies d’accès conditionnel](../authentication/howto-mfa-getstarted.md)
   - [Activer l’inscription convergée pour la réinitialisation de mot de passe en libre-service et l’authentification multifacteur Azure (préversion)](../authentication/concept-registration-mfa-sspr-converged.md)
   - [Activer Azure Active Directory Identity Protection](../identity-protection/enable.md)
      - [Utiliser des événements à risque pour déclencher des modifications de mot de passe et d’authentification multifacteur](../authentication/tutorial-risk-based-sspr-mfa.md)
   - [Conseils pour le choix du mot de passe](https://www.microsoft.com/research/publication/password-guidance/)
      - Respectez une longueur minimale de 8 caractères. Il n’est pas nécessaire de faire plus long.
      - Faites fi des exigences en matière de types de caractères.
      - [Faites fi des réinitialisations de mot de passe périodiques obligatoires pour les comptes d’utilisateur.](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire)
- Synchroniser les utilisateurs depuis Active Directory local
   - [Installer Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md)
   - [Implémenter la synchronisation de hachage du mot de passe](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)
   - [Implémenter l’écriture différée du mot de passe](../authentication/howto-sspr-writeback.md)
   - [Implémenter Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)
- [Affecter des licences aux utilisateurs par appartenance aux groupes dans Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)

## <a name="plan-and-deploy-day-31-90"></a>Planifier et déployer : Jour 31-90

- [Planifier l’accès d’utilisateurs invités](../b2b/what-is-b2b.md)
   - [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure](../b2b/add-users-administrator.md)
   - [Autoriser ou bloquer des invitations aux utilisateurs B2B à partir d’organisations spécifiques](../b2b/allow-deny-list.md)
   - [Grant B2B users in Azure AD access to your on-premises applications](../b2b/hybrid-cloud-to-on-premises.md) (Accorder aux utilisateurs B2B dans Azure AD l’accès à vos applications locales)
- Prendre des décisions au sujet de la stratégie de gestion du cycle de vie utilisateur
- [Décider de la stratégie de gestion des appareils](../devices/overview.md)
   - [Scénarios d’utilisation et considérations relatives au déploiement pour Azure AD Join](../devices/azureadjoin-plan.md)
- [Gérer Windows Hello Entreprise dans votre organisation](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

## <a name="plan-and-deploy-day-90-and-beyond"></a>Planifier et déployer : Jour 90 et au-delà

- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)
   - [Configurer les paramètres des rôles d’annuaire Azure AD dans PIM](../privileged-identity-management/pim-how-to-change-default-settings.md)
   - [Attribuer des rôles d’annuaire Azure AD dans PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md)
- [Terminer une révision d’accès des rôles d’annuaire Azure AD dans PIM](../privileged-identity-management/pim-how-to-start-security-review.md)
- Gérer le cycle de vie utilisateur de façon globale
   - Azure AD dispose d’une approche pour la gestion du cycle de vie des identités
   - Supprimer les étapes manuelles du cycle de vie des comptes de vos employés, pour vous prémunir contre des accès non autorisés :
      - Synchronisez des identités depuis votre source fidèle (système HR) vers Azure AD. lien vers les systèmes HR pris en charge)
      - [Utilisez des groupes dynamiques pour affecter automatiquement des utilisateurs à des groupes en fonction de leurs attributs dans HR (ou votre source fidèle), tels que le département, le titre, la région, et autres.](../users-groups-roles/groups-dynamic-membership.md)
      - [Utilisez l’approvisionnement de gestion d’accès basé sur un groupe pour approvisionner automatiquement des utilisateurs pour les applications SaaS.](../manage-apps/what-is-access-management.md)

## <a name="next-steps"></a>Étapes suivantes

[Configurations des identités et de l’accès aux appareils](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Stratégies d’accès courantes et recommandées pour les appareils et identités](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
