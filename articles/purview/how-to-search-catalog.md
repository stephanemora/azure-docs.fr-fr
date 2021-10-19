---
title: 'Procédure : rechercher dans Data Catalog'
description: Cet article explique dans les grandes lignes comment parcourir le catalogue de données Azure Purview.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 329213fc37edae93d3871c1a52b6d5b73f8f76ed
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659561"
---
# <a name="search-the-azure-purview-data-catalog"></a>Effectuer une recherche dans Data Catalog Azure Purview

Une fois que les données sont analysées et ingérées dans le mappage de données Azure Purview, les consommateurs de données doivent facilement trouver les données nécessaires pour leurs charges de travail d’analyse ou de gouvernance. La découverte de données peut prendre du temps, car vous ne savez peut-être pas où trouver les données que vous souhaitez. Même après avoir trouvé les données, vous avez peut-être des doutes quant à la possibilité de faire confiance à ces données et d’y appliquer une dépendance.

L’objectif de la recherche dans Azure Purview est d’accélérer le processus de découverte des données afin de trouver rapidement les données importantes. Cet article explique comment effectuer une recherche dans le catalogue de données d’Azure Purview pour trouver rapidement les données que vous recherchez.

## <a name="search-the-catalog-for-assets"></a>Rechercher des ressources dans le catalogue

Vous pouvez accéder rapidement à la barre de recherche à partir de la barre supérieure de l’expérience utilisateur de Purview Studio. Sur la page d’accueil du catalogue de données, la barre de recherche se trouve au centre de l’écran.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Capture d’écran montrant l’emplacement de la barre de recherche dans Azure Purview" border="true":::

Une fois que vous avez cliqué sur la barre de recherche, vous voyez s’afficher l’historique de recherche et les ressources auxquelles vous avez accédé récemment dans le catalogue de données. Cela vous permet de récupérer rapidement à partir d’une exploration de données antérieure.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Capture d’écran montrant la barre de recherche avant l’entrée de mots clés" border="true":::

Entrez des mots clés qui vous aident à identifier votre ressource, notamment son nom, le type de données, des classifications et des termes du glossaire. À mesure que vous entrez des mots clés de recherche, Purview suggère de manière dynamique les ressources et les recherches qui peuvent répondre à vos besoins. Pour terminer votre recherche, cliquez sur « Afficher les résultats de la recherche » ou appuyez sur la touche Entrée.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Capture d’écran montrant la barre de recherche lorsqu’un utilisateur entre des mots clés" border="true":::

Une fois que vous avez effectué votre recherche, Purview retourne une liste de ressources de données dont un utilisateur est un lecteur de données et qui correspond aux mots clés entrés.

Le moteur de pertinence de Purview trie toutes les correspondances et les classe en fonction de leur utilité supposée pour un utilisateur. Par exemple, une table qui correspond à plusieurs mots clés à laquelle un gestionnaire de données a affecté des termes de glossaire et qui reçoit une description est susceptible d’être plus intéressante pour un consommateur de données qu’un dossier qui a été non annoté. Un grand ensemble de facteurs déterminent le score de pertinence d’une ressource et l’équipe de recherche Purview ajuste constamment le moteur de pertinence pour s’assurer que les meilleurs résultats de recherche vous sont adressés.

Si les premiers résultats n’incluent pas les ressources que vous recherchez, vous pouvez utiliser les facettes sur le côté gauche pour filtrer par métadonnées métier, telles que les termes de glossaire, les classifications et la collection qui les contient. Si vous êtes intéressé par un type de source de données particulier, tel que Azure Data Lake Storage Gen2 ou Azure SQL Database, vous pouvez utiliser le filtre de pilule du type de source pour affiner votre recherche.

> [!NOTE]
> La recherche ne retourne que les ressources dans les collections pour lesquels vous êtes un lecteur de données ou un conservateur. Pour plus d’informations, voir [créer et gérer des collections](how-to-create-and-manage-collections.md).

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Capture d’écran montrant les résultats d’une recherche" border="true":::

Pour certaines annotations, vous pouvez cliquer sur les points pour choisir entre une condition ET ou une condition OU. 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="Capture d’écran montrant comment choisir entre la condition ET ou OU" border="true":::

