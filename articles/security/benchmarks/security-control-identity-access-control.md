---
title: Contrôle de sécurité Azure - Contrôle des accès et des identités
description: Contrôle des accès et des identités dans le cadre du contrôle de sécurité
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 543573610c2ea3ab0bcd89e1b8f4ee5f5a34dbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934428"
---
# <a name="security-control-identity-and-access-control"></a>Contrôle de sécurité : Contrôle des accès et des identités

Les recommandations en matière de gestion des identités et des accès se concentrent sur le traitement des problèmes liés au contrôle d’accès en fonction de l’identité, au verrouillage de l’accès administratif, à la génération d’alertes lors d’événements liés à l’identité, au comportement anormal des comptes et au contrôle d’accès en fonction du rôle.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3.1 | 4,1 | Customer |

Azure AD comporte des rôles intégrés qui doivent être explicitement attribués et qui peuvent être interrogés. Utilisez le module Azure AD PowerShell pour effectuer des requêtes ad hoc afin de découvrir les comptes membres de groupes d’administration.

Comment obtenir un rôle d’annuaire dans Azure AD avec PowerShell :

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Comment obtenir les membres d’un rôle d’annuaire dans Azure AD avec PowerShell :

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

## <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3.2 | 4,2 | Customer |

Azure AD n’intègre pas le concept des mots de passe par défaut. Selon le service, d’autres ressources Azure qui exigent un mot de passe forcent la création d’un mot de passe conforme à des exigences de complexité et d’une longueur minimale. Vous êtes responsable des applications tierces et des services de la place de marché susceptibles d’utiliser des mots de passe par défaut.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3.3 | 4.3 | Customer |

Créez des procédures standard autour de l’utilisation de comptes d’administration dédiés. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration.

Vous pouvez également activer JIT (Juste-à-temps)/JEA (Just-Enough-Access) à l’aide de rôles Azure AD Privileged Identity Management pour les services Microsoft et d’Azure Resource Manager. 

En savoir plus : https://docs.microsoft.com/azure/active-directory/privileged-identity-management/

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4 : Utiliser l’authentification unique (SSO) avec Azure Active Directory

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3.4 | 4.4 | Customer |

Dans la mesure du possible, utilisez l’authentification unique (SSO) d’Azure Active Directory au lieu de configurer des informations d’identification autonomes individuelles par service. Suivez les recommandations liées à la gestion des identités et des accès dans Azure Security Center.

Comprendre l’authentification unique (SSO) avec Azure AD :

https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3,5 | 4.5, 11.5, 12.11, 16.3 | Customer |

Activez Azure AD MFA et suivez les recommandations liées à la gestion des identités et des accès dans Azure Security Center.

Comment activer l’authentification multifacteur dans Azure :

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Comment surveiller l’identité et l’accès dans Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des ordinateurs dédiés (stations de travail avec accès privilégié) pour toutes les tâches administratives

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Customer |

Utilisez des stations de travail avec accès privilégié avec l’authentification multifacteur (MFA) configurée pour la connexion aux ressources Azure et leur configuration.

En savoir plus sur les stations de travail avec accès privilégié :

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Comment activer l’authentification multifacteur dans Azure :

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes depuis les comptes d’administration

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3.7 | 4.8, 4.9 | Customer |

Utilisez les rapports de sécurité Azure Active Directory pour générer des journaux et des alertes lorsque des activités suspectes ou potentiellement dangereuses se produisent dans l’environnement. Utiliser Azure Security Center pour superviser les activités liées aux identités et aux accès.

Comment identifier les utilisateurs Azure AD signalés pour une activité à risque :

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Comment superviser les activités liées aux identités et aux accès des utilisateurs dans Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3.8 | 11.7 | Customer |

Utilisez des emplacements nommés à accès conditionnel pour autoriser l’accès uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions.

Comment configurer des emplacements nommés dans Azure :

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

## <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Customer |

Utilisez Azure Active Directory (AAD) comme système d’authentification et d’autorisation central. AAD protège les données à l’aide d’un chiffrement renforcé pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

Comment créer et configurer une instance AAD :

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3.10 | 16.9, 16.10 | Customer |

Azure AD fournit des journaux pour vous aider à découvrir les comptes obsolètes. De plus, utilisez les révisions d’accès des identités Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs peut être passé en revue régulièrement pour vérifier que seules les utilisateurs appropriés continuent de bénéficier d’un accès. 

Génération de rapports Azure AD :

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Comment utiliser les révisions d’accès des identités Azure :

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

## <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11 : Superviser les tentatives d’accès aux comptes désactivés

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3.11 | 16.12 | Customer |

Vous avez accès aux activités de connexion Azure AD, aux sources des journaux d’événements à risque et d’audit, ce qui vous permet de les intégrer à un outil SIEM/de supervision.

Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure Active Directory et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Vous pouvez configurer les alertes souhaitées dans un espace de travail Log Analytics.

Comment intégrer des journaux d’activité Azure dans Azure Monitor :

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12 : Alerte en cas d’écart de comportement de connexion à un compte

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3,12 | 16.13 | Customer |

Utilisez les fonctionnalités d’Azure AD Identity Risk and Identity Protection pour configurer des réponses automatiques aux actions suspectes détectées liées aux identités d’utilisateur. Vous pouvez également ingérer des données dans Azure Sentinel pour approfondir votre examen.

Comment afficher les connexions risquées à Azure AD :

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Comment configurer et activer des stratégies de protection des identités :

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Comment intégrer Azure Sentinel :

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 : Fournir à Microsoft un accès aux données client pertinentes pendant les scénarios de support

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 3.13 | 16 | Customer |

Dans le cadre des scénarios de support où Microsoft a besoin d’accéder aux données client, Customer Lockbox fournit une interface qui vous permet de consulter et d’approuver ou refuser les demandes d’accès aux données client.

Comprendre Customer Lockbox :

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

## <a name="next-steps"></a>Étapes suivantes

Reportez-vous au contrôle de sécurité suivant : [Protection des données](security-control-data-protection.md)
