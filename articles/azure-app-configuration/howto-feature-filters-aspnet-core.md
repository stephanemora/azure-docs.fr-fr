---
title: Utiliser des filtres de fonctionnalités afin d'activer une fonctionnalité pour un sous-ensemble d'utilisateurs
titleSuffix: Azure App Configuration
description: Apprenez à utiliser des filtres de fonctionnalités afin d'activer une fonctionnalité pour un sous-ensemble d'utilisateurs
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 5b2eb942581f6e4163012b0f767d04c02689bb7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88206764"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Utiliser des filtres de fonctionnalités afin d'activer une fonctionnalité pour un sous-ensemble d'utilisateurs

Les indicateurs de fonctionnalités vous permettent d'activer ou de désactiver des fonctionnalités dans votre application. Un indicateur de fonctionnalité simple est activé ou désactivé. L'application se comporte toujours de la même façon. Par exemple, vous pouvez déployer une nouvelle fonctionnalité derrière un indicateur de fonctionnalité. Lorsque l'indicateur de fonctionnalité est activé, tous les utilisateurs voient la nouvelle fonctionnalité. La désactivation de l'indicateur de fonctionnalité masque la nouvelle fonctionnalité.

En revanche, un _indicateur de fonctionnalité conditionnel_ permet d'activer ou de désactiver dynamiquement l'indicateur de fonctionnalité. L'application peut se comporter différemment, selon les critères de l'indicateur de fonctionnalité. Supposons que vous souhaitiez commencer par présenter votre nouvelle fonctionnalité à un petit sous-ensemble d'utilisateurs. Un indicateur de fonctionnalité conditionnel vous permet d'activer l'indicateur de fonctionnalité pour certains utilisateurs et de le désactiver pour d'autres. Les _filtres de fonctionnalités_ déterminent l'état de l'indicateur de fonctionnalité chaque fois qu'il est évalué.

La bibliothèque `Microsoft.FeatureManagement` comprend deux filtres de fonctionnalités :

- `PercentageFilter` active l'indicateur de fonctionnalité sur la base d'un pourcentage.
- `TimeWindowFilter` active l'indicateur de fonctionnalité pendant un laps de temps spécifié.

Vous pouvez également créer un filtre de fonctionnalités qui implémente l'[interface Microsoft.FeatureManagement.IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Inscrire un filtre de fonctionnalités

Pour inscrire un filtre de fonctionnalités, vous devez appeler la méthode `AddFeatureFilter` en spécifiant le nom du filtre de fonctionnalités. Par exemple, le code suivant inscrit `PercentageFilter` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Configurer un filtre de fonctionnalités dans Azure App Configuration

Certains filtres de fonctionnalités comportent des paramètres supplémentaires. Par exemple, `PercentageFilter` active une fonctionnalité sur la base d'un pourcentage. Il comporte un paramètre qui définit le pourcentage à utiliser.

Vous pouvez configurer ces paramètres pour les indicateurs de fonctionnalités définis dans Azure App Configuration. Par exemple, procédez comme suit pour utiliser `PercentageFilter` afin d'activer l'indicateur de fonctionnalité pour 50 % des requêtes adressées à une application web :

1. Suivez les instructions données dans [Démarrage rapide : Ajouter des indicateurs de fonctionnalités à une application ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) pour créer une application web avec un indicateur de fonctionnalité.

1. Sur le portail Azure, accédez à votre magasin de configuration et cliquez sur **Gestionnaire de fonctionnalités**.

1. Cliquez sur le menu contextuel de l'indicateur de fonctionnalité *Beta* que vous avez créé dans le guide de démarrage rapide. Cliquez sur **Modifier**.

    > [!div class="mx-imgBorder"]
    > ![Modifier l'indicateur de fonctionnalité Beta](./media/edit-beta-feature-flag.png)

1. Sur l'écran **Modifier**, sélectionnez la case d'option **Activé**. Cliquez ensuite sur le bouton **Ajouter un filtre**. (L'étiquette de la case d'option **Activé** est remplacée par **Conditionnel**.)

1. Dans le champ **Clé**, entrez *Microsoft.Percentage*.

    > [!div class="mx-imgBorder"]
    > ![Ajouter un filtre de fonctionnalités](./media/feature-flag-add-filter.png)

1. Cliquez sur le menu contextuel en regard de la clé de filtre de fonctionnalité. Cliquez sur **Modifier les paramètres**.

    > [!div class="mx-imgBorder"]
    > ![Modifier les paramètres du filtre de fonctionnalités](./media/feature-flag-edit-filter-parameters.png)

1. Passez la souris sous l'en-tête **Nom** afin que les zones de texte apparaissent sur la grille. Entrez un **Nom** de *Valeur* et une **Valeur** de 50. Le champ **Valeur** indique le pourcentage de requêtes pour lesquelles le filtre de fonctionnalités doit être activé.

    > [!div class="mx-imgBorder"]
    > ![Définir les paramètres du filtre de fonctionnalités](./media/feature-flag-set-filter-parameters.png)

1. Cliquez sur **Appliquer** pour revenir à l'écran **Modifier l'indicateur de fonctionnalité**. Cliquez de nouveau sur **Appliquer** pour enregistrer les paramètres de l'indicateur de fonctionnalité.

1. L'**État** de l'indicateur de fonctionnalité est désormais *Conditionnel*. Cet état indique que l'indicateur de fonctionnalité sera activé ou désactivé à la demande, en fonction des critères appliqués par le filtre de fonctionnalité.

    > [!div class="mx-imgBorder"]
    > ![Indicateur de fonctionnalité conditionnel](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Filtres de fonctionnalités en action

Pour voir les effets de cet indicateur de fonctionnalité, lancez l'application et appuyez plusieurs fois sur le bouton **Actualiser** de votre navigateur. Vous constaterez que l'élément *Beta* apparaît environ 50 % du temps sur la barre d'outils. Le reste du temps, il est masqué car `PercentageFilter` désactive la fonctionnalité *Beta* pour un sous-ensemble de requêtes. La vidéo suivante illustre ce comportement.

> [!div class="mx-imgBorder"]
> ![PercentageFilter en action](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Vue d'ensemble de la gestion des fonctionnalités](./concept-feature-management.md)
