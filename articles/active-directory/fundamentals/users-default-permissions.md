---
title: Aautorisations utilisateur par défaut - Azure Active Directory | Microsoft Docs
description: Apprenez-en plus sur les différentes autorisations d’utilisateur disponibles dans Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: lizross
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1a3204e70fdde162d953c9c2241e9d71877cf78
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428922"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Quelles sont les autorisations d’utilisateur par défaut dans Azure Active Directory ?
Dans Azure Active Directory (Azure AD), tous les utilisateurs bénéficient d’un jeu d’autorisations par défaut. L’accès d’un utilisateur se compose du type d’utilisateur, de ses [attributions de rôles](active-directory-users-assign-role-azure-portal.md) et de sa possession d’objets individuels. Cet article décrit ces autorisations par défaut et compare celles des utilisateurs membres et celles des utilisateurs invités. Les autorisations d’utilisateur par défaut ne peuvent être modifiées que dans les paramètres utilisateur dans Azure AD.

## <a name="member-and-guest-users"></a>Utilisateurs membres et utilisateurs invités
Le jeu d'autorisations par défaut reçu varie selon que l'utilisateur est membre natif du locataire (utilisateur membre) ou transféré depuis un autre répertoire en tant qu'invité de collaboration B2B (utilisateur invité). Pour plus d'informations sur l'ajout d'utilisateurs invités, consultez [Qu'est-ce que la collaboration B2B d'Azure AD](../b2b/what-is-b2b.md).
* Les utilisateurs membres peuvent inscrire des applications, gérer leurs numéro de téléphone mobile et photo de profil, changer leur mot de passe et inviter des invités B2B. En outre, les utilisateurs peuvent lire toutes les informations d’annuaire (à quelques exceptions près). 
* Les utilisateurs invités disposent d'autorisations d'annuaire limitées. Par exemple, les utilisateurs invités ne peuvent pas parcourir les informations du locataire au-delà de leurs propres informations de profil. Toutefois, un utilisateur invité peut récupérer des informations relatives à un autre utilisateur en fournissant le nom d’utilisateur principal ou un ID d’objet. Un utilisateur invité peut lire les propriétés des groupes auxquels il appartient, y compris l’appartenance à un groupe, indépendamment du paramètre **Les autorisations des utilisateurs invités sont limitées**. Un invité ne peut pas afficher d'informations sur d'autres objets locataires.

Les autorisations par défaut des invités sont restrictives par défaut. Les invités peuvent être ajoutés à un rôle d’administrateur, bénéficiant ainsi des autorisations de lecture et d’écriture complètes contenues dans le rôle. Il existe une restriction supplémentaire, à savoir la possibilité pour les invités d’inviter d’autres invités. Définir **Les invités peuvent inviter** sur **Non** empêche les invités d’inviter d’autres invités. Pour connaître la marche à suivre, consultez [Déléguer des invitations pour B2B Collaboration](../b2b/delegate-invitations.md). Pour accorder par défaut aux utilisateurs invités les mêmes autorisations qu’aux utilisateurs membres, définissez **Les autorisations d’utilisateurs invités sont limitées** sur **Non**. Ce paramètre accorde par défaut toutes les autorisations d’utilisateur membre aux utilisateurs invités, et permet également aux invités d’être ajoutés aux rôles d’administration.

## <a name="compare-member-and-guest-default-permissions"></a>Comparer les autorisations par défaut des invités et des membres

**Zone** | **Autorisations d’utilisateur membre** | **Autorisations d’utilisateur invité**
------------ | --------- | ----------
Utilisateurs et contacts | Lire toutes les propriétés publiques des utilisateurs et des contacts<br>Inviter des invités<br>Changer son propre mot de passe<br>Gérer son propre numéro de téléphone mobile<br>Gérer sa propre photo<br>Invalider ses propres jetons d’actualisation | Lire ses propres propriétés<br>Lire le nom d’affichage, l’e-mail, le nom de connexion, la photo, le nom d’utilisateur principal et les propriétés de type d’utilisateur des autres utilisateurs et contacts<br>Changer son propre mot de passe
Groupes | Créer des groupes de sécurité<br>Créer des groupes Office 365<br>Lire toutes les propriétés des groupes<br>Lire les appartenances aux groupes non masquées<br>Lire les appartenances aux groupes Office 365 masquées pour un groupe rejoint<br>Gérer les propriétés, l’appartenance et l’adhésion des groupes que possède l’utilisateur<br>Ajouter des invités aux groupes acquis<br>Gérer les paramètres d’appartenance dynamique<br>Supprimer des groupes acquis<br>Restaurer des groupes Office 365 acquis | Lire toutes les propriétés des groupes<br>Lire les appartenances aux groupes non masquées<br>Lire les appartenances aux groupes Office 365 masquées pour des groupes rejoints<br>Gérer des groupes acquis<br>Ajouter des invités aux groupes acquis (si autorisé)<br>Supprimer des groupes acquis<br>Restaurer des groupes Office 365 acquis<br>Lire les propriétés des groupes auxquels ils appartiennent, y compris l'appartenance.
APPLICATIONS | Inscrire (créer) une application<br>Lire les propriétés des applications inscrites et d’entreprise<br>Gérer les propriétés d’application, les affectations et les informations d’identification des applications acquises<br>Créer ou supprimer le mot de passe d’application pour un utilisateur<br>Supprimer des applications acquises<br>Restaurer des applications acquises | Lire les propriétés des applications inscrites et d’entreprise<br>Gérer les propriétés d’application, les affectations et les informations d’identification des applications acquises<br>Supprimer des applications acquises<br>Restaurer des applications acquises
Appareils | Lire toutes les propriétés des appareils<br>Gérer toutes les propriétés des appareils acquis<br> | | Aucune autorisation<br>Supprimer des appareils acquis<br>
Répertoire | Lire toutes les informations de l’entreprise<br>Lire tous les domaines<br>Lire tous les contrats de partenaire | Lire le nom d’affichage et les domaines vérifiés
Rôles et étendues | Lire tous les rôles d’administrateur et toutes les appartenances<br>Lire toutes les propriétés et l’appartenance des unités administratives | | Aucune autorisation 
Abonnements | Lire tous les abonnements<br>Activer le membre de plan de service | | Aucune autorisation
Stratégies | Lire toutes les propriétés des stratégies<br>Gérer toutes les propriétés d’une stratégie acquise | | Aucune autorisation

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Pour restreindre les autorisations par défaut des utilisateurs membres

