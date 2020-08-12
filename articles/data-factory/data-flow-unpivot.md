---
title: Transformation de suppression de tableau croisé dynamique dans le flux de données de mappage
description: Transformation de suppression de tableau croisé dynamique de flux de données de mappage Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: e7c0a4cd6e44994c4b002fcc2e5fde441cf22283
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541649"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Transformation de suppression de tableau croisé dynamique Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilisez la suppression de tableau croisé dynamique dans le flux de données de mappage ADF pour transformer un jeu de données non normalisé en version plus normalisée en développant les valeurs de colonnes multiples d’un seul enregistrement dans plusieurs enregistrements avec les mêmes valeurs dans une colonne unique.

![Transformation de suppression de tableau croisé dynamique](media/data-flow/unpivot1.png "Options de suppression de tableau croisé dynamique 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Dissocier par

![Transformation de suppression de tableau croisé dynamique](media/data-flow/unpivot5.png "Options de suppression de tableau croisé dynamique 2")

Commencez par définir les colonnes de groupement souhaitée pour l’agrégation de votre ajout de tableau croisé dynamique. Définissez une ou plusieurs colonnes à dissocier avec le signe + en regard de la liste des colonnes.

## <a name="unpivot-key"></a>Clé de suppression du tableau croisé dynamique

![Transformation de suppression de tableau croisé dynamique](media/data-flow/unpivot6.png "Options de suppression de tableau croisé dynamique 3")

La clé d’ajout du tableau croisé dynamique est la colonne qu’ADF ajoutera de la ligne dans la colonne. Par défaut, chaque valeur unique du jeu de données pour ce champ ajoutera un tableau croisé dynamique à une colonne. Toutefois, vous pouvez éventuellement entrer les valeurs du jeu de données que vous souhaitez ajouter en tant que tableau croisé dynamique aux valeurs de la colonne.

## <a name="unpivoted-columns"></a>Tableau croisé dynamique des colonnes supprimé

![Transformation de suppression de tableau croisé dynamique](media/data-flow//unpivot7.png "Options de suppression de tableau croisé dynamique 4")

Pour finir, choisissez l’agrégation que vous souhaitez utiliser pour les valeurs du tableau croisé dynamique ajouté et la façon dont vous souhaitez afficher les colonnes dans la nouvelle projection de sortie à partir de la transformation.

(Facultatif) Vous pouvez définir un modèle d’affectation de noms avec un préfixe, un milieu et un suffixe à ajouter à chaque nouveau nom de colonne à partir des valeurs de ligne.

Par exemple, l’ajout d’un tableau croisé dynamique « Ventes » par « Région » vous donnera simplement de nouvelles valeurs de colonne à partir de chaque valeur des ventes. Par exemple : "25", "50", "1000", ... Toutefois, si vous définissez une valeur de préfixe « Ventes », « Ventes » précédera les valeurs.

![Image représentant les colonnes PO, Vendor et Fruit avant et après une transformation UnPivot utilisant la colonne Fruit comme clé de suppression du tableau croisé dynamique.](media/data-flow/unpivot3.png)

La disposition des colonnes sur « Normal » regroupera toutes les colonnes du tableau croisé dynamique ajouté avec leurs valeurs agrégées. La disposition des colonnes sur « Latéral » alternera entre colonne et valeur.

![Transformation de suppression de tableau croisé dynamique](media/data-flow//unpivot7.png "Options de suppression de tableau croisé dynamique 5")

Le tableau croisé dynamique supprimé du jeu de résultats final affiche maintenant les totaux de colonnes sous forme de tableau croisé dynamique supprimé dans des valeurs de ligne distinctes.

## <a name="next-steps"></a>Étapes suivantes

Utilisez la [transformation Tableau croisé dynamique](data-flow-pivot.md) pour convertir des lignes en colonnes.
