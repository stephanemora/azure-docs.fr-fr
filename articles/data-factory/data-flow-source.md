---
title: Azure Data Factory - Transformation source des flux de données de mappage
description: Azure Data Factory - Transformation source des flux de données de mappage
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 38a01b4f81b76ba90a5fda4909d0e65e6307057e
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408712"
---
# <a name="mapping-data-flow-source-transformation"></a>Transformation source des flux de données de mappage

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La transformation source configure la source de données que vous souhaitez utiliser pour importer des données dans votre flux de données. Vous pouvez avoir plusieurs transformations source au sein d’un même flux de données. Commencez toujours par concevoir vos flux de données avec une source.

> [!NOTE]
> Chaque flux de données nécessite au moins une transformation source. Ajoutez autant de sources que nécessaire pour effectuer vos transformations de données. Vous pouvez joindre ces sources ensemble à l’aide d’une transformation de jointure ou d’union.

![Options de transformation source](media/data-flow/source.png "source")

Chaque transformation source de flux de données doit être associée à un seul jeu de données Data Factory, qui définit le format et l’emplacement des données qui doivent être lues ou écrites. Pour utiliser plusieurs fichiers à la fois, vous pouvez utiliser des caractères génériques et des listes de fichiers dans votre source.

## <a name="data-flow-staging-areas"></a>Zones intermédiaires des flux de données

Les flux de données fonctionnent avec des jeux de données « intermédiaires » qui se trouvent tous dans Azure. Ces jeux de données sont utilisés pour mettre en lots les données en vue d’effectuer des transformations. Data Factory a accès à près de 80 connecteurs natifs. Pour inclure les données dans votre flux de données à partir de ces autres sources, commencez par mettre en lot ces données dans l’une des zones intermédiaires de jeux de données Data Flow à l’aide de l’activité de copie.

## <a name="options"></a>Options

### <a name="allow-schema-drift"></a>Autoriser la dérive de schéma
Sélectionnez Autoriser la dérive de schéma si les colonnes sources seront amenées à changer souvent. Ce paramètre autorise tous les champs entrants provenant de votre source à circuler jusqu’au récepteur, via les transformations.

### <a name="validate-schema"></a>Valider le schéma

![Source publique](media/data-flow/source1.png "Source publique 1")

Si la version entrante de la source de données ne correspond pas au schéma défini, l’exécution du flux de données échoue.

### <a name="sampling"></a>échantillonnage
Utilisez l’échantillonnage pour limiter le nombre de lignes provenant de la source.  Cela se révèle utile lorsque vous avez besoin d’un bref aperçu de vos données sources à des fins de test et de débogage.

## <a name="define-schema"></a>Définir le schéma

![Transformation source](media/data-flow/source2.png "Source 2")

Pour les fichiers sources qui ne sont pas fortement typés (par exemple, les fichiers plats par opposition aux fichiers Parquet), vous devez définir les types de données pour chaque champ de la transformation source. Vous pouvez ensuite modifier les noms de colonnes dans une transformation de sélection ainsi que les types de données dans une transformation de colonne dérivée. 

![Transformation source](media/data-flow/source003.png "Types de données")

Pour les sources fortement typées, vous pouvez modifier le 

### <a name="optimize"></a>Optimisation

![Partitions sources](media/data-flow/sourcepart.png "Partitionnement")

Sous l’onglet Optimiser de la transformation source, vous verrez un autre type de partitionnement appelé « Source ». Ce type n’est disponible que si vous avez sélectionné la base de données SQL Azure comme votre source. Cela est dû au fait que le fichier ADF va vouloir paralléliser les connexions pour envoyer des requêtes volumineuses à votre source de base de données SQL Azure.

Le partitionnement des données de votre source de base de données SQL est facultatif. Toutefois, il se révèle utile pour les requêtes volumineuses. Deux options s'offrent à vous :

### <a name="column"></a>Colonne

Sélectionnez une colonne de votre table source pour effectuer la partition. Vous devez également définir le nombre maximal de connexions.

### <a name="query-condition"></a>Condition de requête

Si vous le souhaitez, vous pouvez choisir de partitionner les connexions en fonction d’une requête. Pour cette option, il vous suffit d’ajouter le contenu d’un prédicat WHERE (par exemple, année > 1980).

## <a name="source-file-management"></a>Gestion des fichiers sources
![Nouveaux paramètres sources](media/data-flow/source2.png "Nouveaux paramètres")

* Utiliser un chemin comportant un caractère générique pour choisir une série de fichiers de votre dossier source qui correspondent à un modèle. Cela remplacera tous les fichiers que vous avez définis dans la définition de votre jeu de données.
* Liste de fichiers. Identique à un ensemble de fichiers. Pointe vers un fichier texte que vous créez avec la liste des fichiers avec chemin relatif à traiter.
* Colonne où stocker le nom du fichier. Stocke le nom du fichier de la source dans une colonne de vos données. Entrez un nouveau nom pour stocker la chaîne de nom de fichier.
* Une fois terminé (vous pouvez choisir de ne rien faire avec le fichier source après l’exécution du flux de données, de supprimer un ou plusieurs fichiers sources ou de déplacer les fichiers sources). Pour le déplacement, les chemins sont des chemins relatifs.

### <a name="sql-datasets"></a>Jeux de données SQL

Lorsque vous utilisez la base de données SQL Azure ou Azure SQL Data Warehouse comme votre source, vous disposez d’options supplémentaires.

* Requête : entrez une requête SQL pour votre source. Si vous définissez une requête, les tables que vous avez choisies dans le jeu de données seront remplacées. Notez que les clauses OrderBy ne sont pas prises en charge.

* Taille du lot : entrez la taille que doivent avoir les lots créés à partir d’un large volume de données.

> [!NOTE]
> Les paramètres d’opération de fichier ne sont appliqués que lorsque le flux de données est exécuté à partir d’une exécution de pipeline (débogage ou exécution) à l’aide de l’activité Exécuter le flux de données dans un pipeline. Les opérations de fichiers ne peuvent pas s’exécuter dans le mode de débogage de Data Flow.

### <a name="projection"></a>Projection

![Projection](media/data-flow/source3.png "Projection")

Comme pour les schémas des jeux de données, la projection de la source définit les colonnes, les types et les formats des données sources. Si vous avez un fichier texte sans schéma défini, cliquez sur « Détecter le type de données » pour demander à ADF d’échantillonner et de déduire les types de données. Vous pouvez configurer les formats de données par défaut de façon à être détectés automatiquement l’aide du bouton « Définir le format par défaut ». Vous pouvez modifier les types des données de colonne lors d’une prochaine transformation de colonne dérivée. Les noms de colonnes peuvent être modifiés à l’aide d’une transformation de sélection.

![Formats par défaut](media/data-flow/source2.png "Formats par défaut")

## <a name="next-steps"></a>Étapes suivantes

Créez votre transformation des données avec les options [Colonne dérivée](data-flow-derived-column.md) et [Sélection](data-flow-select.md).