Vous pouvez restreindre les autorisations par défaut des utilisateurs membres des manières suivantes.

Autorisation | Explication du paramètre
---------- | ------------
Possibilité de créer des groupes de sécurité | Définir cette option sur Non empêche les utilisateurs de créer des groupes de sécurité. Les administrateurs généraux et les administrateurs de comptes d’utilisateur peuvent toujours créer des groupes de sécurité. Pour connaître la marche à suivre, consultez [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](../users-groups-roles/groups-settings-cmdlets.md).
Possibilité de créer des groupes Office 365 | Définir cette option sur Non empêche les utilisateurs de créer des groupes Office 365. Définir cette option sur Certain(e)s permet à un ensemble d’utilisateurs spécifique de créer des groupes Office 365. Les administrateurs généraux et les administrateurs de comptes d’utilisateur peuvent toujours créer des groupes Office 365. Pour connaître la marche à suivre, consultez [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](../users-groups-roles/groups-settings-cmdlets.md).
Limiter l’accès au portail d’administration Azure AD | Définir cette option sur Non empêche les utilisateurs d’accéder à Azure Active Directory.
Possibilité de lire d’autres utilisateurs | Ce paramètre est uniquement disponible dans PowerShell. Définir ce paramètre sur $false empêche tous les utilisateurs non administrateurs de lire les informations utilisateur du répertoire. Cela n’empêche pas de lire les informations utilisateur dans d’autres services Microsoft comme Exchange Online. Ce paramètre est destiné à des circonstances particulières et le définir sur $false n’est pas recommandé.

## <a name="object-ownership"></a>Propriété des objets

### <a name="application-registration-owner-permissions"></a>Autorisations des propriétaires liées à l’inscription d’une application
Quand un utilisateur inscrit une application, il est automatiquement ajouté en tant que propriétaire de l’application. En tant que tel, il peut gérer les métadonnées de l’application, telles que le nom et les autorisations que demande l’application. Il peut également gérer la configuration spécifique du locataire de l’application, telle que les affectations d’utilisateurs et la configuration de l’authentification unique. Un propriétaire peut également ajouter ou supprimer des propriétaires. Contrairement aux administrateurs généraux, les propriétaires ne peuvent gérer que les applications qu’ils possèdent.

<!-- ### Enterprise application owner permissions

When a user adds a new enterprise application, they are automatically added as an owner for the tenant-specific configuration of the application. As an owner, they can manage the tenant-specific configuration of the application, such as the SSO configuration, provisioning, and user assignments. An owner can also add or remove other owners. Unlike Global Administrators, owners can manage only the applications they own. <!--To assign an enterprise application owner, see *Assigning Owners for an Application*.-->

### <a name="group-owner-permissions"></a>Autorisations de propriétaire de groupe

Quand un utilisateur crée un groupe, il est automatiquement ajouté en tant que propriétaire de ce groupe. En tant que tel, il peut gérer les propriétés du groupe telles que le nom, ainsi que gérer l’appartenance au groupe. Un propriétaire peut également ajouter ou supprimer des propriétaires. Contrairement aux administrateurs globaux et aux administrateurs de comptes d’utilisateur, les propriétaires ne peuvent gérer que les groupes qu’ils possèdent. Pour assigner un propriétaire de groupe, consultez [Gestion des propriétaires d’un groupe](active-directory-accessmanagement-managing-group-owners.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’attribution des rôles d’administrateur Azure AD, consultez [Assign a user to administrator roles in Azure Active Directory](active-directory-users-assign-role-azure-portal.md) (Attribution d’un utilisateur aux rôles d’administrateur dans Azure Active Directory).
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Pour plus d’informations sur l’association entre Azure Active Directory et votre abonnement Azure, consultez [Association des abonnements Azure avec Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gestion des utilisateurs](add-users-azure-active-directory.md)
