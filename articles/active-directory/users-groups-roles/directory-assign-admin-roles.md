---
title: Attribution de rôles d’administrateur dans Azure Active Directory | Microsoft Docs
description: Le rôle de l’administrateur peut servir à ajouter des utilisateurs, attribuer des rôles d’administrateur, réinitialiser les mots de passe utilisateur, gérer les licences utilisateur et les domaines, etc. Un utilisateur à qui un rôle d’administrateur est affecté a les mêmes autorisations pour tous les services cloud auxquels votre organisation s’est abonnée.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/31/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 384afb36f2a63fcbf290fa96ed15db2a1f469f55
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337849"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Attribution de rôles d’administrateur dans Azure Active Directory

À l’aide d’Azure Active Directory (Azure AD), vous pouvez affecter des administrateurs distincts à différentes fonctions. Les administrateurs peuvent être désignés dans le portail Azure AD pour effectuer des tâches comme l’ajout ou le changement d’utilisateurs, l’attribution de rôles d’administrateur, la réinitialisation des mots de passe utilisateur, la gestion des licences utilisateur et la gestion des noms de domaine.

## <a name="details-about-the-global-administrator-role"></a>Informations sur le rôle d’administrateur général

L’administrateur général a accès à toutes les fonctionnalités d’administration. Par défaut, le rôle d’administrateur général de l’annuaire est affecté à la personne qui souscrit un abonnement Azure. Seuls les administrateurs généraux peuvent affecter d’autres rôles d’administrateur.

## <a name="assign-or-remove-administrator-roles"></a>Attribution ou suppression de rôles d’administrateur

