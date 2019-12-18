---
title: Transformation d’ajout de tableau croisé dynamique dans le flux de données de mappage
description: Ajouter un tableau croisé dynamique à l’aide de la transformation d’ajout de tableau croisé dynamique de flux de données de mappage pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 8f23b5e61e1aee83172a12466fac8d5b5003fea8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930284"
---
# <a name="azure-data-factory-pivot-transformation"></a>Transformation d’ajout de tableau croisé dynamique Azure Data Factory


Utilisez l’ajout d’un tableau croisé dynamique dans ADF Data Flow comme une agrégation où une ou plusieurs colonnes de regroupement voient leurs valeurs de lignes distinctes transformées en colonnes individuelles. En fait, vous pouvez faire ajouter un tableau croisé dynamique de valeurs de lignes à de nouvelles colonnes (transformer des données en métadonnées).

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot1.png "ajouter un tableau croisé dynamique 1")

## <a name="group-by"></a>Regrouper par

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot2.png "ajouter un tableau croisé dynamique 2")

Commencez par définir les colonnes de groupement souhaitée pour l’agrégation de votre ajout de tableau croisé dynamique. Vous pouvez définir plus d’1 colonne ici avec le signe + en regard de la liste des colonnes.

## <a name="pivot-key"></a>Clé de tableau croisé dynamique

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot3.png "ajouter un tableau croisé dynamique 3")

La clé d’ajout du tableau croisé dynamique est la colonne qu’ADF ajoutera de la ligne dans la colonne. Par défaut, chaque valeur unique du jeu de données pour ce champ ajoutera un tableau croisé dynamique à une colonne. Toutefois, vous pouvez éventuellement entrer les valeurs du jeu de données que vous souhaitez ajouter en tant que tableau croisé dynamique aux valeurs de la colonne. Il s’agit de la colonne qui détermine les nouvelles colonnes qui seront créées.

## <a name="pivoted-columns"></a>Colonnes ayant fait l’objet d’un ajout de tableau croisé dynamique

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot4.png "ajouter un tableau croisé dynamique 4")

Pour finir, vous choisirez l’agrégation que vous souhaitez utiliser pour les valeurs du tableau croisé dynamique ajouté et la façon dont vous souhaitez afficher les colonnes dans la nouvelle projection de sortie à partir de la transformation.

(Facultatif) Vous pouvez définir un modèle d’affectation de noms avec un préfixe, un milieu et un suffixe à ajouter à chaque nouveau nom de colonne à partir des valeurs de ligne.

Par exemple, l’ajout d’un tableau croisé dynamique « Ventes » par « Région » génère de nouvelles valeurs de colonne à partir de chaque valeur des ventes, à savoir "25", "50", "1000", etc. Toutefois, si vous définissez une valeur de préfixe « Ventes- », chaque valeur de colonne ajoutera « Ventes- » au début de la valeur.

![Options d’ajout de tableau croisé dynamique](media/data-flow/pivot5.png "ajouter un tableau croisé dynamique 5")

La disposition des colonnes sur « Normal » regroupera toutes les colonnes du tableau croisé dynamique ajouté avec leurs valeurs agrégées. La disposition des colonnes sur « Latéral » alternera entre colonne et valeur.

### <a name="aggregation"></a>Agrégation

Pour définir l’agrégation que vous souhaitez utiliser pour les valeurs d’ajout de tableau croisé dynamique, cliquez sur le champ en bas du volet Colonnes avec ajout de tableau croisé dynamique. Vous entrerez dans le Générateur d’expressions ADF Data Flow, où vous pouvez créer une expression d’agrégation et fournir un nom d’alias descriptif pour vos nouvelles valeurs agrégées.

Utiliser le langage d’expression ADF Data Flow pour décrire les transformations de colonnes ayant fait l’objet d’un ajout de tableau croisé dynamique dans le Générateur d’expressions : https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Métadonnées de tableau croisé dynamique

La transformation Ajout de tableau croisé dynamique produit de nouveaux noms de colonnes dynamiques en fonction de vos données entrantes. La clé de tableau croisé dynamique génère les valeurs pour chaque nouveau nom de colonne. Si vous ne spécifiez pas de valeurs individuelles et que vous souhaitez créer des noms de colonnes dynamiques pour chaque valeur unique dans votre clé de tableau croisé dynamique, l’interface utilisateur n’affichera pas les métadonnées dans Inspecter et il n’y aura aucune propagation de colonne sur la transformation Récepteur. Si vous définissez des valeurs pour la clé de tableau croisé dynamique, ADF peut déterminer les nouveaux noms de colonnes et ces noms de colonnes seront disponibles dans le mappage Inspecter et Récepteur.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Générer un nouveau modèle à partir de colonnes dynamiques

Un tableau croisé dynamique génère de nouveaux noms de colonnes de manière dynamique en fonction des valeurs de ligne. Vous pouvez convertir ces nouvelles colonnes en métadonnées qui peuvent ensuite être référencées dans votre flux de données. Pour ce faire, cliquez sur l’onglet Aperçu des données. Toutes les nouvelles colonnes générées par la transformation de votre tableau croisé dynamique s’affichent avec une icône « dérivée » dans l’en-tête du tableau. Cliquez sur le bouton « Mapper les dérivées » pour transformer ces nouvelles colonnes en métadonnées, de sorte qu’elles fassent partie du modèle de votre flux de données.

![Colonnes de tableau croisé dynamique](media/data-flow/newpivot1.png "Mappage de colonnes dérivées de tableau croisé dynamique")

### <a name="landing-new-columns-in-sink"></a>Chargement de nouvelles colonnes dans Récepteur

Même avec des noms de colonnes dynamiques dans Ajout de tableau croisé dynamique, vous pouvez toujours recevoir vos nouveaux noms de colonnes et nouvelles valeurs dans votre magasin de destination. Il vous suffit d’activer « Autoriser la dérive de schéma » dans vos paramètres de récepteur. Vous ne verrez pas les nouveaux noms dynamiques dans vos métadonnées de colonnes, mais l’option de dérive de schéma vous permettra de charger les données.

### <a name="view-metadata-in-design-mode"></a>Afficher les métadonnées en mode design

Si vous souhaitez voir les nouveaux noms de colonnes en tant que métadonnées dans Inspecter, et que vous souhaitez que les colonnes se propagent explicitement sur la transformation Récepteur, définissez des valeurs explicites sous l’onglet Clé de tableau croisé dynamique.

### <a name="how-to-rejoin-original-fields"></a>Comment rejoindre des champs d’origine
La transformation par ajout de tableau croisé dynamique projettera uniquement les colonnes utilisées dans l’agrégation, le regroupement et l’action d’ajout de tableau croisé dynamique. Si vous souhaitez inclure les autres colonnes de l’étape précédente dans votre flux, utilisez une nouvelle branche de l’étape précédente et le modèle de jointure réflexive pour connecter le flux aux métadonnées d’origine.

## <a name="next-steps"></a>Étapes suivantes

Essayez la [transformation Supprimer le tableau croisé dynamique](data-flow-unpivot.md) pour transformer des valeurs de colonnes en valeurs de lignes. 
