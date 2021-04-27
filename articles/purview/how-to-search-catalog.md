---
title: 'Procédure : rechercher dans Data Catalog'
description: Cet article offre une vue d’ensemble de la recherche dans un catalogue de données.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 178604335968c3664bde51c144759c1c040c359d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564911"
---
# <a name="search-the-azure-purview-data-catalog"></a>Effectuer une recherche dans Data Catalog Azure Purview

La découverte de données constitue la première étape d’une charge de travail d’analytique données ou de gouvernance des données pour les consommateurs de données. La découverte de données peut prendre du temps, car vous ne savez peut-être pas où trouver les données que vous souhaitez. Même après avoir trouvé les données, vous avez peut-être des doutes quant à la possibilité de faire confiance à ces données et d’y appliquer une dépendance.

L’objectif de la recherche dans Azure Purview est d’accélérer le processus de découverte des données afin de trouver rapidement les données importantes. Cet article explique comment effectuer une recherche dans le catalogue de données d’Azure Purview pour trouver rapidement les données que vous recherchez.

## <a name="search-the-catalog-for-assets"></a>Rechercher des ressources dans le catalogue

Dans Azure Purview, la barre de recherche se trouve en haut de l’expérience utilisateur du studio Purview.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Capture d’écran montrant l’emplacement de la barre de recherche dans Azure Purview" border="true":::

Lorsque vous cliquez sur la barre de recherche, vous pouvez voir l’historique de vos recherches récentes et les ressources récemment consultées. Sélectionnez « Afficher tout » pour voir toutes les ressources récemment consultées.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Capture d’écran montrant la barre de recherche avant l’entrée de mots clés" border="true":::

Entrez des mots clés qui vous aident à identifier votre ressource, notamment son nom, le type de données, des classifications et des termes du glossaire. Au fur et à mesure que vous saisissez des mots clés relatifs à la ressource souhaitée, Azure Purview affiche des suggestions sur les éléments à rechercher et les possibles correspondances de ressources. Pour terminer votre recherche, cliquez sur « Afficher les résultats de la recherche » ou appuyez sur la touche Entrée.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Capture d’écran montrant la barre de recherche lorsqu’un utilisateur entre des mots clés" border="true":::

La page des résultats de la recherche affiche une liste des ressources qui correspondent aux mots clés fournis par ordre de pertinence. Plusieurs facteurs peuvent influencer le score de pertinence d’une ressource. Vous pouvez filtrer davantage la liste en sélectionnant des magasins de données, des classifications, des contacts, des étiquettes et des termes de glossaire spécifiques qui s’appliquent à la ressource que vous recherchez.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Capture d’écran montrant les résultats d’une recherche" border="true":::

 Cliquez sur la ressource de votre choix pour afficher la page des détails dans laquelle vous pouvez afficher les propriétés, notamment le schéma, la traçabilité des données et les propriétaires de la ressource.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Capture d’écran montrant la page de détails de la ressource" border="true":::

## <a name="search-query-syntax"></a>Syntaxe des requêtes de recherche

Toutes les requêtes de recherche se composent de mots clés et d’opérateurs. Un mot clé est un élément qui fera partie des propriétés d’une ressource. Les mots clés potentiels peuvent être une classification, un terme du glossaire, une description de ressource ou un nom de ressource. Un mot clé peut être juste une partie de la propriété que vous cherchez à faire correspondre. Utilisez les mots clés et les opérateurs listés ci-dessous pour vous assurer qu’Azure Purview renvoie les ressources que vous recherchez. 

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
