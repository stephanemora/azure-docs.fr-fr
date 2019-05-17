---
title: Configurer une transformation source dans la fonctionnalité de mappage de flux de données d’Azure Data Factory
description: Découvrez comment configurer une transformation source dans le mappage de flux de données.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: dc0a6e008c7a1f4fb414f6d8adad3a94abc7a6b2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792331"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Transformation source pour le mappage de flux de données 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Une transformation source configure votre source de données pour le flux de données. Un flux de données peut inclure plusieurs transformations de code source. Lors de la conception des données de flux, commencent toujours par une transformation source.

Chaque flux de données nécessite une transformation d’au moins une source. Ajouter des sources autant que nécessaire pour effectuer vos transformations de données. Vous pouvez joindre ces sources avec une transformation de jointure ou d’une transformation d’union.

> [!NOTE]
> Lorsque vous déboguez votre flux de données, les données sont lues à partir de la source à l’aide du paramètre d’échantillonnage ou les limites de source de débogage. Pour écrire des données vers un récepteur, vous devez exécuter votre flux de données à partir d’un pipeline d’activité de flux de données. 

![Options de transformation sur l’onglet Paramètres de la Source de source de](media/data-flow/source.png "source")

Associer votre transformation source de flux de données à un seul jeu de données de Data Factory. Le jeu de données définit la forme et l’emplacement des données que vous souhaitez écrire ou de lire à partir de. Vous pouvez utiliser des caractères génériques et le fichier de listes dans votre source pour travailler avec plusieurs fichiers à la fois.

## <a name="data-flow-staging-areas"></a>Zones de transit de flux de données

Flux de données fonctionne avec *intermédiaire* jeux de données qui se trouvent toutes dans Azure. Utilisez ces jeux de données pour la mise en lots lorsque vous êtes transformer vos données. 

Fabrique de données a accès à des connecteurs natifs presque 80. Pour inclure les données de ces autres sources dans votre flux de données, utilisez l’outil de l’activité de copie à utiliser pour stocker ces données dans les zones d’intermédiaire de jeu de données de flux de données.

## <a name="options"></a>Options

Choisissez les options de schéma et d’échantillonnage pour vos données.

### <a name="allow-schema-drift"></a>Autoriser la dérive de schéma
Sélectionnez **autoriser une dérive du schéma** si les colonnes source va changer souvent. Ce paramètre permet à tous les champs sources entrantes à circuler via les transformations au récepteur.

### <a name="validate-schema"></a>Valider le schéma

Si la version entrante de la source de données ne correspond pas au schéma défini, le flux de données échouera à exécuter.

![Paramètres de la source publique, montrant les options pour le schéma de validation et autoriser une dérive du schéma d’échantillonnage](media/data-flow/source1.png "source publique 1")

### <a name="sample-the-data"></a>Échantillon des données
Activer **échantillonnage** pour limiter le nombre de lignes à partir de votre source. Utilisez ce paramètre lorsque vous testez ou échantillonner des données à partir de votre source à des fins de débogage.

## <a name="define-schema"></a>Définir un schéma

Lorsque vos fichiers sources ne sont pas fortement typées (par exemple, fichiers plats plutôt que les fichiers Parquet), définissez les types de données pour chaque champ ici dans la transformation source.  

![Paramètres de transformation sur l’onglet Schéma de définition de la source](media/data-flow/source2.png "source 2")

Vous pouvez modifier ultérieurement les noms de colonne dans une transformation select. Utilisez une transformation de colonne dérivée pour modifier les types de données. Pour les sources fortement typées, vous pouvez modifier les types de données dans une transformation sélectionnez plus tard. 

![Types de données dans une transformation sélectionnez](media/data-flow/source003.png "types de données")

### <a name="optimize-the-source-transformation"></a>Optimiser la transformation source

