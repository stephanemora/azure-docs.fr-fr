---
title: Transformation de suppression de tableau croisé dynamique de mappage de flux de données pour Azure Data Factory
description: Transformation de suppression de tableau croisé dynamique de mappage de flux de données pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 160ff8bbcb8ce5a4f1f32245cc366281640c5919
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57854248"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory de Transformation Unpivot

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilisez la suppression de tableau croisé dynamique dans ADF pour le mappage de flux de données pour transformer un jeu de données non normalisé en version plus normalisée en développant les valeurs de colonnes multiples d’un seul enregistrement dans plusieurs enregistrements avec les mêmes valeurs dans une colonne unique.

![Supprimer le tableau croisé dynamique de la transformation](media/data-flow/unpivot1.png "Options de suppression du tableau croisé dynamique 1")

## <a name="ungroup-by"></a>Dissocier par

![Supprimer le tableau croisé dynamique de la transformation](media/data-flow/unpivot5.png "Options de suppression du tableau croisé dynamique 2")

Commencez par définir les colonnes de groupement souhaitée pour l’agrégation de votre ajout de tableau croisé dynamique. Définissez une ou plusieurs colonnes à dissocier avec le signe + en regard de la liste des colonnes.

## <a name="unpivot-key"></a>Clé de suppression du tableau croisé dynamique

![Supprimer le tableau croisé dynamique de la transformation](media/data-flow/unpivot6.png "Options de suppression du tableau croisé dynamique 3")

La clé d’ajout du tableau croisé dynamique est la colonne qu’ADF ajoutera de la ligne dans la colonne. Par défaut, chaque valeur unique du jeu de données pour ce champ ajoutera un tableau croisé dynamique à une colonne. Toutefois, vous pouvez éventuellement entrer les valeurs du jeu de données que vous souhaitez ajouter en tant que tableau croisé dynamique aux valeurs de la colonne.

## <a name="unpivoted-columns"></a>Tableau croisé dynamique des colonnes supprimé

![Supprimer le tableau croisé dynamique de la transformation](media/data-flow//unpivot7.png "Options de suppression du tableau croisé dynamique 4")

Pour finir, choisissez l’agrégation que vous souhaitez utiliser pour les valeurs du tableau croisé dynamique ajouté et la façon dont vous souhaitez afficher les colonnes dans la nouvelle projection de sortie à partir de la transformation.

(Facultatif) Vous pouvez définir un modèle d’affectation de noms avec un préfixe, un milieu et un suffixe à ajouter à chaque nouveau nom de colonne à partir des valeurs de ligne.

Par exemple, l’ajout d’un tableau croisé dynamique « Ventes » par « Région » vous donnera simplement de nouvelles valeurs de colonne à partir de chaque valeur des ventes. Par exemple :  "25", "50", "1000", ... Toutefois, si vous définissez une valeur de préfixe « Ventes », « Ventes » précédera les valeurs.

<img src="media/data-flow/unpivot3.png" width="400">

La disposition des colonnes sur « Normal » regroupera toutes les colonnes du tableau croisé dynamique ajouté avec leurs valeurs agrégées. La disposition des colonnes sur « Latéral » alternera entre colonne et valeur.

![Supprimer le tableau croisé dynamique de la transformation](media/data-flow//unpivot7.png "Options de suppression du tableau croisé dynamique 5")

Le tableau croisé dynamique supprimé du jeu de résultats final affiche maintenant les totaux de colonnes sous forme de tableau croisé dynamique supprimé dans des valeurs de ligne distinctes.

## <a name="next-steps"></a>Étapes suivantes

Utilisez le [transformation de tableau croisé dynamique](data-flow-pivot.md) aux lignes de tableau croisé dynamique à des colonnes.