Une fois que vous avez trouvé l’élément multimédia que vous recherchez, vous pouvez le sélectionner pour afficher des détails tels que le schéma, le lignage et une liste de classification détaillée. Pour en savoir plus sur la page des détails de ressource, consultez [Gérer les ressources du catalogue](catalog-asset-details.md).

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Capture d’écran montrant la page de détails de la ressource" border="true":::

## <a name="bulk-edit-search-results"></a>Modifier en bloc les résultats de la recherche

Si vous souhaitez apporter des modifications à plusieurs ressources retournées par la fonction de recherche, Azure Purview vous permet de modifier les termes du glossaire, les classifications et les contacts en bloc. Pour en savoir plus, consultez l’article [Modifier en bloc des éléments](how-to-bulk-edit-assets.md).

## <a name="browse-the-data-catalog"></a>Parcourir le catalogue de données

Si la recherche est intéressante si vous savez ce que vous recherchez, il arrive que les consommateurs de données souhaitent explorer les données disponibles. Le catalogue de données Azure Purview offre une navigation qui permet aux utilisateurs d’explorer les données qui leur sont accessibles soit par regroupement, soit par le biais de la hiérarchie de chaque source de données dans le catalogue. Pour plus d’informations, consultez [parcourir le catalogue de données](how-to-browse-catalog.md).

## <a name="search-query-syntax"></a>Syntaxe des requêtes de recherche

Toutes les requêtes de recherche se composent de mots clés et d’opérateurs. Un mot clé est un élément qui fera partie des propriétés d’une ressource. Les mots clés potentiels peuvent être une classification, un terme du glossaire, une description de ressource ou un nom de ressource. Un mot clé peut être juste une partie de la propriété que vous cherchez à faire correspondre. Utilisez les mots clés et les opérateurs listés ci-dessous pour vous assurer qu’Azure Purview renvoie les ressources que vous recherchez.

Certains caractères, à savoir les espaces, les tirets et les virgules, sont interprétés comme des délimiteurs. La recherche d’une chaîne comme `hive-database` est identique à la recherche de deux mots clés `hive database`. 

Voici les opérateurs qui peuvent être utilisés pour composer une requête de recherche. Les opérateurs peuvent être combinés autant de fois que nécessaire dans une seule requête.

| Opérateur | Définition | Exemple |
| -------- | ---------- | ------- |
| OR | Spécifie qu’une ressource doit avoir au moins l’un des deux mots clés. Doit être en majuscules. Un espace blanc est également un opérateur OR.  | La requête `hive OR database` retourne les ressources qui contiennent « hive » ou « database », ou les deux. |
| AND | Spécifie qu’une ressource doit avoir les deux mots clés. Doit être en majuscules. | La requête `hive AND database` retourne les ressources qui contiennent à la fois « hive » et « database ». |
| NOT | Spécifie qu’une ressource ne peut pas contenir le mot clé à droite de la clause NOT. | La requête `hive NOT database` retourne les ressources qui contiennent « hive », mais pas « database ». |
| () | Regroupe un ensemble de mots clés et d’opérateurs. Lors de la combinaison de plusieurs opérateurs, les parenthèses spécifient l’ordre des opérations. | La requête `hive AND (database OR warehouse)` retourne les ressources qui contiennent « hive » et soit « database » soit « warehouse », ou les deux. |
| "" | Spécifie le contenu exact d’une expression à laquelle la requête doit correspondre. | La requête `"hive database"` retourne les ressources qui contiennent l’expression « hive database » dans leurs propriétés. |
| * | Caractère générique qui correspond à un ou plusieurs caractères. Ne peut pas être le premier caractère d’un mot clé. | La requête `dat*` retourne les ressources dont les propriétés commencent par « dat », par exemple « data » ou « database ». |
| ? | Caractère générique qui correspond à un caractère unique. Ne peut pas être le premier caractère d’un mot clé. | La requête `dat?` retourne les ressources dont les propriétés commencent par « dat » et qui comportent quatre lettres comme « date » ou « data ». |

> [!Note]
> Spécifiez toujours les opérateurs booléens (**AND**, **OR**, **NOT**) tout en majuscules. Dans le cas contraire, la casse n’a pas d’importance, ni d’espace supplémentaire.

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour créer, importer et exporter des termes de glossaire](how-to-create-import-export-glossary.md)
- [Guide pratique pour gérer les modèles de termes dans un glossaire métier](how-to-manage-term-templates.md)
