---
title: Annulation de la transformation de tableau croisé dynamique dans le flux de données de mappage
titleSuffix: Azure Data Factory & Azure Synapse
description: Apprenez-en davantage sur la transformation Unpivot (Supprimer le tableau croisé dynamique) du flux de données de mappage dans Azure Data Factory et Synapse Analytics.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 28c89622702435f178e241125545f8f0ac86eb18
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059584"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Annulation de la transformation de tableau croisé dynamique dans le flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Utilisez Unpivot (Supprimer le tableau croisé dynamique) dans le flux de données de mappage pour transformer un jeu de données non normalisé en version plus normalisée en développant les valeurs de colonnes multiples d’un seul enregistrement dans plusieurs enregistrements avec les mêmes valeurs dans une colonne unique.

:::image type="content" source="media/data-flow/unpivot1.png" alt-text="Capture d’écran montrant l’option Supprimer le tableau croisé dynamique sélectionnée dans le menu.":::

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Dissocier par

:::image type="content" source="media/data-flow/unpivot5.png" alt-text="Capture d’écran montrant les paramètres Supprimer le tableau croisé dynamique avec l’onglet Dissocier par sélectionné.":::

Commencez par définir les colonnes de dissociation souhaitées pour l'agrégation de votre suppression de tableau croisé dynamique. Définissez une ou plusieurs colonnes à dissocier avec le signe + en regard de la liste des colonnes.

## <a name="unpivot-key"></a>Clé de suppression du tableau croisé dynamique

:::image type="content" source="media/data-flow/unpivot6.png" alt-text="Capture d’écran montrant les paramètres Supprimer le tableau croisé dynamique avec l’onglet Clé de suppression du tableau croisé dynamique sélectionné.":::

La clé Unpivot (Supprimer le tableau croisé dynamique) est la colonne à partir de laquelle le service basculera d’une colonne à une ligne. Par défaut, chaque valeur unique comprise dans le jeu de données de ce champ ajoutera un tableau croisé dynamique à une ligne. Toutefois, si vous le souhaitez, vous pouvez entrer les valeurs du jeu de données que vous souhaitez ajouter en tant que tableau croisé dynamique aux valeurs de la ligne.

## <a name="unpivoted-columns"></a>Tableau croisé dynamique des colonnes supprimé

:::image type="content" source="media/data-flow//unpivot7.png" alt-text="Capture d’écran montrant les paramètres Supprimer le tableau croisé dynamique avec l’onglet Aperçu des données sélectionné.":::

Enfin, choisissez le nom de la colonne de stockage des valeurs des colonnes faisant l'objet de la suppression de tableau croisé dynamique qui sont transformées en lignes.

(Facultatif) Vous pouvez supprimer des lignes contenant des valeurs Null.

Par exemple, SumCost est le nom de colonne choisi dans l'exemple présenté ci-dessus.

:::image type="content" source="media/data-flow/unpivot3.png" alt-text="Image représentant les colonnes PO, Vendor et Fruit avant et après une transformation UnPivot utilisant la colonne Fruit comme clé de suppression du tableau croisé dynamique.":::

La disposition des colonnes sur « Normal » regroupera toutes les nouvelles colonnes concernées par la suppression de tableau croisé dynamique à partir d'une seule valeur. La disposition des colonnes sur « Latéral » regroupera les nouvelles colonnes concernées par la suppression de tableau croisé dynamique générées à partir d'une colonne existante.

:::image type="content" source="media/data-flow//unpivot7.png" alt-text="Capture d’écran montrant le résultat de la transformation.":::

Le tableau croisé dynamique supprimé du jeu de résultats final affiche maintenant les totaux de colonnes sous forme de tableau croisé dynamique supprimé dans des valeurs de ligne distinctes.

## <a name="next-steps"></a>Étapes suivantes

Utilisez la [transformation Tableau croisé dynamique](data-flow-pivot.md) pour convertir des lignes en colonnes.
