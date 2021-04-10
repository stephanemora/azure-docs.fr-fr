---
title: Utiliser des filtres de fonctionnalités pour activer les indicateurs de fonctionnalité conditionnels
titleSuffix: Azure App Configuration
description: Découvrez comment utiliser des filtres de fonctionnalités pour activer les indicateurs de fonctionnalité conditionnels
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 39455c4bc193cce036bd169c702b5c020d53d2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602243"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>Utiliser des filtres de fonctionnalités pour activer les indicateurs de fonctionnalité conditionnels

Les indicateurs de fonctionnalités vous permettent d'activer ou de désactiver des fonctionnalités dans votre application. Un indicateur de fonctionnalité simple est activé ou désactivé. L'application se comporte toujours de la même façon. Par exemple, vous pouvez déployer une nouvelle fonctionnalité derrière un indicateur de fonctionnalité. Lorsque l'indicateur de fonctionnalité est activé, tous les utilisateurs voient la nouvelle fonctionnalité. La désactivation de l'indicateur de fonctionnalité masque la nouvelle fonctionnalité.

En revanche, un _indicateur de fonctionnalité conditionnel_ permet d'activer ou de désactiver dynamiquement l'indicateur de fonctionnalité. L'application peut se comporter différemment, selon les critères de l'indicateur de fonctionnalité. Supposons que vous souhaitiez commencer par présenter votre nouvelle fonctionnalité à un petit sous-ensemble d'utilisateurs. Un indicateur de fonctionnalité conditionnel vous permet d'activer l'indicateur de fonctionnalité pour certains utilisateurs et de le désactiver pour d'autres. Les _filtres de fonctionnalités_ déterminent l'état de l'indicateur de fonctionnalité chaque fois qu'il est évalué.

La bibliothèque `Microsoft.FeatureManagement` comprend trois filtres de fonctionnalités :

- `PercentageFilter` active l'indicateur de fonctionnalité sur la base d'un pourcentage.
- `TimeWindowFilter` active l'indicateur de fonctionnalité pendant un laps de temps spécifié.
- `TargetingFilter` active l’indicateur de fonctionnalité pour les utilisateurs et les groupes spécifiés.

Vous pouvez également créer un filtre de fonctionnalités qui implémente l'[interface Microsoft.FeatureManagement.IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Inscrire un filtre de fonctionnalités

Pour inscrire un filtre de fonctionnalités, vous devez appeler la méthode `AddFeatureFilter` en spécifiant le nom de type du filtre de fonctionnalités souhaité. Par exemple, le code suivant inscrit `PercentageFilter` :

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

1. Dans l’écran **Modifier**, activez la case à cocher **Activer l’indicateur de fonctionnalité** si ce n’est déjà fait. Activez ensuite la case à cocher **Utiliser le filtre de fonctionnalités**, puis sélectionnez **Personnalisé**. 

1. Dans le champ **Nom**, sélectionnez *Microsoft.Percentage*.

    > [!div class="mx-imgBorder"]
    > ![Ajouter un filtre de fonctionnalités](./media/feature-flag-add-filter.png)

1. Cliquez sur le menu contextuel en regard du nom de filtre de la fonctionnalité. Cliquez sur **Modifier les paramètres de filtrage**.

    > [!div class="mx-imgBorder"]
    > ![Modifier les paramètres du filtre de fonctionnalités](./media/feature-flags-edit-filter-parameters.png)

1. Entrez un **Nom** de *Valeur* et une **Valeur** de 50. Le champ **Valeur** indique le pourcentage de requêtes pour lesquelles le filtre de fonctionnalités doit être activé.

    > [!div class="mx-imgBorder"]
    > ![Définir les paramètres du filtre de fonctionnalités](./media/feature-flag-set-filter-parameters.png)

1. Cliquez sur **Appliquer** pour revenir à l'écran **Modifier l'indicateur de fonctionnalité**. Cliquez de nouveau sur **Appliquer** pour enregistrer les paramètres de l'indicateur de fonctionnalité.

1. Dans la page **Gestionnaire de fonctionnalités**, la valeur de **Filtre de fonctionnalités** de l’indicateur de fonctionnalité est à présent *Personnalisé*. 

    > [!div class="mx-imgBorder"]
    > ![Indicateur de fonctionnalité répertorié avec une valeur de filtre de fonctionnalité « Personnalisé »](./media/feature-flag-filter-custom.png)

## <a name="feature-filters-in-action"></a>Filtres de fonctionnalités en action

Pour voir les effets de cet indicateur de fonctionnalité, lancez l'application et appuyez plusieurs fois sur le bouton **Actualiser** de votre navigateur. Vous constaterez que l'élément *Beta* apparaît environ 50 % du temps sur la barre d'outils. Le reste du temps, il est masqué car `PercentageFilter` désactive la fonctionnalité *Beta* pour un sous-ensemble de requêtes. La vidéo suivante illustre ce comportement.

> [!div class="mx-imgBorder"]
> ![TargetingFilter en action](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Activer le déploiement échelonné des fonctionnalités pour des audiences ciblées](./howto-targetingfilter-aspnet-core.md)
