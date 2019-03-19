---
title: Transformation d'ajout de tableau croisé dynamique de mappage de flux de données pour Azure Data Factory
description: Transformation d'ajout de tableau croisé dynamique de mappage de flux de données pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 5548a62218aaac2e4da3853e8e5d43a584922bc0
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569890"
---
# <a name="azure-data-factory-mapping-data-flow-pivot-transformation"></a>Transformation d'ajout de tableau croisé dynamique de mappage de flux de données pour Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilisez l’ajout d’un tableau croisé dynamique dans ADF Data Flow comme une agrégation où une ou plusieurs colonnes de regroupement voient leurs valeurs de lignes distinctes transformées en colonnes individuelles. En fait, vous pouvez faire ajouter un tableau croisé dynamique de valeurs de lignes à de nouvelles colonnes (transformer des données en métadonnées).

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot1.png "ajout de tableau croisé dynamique 1")

## <a name="group-by"></a>Regroupement

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot2.png "ajout de tableau croisé dynamique 2")

Commencez par définir les colonnes de groupement souhaitée pour l’agrégation de votre ajout de tableau croisé dynamique. Vous pouvez définir plus d’1 colonne ici avec le signe + en regard de la liste des colonnes.

## <a name="pivot-key"></a>Clé d’ajout de tableau croisé dynamique

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot3.png "ajout de tableau croisé dynamique 3")

La clé d’ajout du tableau croisé dynamique est la colonne qu’ADF ajoutera de la ligne dans la colonne. Par défaut, chaque valeur unique du jeu de données pour ce champ ajoutera un tableau croisé dynamique à une colonne. Toutefois, vous pouvez éventuellement entrer les valeurs du jeu de données que vous souhaitez ajouter en tant que tableau croisé dynamique aux valeurs de la colonne.

## <a name="pivoted-columns"></a>Colonnes ayant fait l’objet d’un ajout de tableau croisé dynamique

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot4.png "ajout de tableau croisé dynamique 4")

Pour finir, vous choisirez l’agrégation que vous souhaitez utiliser pour les valeurs du tableau croisé dynamique ajouté et la façon dont vous souhaitez afficher les colonnes dans la nouvelle projection de sortie à partir de la transformation.

(Facultatif) Vous pouvez définir un modèle d’affectation de noms avec un préfixe, un milieu et un suffixe à ajouter à chaque nouveau nom de colonne à partir des valeurs de ligne.

Par exemple, l’ajout d’un tableau croisé dynamique « Ventes » par « Région » génère de nouvelles valeurs de colonne à partir de chaque valeur des ventes, à savoir "25", "50", "1000", etc. Toutefois, si vous définissez une valeur de préfixe de « Sales- », chaque valeur de colonne ajouteriez « Sales- » au début de la valeur.

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot5.png "ajout de tableau croisé dynamique 5")

La disposition des colonnes sur « Normal » regroupera toutes les colonnes du tableau croisé dynamique ajouté avec leurs valeurs agrégées. La disposition des colonnes sur « Latéral » alternera entre colonne et valeur.

### <a name="aggregation"></a>Agrégation

Pour définir l’agrégation que vous souhaitez utiliser pour les valeurs d’ajout de tableau croisé dynamique, cliquez sur le champ en bas du volet Colonnes avec ajout de tableau croisé dynamique. Vous entrerez dans le Générateur d’expressions ADF Data Flow, où vous pouvez créer une expression d’agrégation et fournir un nom d’alias descriptif pour vos nouvelles valeurs agrégées.

Utiliser le langage d’expression ADF Data Flow pour décrire les transformations de colonnes ayant fait l’objet d’un ajout de tableau croisé dynamique dans le Générateur d’expressions : https://aka.ms/dataflowexpressions.

### <a name="how-to-rejoin-original-fields"></a>Comment rejoindre des champs d’origine
> [!NOTE]
> La transformation par ajout de tableau croisé dynamique projettera uniquement les colonnes utilisées dans l’agrégation, le regroupement et l’action d’ajout de tableau croisé dynamique. Si vous souhaitez inclure les autres colonnes à partir de l’étape précédente dans votre flux, utilisez une nouvelle branche à partir de l’étape précédente et utiliser le modèle de jointure réflexive pour connecter le flux avec les métadonnées d’origine.

## <a name="next-steps"></a>Étapes suivantes

Essayez le [transformation unpivot](data-flow-unpivot.md) pour transformer les valeurs de colonne en valeurs de ligne. 
