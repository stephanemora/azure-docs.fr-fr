---
title: Publier des notebooks dans la galerie des notebooks Azure Cosmos DB
description: Découvrez comment télécharger des notebooks à partir de la galerie publique, les modifier et publier vos propres notebooks dans la galerie.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 58ae61bc9e1736b13bb1802e2f39d5ada045cb6a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039257"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Publier des notebooks dans la galerie des notebooks Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Les blocs-notes Jupyter Notebooks intégrés à Azure Cosmos DB sont directement intégrés à vos comptes Azure Cosmos DB dans le portail Azure. À l’aide de ces notebooks, vous pouvez analyser et visualiser vos données à partir du portail Azure. Les notebooks intégrés pour Azure Cosmos DB sont actuellement disponibles dans de [nombreuses régions](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Pour utiliser des notebooks, [créez un compte](create-cosmosdb-resources-portal.md) ou [activez les notebooks sur un compte existant](enable-notebooks.md) dans l’une de ces régions

L’environnement de notebook dans le portail Azure contient des exemples publiés par l’équipe Azure Cosmos DB. Il dispose également d’une galerie publique dans laquelle vous pouvez publier et partager vos propres notebooks. Quand un notebook est publié dans la galerie, tous les utilisateurs Azure Cosmos DB peuvent l’afficher et l’utiliser. Dans cet article, vous allez apprendre à utiliser des notebooks de la galerie publique et à publier votre notebook dans la galerie.

## <a name="download-a-notebook-from-the-gallery"></a>Télécharger un notebook à partir de la galerie

Suivez les étapes ci-dessous pour afficher et télécharger des notebooks à partir de la galerie dans votre propre espace de travail des notebooks :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre compte Azure Cosmos DB qui est activé avec l’environnement de notebook.

1. Accédez à l’onglet **Data Explorer** > **Notebooks** > **Galerie** > **Galerie publique**.

1. Acceptez le [code de conduite](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/) avant d’afficher ou de publier des notebooks dans la galerie. Le code de conduite est journalisé par utilisateur, en fonction du niveau de l’abonnement. Quand vous passez à un autre abonnement, vous devez l’accepter de nouveau avant d’accéder à la galerie. Pour accepter le code de conduite, cochez la case et sélectionnez **Continuer** :

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="Accédez à la galerie publique des notebooks et acceptez le code de conduite.":::

1. Vous pouvez ensuite ajouter un notebook spécifique à vos favoris ou le télécharger. Quand vous téléchargez un notebook, il est copié dans votre espace de travail des notebooks où vous pouvez l’exécuter ou le modifier.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="Téléchargez un notebook à partir de la galerie vers votre espace de travail.":::

## <a name="publish-a-notebook-to-the-gallery"></a>Publier un notebook dans la galerie

Quand vous créez vos propres notebooks ou modifiez les notebooks existants pour les adapter à un autre scénario, vous pouvez souhaiter les publier dans la galerie. Une fois qu’un notebook est publié dans la galerie, les autres utilisateurs peuvent y accéder. Vous pouvez [explorer la galerie d’exemples de notebooks](https://cosmos.azure.com/gallery.html) pour afficher les notebooks actuellement disponibles.

Pour publier un notebook, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre compte Azure Cosmos DB qui est activé avec l’environnement de notebook.

1. Accédez à l’onglet **Data Explorer** > **Notebooks** > **Mes notebooks**.

1. Accédez au notebook que vous souhaitez publier. Sélectionnez le bouton **Enregistrer** dans la barre de commandes, puis sélectionnez **Publier dans la galerie** :

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="Choisissez un notebook à publier dans la galerie.":::

   Vous pouvez également trouver l’option **Publier dans la galerie** en sélectionnant le bouton **...** à côté du nom du notebook :

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="Une autre approche pour choisir un notebook à publier dans la galerie.":::

1. Remplissez le formulaire **Publier dans la galerie** en indiquant les informations suivantes :

   * **Nom :** indiquez un nom convivial pour identifier votre notebook.
   * **Description :** brève description des fonctionnalités de votre notebook.
   * **Étiquettes :** les étiquettes sont facultatives et servent à filtrer les résultats lors de la recherche par mot clé.
   * **Image de couverture :** image utilisée sur la page de couverture quand le notebook est publié. Vous pouvez choisir l'une des options suivantes :
   * **Image personnalisée** : vous pouvez charger une image à partir de votre ordinateur. Choisissez un fichier image avec les proportions 256 x 144.
   * **URL** : indiquez une URL accessible publiquement où se trouve l’image.
   * **Effectuer une capture d’écran** : une capture d’écran de votre notebook ouvert est automatiquement effectuée et chargée dans l’aperçu.
   * **Utilisez la première sortie d’affichage** : sortie de la première cellule ayant une sortie d’affichage. Les cellules qui affichent uniquement du contenu Markdown/texte ne sont pas considérées comme des sorties d’affichage.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="Remplissez le formulaire de publication dans la galerie.":::

   > [!NOTE]
   > Si vous utilisez l’option **Publier dans la galerie** à partir du bouton **...** à côté du nom du notebook, vous ne voyez pas toutes les options d’**image de couverture**. Cela est peut-être dû au fait que le notebook n’est pas ouvert et qu’Azure Cosmos DB n’y a pas accès pour effectuer une capture d’écran ou accéder à la première sortie d’affichage.

1. Vérifiez que l’aperçu semble correct et sélectionnez **Publier**. Une fois publié, ce notebook s’affiche dans la galerie publique des notebooks Azure Cosmos DB. Vérifiez que vous avez supprimé les données ou les sorties sensibles avant la publication. Le contenu du notebook est analysé pour s’assurer qu’il respecte les stratégies Microsoft avant d’être publié. Ce processus prend généralement quelques secondes. Si des violations sont détectées, un message s’affiche sous l’onglet Notification. Votre notebook ne sera pas publié si une violation est détectée. Vous devez supprimer le texte concerné et le publier à nouveau.

   > [!NOTE]
   > Une fois que les notebooks sont publiés dans la galerie publique, tous les utilisateurs Azure Cosmos DB peuvent les afficher. L’accès n’est pas limité par abonnement ou par organisation.

1. Une fois que le notebook est publié dans la galerie, vous pouvez le voir dans l’onglet **Mon travail publié**. Vous pouvez également retirer ou supprimer des notebooks que vous avez publiés dans la galerie publique.

1. Vous pouvez également signaler tout notebook qui ne respecte pas le code de conduite. Si une violation est détectée, Cosmos DB supprime automatiquement le notebook de la galerie. Si un notebook est supprimé, les utilisateurs peuvent voir celui-ci sous l’onglet **Mon travail publié** avec la note de suppression. Pour signaler un notebook, accédez à l’onglet **Data Explorer** > **Notebooks** > **Galerie** > **Galerie publique**. Ouvrez le notebook que vous souhaitez signaler, pointez sur le bouton **Aide** dans le coin droit et sélectionnez **Signaler un abus**.

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="Signalez un notebook qui ne respecte pas le code de conduite.":::

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les avantages des [blocs-notes Jupyter d’Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Utiliser les fonctionnalités et commandes des notebooks Python](use-python-notebook-features-and-commands.md)
* [Utiliser les fonctionnalités et commandes des notebooks C#](use-csharp-notebook-features-and-commands.md)
* [Importer des notebooks depuis un dépôt GitHub](import-github-notebooks.md)
