---
title: Déléguer des rôles d’administrateur d’application – Azure Active Directory | Microsoft Docs
description: Gestion des accès aux applications en déléguant des rôles pour accorder des droits d’autorisation dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ca814551d8c7d309328f236052e1d07ac6f035
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60469126"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>Déléguer des rôles d’administrateur d’application dans Azure Active Directory

 Azure AD vous permet de déléguer la gestion des accès aux applications à un ensemble de rôles administratifs intégrés. En plus de réduire les frais associés à l’administrateur général, la délégation de privilèges spécialisés pour la gestion des tâches d’accès aux applications peut améliorer votre situation de sécurité et réduire les risques d’accès non autorisé. Des problèmes et des instructions générales en lien avec la délégation sont abordés dans [Déléguer l’administration dans Azure Active Directory](roles-concept-delegation.md).

## <a name="delegate-app-administration"></a>Déléguer l’administration des applications

Les rôles suivants accordent des autorisations pour gérer les inscriptions d’applications, les paramètres d’authentification unique, les assignations d’utilisateurs et de groupes, et pour consentir aux permissions déléguées et aux permissions d’application (à l’exception de Microsoft Graph et d’Azure AD Graph). La seule différence est que le rôle Administrateur d’application accorde également des autorisations pour gérer les paramètres de proxy d’application. Aucun rôle n’accorde la possibilité de gérer les paramètres d’accès conditionnel.
> [!IMPORTANT]
> Les utilisateurs auxquels ce rôle a été attribué peuvent ajouter des informations d’identification à une application, et utiliser celles-ci pour emprunter l’identité de l’application. Cet emprunt de l’identité de l’application peut constituer une élévation de privilèges par rapport à ce que l’utilisateur peut faire en vertu de ses attributions de rôle dans Azure AD. Un utilisateur assigné à ce rôle pourrait créer ou mettre à jour des utilisateurs ou d’autres objets tout en empruntant l’identité de l’application.

Pour accorder la possibilité de gérer l’accès aux applications dans le portail Azure :

1. Connectez-vous à votre [locataire Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) avec un compte éligible pour le rôle Administrateur général dans le locataire.
2. Si vous disposez d’autorisations suffisantes, ouvrez la page [Rôles et administrateurs](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators).
3. Ouvrez l’un des rôles suivants pour voir ses assignations de membre :
   * **Administrateur d’application**
   * **Administrateur d’application cloud**
4. Dans la page **Membres** page pour le rôle, sélectionnez **Ajouter un membre**.
5. Sélectionnez un ou plusieurs membres à ajouter au rôle. <!--Members can be users or groups.-->

Vous pouvez voir la description de ces rôles dans [Rôles disponibles](directory-assign-admin-roles.md#available-roles).

## <a name="delegate-app-registration"></a>Déléguer l’inscription des applications

Par défaut, tous les utilisateurs peuvent créer des inscriptions d’application, mais vous pouvez accorder de manière sélective l’autorisation de créer des inscriptions d’application ou de consentir à autoriser une application.

1. Connectez-vous à votre [locataire Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) avec un compte éligible pour le rôle Administrateur général dans le locataire.
2. Après avoir obtenu les autorisations suffisantes, définissez une ou les deux options suivantes :
   * Dans la [page Paramètres utilisateur pour votre locataire](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings), définissez l’option **Les utilisateurs peuvent inscrire des applications** sur Non.
   * Dans la [page Paramètres utilisateur pour vos applications d’entreprise](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/), définissez l’option **Les utilisateurs peuvent autoriser les applications à accéder aux données de l’entreprise en leur nom** sur Non.
3. Assignez ensuite aux utilisateurs ayant besoin de cette autorisation le rôle Développeur d’applications en fonction des besoins.

Quand un utilisateur inscrit une application, il est automatiquement ajouté en tant que premier propriétaire de l’application.

## <a name="delegate-app-ownership"></a>Déléguer l’appartenance des applications

Les propriétaires d'applications et d'inscriptions d'applications ne peuvent gérer que les applications ou inscriptions d'applications dont ils ont la propriété. Par exemple, lorsque vous ajoutez un propriétaire pour l’application Salesforce, celui-ci peut gérer l’accès à Salesforce et sa configuration, à l’exclusion de toute autre application. Une application peut avoir plusieurs propriétaires, et un utilisateur peut être le propriétaire de plusieurs applications.

Le propriétaire d’une application peut :

* Modifier les propriétés de l’application, telles que le nom et les autorisations demandées par l’application
* Gérer les informations d’identification
* Configurer l’authentification unique
* Assigner un accès utilisateur
* Ajouter ou supprimer d’autres propriétaires
* Modifier le manifeste de l’application
* Publier l’application dans la galerie d’applications

> [!IMPORTANT]
> Les utilisateurs auxquels ce rôle a été attribué peuvent ajouter des informations d’identification à une application, et utiliser celles-ci pour emprunter l’identité de l’application. Cet emprunt de l’identité de l’application peut constituer une élévation de privilèges par rapport à ce que l’utilisateur peut faire en vertu de ses attributions de rôle dans Azure AD. Un utilisateur assigné à ce rôle pourrait créer ou mettre à jour des utilisateurs ou d’autres objets tout en empruntant l’identité de l’application.

Le propriétaire d’une inscription d’application peut afficher et modifier celle-ci.

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>Pour assigner un propriétaire à une application

1. Connectez-vous à votre [locataire Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) avec un compte éligible au rôle administrateur d’application ou administrateur d’application cloud pour le locataire.
2. Dans la [page Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) pour le locataire, sélectionnez une application pour ouvrir la page **Vue d’ensemble** de l’application.
3. Sélectionnez **Propriétaires** pour afficher la liste des propriétaires de l’application.
4. Sélectionnez **Ajouter** pour sélectionner un ou plusieurs propriétaires à ajouter à l’application.

### <a name="to-assign-an-owner-to-an-application-registration"></a>Pour assigner un propriétaire à une inscription d’application

1. Connectez-vous à votre [locataire Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) avec un compte éligible au rôle administrateur d’application ou administrateur d’application cloud dans le locataire.
2. Si vous disposez d’autorisations suffisantes, dans la [page Applications d’entreprise](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) pour le locataire, sélectionnez une inscription d’application pour l’ouvrir.
3. Sélectionnez **Paramètres**.
4. Dans la page **Paramètres**, sélectionnez **Propriétaires** pour afficher la liste des propriétaires de l’application.
5. Sélectionnez **Ajouter un propriétaire** pour sélectionner un ou plusieurs propriétaires à ajouter à l’application.

## <a name="next-steps"></a>Étapes suivantes

* [Informations de référence sur le rôle administrateur Azure AD](directory-assign-admin-roles.md)
