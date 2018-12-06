---
title: Autorisations des rôles d’administrateur dans Azure Active Directory | Microsoft Docs
description: Le rôle de l’administrateur peut servir à ajouter des utilisateurs, attribuer des rôles d’administrateur, réinitialiser les mots de passe utilisateur, gérer les licences utilisateur et les domaines, etc.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 10/26/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 5c880ca15eea6ccf77f93f74c9e1ca41f7fc01ee
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276718"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Autorisations des rôles d’administrateur dans Azure Active Directory

À l’aide d’Azure Active Directory (Azure AD), vous pouvez affecter des administrateurs distincts à différentes fonctions. Les administrateurs peuvent être désignés dans le portail Azure AD pour effectuer des tâches comme l’ajout ou le changement d’utilisateurs, l’attribution de rôles d’administrateur, la réinitialisation des mots de passe utilisateur, la gestion des licences utilisateur et la gestion des noms de domaine.

L’administrateur général a accès à toutes les fonctionnalités d’administration. Par défaut, le rôle d’administrateur général de l’annuaire est affecté à la personne qui souscrit un abonnement Azure. Seuls les administrateurs généraux et les administrateurs disposant d'un rôle privilégié peuvent déléguer des rôles d'administrateur.

## <a name="assign-or-remove-administrator-roles"></a>Attribution ou suppression de rôles d’administrateur