Pour découvrir comment attribuer des rôles d’administration à un utilisateur dans Azure Active Directory, consultez l’article [Attribution de rôles d’administrateur à un utilisateur d’Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Rôles disponibles

Les rôles d’administrateur disponibles sont les suivants :

* **[Administrateur d’application](#application-administrator)** : les utilisateurs dotés de ce rôle peuvent créer et gérer tous les aspects des applications d’entreprise, des inscriptions d’applications et des paramètres de proxy d’application. Ce rôle permet également de donner son consentement pour des autorisations déléguées et des autorisations d’application, sauf pour Microsoft Graph et Azure AD Graph. Les membres de ce rôle ne sont pas ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

* **[Développeur d’applications](#application-developer)**: les utilisateurs dotés de ce rôle peuvent créer des inscriptions d’applications quand le paramètre « Les utilisateurs peuvent inscrire des applications » est défini sur Non. Ce rôle permet également aux membres de donner leur consentement pour eux-mêmes lorsque le paramètre « Les utilisateurs peuvent autoriser les applications à accéder aux données de l'entreprise en leur nom » est défini sur Non. Les membres de ce rôle sont ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

* **[Administrateur de facturation](#billing-administrator)** : effectue les achats, gère les abonnements ainsi que les tickets de support, et surveille l’état d’intégrité des services.

* **[Administrateur d’application cloud](#cloud-application-administrator)** : les utilisateurs dotés de ce rôle ont les mêmes autorisations que l’administrateur d’application, à l’exclusion de la capacité à gérer le proxy d’application. Ce rôle permet de créer et de gérer tous les aspects des applications d’entreprise et des inscriptions d’applications. Ce rôle permet également de donner son consentement pour des autorisations déléguées et des autorisations d’application, sauf pour Microsoft Graph et Azure AD Graph. Les membres de ce rôle ne sont pas ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

* **[Administrateur de conformité](#compliance-administrator)** : les utilisateurs dotés de ce rôle possèdent des autorisations de gestion dans le Centre de sécurité et de conformité Office 365 et dans le Centre d’administration Exchange. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrateur de l’accès conditionnel](#conditional-access-administrator)** : les utilisateurs dotés de ce rôle peuvent gérer les paramètres d’accès conditionnel Azure Active Directory.
  > [!NOTE]
  > Pour pouvoir déployer la stratégie d’accès conditionnel Exchange ActiveSync dans Azure, l’utilisateur doit également être administrateur général.
  
* **[Administrateurs d’appareils](#device-administrators)**  : ce rôle est disponible pour attribution uniquement en tant qu’administrateur local supplémentaire dans [Paramètres de l’appareil](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Les utilisateurs dotés de ce rôle deviennent des administrateurs de l'ordinateur local pour tous les appareils Windows 10 qui sont joints à Azure Active Directory. Ils ne peuvent pas gérer des objets appareil dans Azure Active Directory. 

* **[Lecteurs d’annuaire](#directory-readers)** : il s’agit d’un rôle hérité qui doit être affecté aux applications ne prenant pas en charge le [framework de consentement](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Vous ne devez pas l’affecter à tous les utilisateurs.

* **[Comptes de synchronisation d’annuaires](#directory-synchronization-accounts)** : ne pas utiliser. Ce rôle est automatiquement attribué au service Azure AD Connect et n’est pas prévu ni pris en charge pour une autre utilisation.

* **[Enregistreurs d’annuaire](#directory-writers)** : il s’agit d’un rôle hérité qui doit être affecté aux applications ne prenant pas en charge le [framework de consentement](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Vous ne devez pas l’affecter à tous les utilisateurs.

* **[Administrateur de service Dynamics 365/Administrateur de service CRM](#dynamics-365-service-administrator)** : les utilisateurs dotés de ce rôle ont des autorisations globales dans Microsoft Dynamics 365 Online, quand le service est présent. Ils peuvent également gérer les tickets de support et analyser l’intégrité du service. Pour plus d’informations, consultez la page [Utiliser le rôle d’administrateur de service pour gérer votre locataire](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Administrateur de services Exchange](#exchange-service-administrator)** : les utilisateurs dotés de ce rôle ont des autorisations globales dans Microsoft Exchange Online, quand le service est présent. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrateur général/Administrateur d’entreprise](#company-administrator)** : les utilisateurs dotés de ce rôle ont accès à toutes les fonctionnalités d’administration dans Azure Active Directory, ainsi qu’aux services qui utilisent des identités Azure Active Directory, comme Exchange Online, SharePoint Online et Skype Entreprise Online. La personne qui s’inscrit pour le locataire Azure Active Directory devient administrateur général. Seuls les administrateurs généraux peuvent affecter d’autres rôles d’administrateur. Une entreprise peut comprendre plusieurs administrateurs généraux. Les administrateurs généraux peuvent réinitialiser le mot de passe des utilisateurs et de tous les autres administrateurs.

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de l’entreprise ». Il est « Administrateur général » dans le [portail Azure](https://portal.azure.com).
  >
  >

* **[Inviteur](#guest-inviter)** : les utilisateurs dotés de ce rôle peuvent gérer les invitations d’utilisateurs invités Azure Active Directory B2B quand le paramètre utilisateur **Les membres peuvent inviter** est défini sur Non. Pour plus d’informations sur B2B Collaboration, consultez la page [À propos d’Azure AD B2B Collaboration](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Il n’inclut pas d’autres autorisations.

* **[Administrateur Information Protection](#information-protection-administrator)** : les utilisateurs dotés de ce rôle ont toutes les autorisations sur le service Azure Information Protection. Ce rôle permet de configurer les étiquettes pour la stratégie Azure Information Protection, de gérer les modèles de protection et d’activer la protection. Il n’octroie aucune autorisation dans Identity Protection Center, Privileged Identity Management, Monitor Office 365 Service Health ni dans le Centre de sécurité et conformité Office 365.

* **[Administrateur de services Intune](#intune-service-administrator)** : les utilisateurs dotés de ce rôle ont des autorisations globales dans Microsoft Intune Online, quand le service est présent. Ce rôle donne aussi la possibilité de gérer les utilisateurs et les appareils afin d’associer la stratégie, ainsi que de créer et de gérer des groupes. Pour plus d’informations, consultez la page [Contrôle d’accès en fonction du rôle (RBAC) avec Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control).

* **[Administrateur de licence](#license-administrator)** : les utilisateurs dotés de ce rôle peuvent ajouter, supprimer et mettre à jour des affectations de licence pour les utilisateurs, les groupes (à l’aide de la gestion des licences par groupe) et gérer l’emplacement d’utilisation pour les utilisateurs. Le rôle ne permet pas d’acheter ou de gérer des abonnements, de créer ou de gérer des groupes, ni de créer ou de gérer les utilisateurs au-delà de leur emplacement d’utilisation.

* **[Lecteur du Centre de messages](#message-center-reader)** : les utilisateurs dotés de ce rôle peuvent surveiller les notifications et les mises à jour d’avis d’intégrité dans le [Centre de messages Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) pour leur organisation sur les services configurés comme Exchange, Intune et Microsoft Teams. Les lecteurs du Centre de messages reçoivent par e-mail des résumés hebdomadaires des publications, mises à jour, et peuvent partager les messages publiés sur le Centre de messages dans Office 365. Dans Azure AD, les utilisateurs affectés à ce rôle disposeront d’un accès en lecture seule aux services Azure AD comme les utilisateurs et les groupes. 

* **[Prise en charge du partenaire de niveau 1](#partner-tier1-support)** : ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.

* **[Prise en charge du partenaire de niveau 2](#partner-tier2-support)** : ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.

* **[Administrateur de mots de passe/Administrateur du support technique](#helpdesk-administrator)** : les utilisateurs dotés de ce rôle peuvent changer les mots de passe, gérer les requêtes de service et surveiller l’état d’intégrité des services. Les administrateurs du support technique peuvent modifier uniquement les mots de passe des utilisateurs et des autres administrateurs du support technique. 

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur du support technique ». Il est appelé « Administrateur général » dans le [portail Azure](https://portal.azure.com/).
  >
  >
  
* **[Administrateur de service Power BI](#power-bi-service-administrator)** : les utilisateurs dotés de ce rôle ont des autorisations globales dans Microsoft Power BI, quand le service est présent. Ils peuvent également gérer les tickets de support et surveiller l’état d’intégrité des services. Pour plus d’informations, consultez la page [Présentation du rôle d’administrateur Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

* **[Administrateur de rôle privilégié](#privileged-role-administrator)** : les utilisateurs dotés de ce rôle peuvent gérer les attributions de rôles dans Azure Active Directory, ainsi que dans Azure AD Privileged Identity Management. En outre, ce rôle permet de gérer tous les aspects de Privileged Identity Management.

* **[Lecteur de rapports](#reports-reader)** : les utilisateurs dotés de ce rôle peuvent afficher les données des rapports d’utilisation et le tableau de bord des rapports dans le Centre d’administration Office 365, ainsi que le pack du contexte d’adoption dans Power BI. En outre, ce rôle donne accès aux rapports de connexion et aux activités dans Azure AD, ainsi qu’aux données renvoyées par l’API de création de rapports Microsoft Graph. Un utilisateur disposant du rôle Lecteur de rapports peut uniquement accéder au métriques d’utilisation et d’adoption pertinentes. Il n’a pas les autorisations administrateur permettant de configurer les paramètres ou d’accéder aux centres d’administration propres au produit comme Exchange. 

* **[Administrateur de sécurité](#security-administrator)** : les utilisateurs dotés de ce rôle ont toutes les autorisations en lecture seule du rôle Lecteur de sécurité, plus la possibilité de gérer la configuration des services liés à la sécurité : Azure Active Directory Identity Protection, Azure Information Protection, Privileged Identity Management, ainsi que le Centre de sécurité et conformité Office 365. Pour plus d’informations sur les autorisations Office 365, consultez [Autorisations dans le Centre de sécurité et conformité Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  | Dans | Peut |
  | --- | --- |
  | Identity Protection Center |<ul><li>Toutes les autorisations du rôle lecteur de sécurité.<li>En outre, possibilité d’effectuer toutes les opérations IPC à l’exception de la réinitialisation des mots de passe. |
  | Privileged Identity Management |<ul><li>Toutes les autorisations du rôle lecteur de sécurité.<li>**Ne peut pas** gérer les appartenances aux rôles Azure AD ou les paramètres. |
  | <p>Monitor Office 365 Service Health</p><p>Centre de sécurité et conformité Office 365 |<ul><li>Toutes les autorisations du rôle lecteur de sécurité.<li>Peut configurer tous les paramètres de la fonctionnalité de protection avancée contre les menaces (protection contre les virus et logiciels malveillants, configuration des URL malveillantes, suivi des ’URL, etc.). |
  
* **[Lecteur de sécurité](#security-reader)** : les utilisateurs dotés de ce rôle ont un accès global en lecture seule, notamment à toutes les informations dans Azure Active Directory, Identity Protection, Privileged Identity Management, ainsi que la possibilité de lire les rapports de connexion et les journaux d’audit Azure Active Directory. Le rôle accorde également l’autorisation en lecture seule dans le Centre de sécurité et conformité Office 365. Pour plus d’informations sur les autorisations Office 365, consultez [Autorisations dans le Centre de sécurité et conformité Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  | Dans | Peut |
  | --- | --- |
  | Identity Protection Center |Lire tous les rapports de sécurité et informations de paramètres pour les fonctionnalités de sécurité<ul><li>Anti-spam<li>Chiffrement<li>Prévention contre la perte de données<li>Anti-programme malveillant<li>Détection avancée des menaces<li>Anti-hameçonnage<li>Règles du flux de messagerie |
  | Privileged Identity Management |<p>Dispose d’un accès en lecture seule à toutes les informations présentées dans Azure AD PIM : stratégies et rapports pour les attributions de rôle Azure AD, révisions de sécurité et prochainement accès en lecture aux données et rapports de stratégie pour les scénarios en plus de l’attribution de rôle Azure AD.<p>**Ne peut pas** s’inscrire auprès d’AD PIM ou y apporter des modifications. Dans le portail PIM ou via PowerShell, un membre ayant ce rôle peut activer des rôles supplémentaires (par exemple, un administrateur général ou un administrateur de rôle privilégié), s’il est éligible. |
  | <p>Monitor Office 365 Service Health</p><p>Centre de sécurité et conformité Office 365</p> |<ul><li>Lire et gérer les alertes<li>Lire les stratégies de sécurité<li>Lire les informations sur les menaces, Cloud App Discovery et Mise en quarantaine dans Recherche et enquêtes<li>Lecture de tous les rapports |

* **[Administrateur du support technique du service](#service-support-administrator)** : les utilisateurs dotés de ce rôle peuvent ouvrir des requêtes de support auprès de Microsoft pour les services Azure et Office 365, ainsi que consulter le tableau de bord des services et le centre de messages dans le portail Azure et le portail d’administration Office 365. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrateur de services SharePoint](#sharepoint-service-administrator)** : les utilisateurs dotés de ce rôle ont des autorisations globales dans Microsoft SharePoint Online, quand le service est présent. Ils peuvent également gérer les tickets de support et surveiller l’état d’intégrité des services. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrateur Skype Entreprise/des services Lync](#lync-service-administrator)** : les utilisateurs dotés de ce rôle ont des autorisations globales dans Microsoft Skype Entreprise, quand le service est présent. Ils peuvent également gérer les attributs utilisateur propres à Skype dans Azure Active Directory. En outre, ce rôle permet de gérer les tickets de support et de surveiller l’état des services. Plus d’informations, consultez [À propos du rôle d’administrateur Skype Entreprise](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5).

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de services Lync ». Il est appelé « Administrateur Skype Entreprise » dans le [portail Azure](https://portal.azure.com/).
  >
  >

* **[Administrateur des comptes d’utilisateur](#user-account-administrator)** : les utilisateurs dotés de ce rôle peuvent créer et gérer tous les aspects des utilisateurs et des groupes. En outre, ce rôle inclut la possibilité de gérer les tickets de support et de surveiller l’état d’intégrité des services. Quelques restrictions s’appliquent. Par exemple, ce rôle n’autorise pas la suppression d’un administrateur général. Les administrateurs des comptes d’utilisateur peuvent uniquement modifier les mots de passe des utilisateurs, des administrateurs du support technique et des autres administrateurs des comptes d’utilisateur.

| Peut | Ne peut pas |
| --- | --- |
| <p>Afficher les informations sur la société et les utilisateurs</p><p>Gérer les tickets de support Office</p><p>Modifier les mots de passe des utilisateurs, des administrateurs du support technique et des autres administrateurs des comptes d’utilisateur uniquement</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur, avec des restrictions. Il lui est impossible de supprimer un administrateur général ou de créer d’autres administrateurs.</p> |<p>Effectuer des opérations de facturation et d’achat pour des produits Office</p><p>Gérer des domaines</p><p>Modifier les informations de l’entreprise</p><p>Déléguer des rôles administratifs à d’autres personnes</p><p>Utiliser la synchronisation de répertoires</p><p>Activer ou désactiver l’authentification multifacteur</p><p>Afficher les journaux d’audit</p> |

## <a name="deprecated-roles"></a>Rôles déconseillés

Les rôles suivants ne doivent pas être utilisés. Ils sont déconseillés et seront prochainement supprimés d’Azure AD.

* Administrateur de licences ad hoc
* Jonction d’appareils
* Gestionnaires d’appareils
* Utilisateurs d’appareils
* Créateur d’utilisateur vérifié par e-mail
* Administrateur de boîte aux lettres
* Jonction d’appareils d’espace de travail

## <a name="detailed-azure-active-directory-permissions"></a>Autorisations Azure Active Directory détaillées
Les tableaux suivants décrivent les autorisations spécifiques à chaque rôle dans Azure Active Directory. Certains rôles, comme le rôle Administrateur général, peuvent avoir des autorisations supplémentaires dans les services Microsoft en dehors d’Azure Active Directory.

### <a name="adhoc-license-administrator"></a>Administrateur de licences ad hoc
Peut créer et gérer tous les aspects des inscriptions d’applications et des applications d’entreprise.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/domains/default/read | Lisez des propriétés de base sur des domaines dans Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Lisez la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/default/read | Lisez des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Lisez la propriété groups.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Lisez la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Lisez la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Lisez la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/default/read | Lisez des propriétés de base sur des octrois d’autorisation OAuth2 dans Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/update | Mettez à jours des octrois d’autorisation OAuth2 dans Azure Active Directory. |
| microsoft.aad.directory/organization/default/read | Lisez des propriétés de base sur une organisation dans Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Lisez la propriété organization.trustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Lisez la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/default/read | Lisez des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Lisez la propriété users.directReports dans Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Lisez la propriété users.invitedBy dans Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Lisez la propriété users.invitedUsers dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Lisez la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Lisez la propriété users.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Lisez la propriété users.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Lisez la propriété users.ownedDevices dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Lisez la propriété users.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Lisez la propriété users.registeredDevices dans Azure Active Directory. |

### <a name="application-administrator"></a>Administrateur d’application
Peut créer et gérer tous les aspects des inscriptions d’applications et des applications d’entreprise.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/create | Créez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/default/update | Mettez à jour des propriétés de base sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Supprimez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Mettez à jour la propriété applications.owners dans Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Mettez à jour la propriété applications.policies dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Créez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Lisez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Mettez à jour des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Supprimez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/update | Mettez à jour la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Mettez à jour la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Créez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Mettez à jour des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Supprimez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Mettez à jour la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Mettez à jour la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Mettez à jour la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Lisez des rapports Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="application-developer"></a>Développeur d’applications
Peut créer des inscriptions d’applications indépendamment du paramètre �Les utilisateurs peuvent inscrire des applications�.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Créez des applications dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Créez des attributions de rôles d’applications dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Créez des octrois d’autorisation OAuth2 dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Créez des principaux de service dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |

### <a name="billing-administrator"></a>Administrateur de facturation
Peut effectuer des tâches de facturation courantes, comme la mise à jour des informations de paiement.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/organization/default/update | Mettez à jour des propriétés de base sur une organisation dans Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Mettez à jour la propriété organization.trustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gérez tous les aspects de la facturation Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="cloud-application-administrator"></a>Administrateur d'applications cloud
Peut créer et gérer tous les aspects des inscriptions d’applications et des applications d’entreprise, à l’exception du proxy d’application.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/create | Créez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/default/update | Mettez à jour des propriétés de base sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Supprimez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Mettez à jour la propriété applications.owners dans Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Mettez à jour la propriété applications.policies dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Créez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Mettez à jour des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Supprimez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/update | Mettez à jour la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Mettez à jour la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Mettez à jour la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Mettez à jour la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Créez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Mettez à jour des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Supprimez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Mettez à jour la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Lisez des rapports Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="company-administrator"></a>Administrateur d’entreprise
Peut gérer tous les aspects d’Azure AD et des services Microsoft qui utilisent des identités Azure AD.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Créez et supprimez des unités administratives (administrativeUnits), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Créez et supprimez des applications, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Créez et supprimez des attributions de rôles d’applications (appRoleAssignments), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Créez et supprimez des contacts, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Créez et supprimez des contrats, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Créez et supprimez des appareils, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Créez et supprimez des rôles d’annuaire (directoryRoles), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Créez et supprimez des modèles de rôles d’annuaire (DirectoryRoleTemplates), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Créez et supprimez des domaines, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Créez et supprimez des groupes, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Créez et supprimez des paramètres de groupes (groupSettings), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Créez et supprimez des modèles de paramètres de groupes (groupSettingTemplates), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Créez et supprimez une personnalisations de client de connexion (loginTenantBranding), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Créez et supprimez des octrois d’autorisation OAuth2 (oAuth2PermissionGrants), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Créez et supprimez des organisations, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Créez et supprimez des stratégies, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Créez et supprimez des attributions de rôles (roleAssignments), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Créez et supprimez des définitions de rôles (roleDefinitions), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Créez et supprimez des appartenances à un rôle étendu (scopedRoleMemberships), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Peut effectuer l’action de service Activateservice dans Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Peut effectuer l’action de service Disabledirectoryfeature dans Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Peut effectuer l’action de service Enabledirectoryfeature dans Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Peut effectuer l’action de service Getavailableextentionproperties dans Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Créez et supprimez des principaux de service (servicePrincipals), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Créez et supprimez des références SKU souscrites, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Créez et supprimez des utilisateurs, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Exécutez toutes les actions dans Azure AD Connect. |
| microsoft.aad.directorySync/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Lisez toutes les ressources dans microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.reports/allEntities/allTasks | Lisez et configurez des rapports Azure AD. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gérez tous les aspects d’Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gérez tous les aspects de la facturation Office 365. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gérez tous les aspects d’Office 365 Compliance Manager |
| microsoft.office365.exchange/allEntities/allTasks | Gérez tous les aspects d’Exchange Online. |
| microsoft.intune/allEntities/allTasks | Gérez tous les aspects d’Intune. |
| microsoft.office365.lockbox/allEntities/allTasks | Gérez tous les aspects d’Office 365 Customer Lockbox. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gérez tous les aspects de Power BI. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Gérez tous les aspects du Centre de protection Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gérez tous les aspects de Dynamics 365. |

### <a name="compliance-administrator"></a>Administrateur de conformité
Peut lire et gérer la configuration de la conformité et les rapports dans Azure AD et Office 365.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gérez tous les aspects d’Office 365 Compliance Manager |
| microsoft.office365.exchange/allEntities/allTasks | Gérez tous les aspects d’Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="conditional-access-administrator"></a>Administrateur de l’accès conditionnel
Peut gérer les fonctionnalités d’accès conditionnel.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/default/read | Lisez la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/default/update | Mettez à jour la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Lisez la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Mettez à jour la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Lisez la propriété policies.conditionalAccess dans Azure Active Directory. |

### <a name="crm-service-administrator"></a>Administrateur de services CRM
Peut gérer tous les aspects du produit Dynamics 365.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gérez tous les aspects de Dynamics 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="customer-lockbox-access-approver"></a>Approbateur d’accès à Customer LockBox
Peut approuver les demandes de support Microsoft pour accéder aux données organisationnelles du client.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.office365.lockbox/allEntities/allTasks | Gérez tous les aspects d’Office 365 Customer Lockbox. |

### <a name="device-administrators"></a>Administrateurs d’appareils
Les membres dotés de ce rôle sont ajoutés au groupe d’administrateurs locaux sur des appareils joints à AD Azure.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/groupSettings/default/read | Lisez des propriétés de base sur des paramètres de groupes dans Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/default/read | Lisez des propriétés de base sur des modèles de paramètres de groupes dans Azure Active Directory. |

### <a name="device-managers"></a>Gestionnaires d’appareils
Peut approuver les demandes de support Microsoft pour accéder aux données organisationnelles du client.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/devices/default/read | Lisez des propriétés de base sur des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/default/update | Mettez à jour des propriétés de base sur des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Lisez la propriété devices.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Lisez la propriété devices.registeredOwners dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Mettez à jour la propriété devices.registeredOwners dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Lisez la propriété devices.registeredUsers dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Mettez à jour la propriété devices.registeredUsers dans Azure Active Directory. |

### <a name="directory-readers"></a>Lecteurs de répertoires
Peut lire les informations d’annuaire de base. Pour l’octroi de l’accès aux applications.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/default/read | Lisez des propriétés de base sur des unités administratives dans Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Lisez la propriété administrativeUnits.members dans Azure Active Directory. |
| microsoft.aad.directory/applications/default/read | Lisez des propriétés de base sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Lisez la propriété applications.owners dans Azure Active Directory. |
| microsoft.aad.directory/contacts/default/read | Lisez des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Lisez la propriété contacts.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/contracts/default/read | Lisez des propriétés de base sur des contrats dans Azure Active Directory. |
| microsoft.aad.directory/devices/default/read | Lisez des propriétés de base sur des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Lisez la propriété devices.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Lisez la propriété devices.registeredOwners dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Lisez la propriété devices.registeredUsers dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/default/read | Lisez des propriétés de base sur des rôles d’annuaire dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Lisez la propriété directoryRoles.eligibleMembers dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Lisez la propriété directoryRoles.members dans Azure Active Directory. |
| microsoft.aad.directory/domains/default/read | Lisez des propriétés de base sur des domaines dans Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Lisez la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/default/read | Lisez des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Lisez la propriété groups.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Lisez la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Lisez la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Lisez la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/default/read | Lisez des propriétés de base sur des paramètres de groupes dans Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/default/read | Lisez des propriétés de base sur des modèles de paramètres de groupes dans Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/default/read | Lisez des propriétés de base sur des octrois d’autorisation OAuth2 dans Azure Active Directory. |
| microsoft.aad.directory/organization/default/read | Lisez des propriétés de base sur une organisation dans Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Lisez la propriété organization.trustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Lisez la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Lisez la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/read | Lisez des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Lisez la propriété servicePrincipals.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Lisez la propriété servicePrincipals.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Lisez la propriété servicePrincipals.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Lisez la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Lisez la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/default/read | Lisez des propriétés de base sur des références SKU souscrites dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Lisez la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/default/read | Lisez des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Lisez la propriété users.directReports dans Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Lisez la propriété users.invitedBy dans Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Lisez la propriété users.invitedUsers dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Lisez la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Lisez la propriété users.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Lisez la propriété users.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Lisez la propriété users.ownedDevices dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Lisez la propriété users.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Lisez la propriété users.registeredDevices dans Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Comptes de synchronisation d’annuaires
Utilisés uniquement par le service Azure AD Connect.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Mettez à jour la propriété organization.dirSync dans Azure Active Directory. |
| microsoft.aad.directory/policies/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/default/read | Lisez des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/default/update | Mettez à jour des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/owners/read | Lisez la propriété policies.owners dans Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Mettez à jour la propriété policies.owners dans Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Lisez la propriété policies.policiesAppliedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Lisez la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Mettez à jour la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Lisez la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Mettez à jour la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Créez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/read | Lisez des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Mettez à jour des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Lisez la propriété servicePrincipals.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Lisez la propriété servicePrincipals.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Lisez la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Mettez à jour la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Lisez la propriété servicePrincipals.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Lisez la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Exécutez toutes les actions dans Azure AD Connect. |

### <a name="directory-writers"></a>Enregistreurs de répertoire
Peut lire et écrire des informations d’annuaire de base. Pour l’octroi de l’accès aux applications.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Mettez à jour la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/default/update | Mettez à jour des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Mettez à jour la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Créez des paramètres de groupe dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/default/update | Mettez à jour des propriétés de base sur des paramètres de groupe dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Supprimez des paramètres de groupe dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/default/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Mettez à jour la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Mettez à jour la propriété users.userPrincipalName dans Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Administrateur de services Exchange
Peut gérer tous les aspects du produit Exchange.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.exchange/allEntities/allTasks | Gérez tous les aspects d’Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="guest"></a>Invité
Rôle par défaut des utilisateurs invités. Peut lire un ensemble limité d’informations d’annuaire.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/default/read | Lisez des propriétés de base sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Lisez la propriété applications.owners dans Azure Active Directory. |
| microsoft.aad.directory/domains/default/read | Lisez des propriétés de base sur des domaines dans Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Lisez la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/default/read | Lisez des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Lisez la propriété groups.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Lisez la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Lisez la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Lisez la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/organization/basicProfile/read | Lisez des informations de profil d’organisation de base dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Lisez la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Lisez la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/read | Lisez des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Lisez la propriété servicePrincipals.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/members/read | Lisez la propriété servicePrincipals.members dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Lisez la propriété servicePrincipals.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Lisez la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Lisez la propriété servicePrincipals.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Lisez la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/users/basicProfile/read | Lisez la propriété users.basicProfile dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Lisez la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/default/read | Lisez des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Lisez la propriété users.directReports dans Azure Active Directory. |
| microsoft.aad.directory/users/eligibleMemberOf/read | Lisez la propriété users.eligibleMemberOf dans Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Lisez la propriété users.invitedBy dans Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Lisez la propriété users.invitedUsers dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Lisez la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Lisez la propriété users.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Lisez la propriété users.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Lisez la propriété users.ownedDevices dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Lisez la propriété users.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Mettez à jour des mots de passe pour tous les utilisateurs dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.aad.directory/users/pendingMemberOf/read | Lisez la propriété users.pendingMemberOf dans Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Lisez la propriété users.registeredDevices dans Azure Active Directory. |
| microsoft.aad.directory/users/scopedAdministratorOf/read | Lisez la propriété users.scopedAdministratorOf dans Azure Active Directory. |

### <a name="guest-inviter"></a>Inviteur
Peut inviter des utilisateurs invités indépendamment du paramètre �Les membres peuvent inviter des invités�.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Lisez la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/default/read | Lisez des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Lisez la propriété users.directReports dans Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Lisez la propriété users.invitedBy dans Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Invitez des utilisateurs invités dans Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Lisez la propriété users.invitedUsers dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Lisez la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Lisez la propriété users.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Lisez la propriété users.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Lisez la propriété users.ownedDevices dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Lisez la propriété users.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Lisez la propriété users.registeredDevices dans Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Administrateur du support technique
Peut réinitialiser des mots de passe pour les utilisateurs non-administrateurs et les administrateurs du support technique.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Mettez à jour des mots de passe pour tous les utilisateurs dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="information-protection-administrator"></a>Administrateur Information Protection
Peut gérer tous les aspects du produit Azure Information Protection.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gérez tous les aspects d’Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="intune-service-administrator"></a>Administrateur de services Intune
Peut gérer tous les aspects du produit Intune.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/contacts/create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/default/update | Mettez à jour des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/devices/create | Créez des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/default/update | Mettez à jour des propriétés de base sur des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Supprimez des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Mettez à jour la propriété devices.registeredOwners dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Mettez à jour la propriété devices.registeredUsers dans Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Mettez à jour la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/default/update | Mettez à jour des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Lisez la propriété groups.hiddenMembers dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Mettez à jour la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/default/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Mettez à jour la propriété users.manager dans Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.intune/allEntities/allTasks | Gérez tous les aspects d’Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="license-administrator"></a>Administrateur de licence
Peut gérer les licences de produit pour les utilisateurs et les groupes.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Mettez à jour la propriété users.usageLocation dans Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="lync-service-administrator"></a>Administrateur de services Lync
Peut gérer tous les aspects du produit Skype Entreprise.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="message-center-reader"></a>Lecteur du Centre de messages
Peut lire les messages et les mises à jour de son organisation dans le Centre de messages Office 365 uniquement. 

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.accessmessagecenter/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans le Centre de messages. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |

### <a name="partner-tier1-support"></a>Prise en charge de niveau 1 de partenaire
Ne pas utiliser - non destiné à une utilisation générale.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/contacts/create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/default/update | Mettez à jour des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/default/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/delete | Supprimez des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Mettez à jour la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Mettez à jour des mots de passe pour tous les utilisateurs dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.aad.directory/users/restore | Restaurez des utilisateurs (Users) supprimés dans Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Mettez à jour la propriété users.userPrincipalName dans Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="partner-tier2-support"></a>Prise en charge de niveau 2 de partenaire
Ne pas utiliser - non destiné à une utilisation générale.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/contacts/create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/default/update | Mettez à jour des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Créez et supprimez des domaines, et lisez et mettez à jour des propriétés standard dans Azure Active Directory. |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/organization/default/update | Mettez à jour des propriétés de base sur une organisation dans Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Mettez à jour la propriété organization.trustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/default/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/delete | Supprimez des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Mettez à jour la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Mettez à jour des mots de passe pour tous les utilisateurs dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.aad.directory/users/restore | Restaurez des utilisateurs (Users) supprimés dans Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Mettez à jour la propriété users.userPrincipalName dans Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="power-bi-service-administrator"></a>Administrateur du service Power BI
Peut gérer tous les aspects du produit Power BI.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gérez tous les aspects de Power BI. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="privileged-role-administrator"></a>Administrateur de rôle privilégié
Peut gérer les attributions de rôles dans Azure AD.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Mettez à jour des rôles d’annuaire dans Azure Active Directory. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.aad.privilegedIdentityManagement. |

### <a name="reports-reader"></a>Lecteur de rapports
Peut lire les rapports d’audit et de connexion.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.reports/allEntities/read | Lisez des rapports Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.usageReports/allEntities/read | Lisez des rapports d’utilisation Office 365. |

### <a name="security-administrator"></a>Security Administrator
Peut lire des rapports et des informations de sécurité.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Mettez à jour la propriété applications.policies dans Azure Active Directory. |
| microsoft.aad.directory/policies/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/default/update | Mettez à jour des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Mettez à jour la propriété policies.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Lisez toutes les ressources dans microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Mettez à jour toutes les ressources dans microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Lisez toutes les ressources dans microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.protectionCenter/allEntities/read | Lisez tous les aspects du Centre de protection Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Mettez à jour toutes les ressources dans microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="security-reader"></a>Lecteur de sécurité
Peut lire des rapports et des informations de sécurité dans Azure AD et Office 365.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.identityProtection/allEntities/read | Lisez toutes les ressources dans microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Lisez toutes les ressources dans microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.protectionCenter/allEntities/read | Lisez tous les aspects du Centre de protection Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="service-support-administrator"></a>Administrateur de support de service
Peut lire des informations sur l’intégrité du service et gérer les tickets de support.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="sharepoint-service-administrator"></a>Administrateur de services SharePoint
Peut gérer tous les aspects du service SharePoint.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="user-account-administrator"></a>Administrateur de compte utilisateur
Peut gérer tous les aspects des utilisateurs et des groupes.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Créez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Supprimez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Mettez à jour des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/default/update | Mettez à jour des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Mettez à jour la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/default/update | Mettez à jour des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Lisez la propriété groups.hiddenMembers dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Mettez à jour la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/create | Créez des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/default/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/delete | Supprimez des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Mettez à jour la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Mettez à jour des mots de passe pour tous les utilisateurs dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.aad.directory/users/restore | Restaurez des utilisateurs (Users) supprimés dans Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Mettez à jour la propriété users.userPrincipalName dans Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="user"></a>Utilisateur
Rôle par défaut pour les utilisateurs membres. Peut tout lire et écrire un ensemble limité d’informations d’annuaire.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Lecteur d’annuaire.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Créez des applications dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/default/read | Lisez des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/create | Créez des octrois d’autorisation OAuth2 dans Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/delete | Supprimez des octrois d'autorisation OAuth2 dans Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/update | Mettez à jours des octrois d’autorisation OAuth2 dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Créez des principaux de service dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/users/activateServicePlan | Activez le plan de service d’utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Invitez des utilisateurs invités dans Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Supprimez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/restore | Restaurez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/default/update | Mettez à jour des propriétés de base sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Mettez à jour la propriété applications.owners dans Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Mettez à jour la propriété applications.policies dans Azure Active Directory. |
| microsoft.aad.directory/devices/disable | Désactivez des appareils dans Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Mettez à jour la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/default/update | Mettez à jour des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/dynamicMembershipRule/update | Mettez à jour la propriété groups.dynamicMembershipRule dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Mettez à jour la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/default/update | Mettez à jour des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Mettez à jour la propriété policies.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Mettez à jour la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Mettez à jour la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Mettez à jour des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Supprimez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Mettez à jour la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/users/changePassword | Modifiez des mots de passe pour tous les utilisateurs dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/basicProfile/update | Mettez à jour la propriété users.basicProfile dans Azure Active Directory. |
| microsoft.aad.directory/users/mobile/update | Mettez à jour la propriété users.mobile dans Azure Active Directory. |
| microsoft.aad.directory/users/searchableDeviceKey/update | Mettez à jour la propriété users.searchableDeviceKey dans Azure Active Directory. |

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la modification des administrateurs d’un abonnement Azure, consultez [Ajout ou modification de rôles d’administrateur Azure](../../billing/billing-add-change-azure-subscription-administrator.md)
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Pour plus d’informations sur la façon dont le service Azure Active Directory est lié à votre abonnement Azure, consultez [Association des abonnements Azure avec Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
