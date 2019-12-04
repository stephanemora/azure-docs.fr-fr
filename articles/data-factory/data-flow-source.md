---
title: Transformation de la source d’un mappage de flux de données
description: Découvrez comment configurer une transformation de la source dans le mappage de flux de données.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 33a63b8a887594747aba03e19c107653e438853f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217744"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Transformation de la source d’un mappage de flux de données 

Une transformation de la source configure votre source de données pour le flux de données. Lors de la conception de flux de données, la première étape consiste toujours à configurer une transformation de source. Pour ajouter une source, cliquez sur **Ajouter une source** dans le canevas de flux de données.

Chaque flux de données nécessite au moins une transformation de source. Vous pouvez cependant ajouter autant de sources que nécessaire pour effectuer vos transformations de données. Vous pouvez joindre ces sources à l’aide d’une transformation de jointure, de recherche ou d’union.

Un jeu de données Data Factory unique est associé à chaque transformation de source. Le jeu de données définit la forme et l’emplacement des données que vous voulez lire ou écrire. Si le jeu de données est basé sur des fichiers, vous pouvez utiliser des caractères génériques et des listes de fichiers dans votre source pour utiliser plusieurs fichiers à la fois.

## <a name="supported-connectors-in-mapping-data-flow"></a>Connecteurs pris en charge dans le flux de données de mappage

Le flux de données de mappage suit une approche basée sur l’extraction, le chargement et la transformation (ELT, extract, load, transform) et fonctionne avec des jeux de données *intermédiaires* qui se trouvent tous dans Azure. Actuellement, les jeux de données suivants peuvent être utilisés dans une transformation de source :
    
* Stockage Blob Azure (JSON, Avro, Text, Parquet)
* Azure Data Lake Storage Gen1 (JSON, Avro, Text, Parquet)
* Azure Data Lake Storage Gen2 (JSON, Avro, Text, Parquet)
* Azure SQL Data Warehouse
* Azure SQL Database
* Azure CosmosDB

Azure Data Factory a accès à plus de 80 connecteurs natifs. Pour inclure dans votre flux de données des données provenant de ces autres sources, utilisez l’outil Copier l’activité pour charger ces données dans l’une des zones de transit prises en charge.

## <a name="source-settings"></a>Paramètres de la source

Une fois que vous avez ajouté une source, configurez-la à l’aide de l’onglet **Paramètres de la source**. Sous cet onglet, vous pouvez sélectionner ou créer le jeu de données vers lequel pointe la source. Vous pouvez également sélectionner des options de schéma et d’échantillonnage pour vos données.

![Onglet Paramètres de la source](media/data-flow/source1.png "Onglet Paramètres de la source")

**Dérive de schéma :** La [dérive de schéma](concepts-data-flow-schema-drift.md) est la capacité de la fabrique de données à gérer nativement des schémas flexibles dans vos flux de données sans avoir besoin de définir explicitement des changements de colonnes.

* Cochez la case **Autoriser la dérive de schéma** si les colonnes sources sont amenées à changer souvent. Ce paramètre autorise tous les champs sources entrants à circuler jusqu’au récepteur, par le biais des transformations.

* Si vous cochez la case **Déduire les types de colonnes dérivés**, le système demandera à la fabrique de données de détecter et définir les types de données pour chaque nouvelle colonne découverte. Si cette fonctionnalité est désactivée, toutes les colonnes dérivées seront de type chaîne.

**Valider le schéma :** Quand la case Valider le schéma est cochée, l’exécution du flux de données échoue si les données sources entrantes ne correspondent pas au schéma défini du jeu de données.

**Nombre de lignes à ignorer :** Ce champ spécifie le nombre de lignes à ignorer au début du jeu de données.

**Échantillonnage :** Activez l’échantillonnage pour limiter le nombre de lignes provenant de la source. Utilisez ce paramètre quand vous testez ou échantillonnez des données à partir de votre source à des fins de débogage.

**Lignes multilignes :** sélectionnez l’option des lignes multilignes si votre fichier texte source contient des valeurs de chaîne qui s’étendent sur plusieurs lignes, c’est-à-dire de nouvelles lignes à l’intérieur d’une valeur.

