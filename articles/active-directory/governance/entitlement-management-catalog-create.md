---
title: Créer et gérer un catalogue dans la gestion des droits Azure AD (version préliminaire) - Azure Active Directory
description: Découvrez comment créer un conteneur de ressources et l’accès aux packages dans Gestion des habilitations Azure Active Directory (version préliminaire).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d9220cd2162b4c8cb77c1e7abd0372052f5454
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541614"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Créer et gérer un catalogue dans la gestion des droits Azure AD (version préliminaire)

> [!IMPORTANT]
> Gestion des habilitations Azure Active Directory (Azure AD) est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Créer un catalogue

Un catalogue est un conteneur de ressources et l’accès aux packages. Vous créez un catalogue lorsque vous souhaitez regrouper les ressources liées et accéder aux packages. Toute personne qui crée le catalogue devient le propriétaire du catalogue. Un propriétaire de catalogue peut ajouter des propriétaires de catalogue supplémentaires.

**Rôle :** Administrateur de l’utilisateur ou le créateur du catalogue

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **catalogues**.

    ![Catalogues de gestion de droits dans le portail Azure](./media/entitlement-management-catalog-create/catalogs.png)

1. Cliquez sur **nouveau catalogue**.

1. Entrez un nom unique pour le catalogue et fournissez une description.

    Les utilisateurs verront ces informations dans les détails du package d’un accès.

1. Si vous souhaitez les packages d’accès dans ce catalogue doivent être disponibles pour les utilisateurs à demander dès qu’ils sont créés, définissez **activé** à **Oui**.

1. Si vous souhaitez autoriser les utilisateurs dans les répertoires externes sélectionnés pour être en mesure de demander l’accès de packages dans ce catalogue, définissez **activée pour les utilisateurs externes** à **Oui**.

    ![Nouveau volet de catalogue](./media/entitlement-management-catalog-create/new-catalog.png)

1. Cliquez sur **créer** pour créer le catalogue.

## <a name="add-resources-to-a-catalog"></a>Ajouter des ressources à un catalogue

Pour inclure des ressources dans un package d’accès, les ressources doivent exister dans un catalogue. Les types de ressources que vous pouvez ajouter sont des groupes, des applications et des sites SharePoint Online.

**Rôle :** Utilisateur administrateur ou propriétaire du catalogue

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **catalogues** , puis ouvrez le catalogue que vous souhaitez ajouter des ressources à.

1. Dans le menu de gauche, cliquez sur **ressources**.

1. Cliquez sur **ajouter des ressources**.

1. Cliquez sur un type de ressource : **Groupes**, **Applications**, ou **sites SharePoint**.

    Si vous êtes un créateur de catalogue, vous pouvez ajouter n’importe quel groupe Office 365 ou un groupe de sécurité Azure AD que vous possédez aux votre catalogue. Si un groupe que vous souhaitez attribuer aux utilisateurs, mais vous n’êtes pas propriétaire du groupe, vous devez avoir un administrateur de l’utilisateur à ajouter ce groupe à votre catalogue.

    Si vous êtes un créateur de catalogue, vous pouvez ajouter n’importe quelle application d’entreprise Azure AD que vous possédez, y compris les applications SaaS et vos propres applications fédéré avec Azure AD, à votre catalogue. En l’absence d’une application que vous souhaitez affecter les utilisateurs mais que vous ne possédez pas, vous devez avoir un administrateur de l’utilisateur à ajouter cette application à votre catalogue. Une fois que l’application fait partie du catalogue, vous pouvez sélectionner un des rôles de l’application dans un package d’accès.

1. Sélectionnez une ou plusieurs ressources du type que vous souhaitez ajouter au catalogue.

1. Lorsque vous avez terminé, cliquez sur **ajouter**.

    Ces ressources peuvent désormais être inclus dans les packages d’accès dans le catalogue.

## <a name="remove-resources-from-a-catalog"></a>Supprimer des ressources à partir d’un catalogue

Vous pouvez supprimer les ressources d’un catalogue. Une ressource peut uniquement être supprimée à partir d’un catalogue si elle n’est pas utilisé dans un des packages d’accès du catalogue.

**Rôle :** Utilisateur administrateur ou propriétaire du catalogue

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **catalogues** , puis ouvrez le catalogue que vous souhaitez supprimer des ressources à partir de.

1. Dans le menu de gauche, cliquez sur **ressources**.

1. Sélectionnez les ressources que vous souhaitez supprimer.

1. Cliquez sur **supprimer** (ou cliquez sur le bouton de sélection (**...** ) puis cliquez sur **supprimer ressource**).

## <a name="add-catalog-owners-or-access-package-managers"></a>Ajouter des propriétaires de catalogue ou accéder aux gestionnaires de packages

Si vous souhaitez déléguer la gestion de packages accès dans le catalogue ou le catalogue, vous ajoutez des propriétaires de catalogue ou accéder aux gestionnaires de package. Toute personne qui crée un catalogue devient le propriétaire du catalogue.

**Rôle :** Utilisateur administrateur ou propriétaire du catalogue

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **catalogues** , puis ouvrez le catalogue que vous souhaitez ajouter aux administrateurs.

1. Dans le menu de gauche, cliquez sur **rôles et administrateurs**.

1. Cliquez sur **ajouter des propriétaires** ou **ajouter des gestionnaires de packages accès** pour sélectionner les membres de ces rôles.

1. Cliquez sur **sélectionnez** ajouter ces membres.

## <a name="edit-a-catalog"></a>Modifier un catalogue

Vous pouvez modifier le nom et la description d’un catalogue. Les utilisateurs voient ces informations dans les détails du package d’un accès.

**Rôle :** Utilisateur administrateur ou propriétaire du catalogue

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **catalogues** , puis ouvrez le catalogue que vous souhaitez modifier.

1. Sur le catalogue **vue d’ensemble** , cliquez sur **modifier**.

1. Modifier le nom ou la description du catalogue.

1. Cliquez sur **Enregistrer**.

## <a name="delete-a-catalog"></a>Supprimer un catalogue

Vous pouvez supprimer un catalogue, mais uniquement si elle n’a pas accès tous les packages.

**Rôle :** Utilisateur administrateur ou propriétaire du catalogue

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **catalogues** , puis ouvrez le catalogue que vous souhaitez supprimer.

1. Sur le catalogue **vue d’ensemble**, cliquez sur **supprimer**.

1. Dans la boîte de message qui s’affiche, cliquez sur **Oui**.

## <a name="next-steps"></a>Étapes suivantes

- [Créer et gérer un package d’accès](entitlement-management-access-package-create.md)
