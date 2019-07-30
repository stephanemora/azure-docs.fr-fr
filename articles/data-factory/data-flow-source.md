---
title: Configurer une transformation de la source dans la fonctionnalité de mappage de flux de données d’Azure Data Factory
description: Découvrez comment configurer une transformation de la source dans le mappage de flux de données.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 048fe0ef88e8a79e21af7bb6e39a1d7ece3ee4ae
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277455"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Transformation de la source d’un mappage de flux de données 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Une transformation de la source configure votre source de données pour le flux de données. Un flux de données peut inclure plusieurs transformations de la source. Lors de la conception de données de flux, commencez toujours par une transformation de la source.

Chaque flux de données nécessite au moins une transformation de la source. Ajoutez autant de sources que nécessaire pour effectuer vos transformations de données. Vous pouvez joindre ces sources ensemble à l’aide d’une transformation de jointure ou d’une transformation d’union.

> [!NOTE]
> Quand vous déboguez votre flux de données, les données sont lues à partir de la source à l’aide du paramètre d’échantillonnage ou des limites de source de débogage. Pour écrire des données dans un récepteur, vous devez exécuter votre flux de données à partir d’une activité de flux de données de pipeline. 

![Options de transformation de la source sous l’onglet Paramètres de la source](media/data-flow/source.png "source")

Associez la transformation de votre source de flux de données à un seul jeu de données Data Factory. Le jeu de données définit la forme et l’emplacement des données que vous voulez lire ou écrire. Pour utiliser plusieurs fichiers à la fois, vous pouvez utiliser des caractères génériques et des listes de fichiers dans votre source.

## <a name="data-flow-staging-areas"></a>Zones de préproduction des flux de données

Les flux de données fonctionnent avec des jeux de données de *préproduction* qui se trouvent tous dans Azure. Utilisez ces jeux de données pour la préproduction quand vous transformez vos données. 

Data Factory a accès à près de 80 connecteurs natifs. Pour inclure dans votre flux de données des données provenant de ces autres sources, utilisez l’outil Copier l’activité pour effectuer une copie intermédiaire de ces données dans l’une des zones de préproduction des jeux de données Data Flow.

## <a name="options"></a>Options

Choisissez des options de schéma et d’échantillonnage pour vos données.

### <a name="allow-schema-drift"></a>Autoriser la dérive de schéma
Sélectionnez **Autoriser la dérive de schéma** si les colonnes sources seront amenées à changer souvent. Ce paramètre autorise tous les champs sources entrants à circuler jusqu’au récepteur, par le biais des transformations.

### <a name="validate-schema"></a>Valider le schéma

Si la version entrante de la source de données ne correspond pas au schéma défini, l’exécution du flux de données échoue.

![Paramètres de la source publique, montrant les options Valider le schéma, Autoriser la dérive de schéma et Échantillonnage](media/data-flow/source1.png "source publique 1")

### <a name="sample-the-data"></a>Échantillonner les données
Activez **Échantillonnage** pour limiter le nombre de lignes provenant de la source. Utilisez ce paramètre quand vous testez ou échantillonnez des données à partir de votre source à des fins de débogage.

## <a name="define-schema"></a>Définir le schéma

Quand vos fichiers sources ne sont pas fortement typés (par exemple, les fichiers plats à la place des fichiers Parquet), définissez les types de données pour chaque champ de la transformation de la source.  

![Paramètres de transformation de la source sous l’onglet Définir le schéma](media/data-flow/source2.png "source 2")

Vous pouvez ensuite changer les noms de colonnes dans une transformation de sélection. Utilisez une transformation de colonne dérivée pour changer les types de données. Pour les sources fortement typées, vous pouvez modifier les types de données dans une transformation de sélection ultérieure. 

![Types de données dans une transformation de sélection](media/data-flow/source003.png "types de données")

### <a name="optimize-the-source-transformation"></a>Optimiser la transformation de la source

Sous l’onglet **Optimiser** de la transformation de la source, vous pouvez voir un type de partition **Source**. Cette option est disponible uniquement quand votre source est Azure SQL Database. En effet, Data Factory tente de rendre les connexions parallèles pour exécuter des requêtes volumineuses sur votre source SQL Database.

![Paramètres de la partition source](media/data-flow/sourcepart3.png "partitionnement")

Vous n’avez pas besoin de partitionner les données de votre source SQL Database, mais les partitions sont utiles pour les requêtes volumineuses. Vous pouvez baser votre partition sur une colonne ou sur une requête.

### <a name="use-a-column-to-partition-data"></a>Utiliser une colonne pour partitionner des données

Dans la table source, sélectionnez une colonne sur laquelle effectuer le partitionnement. Définissez également le nombre de partitions.

### <a name="use-a-query-to-partition-data"></a>Utiliser une requête pour partitionner des données

Vous pouvez choisir de partitionner les connexions en fonction d’une requête. Il vous suffit d’entrer le contenu d’un prédicat WHERE. Par exemple, entrez year > 1980.

## <a name="source-file-management"></a>Gestion des fichiers sources

Choisissez des paramètres pour gérer les fichiers dans votre source. 

![Nouveaux paramètres de la source](media/data-flow/source2.png "Nouveaux paramètres")

* **Chemin avec des caractères génériques** : Dans le conteneur source, choisissez une série de fichiers qui correspondent à un modèle. Ce paramètre remplace tout fichier de votre définition de jeu de données.

