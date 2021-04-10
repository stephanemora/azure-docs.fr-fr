---
title: Documentation sur les rôles Administrateur dans les services Microsoft 365 – Azure AD | Microsoft Docs
description: Rechercher du contenu et des références d’API pour des rôles d’administrateur de services Microsoft 365 dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae5761c4a31662f447a107e6b5eb89bcf02f70c8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055533"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>Rôles pour les services Microsoft 365 dans Azure Active Directory

Tous les produits de Microsoft 365 peuvent être gérés avec des rôles d’administration dans Azure Active Directory (Azure AD). Certains produits fournissent également des rôles supplémentaires qui sont spécifiques de ce produit. Pour plus d’informations sur les rôles pris en charge par chaque produit, consultez le tableau ci-dessous. Pour obtenir des instructions sur la planification de la sécurité des rôles, consultez [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](security-planning.md).

## <a name="where-to-find-content"></a>Où trouver du contenu

Service Microsoft 365 | Contenu de rôle | Contenu d’API
---------------------- | ------------------ | -----------------
Rôles d’administrateur dans les plans commerciaux Office 365 et Microsoft 365 | [Rôles d’administrateur Microsoft 365](/office365/admin/add-users/about-admin-roles) | Non disponible
Azure Active Directory (Azure AD) et Azure AD Identity Protection| [Rôles d’administrateur Azure AD](permissions-reference.md) | [API Graph](/graph/api/overview)<br>[Récupérer des attributions de rôles](/graph/api/directoryrole-list)
Exchange Online| [Contrôle d’accès en fonction du rôle Exchange](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell pour Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[Récupérer des attributions de rôles](/powershell/module/exchange/role-based-access-control/get-rolegroup)
SharePoint Online | [Rôles d’administrateur Azure AD](permissions-reference.md)<br>Également [À propos du rôle d’administrateur SharePoint dans Microsoft 365](/sharepoint/sharepoint-admin-role) | [API Graph](/graph/api/overview)<br>[Récupérer des attributions de rôles](/graph/api/directoryrole-list)
Teams/Skype Entreprise | [Rôles d’administrateur Azure AD](permissions-reference.md) | [API Graph](/graph/api/overview)<br>[Récupérer des attributions de rôles](/graph/api/directoryrole-list)
Centre de sécurité et de conformité (Protection avancée contre les menaces Office 365, Exchange Online Protection, Information Protection) | [Rôles d’administration Office 365](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[Récupérer des attributions de rôles](/powershell/module/exchange/role-based-access-control/get-rolegroup)
Degré de sécurisation | [Rôles d’administrateur Azure AD](permissions-reference.md) | [API Graph](/graph/api/overview)<br>[Récupérer des attributions de rôles](/graph/api/directoryrole-list)
Compliance Manager | [Rôles Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Non disponible
Azure Information Protection | [Rôles d’administrateur Azure AD](permissions-reference.md) | [API Graph](/graph/api/overview)<br>[Récupérer des attributions de rôles](/graph/api/directoryrole-list)
Microsoft Cloud App Security | [Contrôle d’accès en fonction du rôle](/cloud-app-security/manage-admins) | [Informations de référence sur l'API](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Groupes de rôles Azure ATP](/azure-advanced-threat-protection/atp-role-groups) | Non disponible
Protection avancée contre les menaces Windows Defender | [Contrôle d’accès en fonction du rôle Windows Defender ATP](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Non disponible
Privileged Identity Management | [Rôles d’administrateur Azure AD](permissions-reference.md) | [API Graph](/graph/api/overview)<br>[Récupérer des attributions de rôles](/graph/api/directoryrole-list)
Intune | [Contrôle d’accès en fonction du rôle Intune](/intune/role-based-access-control) | [API Graph](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[Récupérer des attributions de rôles](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true)
Ordinateur de bureau managé | [Rôles d’administrateur Azure AD](permissions-reference.md) | [API Graph](/graph/api/overview)<br>[Récupérer des attributions de rôles](/graph/api/directoryrole-list)

## <a name="next-steps"></a>Étapes suivantes

* [Guide pratique pour attribuer ou supprimer des rôles d’administrateur Azure AD](manage-roles-portal.md)
* [Informations de référence sur les rôles d’administrateur Azure AD](permissions-reference.md)