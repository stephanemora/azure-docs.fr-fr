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
ms.date: 07/25/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 5d6254efbb6051bf4fcd01abd4fbf858b0211319
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399938"
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

* **[Développeur d’applications](#application-developer)** : les utilisateurs dotés de ce rôle peuvent créer des inscriptions d’applications quand le paramètre « Les utilisateurs peuvent inscrire des applications » est défini sur Non. Ce rôle permet également aux membres de donner leur consentement pour eux-mêmes lorsque le paramètre « Les utilisateurs peuvent autoriser les applications à accéder aux données de l'entreprise en leur nom » est défini sur Non. Les membres de ce rôle sont ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

* **[Administrateur de facturation](#billing-administrator)** : effectue les achats, gère les abonnements ainsi que les tickets de support, et surveille l’état d’intégrité des services.

* **[Administrateur d’application cloud](#cloud-application-administrator)** : les utilisateurs dotés de ce rôle ont les mêmes autorisations que l’administrateur d’application, hormis la capacité à gérer le proxy d’application. Ce rôle permet de créer et de gérer tous les aspects des applications d’entreprise et des inscriptions d’applications. Ce rôle permet également de donner son consentement pour des autorisations déléguées et des autorisations d’application, sauf pour Microsoft Graph et Azure AD Graph. Les membres de ce rôle ne sont pas ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

* **[Administrateur de conformité](#compliance-administrator)** : les utilisateurs dotés de ce rôle possèdent des autorisations de gestion dans le Centre de sécurité et de conformité Office 365 et dans le Centre d’administration Exchange. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrateur de l’accès conditionnel](#conditional-access-administrator)** : les utilisateurs dotés de ce rôle peuvent gérer les paramètres d’accès conditionnel Azure Active Directory.
  > [!NOTE]
  > Pour pouvoir déployer la stratégie d’accès conditionnel Exchange ActiveSync dans Azure, l’utilisateur doit également être administrateur général.
  
* **[Administrateurs d’appareil](#device-administrators)** : les utilisateurs dotés de ce rôle deviennent administrateurs d’ordinateur local sur tous les appareils Windows 10 qui sont joints à Azure Active Directory. Ils ne peuvent pas gérer des objets d’appareil dans Azure Active Directory.

* **[Lecteurs d’annuaire](#directory-readers)** : il s’agit d’un rôle hérité qui doit être affecté aux applications ne prenant pas en charge le [framework de consentement](../develop/active-directory-integrating-applications.md). Vous ne devez pas l’affecter à tous les utilisateurs.

* **[Comptes de synchronisation d’annuaires](#directory-synchronization-accounts)** : ne pas utiliser. Ce rôle est automatiquement attribué au service Azure AD Connect et n’est pas prévu ni pris en charge pour une autre utilisation.

* **[Enregistreurs d’annuaire](#directory-writers)** : il s’agit d’un rôle hérité qui doit être affecté aux applications ne prenant pas en charge le [framework de consentement](../develop/active-directory-integrating-applications.md). Vous ne devez pas l’affecter à tous les utilisateurs.

* **[Administrateur de service Dynamics 365/Administrateur de service CRM](#dynamics-365-service-administrator)** : les utilisateurs dotés de ce rôle ont des autorisations globales dans Microsoft Dynamics 365 Online, quand le service est présent. Ils peuvent également gérer les tickets de support et analyser l’intégrité du service. Pour plus d’informations, consultez la page [Utiliser le rôle d’administrateur de service pour gérer votre locataire](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Administrateur de services Exchange](#exchange-service-administrator)** : les utilisateurs dotés de ce rôle ont des autorisations globales dans Microsoft Exchange Online, quand le service est présent. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrateur général/Administrateur d’entreprise](#company-administrator)** : les utilisateurs dotés de ce rôle ont accès à toutes les fonctionnalités d’administration dans Azure Active Directory, ainsi qu’aux services qui utilisent des identités Azure Active Directory, comme Exchange Online, SharePoint Online et Skype Entreprise Online. La personne qui s’inscrit pour le locataire Azure Active Directory devient administrateur général. Seuls les administrateurs généraux peuvent affecter d’autres rôles d’administrateur. Une entreprise peut comprendre plusieurs administrateurs généraux. Les administrateurs généraux peuvent réinitialiser le mot de passe des utilisateurs et de tous les autres administrateurs.

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de l’entreprise ». Il est « Administrateur général » dans le [portail Azure](https://portal.azure.com).
  >
  >

* **[Inviteur](#guest-inviter)** : les utilisateurs dotés de ce rôle peuvent gérer les invitations d’utilisateurs invités Azure Active Directory B2B quand le paramètre utilisateur **Les membres peuvent inviter** est défini sur Non. Pour plus d’informations sur B2B Collaboration, consultez la page [À propos d’Azure AD B2B Collaboration](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Il n’inclut pas d’autres autorisations.

* **[Administrateur Information Protection](#information-protection-administrator)** : les utilisateurs dotés de ce rôle ont toutes les autorisations sur le service Azure Information Protection. Ce rôle permet de configurer les étiquettes pour la stratégie Azure Information Protection, de gérer les modèles de protection et d’activer la protection. Il n’octroie aucune autorisation dans Identity Protection Center, Privileged Identity Management, Monitor Office 365 Service Health ni dans le Centre de sécurité et conformité Office 365.

* **[Administrateur de services Intune](#intune-service-administrator)** : les utilisateurs dotés de ce rôle ont des autorisations globales dans Microsoft Intune Online, quand le service est présent. Ce rôle donne aussi la possibilité de gérer les utilisateurs et les appareils afin d’associer la stratégie, ainsi que de créer et de gérer des groupes. Pour plus d’informations, consultez la page [Contrôle d’accès en fonction du rôle (RBAC) avec Microsoft Intune](https://docs.microsoft.com/en-us/intune/role-based-access-control).

* **[Lecteur du Centre de messages](#message-center-reader)** : les utilisateurs dotés de ce rôle peuvent surveiller les notifications et les mises à jour d’avis d’intégrité dans le [Centre de messages Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) pour leur organisation sur les services configurés comme Exchange, Intune et Microsoft Teams. Les lecteurs du Centre de messages reçoivent par e-mail des résumés hebdomadaires des publications, mises à jour, et peuvent partager les messages publiés sur le Centre de messages dans Office 365. Dans Azure AD, les utilisateurs affectés à ce rôle disposeront d’un accès en lecture seule aux services Azure AD comme les utilisateurs et les groupes. 

* **[Prise en charge du partenaire de niveau 1](#partner-tier1-support)** : ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.

* **[Prise en charge du partenaire de niveau 2](#partner-tier2-support)** : ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.

* **[Administrateur de mots de passe/Administrateur du support technique](#helpdesk-administrator)** : les utilisateurs dotés de ce rôle peuvent changer les mots de passe, gérer les demandes de service et surveiller l’état d’intégrité des services. Les administrateurs du support technique peuvent modifier uniquement les mots de passe des utilisateurs et des autres administrateurs du support technique. 

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur du support technique ». Il est appelé « Administrateur général » dans le [portail Azure](https://portal.azure.com/).
  >
  >
  
* **[Administrateur de service Power BI](#power-bi-service-administrator)** : les utilisateurs dotés de ce rôle ont des autorisations globales dans Microsoft Power BI, quand le service est présent. Ils peuvent également gérer les tickets de support et surveiller l’état d’intégrité des services. Pour plus d’informations, consultez la page [Présentation du rôle d’administrateur Power BI](https://docs.microsoft.com/en-us/power-bi/service-admin-role).

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

* **[Administrateur Skype Entreprise/des services Lync](#lync-service-administrator)** : les utilisateurs dotés de ce rôle ont des autorisations globales dans Microsoft Skype Entreprise, quand le service est présent. Ils peuvent également gérer les attributs utilisateur propres à Skype dans Azure Active Directory. En outre, ce rôle permet de gérer les tickets de support et de surveiller l’état des services. Plus d’informations, consultez la page [À propos du rôle d’administrateur Skype Entreprise](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5).

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


### <a name="to-add-a-colleague-as-a-global-administrator"></a>Pour ajouter un collègue en tant qu’administrateur général

1. Connectez-vous au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) en utilisant un compte d’administrateur général ou d’administrateur avec un rôle privilégié pour l’annuaire du locataire.

   ![Ouvrez le Centre d’administration Azure Active Directory](./media/directory-assign-admin-roles/active-directory-admin-center.png)

2. Sélectionnez **Utilisateurs**.

3. Recherchez l’utilisateur que vous souhaitez désigner comme administrateur général et ouvrez le panneau de cet utilisateur.

4. Dans le panneau de l’utilisateur, sélectionnez **Rôle Directory**.
 
5. Dans le panneau du rôle Directory, sélectionnez le rôle **Administrateur général** et enregistrez.

## <a name="detailed-azure-active-directory-permissions"></a>Autorisations Azure Active Directory détaillées
Les tableaux suivants décrivent les autorisations spécifiques à chaque rôle dans Azure Active Directory. Certains rôles, comme le rôle Administrateur général, peuvent avoir des autorisations supplémentaires dans les services Microsoft en dehors d’Azure Active Directory.


### <a name="application-administrator"></a>Administrateur d’application
Peut créer et gérer tous les aspects des inscriptions d’applications et des applications d’entreprise.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Créez des applications dans Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Supprimez des applications dans Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Mettez à jour des propriétés standard sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Mettez à jour la propriété Applications.DefaultPolicy dans Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Mettez à jour la propriété Applications.Owners dans Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Créez des attributions de rôles d’applications (AppRoleAssignments) dans Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Supprimez des attributions de rôles d’applications (AppRoleAssignments) dans Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Mettez à jour des propriétés standard sur des attributions de rôles d’applications (AppRoleAssignments) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Créez des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Supprimez des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Mettez à jour des propriétés standard sur des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Mettez à jour la propriété Policies.Owners dans Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Pour le compte de tous les utilisateurs, peut accepter toutes les ressources, sauf Azure Active Directory (Azure AD Graph et Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | Créez des principaux de service (ServicePrincipals) dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Supprimez des principaux de service (ServicePrincipals) dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Mettez à jour des propriétés standard sur des principaux de service (ServicePrincipals) dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Mettez à jour la propriété ServicePrincipals.AppRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Mettez à jour la propriété ServicePrincipals.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Mettez à jour la propriété ServicePrincipals.DefaultPolicy dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Mettez à jour la propriété ServicePrincipals.Owners dans Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gérez des licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Lisez des rapports Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |

### <a name="application-developer"></a>Développeur d’applications
Peut créer des inscriptions d’applications indépendamment du paramètre **Les utilisateurs peuvent inscrire des applications**.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/Application/CreateAsOwner | Créez des applications dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/AppRoleAssignment/CreateAsOwner | Créez des attributions de rôles d’applications (AppRoleAssignments) dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/OAuth2PermissionGrant/CreateAsOwner | Créez des octrois d’autorisation OAuth2 (OAuth2PermissionGrants) dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/ServicePrincipal/CreateAsOwner | Créez des principaux de service (ServicePrincipals) dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |

### <a name="billing-administrator"></a>Administrateur de facturation
Peut effectuer des tâches de facturation courantes, comme la mise à jour des informations de paiement.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Mettez à jour des propriétés standard sur des organisations dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Mettez à jour la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.billing/AllEntities/AllActions | Gérez tous les aspects de la facturation Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |

### <a name="cloud-application-administrator"></a>Administrateur d'applications cloud
Peut créer et gérer tous les aspects des inscriptions d’applications et des applications d’entreprise, à l’exception du proxy d’application.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Créez des applications dans Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Supprimez des applications dans Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Mettez à jour des propriétés standard sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Mettez à jour la propriété Applications.DefaultPolicy dans Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Mettez à jour la propriété Applications.Owners dans Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Créez des attributions de rôles d’applications (AppRoleAssignments) dans Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Supprimez des attributions de rôles d’applications (AppRoleAssignments) dans Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Mettez à jour des propriétés standard sur des attributions de rôles d’applications (AppRoleAssignments) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Créez des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Supprimez des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Mettez à jour des propriétés standard sur des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Mettez à jour la propriété Policies.Owners dans Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Pour le compte de tous les utilisateurs, peut accepter toutes les ressources, sauf Azure Active Directory (Azure AD Graph et Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | Créez des principaux de service (ServicePrincipals) dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Supprimez des principaux de service (ServicePrincipals) dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Mettez à jour des propriétés standard sur des principaux de service (ServicePrincipals) dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Mettez à jour la propriété ServicePrincipals.AppRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Mettez à jour la propriété ServicePrincipals.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Mettez à jour la propriété ServicePrincipals.DefaultPolicy dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Mettez à jour la propriété ServicePrincipals.Owners dans Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gérez des licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Lisez des rapports Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |

### <a name="company-administrator"></a>Administrateur d’entreprise
Peut gérer tous les aspects d’Azure AD et des services Microsoft qui utilisent des identités Azure AD. Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de l’entreprise ». Il est « Administrateur général » dans le [portail Azure](https://portal.azure.com).

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/AllActions/AllProperties | Créez et supprimez des unités administratives (AdministrativeUnits), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/Application/AllActions/AllProperties | Créez et supprimez des applications, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/AllActions/AllProperties | Créez et supprimez des attributions de rôles d’applications (AppRoleAssignments), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/AllActions/AllProperties | Créez et supprimez des espaces de collaboration (CollaborationSpaces), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/Contact/AllActions/AllProperties | Créez et supprimez des contacts, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/Device/AllActions/AllProperties | Créez et supprimez des appareils (Devices), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/AllActions/AllProperties | Créez et supprimez des rôles d’annuaire (DirectoryRoles), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/DirectoryRoleTemplate/AllActions/AllProperties | Créez et supprimez des modèles de rôles d’annuaire (DirectoryRoleTemplates), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/AllActions/AllProperties | Créez et supprimez des paramètres d’annuaire (DirectorySettings), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/AllActions/AllProperties | Créez et supprimez des modèles de paramètre d’annuaire (DirectorySettingTemplates), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions/AllProperties | Créez et supprimez des domaines, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/Group/AllActions/AllProperties | Créez et supprimez des groupes, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/LoginTenantBranding/AllActions/AllProperties | Créez et supprimez des personnalisations de locataires de connexion (LoginTenantBrandings), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/AllActions/AllProperties | Créez et supprimez des octrois d’autorisation OAuth2 (OAuth2PermissionGrants), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/Policy/AllActions/AllProperties | Créez et supprimez des stratégies (Policies), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllWithDirectory | Pour le compte de tous les utilisateurs, peut accepter toutes les ressources, notamment Azure Active Directory (Azure AD Graph et Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/AllActions/AllProperties | Créez et supprimez des principaux de service (ServicePrincipals), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/Organization/AllActions/AllProperties | Créez et supprimez des organisations (Organizations), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/User/AllActions/AllProperties | Créez et supprimez des utilisateurs (Users), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.aadconnect/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.aad.aadconnect. |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.billing/AllEntities/AllActions | Gérez tous les aspects de la facturation Office 365. |
| microsoft.aad.compliance/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans le Centre de conformité. |
| microsoft.aad.directorysync/AllEntities/AllActions | Exécutez toutes les actions dans Azure AD Connect. |
| microsoft.aad.lockbox/AllEntities/AllActions | Gérez tous les aspects du service Lockbox. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Gérez tous les aspects du service Gestion des rôles privilégiés. |
| microsoft.aad.reports/AllEntities/AllActions | Lisez et configurez des rapports Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |
| microsoft.crm/AllEntities/AllActions | Gérez tous les aspects de Dynamics 365. |
| microsoft.exchange/AllEntities/AllActions | Gérez tous les aspects d’Exchange Online. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Gérez tous les aspects d’Information Protection. |
| microsoft.intune/AllEntities/AllActions | Gérez tous les aspects d’Intune. |
| microsoft.powerbi/AllEntities/AllActions | Gérez tous les aspects de Power BI. |
| microsoft.protectioncenter/AllEntities/AllActions | Gérez le Centre de protection Office 365. |
| microsoft.sharepoint/AllEntities/AllActions | Gérez SharePoint Online. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Gérez Skype Entreprise Online. |

### <a name="compliance-administrator"></a>Administrateur de conformité
Peut lire et gérer la configuration de la conformité et les rapports dans Azure AD et Office 365.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.compliance/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans le Centre de conformité. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |
| microsoft.exchange/Compliance/AllActions | Gérer la conformité dans Exchange Online. |
| microsoft.sharepoint/Compliance/AllActions | Gérer la conformité dans SharePoint Online. |
| microsoft.skypeforbusiness/Compliance/AllActions | Gérer la conformité dans Skype Entreprise Online. |

### <a name="conditional-access-administrator"></a>Administrateur de l’accès conditionnel
Peut gérer les fonctionnalités d’accès conditionnel.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/ConditionalAccessPolicy/Create | Créez des stratégies d’accès conditionnel (ConditionalAccessPolicys) dans Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Delete | Supprimez des stratégies d’accès conditionnel (ConditionalAccessPolicys) dans Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read | Lisez des propriétés standard sur des stratégies d’accès conditionnel (ConditionalAccessPolicys) dans Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/Owners | Lisez la propriété ConditionalAccessPolicys.Owners dans Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/PolicyAppliedTo | Lisez la propriété ConditionalAccessPolicys.PolicyAppliedTo dans Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update | Mettez à jour des propriétés standard sur des stratégies d’accès conditionnel (ConditionalAccessPolicys) dans Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update/Owners | Mettez à jour la propriété ConditionalAccessPolicys.Owners dans Azure Active Directory. |

### <a name="device-administrators"></a>Administrateurs d’appareils
Les membres dotés de ce rôle sont ajoutés au groupe d’administrateurs locaux sur des appareils joints à AD Azure.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Description** |
| --- | --- |

### <a name="directory-readers"></a>Lecteurs de répertoires
Peut lire les informations d’annuaire de base. Pour accorder l’accès aux applications.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/Read | Lisez des propriétés standard sur des unités administratives (AdministrativeUnits) dans Azure Active Directory. |
| microsoft.aad.directory/AdministrativeUnit/Read/Members | Lisez la propriété AdministrativeUnits.Members dans Azure Active Directory. |
| microsoft.aad.directory/Application/Read | Lisez des propriétés standard sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/Application/Read/Owners | Lisez la propriété Applications.Owners dans Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read | Lisez des propriétés standard sur des espaces de collaboration (CollaborationSpace) dans Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read/Owners | Lisez la propriété CollaborationSpaces.Owners dans Azure Active Directory. |
| microsoft.aad.directory/Contact/Read | Lisez des propriétés standard sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Contact/Read/MemberOf | Lisez la propriété Contacts.MemberOf dans Azure Active Directory. |
| microsoft.aad.directory/Device/Read | Lisez des propriétés standard sur des appareils (Devices) dans Azure Active Directory. |
| microsoft.aad.directory/Device/Read/MemberOf | Lisez la propriété Devices.MemberOf dans Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredOwners | Lisez la propriété Devices.RegisteredOwners dans Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredUsers | Lisez la propriété Devices.RegisteredUsers dans Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read | Lisez des propriétés standard sur les rôles d’annuaire (DirectoryRoles) dans Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/EligibleMembers | Lisez la propriété DirectoryRoles.EligibleMembers dans Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/Members | Lisez la propriété DirectoryRoles.Members dans Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Read | Lisez des propriétés standard sur des paramètres d’annuaire (DirectorySettings) dans Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/Read | Lisez des propriétés standard sur des modèles de paramètre d’annuaire (DirectorySettingTemplates) dans Azure Active Directory. |
| microsoft.aad.directory/Domain/Read | Lisez des propriétés standard sur des domaines dans Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Lisez des propriétés standard sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Read/AppRoleAssignments | Lisez la propriété Groups.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/Group/Read/MemberOf | Lisez la propriété Groups.MemberOf dans Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Members | Lisez la propriété Groups.Members dans Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Owners | Lisez la propriété Groups.Owners dans Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Settings | Lisez la propriété Groups.Settings dans Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/Read | Lisez des propriétés standard sur des octrois d’autorisation OAuth2 dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Lisez des propriétés standard sur des principaux de service (ServicePrincipals) dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Lisez la propriété ServicePrincipals.AppRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Lisez la propriété ServicePrincipals.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Lisez la propriété ServicePrincipals.DefaultPolicy dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Lisez la propriété ServicePrincipals.MemberOf dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Lisez la propriété ServicePrincipals.OAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Lisez la propriété ServicePrincipals.Owners dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Lisez la propriété ServicePrincipals.OwnedObjects dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Read | Lisez des propriétés standard sur des organisations (Organizations) dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/User/Read | Lisez des propriétés standard sur des utilisateurs (Users) dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Lisez la propriété Users.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Lisez la propriété Users.DirectReports dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Lisez la propriété Users.InvitedBy dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Lisez la propriété Users.InvitedUsers dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Lisez la propriété Users.Manager dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Lisez la propriété Users.MemberOf dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Lisez la propriété Users.OAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Lisez la propriété Users.OwnedDevices dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Lisez la propriété Users.OwnedObjects dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Lisez la propriété Users.RegisteredDevices dans Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Comptes de synchronisation d’annuaires
Utilisés uniquement par le service Azure AD Connect.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/Policy/Create | Créez des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Supprimez des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Read | Lisez des propriétés standard sur des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/Owners | Lisez la propriété Policies.Owners dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/PolicyAppliedTo | Lisez la propriété Policies.PolicyAppliedTo dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Mettez à jour des propriétés standard sur des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Mettez à jour la propriété Policies.Owners dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Create | Créez des principaux de service (ServicePrincipals) dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Lisez des propriétés standard sur des principaux de service (ServicePrincipals) dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Lisez la propriété ServicePrincipals.AppRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Lisez la propriété ServicePrincipals.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Lisez la propriété ServicePrincipals.DefaultPolicy dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Lisez la propriété ServicePrincipals.MemberOf dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Lisez la propriété ServicePrincipals.OAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Lisez la propriété ServicePrincipals.Owners dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Lisez la propriété ServicePrincipals.OwnedObjects dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Mettez à jour des propriétés standard sur des principaux de service (ServicePrincipals) dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Mettez à jour la propriété ServicePrincipals.AppRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Mettez à jour la propriété ServicePrincipals.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Mettez à jour la propriété ServicePrincipals.DefaultPolicy dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Mettez à jour la propriété ServicePrincipals.Owners dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/DirSync | Mettez à jour la propriété Organizations.DirSync dans Azure Active Directory. |
| microsoft.aad.directorysync/AllEntities/AllActions | Exécutez toutes les actions dans Azure AD Connect. |

### <a name="directory-writer"></a>Enregistreur d’annuaire
Peut lire et écrire des informations d’annuaire de base. Pour l’octroi de l’accès aux applications.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/DirectorySetting/Create | Créez des paramètres d’annuaire (DirectorySettings) dans Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Delete | Supprimez des paramètres d’annuaire (DirectorySettings) dans Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Update | Mettez à jour des propriétés standard sur des paramètres d’annuaire (DirectorySettings) dans Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/Group/Read | Lisez des propriétés standard sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Mettez à jour des propriétés standard sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Mettez à jour la propriété Groups.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Mettez à jour la propriété Groups.Members dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Mettez à jour la propriété Groups.Owners dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Mettez à jour la propriété Groups.Settings dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gérez des licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/User/Update | Mettez à jour des propriétés standard sur des utilisateurs (Users) dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Mettez à jour la propriété Users.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Mettez à jour la propriété Users.Manager dans Azure Active Directory. |

### <a name="dynamics-365-service-administrator"></a>Administrateur de service Dynamics 365
Peut gérer tous les aspects du produit Dynamics 365.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |
| microsoft.crm/AllEntities/AllActions | Gérez tous les aspects de Dynamics 365. |

### <a name="exchange-service-administrator"></a>Administrateur de services Exchange
Peut gérer tous les aspects du produit Exchange.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.exchange/AllEntities/AllActions | Gérez tous les aspects d’Exchange Online. |

### <a name="guest-inviter"></a>Inviteur
Peut inviter des utilisateurs invités indépendamment du paramètre **Les membres peuvent inviter des invités**.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du rôle Invité.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/User/InviteGuest | Invitez des utilisateurs invités dans Azure Active Directory. |
| microsoft.aad.directory/User/Read | Lisez des propriétés standard sur des utilisateurs (Users) dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Lisez la propriété Users.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Lisez la propriété Users.DirectReports dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Lisez la propriété Users.InvitedBy dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Lisez la propriété Users.InvitedUsers dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Lisez la propriété Users.Manager dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Lisez la propriété Users.MemberOf dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Lisez la propriété Users.OAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Lisez la propriété Users.OwnedDevices dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Lisez la propriété Users.OwnedObjects dans Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Lisez la propriété Users.RegisteredDevices dans Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Administrateur du support technique
Peut réinitialiser des mots de passe pour les utilisateurs non-administrateurs et les administrateurs du support technique.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordHelpdeskScope | Mettez à jour les mots de passe des administrateurs limités et des autres administrateurs du support technique dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |

### <a name="information-protection-administrator"></a>Administrateur Information Protection
Peut gérer tous les aspects du produit Azure Information Protection.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Lisez des propriétés standard sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Gérez tous les aspects d’Information Protection. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |

### <a name="intune-service-administrator"></a>Administrateur de services Intune
Peut gérer tous les aspects du produit Intune.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/Contact/Create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Mettez à jour des propriétés standard sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Device/Create | Créez des appareils (Devices) dans Azure Active Directory. |
| microsoft.aad.directory/Device/Delete | Supprimez des appareils (Devices) dans Azure Active Directory. |
| microsoft.aad.directory/Device/Update | Mettez à jour des propriétés standard sur des appareils (Devices) dans Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredOwners | Mettez à jour la propriété Devices.RegisteredOwners dans Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredUsers | Mettez à jour la propriété Devices.RegisteredUsers dans Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/Group/Delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Lisez des propriétés standard sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Lisez la propriété Groups.HiddenMembers dans Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Mettez à jour des propriétés standard sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Mettez à jour la propriété Groups.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Mettez à jour la propriété Groups.Members dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Mettez à jour la propriété Groups.Owners dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Mettez à jour la propriété Groups.Settings dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/User/Update | Mettez à jour des propriétés standard sur des utilisateurs (Users) dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Mettez à jour la propriété Users.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Mettez à jour la propriété Users.Manager dans Azure Active Directory. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |
| microsoft.intune/AllEntities/AllActions | Gérez tous les aspects d’Intune. |

### <a name="lync-service-administrator"></a>Administrateur de services Lync
Peut gérer tous les aspects du produit Skype Entreprise.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Gérez Skype Entreprise Online. |

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
| microsoft.aad.directory/Group/Read | Lisez des propriétés standard sur des groupes dans Azure Active Directory. |
| microsoft.aad.accessmessagecenter/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans le Centre de messages. |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |

### <a name="partner-tier1-support"></a>Prise en charge de niveau 1 de partenaire
Ne pas utiliser - non destiné à une utilisation générale.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.directory/Contact/Create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Mettez à jour des propriétés standard sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/Group/Read | Lisez des propriétés standard sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Mettez à jour la propriété Groups.Members dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Mettez à jour la propriété Groups.Owners dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gérez des licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Supprimez des utilisateurs (Users) dans Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Restaurez des utilisateurs (Users) supprimés dans Azure Active Directory. |
| microsoft.aad.directory/User/Update | Mettez à jour des propriétés standard sur des utilisateurs (Users) dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Mettez à jour la propriété Users.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Mettez à jour la propriété Users.Manager dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserScope | Mettez à jour des mots de passe pour les utilisateurs non-administrateurs dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |

### <a name="partner-tier2-support"></a>Prise en charge de niveau 2 de partenaire
Ne pas utiliser - non destiné à une utilisation générale.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.directory/Contact/Create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Mettez à jour des propriétés standard sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions | Créez et supprimez des domaines, et lisez et mettez à jour des propriétés standard dans Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Lisez des propriétés standard sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Mettez à jour la propriété Groups.Members dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Mettez à jour des propriétés standard sur des organisations dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Mettez à jour la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gérez des licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Supprimez des utilisateurs (Users) dans Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Restaurez des utilisateurs (Users) supprimés dans Azure Active Directory. |
| microsoft.aad.directory/User/Update | Mettez à jour des propriétés standard sur des utilisateurs (Users) dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Mettez à jour la propriété Users.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Mettez à jour la propriété Users.Manager dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/Password | Mettez à jour des mots de passe pour tous les utilisateurs dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |

### <a name="power-bi-service-administrator"></a>Administrateur du service Power BI
Peut gérer tous les aspects du produit Power BI.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |
| microsoft.powerbi/AllEntities/AllActions | Gérez tous les aspects de Power BI. |

### <a name="privileged-role-administrator"></a>Administrateur de rôle privilégié
Peut gérer les attributions de rôles dans Azure AD.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/DirectoryRole/Update | Mettez à jour des propriétés standard sur les rôles d’annuaire (DirectoryRoles) dans Azure Active Directory. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Gérez tous les aspects du service Gestion des rôles privilégiés. |

### <a name="reports-reader"></a>Lecteur de rapports
Peut lire les rapports d’audit et sur les connexions.

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
| microsoft.aad.reports/AllEntities/Read | Lisez des rapports Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.usagereports/AllEntities/Read | Lisez des rapports d’utilisation Office 365. |

### <a name="security-administrator"></a>Security Administrator
Peut lire des rapports et des informations de sécurité.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Mettez à jour la propriété Applications.DefaultPolicy dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Créez des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Supprimez des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Mettez à jour des propriétés standard sur des stratégies (Policies) dans Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Mettez à jour la propriété Policies.Owners dans Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Mettez à jour la propriété ServicePrincipals.DefaultPolicy dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.privilegedrolemanagement/AllEntities/Read | Lisez tous les aspects de Privileged Identity Management. |
| microsoft.protectioncenter/AllEntities/Read | Lisez tous les aspects du Centre de protection Office 365. |
| microsoft.protectioncenter/AllEntities/Update | Gérez le Centre de protection Office 365. |

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
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.privilegedidentitymanagement/AllEntities/Read | Lisez tous les aspects de Privileged Identity Management. |
| microsoft.protectioncenter/AllEntities/Read | Lisez tous les aspects du Centre de protection Office 365. |

### <a name="service-support-administrator"></a>Administrateur de support de service
Peut lire des informations sur l’intégrité du service et gérer les tickets de support.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |

### <a name="sharepoint-service-administrator"></a>Administrateur de services SharePoint
Peut gérer tous les aspects du service SharePoint.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |
| microsoft.sharepoint/AllEntities/AllActions | Gérez SharePoint Online. |

### <a name="user-account-administrator"></a>Administrateur de compte utilisateur
Peut gérer tous les aspects des utilisateurs et des groupes.

  > [!NOTE]
  > Ce rôle hérite d’autorisations supplémentaires du [rôle utilisateur](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/AppRoleAssignment/Create | Créez des attributions de rôles d’applications (AppRoleAssignments) dans Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Supprimez des attributions de rôles d’applications (AppRoleAssignments) dans Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Mettez à jour des propriétés standard sur des attributions de rôles d’applications (AppRoleAssignments) dans Azure Active Directory. |
| microsoft.aad.directory/Contact/Create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Mettez à jour des propriétés standard sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/Group/Delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Lisez des propriétés standard sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Lisez la propriété Groups.HiddenMembers dans Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Mettez à jour des propriétés standard sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Mettez à jour la propriété Groups.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Mettez à jour la propriété Groups.Members dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Mettez à jour la propriété Groups.Owners dans Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Mettez à jour la propriété Groups.Settings dans Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Lisez la propriété Organizations.TrustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gérez des licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/User/Create | Créez des utilisateurs (Users) dans Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Supprimez des utilisateurs (Users) dans Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Restaurez des utilisateurs (Users) supprimés dans Azure Active Directory. |
| microsoft.aad.directory/User/Update | Mettez à jour des propriétés standard sur des utilisateurs (Users) dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Mettez à jour la propriété Users.AppRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Mettez à jour la propriété Users.Manager dans Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserAcctAdminScope | Mettez à jour les mots de passe des administrateurs limités, des administrateurs du support technique et des autres administrateurs des comptes d’utilisateur dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.aad.accessservice/AllEntities/AllActions | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Créez et gérez des tickets de support Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Lisez et configurez Office 365 Service Health. |

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la modification des administrateurs d’un abonnement Azure, consultez [Ajout ou modification de rôles d’administrateur Azure](../../billing/billing-add-change-azure-subscription-administrator.md)
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Pour plus d’informations sur la façon dont le service Azure Active Directory est lié à votre abonnement Azure, consultez [Association des abonnements Azure avec Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