Pour découvrir comment attribuer des rôles d’administrateur à un utilisateur dans Azure Active Directory, consultez l’article [Afficher et attribuer des rôles d’administrateur dans Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Rôles disponibles

Les rôles d’administrateur disponibles sont les suivants :

* **[Administrateur d’application](#application-administrator)** : les utilisateurs dotés de ce rôle peuvent créer et gérer tous les aspects des applications d’entreprise, des inscriptions d’applications et des paramètres de proxy d’application. Ce rôle permet également de donner son consentement pour des autorisations déléguées et des autorisations d’application, sauf pour Microsoft Graph et Azure AD Graph. Les membres de ce rôle ne sont pas ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

  <b>Important</b> : ce rôle permet de gérer des informations d’identification d’application. Les utilisateurs auxquels ce rôle a été attribué peuvent ajouter des informations d’identification à une application et les utiliser pour emprunter l’identité de l’application. Si l’identité de l’application a obtenu l’accès à Azure Active Directory, comme la possibilité de créer ou de mettre à jour un utilisateur ou d’autres objets, un utilisateur auquel ce rôle a été attribué peut effectuer ces actions tout en se faisant passer pour l’application. Cette capacité à emprunter l’identité de l’application peut correspondre une élévation de privilèges que l’utilisateur réalise dans ses attributions de rôle au sein d’Azure AD. Il est important de comprendre que l’affectation d’un utilisateur au rôle Administrateur d’applications lui donne la possibilité d’emprunter l’identité d’une application.

* **[Développeur d’applications](#application-developer)**: les utilisateurs dotés de ce rôle peuvent créer des inscriptions d’applications quand le paramètre « Les utilisateurs peuvent inscrire des applications » est défini sur Non. Ce rôle permet également aux membres de donner leur consentement pour eux-mêmes lorsque le paramètre « Les utilisateurs peuvent autoriser les applications à accéder aux données de l'entreprise en leur nom » est défini sur Non. Les membres de ce rôle sont ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

* **[Administrateur de facturation](#billing-administrator)** : effectue les achats, gère les abonnements ainsi que les tickets de support, et surveille l’état d’intégrité des services.

* **[Administrateur d’application cloud](#cloud-application-administrator)** : les utilisateurs dotés de ce rôle ont les mêmes autorisations que l’administrateur d’application, à l’exclusion de la capacité à gérer le proxy d’application. Ce rôle permet de créer et de gérer tous les aspects des applications d’entreprise et des inscriptions d’applications. Ce rôle permet également de donner son consentement pour des autorisations déléguées et des autorisations d’application, sauf pour Microsoft Graph et Azure AD Graph. Les membres de ce rôle ne sont pas ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

  <b>Important</b> : ce rôle permet de gérer des informations d’identification d’application. Les utilisateurs auxquels ce rôle a été attribué peuvent ajouter des informations d’identification à une application et les utiliser pour emprunter l’identité de l’application. Si l’identité de l’application a obtenu l’accès à Azure Active Directory, comme la possibilité de créer ou de mettre à jour un utilisateur ou d’autres objets, un utilisateur auquel ce rôle a été attribué peut effectuer ces actions tout en se faisant passer pour l’application. Cette capacité à emprunter l’identité de l’application peut correspondre une élévation de privilèges que l’utilisateur réalise dans ses attributions de rôle au sein d’Azure AD. Il est important de comprendre que l’affectation d’un utilisateur au rôle Administrateur d’applications cloud lui donne la possibilité d’emprunter l’identité d’une application.

* **[Administrateur d’appareil cloud](#cloud-device-administrator)** : les utilisateurs dotés de ce rôle peuvent activer, désactiver et supprimer des appareils dans Azure AD et lire des clés BitLocker Windows 10 (le cas échéant) dans le Portail Azure. Ce rôle ne permet pas de gérer d’autres propriétés sur l’appareil.

* **[Administrateur de conformité](#compliance-administrator)** : les utilisateurs dotés de ce rôle possèdent des autorisations de gestion dans le Centre de sécurité et de conformité Office 365 et dans le Centre d’administration Exchange. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrateur de l’accès conditionnel](#conditional-access-administrator)** : les utilisateurs dotés de ce rôle peuvent gérer les paramètres d’accès conditionnel Azure Active Directory.
  > [!NOTE]
  > Pour pouvoir déployer la stratégie d’accès conditionnel Exchange ActiveSync dans Azure, l’utilisateur doit également être administrateur général.
  
* **[Administrateurs d’appareils](#device-administrators)**  : ce rôle est disponible pour attribution uniquement en tant qu’administrateur local supplémentaire dans [Paramètres de l’appareil](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Les utilisateurs dotés de ce rôle deviennent des administrateurs de l'ordinateur local pour tous les appareils Windows 10 qui sont joints à Azure Active Directory. Ils ne peuvent pas gérer des objets appareil dans Azure Active Directory. 

* **[Lecteurs d’annuaire](#directory-readers)** : il s’agit d’un rôle hérité qui doit être affecté aux applications ne prenant pas en charge le [framework de consentement](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Vous ne devez pas l’affecter à tous les utilisateurs.

* **[Comptes de synchronisation d’annuaires](#directory-synchronization-accounts)** : ne pas utiliser. Ce rôle est automatiquement attribué au service Azure AD Connect et n’est pas prévu ni pris en charge pour une autre utilisation.

* **[Enregistreurs d’annuaire](#directory-writers)** : il s’agit d’un rôle hérité qui doit être affecté aux applications ne prenant pas en charge le [framework de consentement](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Vous ne devez pas l’affecter à tous les utilisateurs.

* **[Administrateur Dynamics 365 / Administrateur CRM](#dynamics-365-administrator)** : les utilisateurs dotés de ce rôle ont des autorisations globales dans Microsoft Dynamics 365 Online, quand le service est présent. Ils peuvent également gérer les tickets de support et analyser l’intégrité du service. Pour plus d’informations, consultez la page [Utiliser le rôle d’administrateur de service pour gérer votre locataire](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de service Dynamics 365 ». Il est appelé « Administrateur Dynamics 365 » dans le portail Azure.

* **[Administrateur Exchange](#exchange-administrator)** : les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft Exchange Online, lorsque le service est présent. ainsi que la possibilité de créer et de gérer tous les groupes Office 365, de gérer les tickets de support et de surveiller l’état d’intégrité des services. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de services Exchange ». Il est appelé « Administrateur Exchange » dans le portail Azure.

* **[Administrateur général/Administrateur d’entreprise](#company-administrator)** : les utilisateurs dotés de ce rôle ont accès à toutes les fonctionnalités d’administration dans Azure Active Directory, ainsi qu’aux services qui utilisent des identités Azure Active Directory, comme Exchange Online, SharePoint Online et Skype Entreprise Online. La personne qui s’inscrit pour le locataire Azure Active Directory devient administrateur général. Seuls les administrateurs généraux peuvent affecter d’autres rôles d’administrateur. Une entreprise peut comprendre plusieurs administrateurs généraux. Les administrateurs généraux peuvent réinitialiser le mot de passe des utilisateurs et de tous les autres administrateurs.

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de l’entreprise ». Il est « Administrateur général » dans le [portail Azure](https://portal.azure.com).
  >
  >

* **[Inviteur](#guest-inviter)** : les utilisateurs dotés de ce rôle peuvent gérer les invitations d’utilisateurs invités Azure Active Directory B2B quand le paramètre utilisateur **Les membres peuvent inviter** est défini sur Non. Pour plus d’informations sur B2B Collaboration, consultez la page [À propos d’Azure AD B2B Collaboration](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Il n’inclut pas d’autres autorisations.

* **[Administrateur Information Protection](#information-protection-administrator)** : les utilisateurs dotés de ce rôle ont toutes les autorisations sur le service Azure Information Protection. Ce rôle permet de configurer les étiquettes pour la stratégie Azure Information Protection, de gérer les modèles de protection et d’activer la protection. Il n’octroie aucune autorisation dans Identity Protection Center, Privileged Identity Management, Monitor Office 365 Service Health ni dans le Centre de sécurité et conformité Office 365.

* **[Administrateur Intune](#intune-administrator)** : les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft Intune Online, lorsque le service est présent. Ce rôle donne aussi la possibilité de gérer les utilisateurs et les appareils afin d’associer la stratégie, ainsi que de créer et de gérer des groupes. Pour plus d’informations, consultez la page [Contrôle d’accès en fonction du rôle (RBAC) avec Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control).
  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de service Intune ». Il est « Administrateur Intune » dans le portail Azure.

* **[Administrateur de licence](#license-administrator)** : les utilisateurs dotés de ce rôle peuvent ajouter, supprimer et mettre à jour des affectations de licence pour les utilisateurs, les groupes (à l’aide de la gestion des licences par groupe) et gérer l’emplacement d’utilisation pour les utilisateurs. Le rôle ne permet pas d’acheter ou de gérer des abonnements, de créer ou de gérer des groupes, ni de créer ou de gérer les utilisateurs au-delà de leur emplacement d’utilisation.

* **[Lecteur du Centre de messages](#message-center-reader)** : les utilisateurs dotés de ce rôle peuvent surveiller les notifications et les mises à jour d’avis d’intégrité dans le [Centre de messages Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) pour leur organisation sur les services configurés comme Exchange, Intune et Microsoft Teams. Les lecteurs du Centre de messages reçoivent par e-mail des résumés hebdomadaires des publications, mises à jour, et peuvent partager les messages publiés sur le Centre de messages dans Office 365. Dans Azure AD, les utilisateurs affectés à ce rôle disposeront d’un accès en lecture seule aux services Azure AD comme les utilisateurs et les groupes. 

* **[Prise en charge du partenaire de niveau 1](#partner-tier1-support)** : ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.

* **[Prise en charge du partenaire de niveau 2](#partner-tier2-support)** : ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.

* **[Administrateur de mots de passe/Administrateur de support technique](#helpdesk-administrator)** : les utilisateurs dotés de ce rôle peuvent modifier les mots de passe, invalider les jetons d’actualisation, gérer les demandes de service et surveiller l’intégrité des services. L’invalidation d’un jeton d’actualisation oblige l’utilisateur à se reconnecter. Les administrateurs du support technique peuvent réinitialiser les mots de passe et invalider les jetons d’actualisation des autres utilisateurs qui ne sont pas administrateurs ou membres des rôles suivants uniquement :
  * Lecteurs de répertoires
  * Inviteur
  * Administrateur du support technique
  * Lecteur du Centre de messages
  * Lecteur de rapports
  
  <b>Important</b> : les utilisateurs ayant ce rôle peuvent changer les mots de passe des personnes susceptibles d’avoir accès à des informations sensibles ou privées ou à des configurations critiques à l’intérieur et à l’extérieur d’Azure Active Directory. Changer le mot de passe d’un utilisateur peut signifier la capacité d’assumer l’identité et les autorisations de cet utilisateur. Par exemple : 
  * Les propriétaires d’inscription d’application et d’application d’entreprise, qui peuvent gérer les informations d’identification des applications qu’ils possèdent. Ces applications peuvent disposer d’autorisations privilégiées dans Azure AD et ailleurs qui ne sont pas accordées aux administrateurs du support technique. Grâce à ce chemin d’accès, un administrateur de support technique peut être en mesure d’assumer l’identité d’un propriétaire d’application, puis d’assumer l’identité d’une application privilégiée en mettant à jour les identifiants de celle-ci.
  * Propriétaires d’abonnement Azure, qui peuvent avoir accès à des informations confidentielles ou privées ou à une configuration critique dans Azure.
  * Propriétaires de groupe de sécurité et de groupe Office 365, qui peuvent gérer l’appartenance à un groupe. Ces groupes peuvent accorder l’accès à des informations sensibles ou privées ou à une configuration critique dans Azure AD et ailleurs.
  * Administrateurs d’autres services en dehors d’Azure AD, tels qu’Exchange Online, le Centre de sécurité et de conformité Office et les systèmes de ressources humaines.
  * Les non-administrateurs comme les cadres supérieurs, les conseillers juridiques et les employés des ressources humaines qui peuvent avoir accès à des informations sensibles ou privées.

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur du support technique ». Il est appelé « Administrateur général » dans le [portail Azure](https://portal.azure.com/).
  >
  
* **[Administrateur Power BI](#power-bi-administrator)** : les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft Power BI, lorsque le service est présent. Ils peuvent également gérer les tickets de support et surveiller l’état des services. Pour plus d’informations, consultez la page [Présentation du rôle d’administrateur Power BI](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de service Power BI ». Il est appelé « Administrateur Power BI » dans le portail Azure.

* **[Administrateur de rôle privilégié](#privileged-role-administrator)** : les utilisateurs dotés de ce rôle peuvent gérer les attributions de rôles dans Azure Active Directory, ainsi que dans Azure AD Privileged Identity Management. En outre, ce rôle permet de gérer tous les aspects de Privileged Identity Management.

  <b>Important</b> : ce rôle donne la possibilité de gérer l’appartenance dans tous les rôles d’Azure AD, y compris le rôle d’administrateur global. Ce rôle n’inclut pas d’autres capacités privilégiées dans Azure AD, comme la création ou la mise à jour des utilisateurs. Toutefois, les utilisateurs affectés à ce rôle peuvent s’accorder ou accorder à d’autres des privilèges supplémentaires en leur attribuant des rôles supplémentaires.

* **[Lecteur de rapports](#reports-reader)**  : les utilisateurs disposant de ce rôle peuvent afficher les données des rapports d’utilisation et le tableau de bord des rapports dans le centre d’administration Office 365, ainsi que le pack du contexte d’adoption dans Power BI. En outre, ce rôle donne accès aux rapports de connexion et aux activités dans Azure AD, ainsi qu’aux données renvoyées par l’API de création de rapports Microsoft Graph. Un utilisateur disposant du rôle Lecteur de rapports peut uniquement accéder au métriques d’utilisation et d’adoption pertinentes. Il n’a pas les autorisations administrateur permettant de configurer les paramètres ou d’accéder aux centres d’administration propres au produit comme Exchange. 

* **[Administrateur de la sécurité](#security-administrator)** : les utilisateurs dotés de ce rôle disposent de toutes les autorisations en lecture seule du rôle Lecteur Sécurité, ainsi que de la possibilité de gérer la configuration des services de sécurité : Azure Active Directory Identity Protection, Azure Information Protection et Centre de sécurité et de conformité Office 365. Pour plus d’informations sur les autorisations Office 365, consultez [Autorisations dans le Centre de sécurité et conformité Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
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

* **[Administrateur SharePoint](#sharepoint-administrator)**  : les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft SharePoint Online, lorsque le service est présent. Ils peuvent également gérer tous les groupes Office 365, gérer les tickets de support et surveiller l’état des services. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de services SharePoint ». Il est appelé « Administrateur SharePoint » dans le portail Azure.

* **[Administrateur Lync/Skype Entreprise](#skype-for-business-administrator)** : les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft SharePoint Online, lorsque le service est présent. Ils peuvent également gérer les attributs utilisateur propres à Skype dans Azure Active Directory. En outre, ce rôle permet de gérer les tickets de support et de surveiller l’intégrité des services, ainsi que d’accéder au Centre d’administration Teams et Skype Entreprise. Le compte doit également disposer d’une licence Teams ; dans le cas contraire, il ne pourra pas exécuter les applets de commande PowerShell Teams. Pour plus d’informations, consultez la page [About the Skype for Business admin role](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) (À propos du rôle d’administrateur Skype Entreprise) ; pour plus d’informations sur les licences Teams, consultez l’article [Licences de compléments pour Skype Entreprise et Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing).

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de services Lync ». Il est appelé « Administrateur Skype Entreprise » dans le [portail Azure](https://portal.azure.com/).

* **[Administrateur des communications Teams](#teams-communications-administrator)** : les utilisateurs dotés de ce rôle peuvent gérer les aspects de la charge de travail Microsoft Teams liés à la voix et à la téléphonie. Cela comprend les outils de gestion pour l’attribution des numéros de téléphone, les stratégies de voix et de réunion, ainsi que l’accès total à un ensemble d’outils d’analyse des appels.

* **[Ingénieur de support des communications Teams](#teams-communications-support-engineer)** : les utilisateurs dotés de ce rôle peuvent résoudre les problèmes de communication au sein de Microsoft Teams et Skype Entreprise à l’aide des outils de résolution des appels utilisateur dans le Centre d’administration Microsoft Teams et Skype Entreprise. Les utilisateurs disposant de ce rôle peuvent visualiser l’intégralité des informations d’enregistrement des appels pour l’ensemble des participants impliqués.

* **[Spécialiste du support des communications Teams](#teams-communications-support-specialist)** : les utilisateurs dotés de ce rôle peuvent résoudre les problèmes de communication au sein de Microsoft Teams et Skype Entreprise à l’aide des outils de résolution des appels utilisateur dans le Centre d’administration Microsoft Teams et Skype Entreprise. Les utilisateurs disposant de ce rôle ne peuvent visualiser les détails utilisateur d’un appel que pour l’utilisateur qu’ils ont spécifiquement recherché.

* **[Administrateur Teams](#teams-administrator)** : les utilisateurs dotés de ce rôle peuvent gérer tous les aspects de la charge de travail Microsoft Teams par le biais du Centre d’administration Microsoft Teams et Skype Entreprise et des modules PowerShell respectifs. Cela inclut notamment tous les outils de gestion liés à la téléphonie, à la messagerie, aux réunions et aux équipes proprement dites. En outre, ce rôle permet de créer et de gérer tous les groupes Office 365, de gérer les tickets de support et de surveiller l’état d’intégrité des services.
  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur du service Teams ». Il est « Administrateur Teams » dans le portail Azure.

* **[Administrateur des comptes d’utilisateur](#user-account-administrator)**  : les utilisateurs disposant de ce rôle peuvent créer des utilisateurs avec et gérer tous les aspects relatifs aux utilisateurs avec certaines restrictions (voir ci-dessous). De plus, les utilisateurs dotés de ce rôle peuvent créer et gérer tous les groupes. Ce rôle inclut également la possibilité de créer et de gérer des affichages utilisateur, de gérer les tickets de support et de surveiller l’état d’intégrité des services.

  | | |
  | --- | --- |
  |Autorisations générales|<p>Créer des utilisateurs et des groupes</p><p>Créer et gérer des vues utilisateur</p><p>Gérer les tickets de support Office|
  |<p>Tous les utilisateurs, notamment les administrateurs</p>|<p>Gérer les licences</p><p>Gérer toutes les propriétés de l’utilisateur, sauf le nom d’utilisateur principal</p>
  |Uniquement pour les utilisateurs qui ne sont pas administrateurs ou qui ont l’un des rôles d’administrateur limités suivants :<ul><li>Lecteurs de répertoires<li>Inviteur<li>Administrateur du support technique<li>Lecteur du Centre de messages<li>Lecteur de rapports<li>Administrateur de compte utilisateur|<p>Supprimer et restaurer</p><p>Désactiver et activer</p><p>Invalider les jetons d’actualisation</p><p>Gérer toutes les propriétés de l’utilisateur, y compris le nom d’utilisateur principal</p><p>Réinitialiser le mot de passe</p><p>Mettre à jour les clés d’appareil (FIDO)</p>
  
  <b>Important</b> : les utilisateurs ayant ce rôle peuvent changer les mots de passe des personnes susceptibles d’avoir accès à des informations sensibles ou privées ou à des configurations critiques à l’intérieur et à l’extérieur d’Azure Active Directory. Changer le mot de passe d’un utilisateur peut signifier la capacité d’assumer l’identité et les autorisations de cet utilisateur. Par exemple : 
  * Les propriétaires d’inscription d’application et d’application d’entreprise, qui peuvent gérer les informations d’identification des applications qu’ils possèdent. Ces applications peuvent disposer d’autorisations privilégiées dans Azure AD et ailleurs qui ne sont pas accordées aux administrateurs utilisateurs. Grâce à ce chemin d’accès, un administrateur utilisateur peut être en mesure d’assumer l’identité d’un propriétaire d’application, puis d’assumer l’identité d’une application privilégiée en mettant à jour les identifiants de celle-ci.
  * Propriétaires d’abonnement Azure, qui peuvent avoir accès à des informations confidentielles ou privées ou à une configuration critique dans Azure.
  * Propriétaires de groupe de sécurité et de groupe Office 365, qui peuvent gérer l’appartenance à un groupe. Ces groupes peuvent accorder l’accès à des informations sensibles ou privées ou à une configuration critique dans Azure AD et ailleurs.
  * Administrateurs d’autres services en dehors d’Azure AD, tels qu’Exchange Online, le Centre de sécurité et de conformité Office et les systèmes de ressources humaines.
  * Les non-administrateurs comme les cadres supérieurs, les conseillers juridiques et les employés des ressources humaines qui peuvent avoir accès à des informations sensibles ou privées.

## <a name="role-permissions"></a>Autorisations des rôles
Les tableaux suivants décrivent les autorisations spécifiques à chaque rôle dans Azure Active Directory. Certains rôles peuvent disposer d’autorisations supplémentaires dans les services Microsoft en dehors d’Azure Active Directory.

### <a name="application-administrator"></a>Administrateur d’application
Peut créer et gérer tous les aspects des inscriptions d’applications et des applications d’entreprise.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Mettez à jour la propriété applications.audience dans Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Mettez à jour la propriété applications.authentication dans Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Mettez à jour des propriétés de base sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/create | Créez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Mettez à jour la propriété applications.credentials dans Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Supprimez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Mettez à jour la propriété applications.owners dans Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Mettez à jour la propriété applications.permissions dans Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Mettez à jour la propriété applications.policies dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Créez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Lisez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Mettez à jour des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Supprimez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Mettez à jour la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Mettez à jour la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Mettez à jour des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Créez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Supprimez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Mettez à jour la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Mettez à jour la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Mettez à jour la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.reports/applicationAuditLogs/read | Lisez les journaux applicationAuditLogs dans les rapports Azure AD. |
| microsoft.aad.reports/applicationSignInReports/read | Lisez les rapports applicationSignInReports dans les rapports Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="application-developer"></a>Développeur d’applications
Peut créer des inscriptions d’applications indépendamment du paramètre « Les utilisateurs peuvent inscrire des applications ».

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Créez des applications dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Créez des attributions de rôles d’applications dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Créez des octrois d’autorisation OAuth2 dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Créez des principaux de service dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |

### <a name="billing-administrator"></a>Administrateur de facturation
Peut effectuer des tâches de facturation courantes, comme la mise à jour des informations de paiement.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Mettez à jour des propriétés de base sur une organisation dans Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Mettez à jour la propriété organization.trustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gérez tous les aspects de la facturation Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="desktop-analytics-administrator"></a>Administrateur Desktop Analytics
Peut consulter et gérer des services et outils de gestion de bureau, notamment Intune.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gérez tous les aspects de Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="cloud-application-administrator"></a>Administrateur d'applications cloud
Peut créer et gérer tous les aspects des inscriptions d’applications et des applications d’entreprise, à l’exception du proxy d’application.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Mettez à jour la propriété applications.audience dans Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Mettez à jour la propriété applications.authentication dans Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Mettez à jour des propriétés de base sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/create | Créez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Mettez à jour la propriété applications.credentials dans Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Supprimez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Mettez à jour la propriété applications.owners dans Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Mettez à jour la propriété applications.permissions dans Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Mettez à jour la propriété applications.policies dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Créez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Mettez à jour des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Supprimez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Mettez à jour la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Mettez à jour la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Mettez à jour la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Mettez à jour la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Mettez à jour des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Créez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Supprimez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Mettez à jour la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.reports/applicationAuditLogs/read | Lisez les journaux applicationAuditLogs dans les rapports Azure AD. |
| microsoft.aad.reports/applicationSignInReports/read | Lisez les rapports applicationSignInReports dans les rapports Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="cloud-device-administrator"></a>Administrateur d’appareil cloud
Accès total pour gérer des appareils dans Azure AD.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/devices/delete | Supprimez des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/disable | Désactivez des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Activez des appareils dans Azure Active Directory. |
| microsoft.aad.reports/applicationAuditLogs/read | Lisez les journaux applicationAuditLogs dans les rapports Azure AD. |
| microsoft.aad.reports/applicationSignInReports/read | Lisez les rapports applicationSignInReports dans les rapports Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="company-administrator"></a>Administrateur d’entreprise
Peut gérer tous les aspects d’Azure AD et des services Microsoft qui utilisent des identités Azure AD.

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
| microsoft.aad.reports/applicationAuditLogs/read | Lisez les journaux applicationAuditLogs dans les rapports Azure AD. |
| microsoft.aad.reports/applicationSignInReports/read | Lisez les rapports applicationSignInReports dans les rapports Azure AD. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gérez tous les aspects d’Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gérez tous les aspects de la facturation Office 365. |
| microsoft.intune/allEntities/allTasks | Gérez tous les aspects d’Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gérez tous les aspects d’Office 365 Compliance Manager |
| microsoft.office365.exchange/allEntities/allTasks | Gérez tous les aspects d’Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Gérez tous les aspects d’Office 365 Customer Lockbox. |
| microsoft.office365.messageCenter/messages/read | Lisez les messages dans microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Lisez les messages securityMessages dans microsoft.office365.messageCenter. |
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

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Lisez la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Mettez à jour la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Lisez la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Mettez à jour la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Lisez la propriété policies.conditionalAccess dans Azure Active Directory. |

### <a name="crm-service-administrator"></a>Administrateur de services CRM
Peut gérer tous les aspects du produit Dynamics 365.

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
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.office365.lockbox/allEntities/allTasks | Gérez tous les aspects d’Office 365 Customer Lockbox. |

### <a name="device-administrators"></a>Administrateurs d’appareils
Les membres dotés de ce rôle sont ajoutés au groupe d’administrateurs locaux sur des appareils joints à AD Azure.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Lisez des propriétés de base sur des paramètres de groupes dans Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Lisez des propriétés de base sur des modèles de paramètres de groupes dans Azure Active Directory. |

### <a name="directory-readers"></a>Lecteurs de répertoires
Peut lire les informations d’annuaire de base. Pour accorder l’accès aux applications, non destiné aux utilisateurs.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Lisez des propriétés de base sur des unités administratives dans Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Lisez la propriété administrativeUnits.members dans Azure Active Directory. |
| microsoft.aad.directory/applications/audience/read | Lisez la propriété applications.audience dans Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/read | Lisez la propriété applications.authentication dans Azure Active Directory. |
| microsoft.aad.directory/applications/basic/read | Lisez des propriétés de base sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/read | Lisez la propriété applications.credentials dans Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Lisez la propriété applications.owners dans Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/read | Lisez la propriété applications.permissions dans Azure Active Directory. |
| microsoft.aad.directory/applications/policies/read | Lisez la propriété applications.policies dans Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/read | Lisez des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Lisez la propriété contacts.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/contracts/basic/read | Lisez des propriétés de base sur des contrats dans Azure Active Directory. |
| microsoft.aad.directory/devices/basic/read | Lisez des propriétés de base sur des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Lisez la propriété devices.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Lisez la propriété devices.registeredOwners dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Lisez la propriété devices.registeredUsers dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Lisez des propriétés de base sur des rôles d’annuaire dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Lisez la propriété directoryRoles.eligibleMembers dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Lisez la propriété directoryRoles.members dans Azure Active Directory. |
| microsoft.aad.directory/domains/basic/read | Lisez des propriétés de base sur des domaines dans Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Lisez la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/basic/read | Lisez des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Lisez la propriété groups.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Lisez la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Lisez la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Lisez la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Lisez des propriétés de base sur des paramètres de groupes dans Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Lisez des propriétés de base sur des modèles de paramètres de groupes dans Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Lisez des propriétés de base sur des octrois d’autorisation OAuth2 dans Azure Active Directory. |
| microsoft.aad.directory/organization/basic/read | Lisez des propriétés de base sur une organisation dans Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Lisez la propriété organization.trustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Lisez des propriétés de base sur roleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Lisez des propriétés de base sur roleDefinitions dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Lisez la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Lisez la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Lisez des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Lisez la propriété servicePrincipals.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Lisez la propriété servicePrincipals.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Lisez la propriété servicePrincipals.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Lisez la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Lisez la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Lisez des propriétés de base sur des références SKU souscrites dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Lisez la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Lisez des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Lisez la propriété users.directReports dans Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Lisez la propriété users.invitedBy dans Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Lisez la propriété users.invitedUsers dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Lisez la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Lisez la propriété users.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Lisez la propriété users.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Lisez la propriété users.ownedDevices dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Lisez la propriété users.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Lisez la propriété users.registeredDevices dans Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Comptes de synchronisation d’annuaires
Utilisés uniquement par le service Azure AD Connect.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Mettez à jour la propriété organization.dirSync dans Azure Active Directory. |
| microsoft.aad.directory/policies/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/basic/read | Lisez des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Mettez à jour des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/owners/read | Lisez la propriété policies.owners dans Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Mettez à jour la propriété policies.owners dans Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Lisez la propriété policies.policiesAppliedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Lisez la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Mettez à jour la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Lisez la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Mettez à jour la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Lisez des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Mettez à jour des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Créez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Lisez la propriété servicePrincipals.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Lisez la propriété servicePrincipals.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Lisez la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Mettez à jour la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Lisez la propriété servicePrincipals.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Lisez la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Exécutez toutes les actions dans Azure AD Connect. |

### <a name="directory-writers"></a>Enregistreurs de répertoire
Peut lire et écrire des informations d’annuaire de base. Pour accorder l’accès aux applications, non destiné aux utilisateurs.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Mettez à jour la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Mettez à jour des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Mettez à jour la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Mettez à jour des propriétés de base sur des paramètres de groupe dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Créez des paramètres de groupe dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Supprimez des paramètres de groupe dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Mettez à jour la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Mettez à jour la propriété users.userPrincipalName dans Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Administrateur de services Exchange
Peut gérer tous les aspects du produit Exchange.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Mettez à jour la propriété groups.unified dans Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Mettez à jour les propriétés de base des groupes Office 365. |
| microsoft.aad.directory/groups/unified/create | Créez des groupes Office 365. |
| microsoft.aad.directory/groups/unified/delete | Supprimez des groupes Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Mettez à jour l’appartenance des groupes Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Mettez à jour la propriété des groupes Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.exchange/allEntities/allTasks | Gérez tous les aspects d’Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="guest-inviter"></a>Inviteur
Peut inviter des utilisateurs invités indépendamment du paramètre « Les membres peuvent inviter des invités ».

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Lisez la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Lisez des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Lisez la propriété users.directReports dans Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Lisez la propriété users.invitedBy dans Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Invitez des utilisateurs invités dans Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Lisez la propriété users.invitedUsers dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Lisez la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Lisez la propriété users.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Lisez la propriété users.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Lisez la propriété users.ownedDevices dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Lisez la propriété users.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Lisez la propriété users.registeredDevices dans Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Administrateur du support technique
Peut réinitialiser des mots de passe pour les utilisateurs non-administrateurs et les administrateurs du support technique.

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
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Mettez à jour des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/devices/basic/update | Mettez à jour des propriétés de base sur des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/create | Créez des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Supprimez des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Mettez à jour la propriété devices.registeredOwners dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Mettez à jour la propriété devices.registeredUsers dans Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Mettez à jour la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Mettez à jour des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Lisez la propriété groups.hiddenMembers dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Mettez à jour la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Mettez à jour la propriété users.manager dans Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.intune/allEntities/allTasks | Gérez tous les aspects d’Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="license-administrator"></a>Administrateur de licence
Peut gérer les licences de produit pour les utilisateurs et les groupes.

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
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.office365.messageCenter/messages/read | Lisez les messages dans microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>Prise en charge de niveau 1 de partenaire
Ne pas utiliser - non destiné à une utilisation générale.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Mettez à jour des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
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
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Mettez à jour des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Créez et supprimez des domaines, et lisez et mettez à jour des propriétés standard dans Azure Active Directory. |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/organization/basic/update | Mettez à jour des propriétés de base sur une organisation dans Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Mettez à jour la propriété organization.trustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
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
Peut gérer les attributions de rôles dans Azure AD et tous les aspects de Privileged Identity Management.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Mettez à jour des rôles d’annuaire dans Azure Active Directory. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.aad.privilegedIdentityManagement. |

### <a name="reports-reader"></a>Lecteur de rapports
Peut lire les rapports d’audit et sur les connexions.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.reports/applicationAuditLogs/read | Lisez les journaux applicationAuditLogs dans les rapports Azure AD. |
| microsoft.aad.reports/applicationSignInReports/read | Lisez les rapports applicationSignInReports dans les rapports Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.usageReports/allEntities/read | Lisez des rapports d’utilisation Office 365. |

### <a name="security-administrator"></a>Security Administrator
Peut lire des rapports et des informations de sécurité, ainsi que gérer la configuration dans Azure AD et Office 365.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Mettez à jour la propriété applications.policies dans Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Mettez à jour des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Mettez à jour la propriété policies.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Lisez toutes les ressources dans microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Mettez à jour toutes les ressources dans microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Lisez toutes les ressources dans microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.reports/applicationAuditLogs/read | Lisez les journaux applicationAuditLogs dans les rapports Azure AD. |
| microsoft.aad.reports/applicationSignInReports/read | Lisez les rapports applicationSignInReports dans les rapports Azure AD. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.protectionCenter/allEntities/read | Lisez tous les aspects du Centre de protection Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Mettez à jour toutes les ressources dans microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="security-reader"></a>Lecteur de sécurité
Peut lire des rapports et des informations de sécurité dans Azure AD et Office 365.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.identityProtection/allEntities/read | Lisez toutes les ressources dans microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Lisez toutes les ressources dans microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.reports/applicationAuditLogs/read | Lisez les journaux applicationAuditLogs dans les rapports Azure AD. |
| microsoft.aad.reports/applicationSignInReports/read | Lisez les rapports applicationSignInReports dans les rapports Azure AD. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.protectionCenter/allEntities/read | Lisez tous les aspects du Centre de protection Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="service-support-administrator"></a>Administrateur de support de service
Peut lire des informations sur l’intégrité du service et gérer les tickets de support.

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
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Mettez à jour la propriété groups.unified dans Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Mettez à jour les propriétés de base des groupes Office 365. |
| microsoft.aad.directory/groups/unified/create | Créez des groupes Office 365. |
| microsoft.aad.directory/groups/unified/delete | Supprimez des groupes Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Mettez à jour l’appartenance des groupes Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Mettez à jour la propriété des groupes Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="teams-communications-administrator"></a>Administrateur des communications Teams
Peut gérer les fonctionnalités d’appel et de réunion au sein du service Microsoft Teams.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/policies/basic/read | Lisez des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |
| microsoft.office365.usageReports/allEntities/read | Lisez des rapports d’utilisation Office 365. |

### <a name="teams-communications-support-engineer"></a>Ingénieur de support des communications Teams
Peut résoudre les problèmes de communication au sein de Teams à l’aide d’outils avancés.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/policies/basic/read | Lisez des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="teams-communications-support-specialist"></a>Spécialiste du support des communications Teams
Peut résoudre les problèmes de communication au sein de Teams à l’aide d’outils de base.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/policies/basic/read | Lisez des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="teams-service-administrator"></a>Administrateur du service Teams
Peut gérer le service Microsoft Teams.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Lisez la propriété groups.hiddenMembers dans Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Mettez à jour la propriété groups.unified dans Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Mettez à jour les propriétés de base des groupes Office 365. |
| microsoft.aad.directory/groups/unified/create | Créez des groupes Office 365. |
| microsoft.aad.directory/groups/unified/delete | Supprimez des groupes Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Mettez à jour l’appartenance des groupes Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Mettez à jour la propriété des groupes Office 365. |
| microsoft.aad.directory/policies/basic/read | Lisez des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gérez tous les aspects du service Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |
| microsoft.office365.usageReports/allEntities/read | Lisez des rapports d’utilisation Office 365. |

### <a name="user-account-administrator"></a>Administrateur de compte utilisateur
Peut gérer tous les aspects des utilisateurs et groupes, notamment la réinitialisation des mots de passe pour les administrateurs limités.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Créez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Supprimez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Mettez à jour des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/update | Mettez à jour des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Mettez à jour la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Mettez à jour des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Lisez la propriété groups.hiddenMembers dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Mettez à jour la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/create | Créez des utilisateurs dans Azure Active Directory. |
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

## <a name="deprecated-roles"></a>Rôles déconseillés

Les rôles suivants ne doivent pas être utilisés. Ils sont déconseillés et seront prochainement supprimés d’Azure AD.

* Administrateur de licences ad hoc
* Jonction d’appareils
* Gestionnaires d’appareils
* Utilisateurs d’appareils
* Créateur d’utilisateur vérifié par e-mail
* Administrateur de boîte aux lettres
* Jonction d’appareils d’espace de travail

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’affectation d’un utilisateur en tant qu’administrateur d’un abonnement Azure, consultez l’article [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../../role-based-access-control/role-assignments-portal.md)
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Pour plus d’informations sur la façon dont le service Azure Active Directory est lié à votre abonnement Azure, consultez [Association des abonnements Azure avec Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
