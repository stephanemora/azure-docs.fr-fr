---
title: Benchmark de sécurité Azure v2 – Accès privilégié
description: 'Benchmark de sécurité Azure v2 : Accès privilégié'
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059044"
---
# <a name="security-control-privileged-access"></a>Contrôle de sécurité : Accès privilégié

L’accès privilégié recouvre les contrôles permettant de protéger l’accès privilégié à votre locataire Azure et à vos ressources. Cela comprend une série de contrôles visant à protéger votre modèle d’administration, vos comptes d’administration et les stations de travail à accès privilégié contre les risques délibérés et involontaires.

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1 : Protéger et limiter les administrateurs généraux

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| PA-1 | 4.3, 4.8 | AC-2 |

Les utilisateurs qui sont affectés au rôle d’administrateur d’entreprise peuvent lire et modifier chaque paramètre d’administration de votre organisation Azure AD. Limitez le nombre de vos comptes Administrateur général Azure à un maximum de deux pour chaque abonnement. Les rôles intégrés les plus critiques dans Azure AD sont Administrateur général et Administrateur de rôle privilégié, car les utilisateurs affectés à ces deux rôles peuvent déléguer des rôles Administrateur :
- Administrateur général ou Administrateur d’entreprise : Les utilisateurs disposant de ce rôle ont accès à toutes les fonctionnalités d’administration d’Azure AD, ainsi qu’aux services qui utilisent des identités Azure AD.

- Administrateur de rôle privilégié : Les utilisateurs avec ce rôle peuvent gérer les attributions de rôles dans Azure AD et Azure AD Privileged Identity Management (PIM). De plus, ce rôle permet de gérer tous les aspects de PIM et des unités administratives.

Remarque : Vous pouvez avoir d’autres rôles critiques qui doivent être régis si vous utilisez des rôles personnalisés avec certaines autorisations attribuées. Vous pouvez également appliquer des contrôles similaires au compte Administrateur des ressources métier critiques.  

Vous pouvez activer l’accès privilégié juste-à-temps (JAT) aux ressources Azure et Azure AD en utilisant Azure AD Privileged Identity Management (PIM). JAT accorde des autorisations temporaires pour effectuer des tâches privilégiées uniquement lorsque les utilisateurs en ont besoin. PIM peut également générer des alertes de sécurité en cas d’activité suspecte ou non sécurisée dans votre organisation Azure AD.

Remarque : Certains services Azure tels qu’Azure SQL prennent en charge l’authentification des utilisateurs locaux en plus de l’authentification Azure AD. Ce type d’authentification d’utilisateur local n’est pas géré par le biais d’Azure AD. Vous devrez gérer ces utilisateurs séparément.

- [Autorisations de rôle Administrateur dans Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Rôle personnalisés Azure](../../role-based-access-control/custom-roles.md)