Exemples de caractères génériques :

* ```*``` Représente n’importe quel ensemble de caractères
* ```**``` Représente une imbrication de répertoires récursifs
* ```?``` Remplace un caractère
* ```[]``` Met en correspondance un ou plusieurs caractères indiqués entre crochets

* ```/data/sales/**/*.csv``` Obtient tous les fichiers CSV se trouvant sous /data/sales
* ```/data/sales/20??/**``` Obtient tous les fichiers datés du 20ème siècle
* ```/data/sales/2004/*/12/[XY]1?.csv``` Obtient tous les fichiers CSV datés de décembre 2004, commençant par X ou Y et ayant comme préfixe un nombre à 2 chiffres

Un conteneur doit être spécifié dans le jeu de données. Votre chemin contenant des caractères génériques doit donc également inclure le chemin de votre dossier à partir du dossier racine.

* **Liste de fichiers** : Il s’agit d’un ensemble de fichiers. Créez un fichier texte qui inclut une liste de fichiers avec chemin relatif à traiter. Pointez sur ce fichier texte.
* **Colonne où stocker le nom du fichier** : Stockez le nom du fichier source dans une colonne de vos données. Entrez un nouveau nom pour stocker la chaîne de nom de fichier.
* **Une fois que vous avez terminé** : après l’exécution du flux de données, choisissez de ne rien faire avec le fichier source, de le supprimer ou de le déplacer. Pour le déplacement, les chemins sont des chemins relatifs.

Pour déplacer les fichiers sources vers un autre emplacement de post-traitement, sélectionnez tout d’abord « Déplacer » comme opération de fichier. Définissez ensuite le répertoire de provenance (« from »). Si vous n’utilisez pas de caractères génériques pour votre chemin, le paramètre « from » sera le même dossier que votre dossier source.

Si vous avez un chemin source contenant des caractères génériques, par exemple :

```/data/sales/20??/**/*.csv```

vous pouvez spécifier «from» sous la forme

```/data/sales```

et « to » sous la forme

```/backup/priorSales```

Dans le cas présent, tous les sous-répertoires sous /data/sales fournis sont déplacés par rapport à /backup/priorSales.

### <a name="sql-datasets"></a>Jeux de données SQL

Si votre source se trouve dans SQL Database ou dans SQL Data Warehouse, vous disposez d’options supplémentaires pour la gestion des fichiers sources.

* **Requête** : entrez une requête SQL pour votre source. Ce paramètre remplace toute table que vous avez choisie dans le jeu de données. Notez que les clauses **Order By** ne sont pas prises en charge ici, mais vous pouvez définir une instruction SELECT FROM complète. Vous pouvez également utiliser des fonctions de table définies par l’utilisateur. **select * from udfGetData()** est une fonction UDF dans SQL qui retourne une table. Cette requête génère une table source que vous pouvez utiliser dans votre flux de données.
* **Taille de lot** : entrez la taille de lot que doivent avoir les lectures créées à partir d’un large volume de données.
* **Niveaux d’isolement** : La valeur par défaut pour les sources SQL dans le mappage des flux de données ADF est Lecture non validée. Vous pouvez remplacer ici le niveau d’isolement par l’une des valeurs suivantes :
* Lecture validée
* Lecture non validée
* Lecture renouvelée
* Sérialisable
* Aucun (ignorer le niveau d’isolement)

![Niveau d’isolement](media/data-flow/isolationlevel.png "Niveau d’isolement")

> [!NOTE]
> Les opérations de fichier s’exécutent uniquement quand vous démarrez le flux de données à partir d’une exécution de pipeline (débogage ou exécution) qui utilise l’activité Exécuter le flux de données dans un pipeline. Les opérations de fichiers ne s’exécutent *pas* en mode de débogage de flux de données.

### <a name="projection"></a>Projection

Comme les schémas des jeux de données, la projection d’une source définit les colonnes, les types et les formats des données sources. 

![Paramètres de l’onglet Projection](media/data-flow/source3.png "Projection")

Si votre fichier texte ne comporte aucun schéma défini, sélectionnez **Détecter le type de données** afin que Data Factory échantillonne et déduise les types de données. Sélectionnez **Définir le format par défaut** pour détecter automatiquement les formats de données par défaut. 

Vous pouvez modifier les types de données des colonnes lors d’une transformation de colonne dérivée ultérieure. Utilisez une transformation de sélection pour modifier les noms de colonnes.

![Paramètres pour les formats de données par défaut](media/data-flow/source2.png "Formats par défaut")

### <a name="add-dynamic-content"></a>Ajout de contenu dynamique

Quand vous cliquez à l’intérieur des champs dans le volet des paramètres, vous voyez un lien hypertexte pour « Ajouter du contenu dynamique ». Le fait de cliquer dessus lance le Générateur d’expressions est lancé. C'est là que vous pouvez définir dynamiquement des valeurs pour les paramètres à l’aide d’expressions, de valeurs littérales statiques ou de paramètres.

![Paramètres](media/data-flow/params6.png "Paramètres")

## <a name="next-steps"></a>Étapes suivantes

Commencez à créer une [transformation de colonne dérivée](data-flow-derived-column.md), puis une [transformation de sélection](data-flow-select.md).
