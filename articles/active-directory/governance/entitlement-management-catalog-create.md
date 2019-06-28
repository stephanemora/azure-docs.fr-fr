---
title: Créer et gérer un catalogue dans la gestion des droits d’utilisation Azure AD (préversion) - Azure Active Directory
description: Découvrez comment créer un nouveau conteneur de ressources et de packages d’accès dans la gestion des droits d’utilisation Azure Active Directory (préversion).
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541614"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Créer et gérer un catalogue dans la gestion des droits d’utilisation Azure AD (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Créer un catalogue

Un catalogue est un conteneur de ressources et de packages d’accès. Vous créez un catalogue lorsque vous souhaitez regrouper des ressources et packages d’accès liés. La personne qui crée le catalogue en devient le premier propriétaire. Un propriétaire de catalogue peut ajouter d’autres propriétaires de catalogue.

**Rôle prérequis :** Administrateur d’utilisateurs ou créateur de catalogue

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Catalogues**.

    ![Catalogues de gestion des droits d’utilisation dans le Portail Azure](./media/entitlement-management-catalog-create/catalogs.png)

1. Cliquez sur **Nouveau catalogue**.

1. Entrez un nom unique pour le catalogue et fournissez une description.

    Les utilisateurs verront ces informations dans les détails d’un package d’accès.

1. Si vous souhaitez que les packages d’accès dans ce catalogue soient accessibles aux utilisateurs qui les demandent dès leur création, définissez **Activé** sur **Oui**.

1. Si vous souhaitez que les utilisateurs de répertoires externes sélectionnés puissent demander des packages d’accès dans ce catalogue, définissez **Activé pour les utilisateurs externes** sur **Oui**.

    ![Volet Nouveau catalogue](./media/entitlement-management-catalog-create/new-catalog.png)

1. Cliquez sur **Créer** pour créer le catalogue.

## <a name="add-resources-to-a-catalog"></a>Ajouter des ressources à un catalogue

Pour inclure des ressources dans un package d’accès, les ressources doivent exister dans un catalogue. Les types de ressources que vous pouvez ajouter sont des groupes, des applications et des sites SharePoint Online.

**Rôle prérequis :** Administrateur d’utilisateurs ou propriétaire de catalogue

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Catalogues**, puis ouvrez le catalogue auquel vous souhaitez ajouter des ressources.

1. Dans le menu de gauche, cliquez sur **Ressources**.

1. Cliquez sur **Ajouter des ressources**.

1. Cliquez sur un type de ressources : **Groupes**, **Applications** ou **Sites SharePoint**.

    En tant que créateur de catalogue, vous pouvez ajouter à votre catalogue n’importe quel groupe Office 365 ou groupe de sécurité Azure AD que vous possédez. Si vous souhaitez affecter un groupe à des utilisateurs, mais que vous n’êtes pas propriétaire du groupe, un administrateur d’utilisateurs devra ajouter ce groupe à votre catalogue.

    En tant que créateur de catalogue, vous pouvez ajouter à votre catalogue n’importe quelle application d’entreprise Azure AD que vous possédez, y compris des applications SaaS et vos propres applications fédérées avec Azure AD. Si vous souhaitez affecter une application à des utilisateurs, mais que vous ne la possédez pas, un administrateur d’utilisateurs devra ajouter cette application à votre catalogue. Lorsque l’application est incluse dans le catalogue, vous pouvez sélectionner un des rôles d’application dans un package d’accès.

1. Sélectionnez une ou plusieurs ressources du type que vous souhaitez ajouter au catalogue.

1. Lorsque vous avez terminé, cliquez sur **Ajouter**.

    Ces ressources peuvent désormais être incluses dans des packages d’accès du catalogue.

## <a name="remove-resources-from-a-catalog"></a>Supprimer des ressources d’un catalogue

Vous pouvez supprimer des ressources d’un catalogue. Une ressource ne peut être supprimée d’un catalogue que si elle n’est pas utilisée dans un des packages d’accès du catalogue.

**Rôle prérequis :** Administrateur d’utilisateurs ou propriétaire de catalogue

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Catalogues**, puis ouvrez le catalogue duquel vous souhaitez supprimer des ressources.

1. Dans le menu de gauche, cliquez sur **Ressources**.

1. Sélectionnez les ressources que vous souhaitez supprimer.

1. Cliquez sur **Supprimer** (ou cliquez sur le bouton de sélection ( **...** ), puis sur **Supprimer la ressource**).

## <a name="add-catalog-owners-or-access-package-managers"></a>Ajouter des propriétaires de catalogue ou des gestionnaires de package d’accès

Si vous souhaitez déléguer la gestion du catalogue ou des packages accès dans le catalogue, ajoutez des propriétaires de catalogue ou des gestionnaires de package d’accès. La personne qui crée un catalogue en devient le premier propriétaire.

**Rôle prérequis :** Administrateur d’utilisateurs ou propriétaire de catalogue

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Catalogues**, puis ouvrez le catalogue auquel vous souhaitez ajouter des administrateurs.

1. Dans le menu de gauche, cliquez sur **Rôles et administrateurs**.

1. Cliquez sur **Ajouter des propriétaires** ou **Ajouter des gestionnaires de package d’accès** pour sélectionner les membres de ces rôles.

1. Cliquez sur **Sélectionner** pour ajouter ces membres.

## <a name="edit-a-catalog"></a>Modifier un catalogue

Vous pouvez modifier le nom et la description d’un catalogue. Les utilisateurs verront ces informations dans les détails d’un package d’accès.

**Rôle prérequis :** Administrateur d’utilisateurs ou propriétaire de catalogue

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Catalogues**, puis ouvrez le catalogue que vous souhaitez modifier.

1. Dans la page **Vue d’ensemble** du catalogue, cliquez sur **Modifier**.

1. Modifiez le nom ou la description du catalogue.

1. Cliquez sur **Enregistrer**.

## <a name="delete-a-catalog"></a>Supprimer un catalogue

Vous pouvez supprimer un catalogue, mais seulement s’il ne contient pas de packages d’accès.

**Rôle prérequis :** Administrateur d’utilisateurs ou propriétaire de catalogue

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Catalogues**, puis ouvrez le catalogue que vous souhaitez supprimer.

1. Dans la **Vue d’ensemble** du catalogue, cliquez sur **Supprimer**.

1. Dans la boîte de message qui s’affiche, cliquez sur **Oui**.

## <a name="next-steps"></a>Étapes suivantes

- [Créer et gérer un package d’accès](entitlement-management-access-package-create.md)
