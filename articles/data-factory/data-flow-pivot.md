---
title: Transformation d'ajout de tableau croisé dynamique de mappage de flux de données pour Azure Data Factory
description: Les données des lignes pour les colonnes à l’aide d’Azure Data Factory mappage flux Pivot Transformation des données de tableau croisé dynamique
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e16cac281b77f3ca93d9ef358ae806203bc8b663
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794361"
---
# <a name="azure-data-factory-pivot-transformation"></a>Transformation de tableau croisé dynamique de fabrique de données Azure
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilisez l’ajout d’un tableau croisé dynamique dans ADF Data Flow comme une agrégation où une ou plusieurs colonnes de regroupement voient leurs valeurs de lignes distinctes transformées en colonnes individuelles. En fait, vous pouvez faire ajouter un tableau croisé dynamique de valeurs de lignes à de nouvelles colonnes (transformer des données en métadonnées).

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot1.png "ajout de tableau croisé dynamique 1")

## <a name="group-by"></a>Regrouper par

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot2.png "ajout de tableau croisé dynamique 2")

Commencez par définir les colonnes de groupement souhaitée pour l’agrégation de votre ajout de tableau croisé dynamique. Vous pouvez définir plus d’1 colonne ici avec le signe + en regard de la liste des colonnes.

## <a name="pivot-key"></a>Clé de tableau croisé dynamique

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot3.png "ajout de tableau croisé dynamique 3")

La clé d’ajout du tableau croisé dynamique est la colonne qu’ADF ajoutera de la ligne dans la colonne. Par défaut, chaque valeur unique du jeu de données pour ce champ ajoutera un tableau croisé dynamique à une colonne. Toutefois, vous pouvez éventuellement entrer les valeurs du jeu de données que vous souhaitez ajouter en tant que tableau croisé dynamique aux valeurs de la colonne. Il s’agit de la colonne qui détermine les nouvelles colonnes qui seront créés.

## <a name="pivoted-columns"></a>Colonnes croisée dynamiques

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot4.png "ajout de tableau croisé dynamique 4")

Pour finir, vous choisirez l’agrégation que vous souhaitez utiliser pour les valeurs du tableau croisé dynamique ajouté et la façon dont vous souhaitez afficher les colonnes dans la nouvelle projection de sortie à partir de la transformation.

(Facultatif) Vous pouvez définir un modèle d’affectation de noms avec un préfixe, un milieu et un suffixe à ajouter à chaque nouveau nom de colonne à partir des valeurs de ligne.

Par exemple, l’ajout d’un tableau croisé dynamique « Ventes » par « Région » génère de nouvelles valeurs de colonne à partir de chaque valeur des ventes, à savoir "25", "50", "1000", etc. Toutefois, si vous définissez une valeur de préfixe de « Sales- », chaque valeur de colonne ajouteriez « Sales- » au début de la valeur.

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot5.png "ajout de tableau croisé dynamique 5")

La disposition des colonnes sur « Normal » regroupera toutes les colonnes du tableau croisé dynamique ajouté avec leurs valeurs agrégées. La disposition des colonnes sur « Latéral » alternera entre colonne et valeur.

### <a name="aggregation"></a>Agrégation

Pour définir l’agrégation que vous souhaitez utiliser pour les valeurs d’ajout de tableau croisé dynamique, cliquez sur le champ en bas du volet Colonnes avec ajout de tableau croisé dynamique. Vous entrerez dans le Générateur d’expressions ADF Data Flow, où vous pouvez créer une expression d’agrégation et fournir un nom d’alias descriptif pour vos nouvelles valeurs agrégées.

Utiliser le langage d’expression ADF Data Flow pour décrire les transformations de colonnes ayant fait l’objet d’un ajout de tableau croisé dynamique dans le Générateur d’expressions : https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Métadonnées de tableau croisé dynamique

La transformation Pivot produira des nouveaux noms de colonne sont dynamiques en fonction de vos données entrantes. La clé de tableau croisé dynamique génère les valeurs pour chaque nouveau nom de colonne. Si vous ne faites pas spécifier des valeurs individuelles et pour créer des noms de colonnes dynamiques pour chaque valeur unique dans votre clé de tableau croisé dynamique, puis l’interface utilisateur n’affichera pas les métadonnées dans inspecter et qu’il n’y aura aucune propagation de la colonne à la transformation de récepteur. Si vous définissez des valeurs pour la clé de tableau croisé dynamique, puis ADF peut déterminer les nouveaux noms de colonne et ces noms de colonnes seront disponibles dans l’inspecter et mappage du récepteur.

### <a name="landing-new-columns-in-sink"></a>Lancement de nouvelles colonnes dans le récepteur

Même avec les noms de colonnes dynamiques dans le tableau croisé dynamique, vous pouvez toujours récepteur vos nouveaux noms de colonnes et les valeurs dans votre magasin de destination. Uniquement activé « Autoriser les dérives de schéma » dans vos paramètres de récepteur. Vous ne verrez pas les nouveaux noms dynamiques dans vos métadonnées de colonne, mais l’option de dérive de schéma vous permettra de charger les données.

### <a name="view-metadata-in-design-mode"></a>Afficher les métadonnées en mode design

Si vous souhaitez afficher les nouveaux noms de colonne en tant que métadonnées dans inspecter et que vous souhaitez afficher les colonnes propager explicitement à la transformation de récepteur, puis définissez des valeurs explicites dans l’onglet de la clé de tableau croisé dynamique.

### <a name="how-to-rejoin-original-fields"></a>Comment rejoindre des champs d’origine
La transformation par ajout de tableau croisé dynamique projettera uniquement les colonnes utilisées dans l’agrégation, le regroupement et l’action d’ajout de tableau croisé dynamique. Si vous souhaitez inclure les autres colonnes à partir de l’étape précédente dans votre flux, utilisez une nouvelle branche à partir de l’étape précédente et utiliser le modèle de jointure réflexive pour connecter le flux avec les métadonnées d’origine.

## <a name="next-steps"></a>Étapes suivantes

Essayez le [transformation unpivot](data-flow-unpivot.md) pour transformer les valeurs de colonne en valeurs de ligne. 