- [Utiliser les alertes de sécurité Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Guide pratique pour obtenir un rôle d’annuaire dans Azure AD avec PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Utiliser Azure Security Center pour surveiller l’identité et l’accès](../../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Identité et clés](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sécurité des applications et DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2 : Examiner et rapprocher régulièrement les accès utilisateur

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| PA-2 | 4.1, 16.9, 16.10 | AC-2 |

Vérifiez régulièrement les comptes d’utilisateur et les droits d’accès pour vous assurer de leur validité. 

Utilisez les révisions d’accès et des identités Azure AD pour gérer les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. Les rapports Azure AD peuvent fournir des journaux pour vous aider à découvrir les comptes obsolètes. Vous pouvez également utiliser Azure AD Privileged Identity Management pour créer un workflow de rapport de révision d’accès afin de faciliter le processus de révision.

Pour les utilisateurs administratifs au niveau du service et de la charge de travail Azure, une révision plus fréquente des utilisateurs et des accès doit être effectuée. Vous pouvez également utiliser les alertes de sécurité d’Azure Privileged Identity Management pour détecter la création de comptes Administrateur et pour rechercher ceux qui sont obsolètes ou mal configurés. 

Remarque : Certains services Azure tels qu’Azure SQL prennent en charge les utilisateurs locaux qui ne sont pas gérés par le biais d’Azure AD. Vous devrez gérer ces utilisateurs séparément.

- [Guide pratique pour obtenir un rôle d’annuaire dans Azure AD avec PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Guide pratique pour obtenir les membres d’un rôle d’annuaire dans Azure AD avec PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Utiliser Azure Security Center pour surveiller l’identité et l’accès](../../security-center/security-center-identity-access.md)

- [Utiliser les alertes de sécurité Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Aligner les responsabilités administratives entre les équipes](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [Présentation des rapports Azure AD](/azure/active-directory/reports-monitoring/)

- [Utilisation des révisions d’accès et des identités Azure AD](../../active-directory/governance/access-reviews-overview.md)

- [Privileged Identity Management : Réviser l’accès aux rôles Azure AD](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Azure Security Center : Surveiller l’identité et l’accès](../../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Identité et clés](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sécurité des applications et DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestion de la conformité de la sécurité](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3 : Configurer un compte d’accès d’urgence dans Azure AD

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| PA-3 | 12.3 | AC-2 |

Pour empêcher le verrouillage accidentel de votre organisation Azure AD, configurez un compte d’accès d’urgence pour conserver un accès lorsque les comptes administratifs normaux ne peuvent pas être utilisés. Les comptes d’accès d’urgence sont généralement hautement privilégiés et ne doivent pas être attribués à des personnes spécifiques. Les comptes d’accès d’urgence sont limités à des cas d’urgence ou à des scénarios « de secours » où il est impossible d’utiliser des comptes d’administration normaux.

Vous devez vous assurer que les informations d’identification (telles que le mot de passe, le certificat ou la carte à puce) des comptes d’accès d’urgence restent sécurisées et connues des seules personnes autorisées à les utiliser en cas d’urgence.

- [Gérer des comptes d’accès d’urgence dans Azure AD](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Identité et clés](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sécurité des applications et DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestion de la conformité de la sécurité](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Centre des opérations de sécurité](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4 : Automatiser le workflow des demandes d’identité et d’accès Azure

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| PA-4 | N/A | AC-2, AC-5, PM-10 |

Utilisez les fonctionnalités de gestion des droits d’utilisation d’Azure AD pour automatiser les workflows de demandes d’accès Azure, notamment les attributions, les révisions et l’expiration des accès. L’approbation en deux ou plusieurs étapes est également prise en charge.  

- [Présentation de la gestion des droits d’utilisation Azure AD](../../active-directory/governance/entitlement-management-overview.md)

- [Procédure de configuration d’une demande d’accès et d’un processus d’approbation](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Identité et clés](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Sécurité des applications et DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestion de la conformité de la sécurité](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5 : Utiliser des ordinateurs hautement sécurisés pour les tâches d’administration

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| PA-5 | 4.6, 11.6, 12.12 | AC-2, SC-7 |

Les stations de travail sécurisées et isolées sont extrêmement importantes pour la sécurité des rôles sensibles comme les administrateurs, développeurs et opérateurs de service critique. Utilisez des stations de travail utilisateur hautement sécurisées et/ou Azure Bastion pour les tâches d’administration :
- Utilisez Azure Active Directory, Microsoft Defender Advanced Threat Protection (MDATP) et/ou Microsoft Intune pour déployer une station de travail utilisateur sécurisée et gérée pour les tâches d’administration. Les stations de travail sécurisées peuvent être gérées de manière centralisée pour appliquer une configuration sécurisée, notamment une authentification forte, des lignes de base logicielles et matérielles et un accès réseau et logique restreint. 

- Utilisez la fonctionnalité Azure Bastion pour obtenir un chemin d’accès sécurisé à vos machines virtuelles via RDP ou SSH. Azure Bastion est un service PaaS complètement managé qui peut être approvisionné par réseau virtuel sans créer de nouveau réseau virtuel. 

- [Comprendre les stations de travail sécurisées gérées par Azure](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Déployer une station de travail sécurisée gérée par Azure](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Utiliser l’hôte Azure Bastion](../../bastion/bastion-create-host-portal.md)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Sécurité des applications et DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Centre des opérations de sécurité](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Identité et clés](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6 : Attribuer des privilèges aux ressources à l’aide d’Azure RBAC

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| PA-6 | 14.6 | AC-2, AC-3 |

Le contrôle d’accès en fonction du rôle Azure (RBAC) vous permet de gérer les privilèges d’accès aux ressources Azure par le biais d’attributions de rôles. Vous pouvez affecter ces rôles à des utilisateurs, regrouper des principaux de service et des identités managées. Les rôles intégrés prédéfinis pour certaines ressources peuvent être inventoriés ou interrogés par le biais d’outils tels que Azure CLI, Azure PowerShell ou le Portail Azure. 

- [Présentation du contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md)

- [Configurer le contrôle d'accès en fonction du rôle dans Azure](../../role-based-access-control/role-assignments-portal.md)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Sécurité des applications et DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestion de la conformité de la sécurité](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestion de la posture](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Identité et clés](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7 : Choisir le processus d’approbation pour le support Microsoft

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| PA-7 | 16 | AC-2, AC-3, AC-4 |

Dans le cadre des scénarios de support où Microsoft a besoin d’accéder aux données client, Customer Lockbox fournit une capacité qui vous permet de consulter et d’approuver ou refuser explicitement chaque demande d’accès aux données client.

- [Présentation de Customer Lockbox](../fundamentals/customer-lockbox-overview.md)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Sécurité des applications et DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestion de la conformité de la sécurité](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Identité et clés](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