Pour vérifier que votre source est correctement configurée, activez le mode débogage et récupérez un aperçu des données. Pour en savoir plus, consultez [Mode débogage](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Quand le mode débogage est activé, la configuration de la limite du nombre de lignes dans les paramètres de débogage remplace le paramètre d’échantillonnage dans la source lors de l’affichage de l’aperçu des données.

## <a name="file-based-source-options"></a>Options pour les sources basées sur des fichiers

Si vous utilisez un jeu de données basé sur des fichiers tel que le stockage Blob Azure ou Azure Data Lake Storage, l’onglet **Options de source** vous permet de gérer la façon dont la source lit les fichiers.

![Options de la source](media/data-flow/sourceOPtions1.png "Options de la source")

**Chemin contenant des caractères génériques :** Si vous utilisez un modèle à caractères génériques, le système demande à ADF de lire chaque dossier et fichier correspondants en boucle dans une même transformation de source. Il s’agit d’un moyen efficace de traiter plusieurs fichiers dans un seul et même flux. Ajoutez plusieurs modèles de correspondance à caractères génériques avec le signe + qui apparaît quand vous placez le pointeur sur votre modèle existant.

Dans le conteneur source, choisissez une série de fichiers qui correspondent à un modèle. Seul le conteneur peut être spécifié dans le jeu de données. Votre chemin contenant des caractères génériques doit donc également inclure le chemin de votre dossier à partir du dossier racine.

Exemples de caractères génériques :

* ```*``` Représente n’importe quel ensemble de caractères
* ```**``` Représente une imbrication de répertoires récursifs
* ```?``` Remplace un caractère
* ```[]``` Met en correspondance un ou plusieurs caractères indiqués entre crochets

* ```/data/sales/**/*.csv``` Obtient tous les fichiers CSV se trouvant sous /data/sales
* ```/data/sales/20??/**``` Obtient tous les fichiers datés du 20ème siècle
* ```/data/sales/2004/*/12/[XY]1?.csv``` Obtient tous les fichiers CSV datés de décembre 2004, commençant par X ou Y et ayant comme préfixe un nombre à deux chiffres

**Chemin racine de la partition :** Si vous avez partitionné des dossiers dans votre source de fichiers avec un format ```key=value``` (par exemple, année = 2019), vous pouvez attribuer le niveau supérieur de cette arborescence de dossiers de partitions à un nom de colonne dans votre flux de données.

Tout d’abord, définissez un caractère générique pour inclure tous les chemins d’accès aux dossiers partitionnés, ainsi qu’aux fichiers feuilles que vous souhaitez lire.

![Paramètres du fichier source de partition](media/data-flow/partfile2.png "Paramètre du fichier de partition")

Utilisez le paramètre Chemin racine de la partition pour définir le niveau supérieur de la structure de dossiers. Quand vous affichez le contenu de vos données à l’aide d’un aperçu des données, vous voyez qu’ADF ajoute les partitions résolues trouvées dans chacun de vos niveaux de dossiers.

![Chemin racine de la partition](media/data-flow/partfile1.png "Aperçu du chemin racine de la partition")

**Liste de fichiers :** Il s’agit d’un ensemble de fichiers. Créez un fichier texte qui inclut une liste de fichiers avec chemin relatif à traiter. Pointez sur ce fichier texte.

**Colonne où stocker le nom du fichier :** Stockez le nom du fichier source dans une colonne de vos données. Entrez un nouveau nom de colonne pour stocker la chaîne de nom de fichier.

**Après l’exécution :** après l’exécution du flux de données, choisissez de ne rien faire avec le fichier source, de le supprimer ou de le déplacer. Pour le déplacement, les chemins sont des chemins relatifs.

Pour déplacer les fichiers sources vers un autre emplacement de post-traitement, sélectionnez tout d’abord « Déplacer » comme opération de fichier. Définissez ensuite le répertoire de provenance (« from »). Si vous n’utilisez pas de caractères génériques pour votre chemin, le paramètre « from » sera le même dossier que votre dossier source.

Si vous avez un chemin d’accès source avec caractère générique, votre syntaxe se présente comme suit :

```/data/sales/20??/**/*.csv```

vous pouvez spécifier «from» sous la forme

```/data/sales```

et « to » sous la forme

```/backup/priorSales```

Dans le cas présent, tous les fichiers qui provenaient de /data/sales sont déplacés dans /backup/priorSales.

> [!NOTE]
> Les opérations de fichier s’exécutent uniquement quand vous démarrez le flux de données à partir d’une exécution de pipeline (débogage ou exécution) qui utilise l’activité Exécuter le flux de données dans un pipeline. Les opérations de fichiers ne s’exécutent *pas* en mode de débogage de flux de données.

**Filtrer par date de dernière modification :** Vous pouvez filtrer les fichiers traités en spécifiant une plage de dates sur laquelle les fichiers ont été modifiés pour la dernière fois. Toutes les dates et heures sont exprimées en UTC. 

### <a name="add-dynamic-content"></a>Ajout de contenu dynamique

Tous les paramètres de source peuvent être spécifiés sous forme d’expressions à l’aide du [langage d’expression de transformation du flux de données de mappage](data-flow-expression-functions.md). Pour ajouter du contenu dynamique, cliquez ou placez le pointeur de la souris sur les champs du volet de paramètres. Cliquez sur le lien hypertexte **Ajouter du contenu dynamique**. Ceci lance le générateur d’expressions qui vous permet de définir des valeurs de manière dynamique à l’aide d’expressions, de valeurs littérales statiques ou de paramètres.

![Paramètres](media/data-flow/params6.png "parameters")

## <a name="sql-source-options"></a>Options de source SQL

Si votre source est dans une base de données ou un entrepôt de données SQL, d’autres paramètres spécifiques à SQL sont disponibles sous l’onglet **Options de source**. 

**Entrée :** Indiquez si votre source pointe vers une table (ce qui correspond à ```Select * from <table-name>```) ou si vous souhaitez entrer une requête SQL personnalisée.

**Requête** : Si vous sélectionnez Requête dans le champ Entrée, entrez une requête SQL pour votre source. Ce paramètre remplace toute table que vous avez choisie dans le jeu de données. Les clauses **Order By** ne sont pas prises en charge ici, mais vous pouvez définir une instruction SELECT FROM complète. Vous pouvez également utiliser des fonctions de table définies par l’utilisateur. **select * from udfGetData()** est une fonction UDF dans SQL qui retourne une table. Cette requête génère une table source que vous pouvez utiliser dans votre flux de données. L’utilisation de requêtes est également un excellent moyen de réduire les lignes pour les tests ou les recherches. Exemple : ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Taille de lot** : entrez la taille de lot que doivent avoir les lectures créées à partir d’un large volume de données.

**Niveaux d’isolement** : La valeur par défaut pour les sources SQL dans le flux de données de mappage est Lecture non validée. Vous pouvez remplacer ici le niveau d’isolement par l’une des valeurs suivantes :
* Lecture validée
* Lecture non validée
* Lecture renouvelée
* Sérialisable
* Aucun (ignorer le niveau d’isolement)

![Niveaux d’isolement](media/data-flow/isolationlevel.png "Niveau d’isolation")

## <a name="projection"></a>Projection

Comme les schémas des jeux de données, la projection d’une source définit les colonnes, les types et les formats des données sources. Pour la plupart des types de jeu de données tels que SQL et Parquet, la projection dans une source est corrigée pour refléter le schéma défini dans un jeu de données. Quand vos fichiers sources ne sont pas fortement typés (par exemple, des fichiers CSV plats et non des fichiers Parquet), vous pouvez définir les types de données pour chaque champ de la transformation de source.

![Paramètres de l’onglet Projection](media/data-flow/source3.png "Projection")

Si votre fichier texte ne comporte aucun schéma défini, sélectionnez **Détecter le type de données** afin que Data Factory échantillonne et déduise les types de données. Sélectionnez **Définir le format par défaut** pour détecter automatiquement les formats de données par défaut. 

Vous pouvez modifier les types de données des colonnes lors d’une transformation de colonne dérivée en aval. Utilisez une transformation de sélection pour modifier les noms de colonnes.

### <a name="import-schema"></a>Importer un schéma

Les jeux de données, tels que Avro et CosmosDB, qui prennent en charge des structures de données complexes n’ont pas besoin de définitions de schéma pour exister dans le jeu de données. Par conséquent, vous pouvez cliquer sur le bouton « Importer un schéma » dans l’onglet Projection pour ces types de sources.

## <a name="cosmosdb-specific-settings"></a>Paramètres spécifiques à CosmosDB

Lorsque vous utilisez CosmosDB comme type de source, il y a quelques options à prendre en compte :

* Inclure des colonnes système : si vous activez cette option, ```id```, ```_ts```et d’autres colonnes système sont englobées dans vos métadonnées de flux de données à partir de CosmosDB. Lors de la mise à jour de collections, il est important de l’inclure, afin de pouvoir récupérer l’ID de ligne existant.
* Taille de la page : nombre de documents par page du résultat de la requête. La valeur par défaut est « -1 », qui utilise la page dynamique du service jusqu’à 1 000.
* Débit : définissez une valeur facultative du nombre de RU que vous souhaitez appliquer à votre collection CosmosDB pour chaque exécution de ce flux de données pendant l’opération de lecture. La valeur minimale est 400.
* Régions préférées : vous pouvez choisir les régions de lecture préférées pour ce processus.

## <a name="optimize-the-source-transformation"></a>Optimiser la transformation de la source

Sous l’onglet **Optimiser** de la transformation de la source, vous pouvez voir un type de partition **Source**. Cette option est disponible uniquement quand votre source est Azure SQL Database. En effet, Data Factory tente de rendre les connexions parallèles pour exécuter des requêtes volumineuses sur votre source SQL Database.

![Paramètres de la partition source](media/data-flow/sourcepart3.png "partitionnement")

Vous n’avez pas besoin de partitionner les données de votre source SQL Database, mais les partitions sont utiles pour les requêtes volumineuses. Vous pouvez baser votre partition sur une colonne ou sur une requête.

### <a name="use-a-column-to-partition-data"></a>Utiliser une colonne pour partitionner des données

Dans la table source, sélectionnez une colonne sur laquelle effectuer le partitionnement. Définissez également le nombre de partitions.

### <a name="use-a-query-to-partition-data"></a>Utiliser une requête pour partitionner des données

Vous pouvez choisir de partitionner les connexions en fonction d’une requête. Entrez le contenu d’un prédicat WHERE. Par exemple, entrez year > 1980.

Pour plus d’informations sur l’optimisation dans le mappage de flux de données, consultez la section [Onglet Optimiser](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Étapes suivantes

Commencez à créer une [transformation de colonne dérivée](data-flow-derived-column.md), puis une [transformation de sélection](data-flow-select.md).
