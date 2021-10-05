---
title: 'Démarrage rapide : Création d’une collection'
description: Les collections sont utilisées pour le contrôle d’accès et l’organisation des ressources dans Azure Purview. Cet article explique comment créer une collection et ajouter des autorisations, inscrire des sources et inscrire des ressources dans les collections.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: quickstart
ms.date: 09/27/2021
ms.custom: template-quickstart
ms.openlocfilehash: 47db60e99048d5be58eb1aae6707caa6ae9c4d6e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218780"
---
# <a name="quickstart-create-a-collection-and-assign-permissions-in-purview"></a>Démarrage rapide : Création d’une collection et attribution d’autorisations dans Purview

> [!NOTE]
> Pour le moment, ce guide de démarrage rapide ne s’applique qu’aux instances Azure Purview créées le 18 août 2021 ou après cette date. Les instances créées auparavant peuvent créer des collections, mais ne gèrent pas les autorisations par le biais de ces collections. Pour plus d’informations sur la création d’une collection pour une instance Purview créée avant le 18 août, consultez notre [**guide des collections héritées**](#legacy-collection-guide) en bas de la page.
> 
> Tous les comptes hérités seront automatiquement mis à niveau dans les semaines à venir. Vous recevrez une notification par e-mail lors de la mise à niveau de votre compte Purview. Pour plus d’informations sur ce qui va changer une fois votre compte mis à niveau, consultez notre [Guide des comptes mis à niveau](concept-account-upgrade.md).

Les collections constituent l’outil d’Azure Purview permettant de gérer la propriété et le contrôle d’accès sur les différentes ressources, sources et informations. Elles organisent également vos sources et ressources dans des catégories personnalisées en fonction de votre expérience de gestion avec vos données. Ce guide vous explique comment configurer votre première collection et votre premier administrateur de collection afin de préparer votre environnement Purview pour votre organisation.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Votre propre [locataire Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Une [ressource Purview](create-catalog-portal.md) active.

## <a name="check-permissions"></a>Vérifiez les autorisations

Pour créer et gérer des collections dans Purview, vous devez être **administrateur de collection**. Vous pouvez vérifier ces autorisations dans [Purview Studio](use-purview-studio.md). Pour ouvrir le studio, accédez à votre ressource Purview sur le [Portail Azure](https://portal.azure.com), puis sélectionnez la vignette **Ouvrir Purview Studio** sur la page de présentation.

1. Sélectionnez Data Map > Collections dans le volet gauche pour ouvrir la page gestion des collections.

    :::image type="content" source="./media/quickstart-create-collection/find-collections.png" alt-text="Capture d’écran de Purview Studio, affichant le volet Mappage de données avec l’onglet Collections sélectionné" border="true":::

1. Sélectionnez votre collection racine. Il s’agit de la première collection de la liste. Elle porte le même nom que votre ressource Purview. Dans l’exemple ci-dessous, elle s’appelle Contoso Purview.

    :::image type="content" source="./media/quickstart-create-collection/select-root-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur Data Map, avec la collection racine encadrée." border="true":::

1. Sélectionnez Attributions de rôles dans la fenêtre de la collection.

    :::image type="content" source="./media/quickstart-create-collection/role-assignments.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur Data Map, avec l’onglet Attributions de rôles encadré." border="true":::

1. Pour créer une collection, vous devez figurer dans la liste des administrateurs de collections sous attributions de rôle. En principe, si vous êtes l’auteur de la ressource Purview, vous figurez déjà parmi les administrateurs de collection sous la collection racine. Si ce n’est pas le cas, vous devez contacter l’administrateur de collection pour qu’il vous accorde l’autorisation.

    :::image type="content" source="./media/quickstart-create-collection/collection-admins.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur Data Map, avec la section Administrateurs de collection encadrée." border="true":::

## <a name="create-a-collection-in-the-portal"></a>Création d’une collection sur le portail

Pour créer votre collection, vous allons partir de [Purview Studio](use-purview-studio.md). Pour ouvrir le studio, accédez à votre ressource Purview sur le Portail Azure, puis sélectionnez la vignette **Ouvrir Purview Studio** sur la page de présentation.

1. Sélectionnez Data Map > Collections dans le volet gauche pour ouvrir la page gestion des collections.

    :::image type="content" source="./media/quickstart-create-collection/find-collections-2.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur Data Map, avec l’onglet Collections sélectionné." border="true":::

1. Sélectionnez **+ Ajouter une collection**.

    :::image type="content" source="./media/quickstart-create-collection/select-add-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, affichant le volet Mappage de données avec l’onglet Collections sélectionné et le bouton Ajouter une collection encadré" border="true":::

1. Dans le volet droit, entrez le nom de la collection et sa description, puis recherchez les utilisateurs à ajouter comme administrateurs de collection.

    :::image type="content" source="./media/quickstart-create-collection/create-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, montrant la fenêtre Nouvelle collection, avec un nom d’affichage et des administrateurs de collection sélectionnés, ainsi que le bouton Créer encadré." border="true":::

1. Sélectionnez **Create** (Créer). Les informations sur la collection sont répercutées sur la page.

    :::image type="content" source="./media/quickstart-create-collection/created-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, montrant la fenêtre de la nouvelle collection." border="true":::

## <a name="assign-permissions-to-collection"></a>Attribution d’autorisations à une collection

Maintenant que vous disposez d’une collection, vous pouvez y attribuer des autorisations pour gérer l’accès de vos utilisateurs à Purview.

### <a name="roles"></a>Rôles

Tous les rôles attribués s’appliquent aux sources, aux ressources et aux autres objets de la collection concernés.

* **Administrateurs de collection** : peuvent modifier une collection et ses détails, gérer l’accès dans la collection et ajouter des sous-collections.
* **Administrateurs de source de données** : peuvent gérer les sources de données et les analyses de données.
* **Éditeurs de données** : peuvent créer, lire, modifier et supprimer des actions sur des objets de données de catalogue.
* **Lecteurs de données** : peuvent accéder aux objets de données de catalogue, mais pas les modifier.

### <a name="assign-permissions"></a>Attribuer des autorisations

1. Sélectionnez l’onglet **Attributions de rôles** pour afficher tous les rôles d’une collection.

    :::image type="content" source="./media/quickstart-create-collection/select-role-assignments.png" alt-text="Capture d’écran de la fenêtre de collection Purview Studio, avec l’onglet Attributions de rôles encadré." border="true":::

1. Sélectionnez **Modifier les attributions de rôles** ou l’icône représentant une personne pour modifier chacun des membres de rôles.

    :::image type="content" source="./media/quickstart-create-collection/edit-role-assignments.png" alt-text="Capture d’écran de la fenêtre de collection Purview Studio, avec la liste déroulante Modifier les attributions de rôles sélectionnée." border="true":::

1. Tapez dans la zone de texte pour rechercher les utilisateurs que vous souhaitez ajouter aux membres de rôles. Sélectionnez **OK** pour enregistrer la modification.

## <a name="legacy-collection-guide"></a>Guide des collections héritées

> [!NOTE]
> Ce guide des collections héritées ne concerne que les instances Purview créées avant le 18 août 2021. Pour les instances créées après cette date, vous devez suivre le guide ci-dessus.

### <a name="create-a-legacy-collection"></a>Création d’une collection héritée

1. Sélectionnez Data Map dans le volet gauche pour ouvrir le mappage de données. Cette vue vous permet de voir vos collections et les sources listées dessous.

    :::image type="content" source="./media/quickstart-create-collection/legacy-collection-view.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur la Data Map." border="true":::

1. Sélectionnez **+ Nouvelle collection**.

    :::image type="content" source="./media/quickstart-create-collection/legacy-collection-create.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur la Data Map, avec l’option + Nouvelle collection mise en évidence." border="true":::

1. Donnez un nom à votre collection et sélectionnez un parent ou « Aucun ». Sélectionnez **Create** (Créer). Les informations sur la collection sont répercutées sur la carte des données.

    :::image type="content" source="./media/quickstart-create-collection/legacy-collection-name.png" alt-text="Capture d’écran de la fenêtre contextuelle Nouvelle collection de Purview Studio." border="true":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’une collection, vous pouvez suivre les guides ci-dessous pour ajouter des ressources, et analyser et gérer vos collections.

* [Inscription d’une source à une collection](how-to-create-and-manage-collections.md#register-source-to-a-collection)
* [Gestion des accès par le biais de collections](how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)