Sur le **optimiser** onglet pour la transformation de la source, vous pouvez voir un **Source** type de partition. Cette option est disponible uniquement lorsque votre source est la base de données SQL Azure. Il s’agit, car la fabrique de données tente d’établir des connexions parallèlement à exécuter des requêtes volumineuses sur votre source de base de données SQL.

![Paramètres de partition de la source](media/data-flow/sourcepart2.png "partitionnement")

Vous n’êtes pas obligé de partitionner les données de votre source de base de données SQL, mais les partitions sont utiles pour les requêtes volumineuses. Vous pouvez baser votre partition sur une colonne ou une requête.

### <a name="use-a-column-to-partition-data"></a>Utiliser une colonne de partitionnement des données

À partir de votre table source, sélectionnez une colonne de partition sur. Définissez également le nombre maximal de connexions.

### <a name="use-a-query-to-partition-data"></a>Utiliser une requête pour partitionner des données

Vous pouvez choisir de partitionner les connexions basées sur une requête. Entrez simplement le contenu d’un prédicat WHERE. Par exemple, entrez année 1980 >.

## <a name="source-file-management"></a>Gestion des fichiers sources

Choisissez les paramètres pour gérer les fichiers dans votre source. 

![Nouveaux paramètres de source](media/data-flow/source2.png "nouveaux paramètres")

* **Chemin d’accès générique**: À partir de votre dossier source, choisissez une série de fichiers qui correspondent à un modèle. Ce paramètre remplace n’importe quel fichier dans votre définition de jeu de données.
* **Liste des fichiers**: Il s’agit d’un jeu de fichiers. Créez un fichier texte qui inclut une liste de fichiers du chemin d’accès relatif à traiter. Pointer vers ce fichier texte.
* **La colonne nom de fichier**: Store le nom du fichier source dans une colonne dans vos données. Entrez un nouveau nom pour stocker la chaîne de nom de fichier.
* **Après l’achèvement**: Choisissez cette option pour ne rien faire avec le fichier source une fois que les données d’exécutions de flux, de supprimer le fichier source ou de déplacement le fichier source. Les chemins d’accès pour le déplacement sont relatives.

### <a name="sql-datasets"></a>Jeux de données SQL

Si votre source est dans la base de données SQL ou SQL Data Warehouse, vous disposez des options supplémentaires pour la gestion des fichiers sources.

* **Requête** : entrez une requête SQL pour votre source. Ce paramètre remplace n’importe quelle table que vous avez choisi dans le jeu de données. Notez que **Order By** clauses ne sont pas pris en charge ici. Mais vous pouvez définir une instruction SELECT FROM complet ici.
* **Taille de lot** : Entrez une taille de lot pour découper les données volumineuses en lectures.

> [!NOTE]
> Opérations de fichier s’exécutent uniquement lorsque vous démarrez le flux de données à partir de l’exécution d’un pipeline (un débogage du pipeline ou une série de tests) qui utilise l’activité d’exécution de flux de données dans un pipeline. Opérations de fichier *ne le faites pas* exécuter en mode de débogage de flux de données.

### <a name="projection"></a>Projection

Comme les schémas dans les jeux de données, la projection dans une source définit les colonnes de données, les types et les formats à partir de la source de données. 

![Paramètres de l’onglet de Projection](media/data-flow/source3.png "Projection")

Si votre fichier texte ne comporte aucun schéma défini, sélectionnez **détecter le type de données** afin que Data Factory s’exemple et déduire les types de données. Sélectionnez **format par défaut de définir** pour la détection automatique des formats de données par défaut. 

Vous pouvez modifier les types de données de colonne dans une transformation de colonne dérivée ultérieurement. Utilisez une transformation select pour modifier les noms de colonne.

![Paramètres pour les formats de données par défaut](media/data-flow/source2.png "formats par défaut")

## <a name="next-steps"></a>Étapes suivantes

Commencer à créer un [transformation de colonne dérivée](data-flow-derived-column.md) et un [sélectionnez transformation](data-flow-select.md).
