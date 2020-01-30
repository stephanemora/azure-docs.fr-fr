---
title: Vue d’ensemble des comptes d’utilisateur dans Azure Active Directory B2C
description: Découvrez les types de comptes d’utilisateur utilisables dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f9573f4d226df4eb6068af679f2eb45dc87c7e40
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840058"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Vue d’ensemble des comptes d’utilisateur dans Azure Active Directory B2C

Dans Azure Active Directory B2C (Azure AD B2C), plusieurs types de comptes peuvent être créés. Azure Active Directory, Active Directory B2B et Active Directory B2C partagent les types de comptes d’utilisateur utilisables.

Les types de comptes suivants sont disponibles :

- **Compte professionnel** – Un compte professionnel peut accéder aux ressources dans un locataire et, avec un rôle d’administrateur, peut gérer des locataires.
- **Compte invité** – Un compte invité peut uniquement être un compte Microsoft ou un utilisateur Azure Active Directory qui peut être utilisé pour accéder à des applications ou gérer des locataires.
- **Compte de consommateur** – Un compte de consommateur est utilisé par un utilisateur des applications inscrites auprès d’Azure AD B2C. Il existe plusieurs moyens de créer des comptes de consommateur :
  - L’utilisateur suit un flux utilisateur d’inscription dans une application Azure AD B2C
  - Utilisation de l’API Graph Azure AD
  - Utilisation du portail Azure

## <a name="work-account"></a>Compte professionnel

Un compte professionnel est créé de la même manière pour tous les locataires basés sur Azure AD. Pour créer un compte professionnel, vous pouvez utiliser les informations contenues dans [Démarrage rapide : Ajouter de nouveaux utilisateurs à Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Un compte professionnel est créé en choisissant **Nouvel utilisateur** dans le portail Azure.

Lorsque vous ajoutez un nouveau compte professionnel, vous devez prendre en compte les paramètres de configuration suivants :

- **Nom** et **Nom d’utilisateur** – La propriété **Nom** contient le prénom et le nom de l’utilisateur. Le **nom d’utilisateur** est l’identificateur que l’utilisateur utilise pour se connecter. Le nom d’utilisateur inclut le domaine complet. La partie du nom de domaine du nom d’utilisateur doit être le nom de domaine initial par défaut, *nom de domaine.onmicrosoft.com*, ou un nom de [domaine personnalisé](../active-directory/fundamentals/add-custom-domain.md) vérifié, non fédéré, tel que *contoso.com*.
- **Profil** – Le compte est configuré avec un profil de données utilisateur. Vous avez la possibilité d’entrer un prénom, un nom, un poste et un nom de service. Vous pouvez modifier le profil une fois que le compte a été créé.
- **Groupes** – Un groupe vous permet d’effectuer différentes tâches de gestion, comme attribuer des licences ou des autorisations à plusieurs utilisateurs ou appareils simultanément. Vous pouvez placer le nouveau compte dans un [groupe](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) existant dans votre locataire.
- **Rôle d’annuaire** – Vous devez spécifier le niveau d’accès que le compte d’utilisateur possède pour les ressources dans votre locataire. Les niveaux d’autorisation suivants sont disponibles :

    - **Utilisateur** – Les utilisateurs peuvent accéder aux ressources affectées, mais ne peuvent pas gérer la plupart des ressources de locataire.
    - **Administrateur général** – Les administrateurs généraux contrôlent totalement toutes les ressources de locataire.
    - **Administrateur limité** – Sélectionnez le ou les rôles d’administration pour l’utilisateur. Pour plus d’informations sur les rôles qui peuvent être sélectionnés, consultez [Attribution de rôles d’administrateur dans Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="create-a-work-account"></a>Créer un compte professionnel

Vous pouvez utiliser les informations suivantes pour créer un nouveau compte professionnel :

- [Azure portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Mettre à jour un profil utilisateur

Vous pouvez utiliser les informations suivantes pour mettre à jour le profil d’un utilisateur :

- [Azure portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Réinitialiser un mot de passe pour un utilisateur

Vous pouvez utiliser les informations suivantes pour réinitialiser le mot de passe d’un utilisateur :

- [Azure portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Utilisateur invité

Vous pouvez inviter des utilisateurs externes dans votre locataire en tant qu’utilisateur invité. Un scénario standard pour inviter un utilisateur dans votre locataire Azure AD B2C consiste à partager les responsabilités d’administration. Pour obtenir un exemple d’utilisation d’un compte invité, consultez [Propriétés d’un utilisateur Azure Active Directory B2B Collaboration](../active-directory/b2b/user-properties.md).

Lorsque vous invitez un utilisateur dans votre locataire, vous fournissez l’adresse e-mail du destinataire et un message décrivant l’invitation. Le lien d’invitation dirige l’utilisateur vers la page de consentement où le bouton **Prise en main** est sélectionné et la révision des autorisations est acceptée. Si une boîte de réception n’est pas attachée à l’adresse de messagerie, l’utilisateur peut accéder à la page de consentement en accédant à une page de Microsoft en utilisant les informations d’identification d’invité. L’utilisateur est alors obligé d’utiliser l’invitation de la même façon qu’en cliquant sur le lien dans l’e-mail. Par exemple : `https://myapps.microsoft.com/B2CTENANTNAME`.

Vous pouvez également utiliser l’[API Microsoft Graph](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) pour inviter un utilisateur.

## <a name="consumer-user"></a>Utilisateur consommateur

L’utilisateur consommateur peut se connecter aux applications sécurisées par Azure AD B2C, mais il ne peut pas accéder aux ressources Azure telles que le portail Azure. L’utilisateur consommateur peut utiliser un compte local ou des comptes fédérés, tels que Facebook ou Twitter. Pour créer un compte de consommateur, on peut utiliser un [flux utilisateur d’inscription ou de connexion](user-flow-overview.md), l’API Graph Azure AD ou le Portail Azure.

Vous pouvez spécifier les données qui sont collectées lorsqu’un compte d’utilisateur consommateur est créé à l’aide des attributs d’utilisateur personnalisés. Pour plus d’informations, consultez [Définir des attributs personnalisés dans Azure Active Directory B2C](user-flow-custom-attributes.md).

Vous pouvez utiliser les informations contenues dans la section **Créer des comptes d’utilisateurs consommateurs** de la rubrique [Utiliser l’API Azure AD Graph](manage-user-accounts-graph-api.md) pour créer un compte de consommateur Azure AD B2C. Vous pouvez également utiliser les informations contenues dans la section **Mettre à jour les comptes d’utilisateurs consommateurs** du même article, pour gérer les propriétés du compte.

### <a name="migrate-consumer-user-accounts"></a>Migrer des comptes d’utilisateurs consommateurs

Vous pouvez avoir besoin de migrer des comptes d’utilisateur existants de n’importe quel fournisseur d’identité vers Azure AD B2C. Pour plus d’informations, consultez [Migration utilisateur](user-migration.md) ou [Migrer des utilisateurs avec des identités de réseaux sociaux](migrate-social-identities.md).
