---
title: Se connecter une base de connaissances avec Power BI
titleSuffix: Azure Cognitive Search
description: Connectez une base de connaissances Recherche cognitive Azure à Power BI à des fins d’analyse et d’exploration.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 97a0025437b7f055f9fcf2e7860e926e9693cde6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766926"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Connecter une base de connaissances à Power BI

Cet article explique comment connecter et explorer une base de connaissances à l’aide de Power Query dans l’application Power BI Desktop. Vous pouvez commencer plus rapidement avec des modèles, ou créer un tableau de bord personnalisé à partir de rien.

Un magasin de connaissances composé de tables dans stockage Azure fonctionnent mieux dans Power BI. Si les tables contiennent des projections provenant des mêmes compétences et du même groupe de projection, vous pouvez facilement les « joindre » pour générer des visualisations de table qui incluent des champs de tables associées.

Suivez les étapes décrites dans cet article à l’aide des exemples de données et du magasin de connaissances [créés dans le Portail Azure](knowledge-store-create-portal.md) ou par le biais des [API Postman et REST](knowledge-store-create-rest.md). 

## <a name="connect-to-azure-storage"></a>Se connecter à Stockage Azure

1. Lancez [Power BI Desktop](https://powerbi.microsoft.com/downloads/) et sélectionnez **Obtenir les données**. 

1. Dans la fenêtre **Obtenir les données**, sélectionnez **Azure**, puis **Stockage Table Azure**.

1. Sélectionnez **Connecter**.

1. Pour **Nom du compte ou URL**, entrez le nom de votre compte Stockage Azure (l’URL complète est créée automatiquement).

1. Si vous y êtes invité, entrez la clé du compte de stockage.

## <a name="set-up-tables"></a>Configurer des tables

1. Cochez la case en regard de toutes les tables qui ont été créées à partir des mêmes compétences, puis sélectionnez **Charger**.

   ![Charger les tables](media/knowledge-store-connect-power-bi/power-bi-load-tables.png "Charger les tables")

1. Dans le ruban supérieur, sélectionnez **Transformer les données** pour ouvrir l’**Éditeur Power Query**.

   ![Ouvrir Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Ouvrir Power Query")

1. Sélectionnez *hotelReviewsSsDocument*, puis supprimez les colonnes *PartitionKey*, *RowKey* et *Timestamp*. 

   ![Modifier les tables](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Modifier les tables")

1. En haut à droite de la table, cliquez sur l’icône montrant des flèches opposées pour développer le *Contenu*. Une fois que la liste des colonnes s’affiche, sélectionnez toutes les colonnes, puis désélectionnez les colonnes commençant par « métadonnées ». Cliquez sur **OK** pour afficher les colonnes sélectionnées.

   ![Développer le contenu](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Développer le contenu")

1. Changez le type de données des colonnes suivantes en cliquant en haut à gauche de la colonne sur l’icône ABC-123.

   + Pour *Content.latitude* et *Content.longitude*, sélectionnez **Nombre décimal**.
   + Pour *Content.reviews_date* et *Content.reviews_dateAdded*, sélectionnez **Date/Heure**.

   ![Changer les types de données](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Changer les types de données")

1. Sélectionnez *hotelReviewsSsPages*, puis répétez les étapes 9 et 10 pour supprimer les colonnes et développer le *Contenu*.

1. Sélectionnez *hotelReviewsSsKeyPhrases*, puis répétez les étapes 9 et 10 pour supprimer les colonnes et développer le *Contenu*. Il n’y a aucune modification de type de données pour cette table.

1. Dans la barre de commandes, cliquez sur **Fermer et appliquer**.

## <a name="check-table-relationships"></a>Vérifier les relations entre les tables

1. Cliquez sur la vignette Modèle dans le volet de navigation de gauche, puis vérifiez que Power BI montre les relations entre les trois tables.

   ![Valider les relations](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Valider les relations")

1. Double-cliquez sur chaque relation, puis vérifiez que l’option **Direction du filtre croisé** a la valeur **Les deux**.  Cela permet d’actualiser les visuels quand un filtre est appliqué.

## <a name="build-a-report"></a>Créer un rapport

1. Cliquez sur la vignette Rapport dans le volet de navigation gauche pour explorer les données dans des visualisations. Pour les champs de texte, les tables et les cartes s’avèrent être des visualisations utiles.

1. Choisissez des champs dans chacune des trois tables pour renseigner la table ou la carte.

   ![Générer un rapport de table](media/knowledge-store-connect-power-bi/power-bi-table-report.png "Construire un rapport de table")

## <a name="sample-power-bi-template---azure-portal-only"></a>Exemple de modèle Power BI - Portail Azure uniquement

Lorsque vous créez une [base de connaissances à l’aide du portail Azure](knowledge-store-create-portal.md), vous avez la possibilité de télécharger un [modèle Power BI](https://github.com/Azure-Samples/cognitive-search-templates) dans la deuxième page de l’Assistant **Importer des données**. Ce modèle propose plusieurs visualisations, comme WordCloud et Network Navigator, pour le contenu textuel. 

Cliquez sur **Obtenir le modèle Power BI** dans la page **Ajouter des compétences cognitives** pour récupérer et télécharger le modèle à partir de son emplacement GitHub public. L’Assistant modifie le modèle pour qu’il s’adapte à la forme de vos données, telles qu’elles sont capturées dans les projections de la base de connaissances spécifiées dans l’Assistant. C’est la raison pour laquelle le modèle que vous téléchargez varie chaque fois que vous exécutez l’Assistant, conformément aux entrées de données et sélections de compétences différentes.

![Exemple de modèle Recherche cognitive Azure Power BI](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Exemple de modèle Power BI")

> [!NOTE]
> Le modèle est téléchargé à mi-parcours de l’Assistant. Vous devez attendre que la base de connaissances soit réellement créée dans le Stockage Table Azure avant de pouvoir l’utiliser.

## <a name="video-introduction"></a>Introduction vidéo

Pour une démonstration de l’utilisation de Power BI avec un magasin de connaissances, regardez la vidéo suivante.

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tableaux dans les rapports et les tableaux de bord Power BI](/power-bi/visuals/power-bi-visualization-tables)