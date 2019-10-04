---
title: Configurer la gestion de groupes en libre-service - Azure Active Directory | Microsoft Docs
description: Créer et gérer des groupes de sécurité ou Office 365 dans Azure Active Directory et demander des appartenances aux groupes de sécurité ou Office 365
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b52604d4ad20fed83c4649f046722ed45e766c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097698"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Configurer la gestion de groupes en libre-service dans Azure Active Directory 

Vous pouvez autoriser les utilisateurs à créer et gérer leurs propres groupes de sécurité ou Office 365 dans Azure Active Directory (Azure AD). Le propriétaire du groupe peut approuver ou refuser des demandes d’appartenance et peut déléguer le contrôle de l’appartenance au groupe. Les fonctionnalités de gestion de groupes en libre-service ne sont pas disponibles pour les groupes de sécurité activés pour la messagerie électronique ou les listes de distribution.

## <a name="self-service-group-membership-defaults"></a>Valeurs par défaut de l’appartenance de groupes en libre-service

Lorsque des groupes de sécurité sont créés dans le Portail Azure ou en utilisant Azure AD PowerShell, seuls les propriétaires du groupe peuvent mettre à jour leur appartenance. Les groupes de sécurité créés dans le [panneau Accès](https://account.activedirectory.windowsazure.com/r#/joinGroups) et tous les groupes Office 365 sont disponibles pour tous les utilisateurs, qu’ils soient approuvés par le propriétaire ou approuvés automatiquement. Dans le volet Accès, vous pouvez modifier les options d’appartenance lorsque vous créez le groupe.

Groupes créés dans | Comportement par défaut des groupes de sécurité | Comportement par défaut des groupes Office 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Seuls les propriétaires peuvent ajouter des membres<br>Visible mais non disponible pour se joindre au panneau Accès | Ouvert pour tous les utilisateurs
[Portail Azure](https://portal.azure.com) | Seuls les propriétaires peuvent ajouter des membres<br>Visible mais non disponible pour se joindre au panneau Accès<br>Propriétaire n’est pas affecté automatiquement lors de la création de groupe | Ouvert pour tous les utilisateurs
[Volet d’accès](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Ouvert pour tous les utilisateurs<br>Les options d’appartenance peuvent être modifiées lorsque le groupe est créé. | Ouvert pour tous les utilisateurs<br>Les options d’appartenance peuvent être modifiées lorsque le groupe est créé.

## <a name="self-service-group-management-scenarios"></a>Scénarios de gestion de groupes en libre-service

* **Gestion de groupes déléguée** : prenons l’exemple d’un administrateur qui gère l’accès à une application SaaS utilisée dans son entreprise. La gestion de ces droits d’accès devenant fastidieuse, cet administrateur demande au propriétaire de l’entreprise d’ajouter un nouveau groupe. L’administrateur accorde l’accès à l’application au nouveau groupe et ajoute au groupe toutes les personnes accédant déjà à l’application. Le propriétaire de l’entreprise peut ainsi ajouter d’autres utilisateurs, qui sont automatiquement approvisionnés dans l’application. Il n’a pas besoin d’attendre que l’administrateur gère l’accès des utilisateurs. Si l’administrateur accorde la même autorisation à un responsable d’un autre groupe de l’entreprise, cette personne peut également gérer l’accès de ses propres membres de groupe. Le propriétaire de l’entreprise ne peut pas visualiser ni gérer les appartenances de groupe, et inversement. L’administrateur a toujours la possibilité de voir l’ensemble des utilisateurs ayant accès à l’application et, si nécessaire, de bloquer les droits d’accès.
* **Gestion de groupes en libre-service** Prenons l’exemple de deux utilisateurs disposant tous deux d’un site SharePoint Online. Ils les gèrent indépendamment. Cependant, ils veulent les rendre accessibles à chacune des deux équipes. Pour cela, ils peuvent créer un groupe dans Azure AD, puis, dans SharePoint Online, chacun d’eux choisit ce même groupe pour lui donner accès aux deux sites. Ainsi, quand une personne souhaite obtenir un accès, elle en effectue la demande dans le volet d’accès, et dès que sa demande a été approuvée, elle obtient automatiquement un accès aux deux sites SharePoint Online. Par la suite, l’un des deux utilisateurs décide que toutes les personnes ayant accès au site doivent également pouvoir accéder à une application SaaS. L’administrateur de l’application SaaS peut ajouter des droits d’accès à l’application pour le site SharePoint Online. Les demandes qu’il approuvera par la suite donneront ainsi accès non seulement aux deux sites SharePoint Online, mais également à cette application SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Mettre un groupe à disposition en libre-service pour un utilisateur

1. Connectez-vous au [centre d’administration Azure AD](https://aad.portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Utilisateurs et groupes**, puis **Paramètres de groupe**.
3. Définissez **Gestion de groupes en libre-service activée** sur **Oui**.
4. Définissez **Les utilisateurs peuvent créer des groupes de sécurité** ou **Les utilisateurs peuvent créer des groupes Office 365** sur **Oui**.
   * Une fois ces paramètres activés, tous les utilisateurs présents dans votre répertoire sont autorisés à créer des groupes de sécurité et à ajouter des membres à ces groupes. Ces nouveaux groupes apparaissent également dans le volet d’accès de tous les autres utilisateurs. Si le paramètre de stratégie du groupe l’autorise, d’autres utilisateurs peuvent créer des demandes d’adhésion à ces groupes. 
   * Quand ces paramètres sont désactivés, les utilisateurs ne peuvent pas créer de groupes, ni modifier les groupes existants dont ils sont propriétaires. Cependant, ils peuvent toujours gérer les appartenances de ces groupes et approuver les demandes d’adhésion d’autres utilisateurs à leurs groupes.

Vous pouvez également utiliser **Utilisateurs pouvant gérer les groupes de sécurité** et **Utilisateurs pouvant gérer les groupes Office 365** pour obtenir un contrôle d’accès plus fin de la gestion de groupes en libre-service pour vos utilisateurs. Quand l’option **Les utilisateurs peuvent créer des groupes** est activée, tous les utilisateurs présents dans votre locataire sont autorisés à créer des groupes et à ajouter des membres à ces groupes. Vous ne pouvez pas spécifier les personnes qui peuvent créer leurs propres groupes. Vous ne pouvez spécifier une personne du groupe que si vous la promouvez propriétaire celui-ci.

En définissant **Utilisateurs pouvant utiliser le libre-service pour les groupes de sécurité** et **Utilisateurs pouvant gérer les groupes Office 365** sur **Oui**, vous autorisez tous les utilisateurs de votre locataire à créer des groupes.

Vous pouvez également utiliser **Groupe pouvant gérer les groupes de sécurité** ou **Groupe pouvant gérer les groupes Office 365** pour spécifier un groupe unique dont les membres peuvent utiliser le libre-service.

## <a name="next-steps"></a>Étapes suivantes

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gérer l’accès aux ressources avec les groupes Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](groups-settings-cmdlets.md)
* [Gestion des applications dans Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Qu’est-ce qu’Azure Active Directory ?](../fundamentals/active-directory-whatis.md)
* [Intégrer des répertoires locaux à Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
