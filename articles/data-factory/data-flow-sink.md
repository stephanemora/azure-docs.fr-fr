---
title: Configurer une transformation de réception dans la fonctionnalité de flux de données de mappage d’Azure Data Factory
description: Découvrez comment configurer une transformation de réception dans le flux de données de mappage.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 24ad0f2e917420c327577851cabc9e5bdbad2825
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515682"
---
# <a name="sink-transformation-for-a-data-flow"></a>Transformation de réception des flux de données

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Après avoir transformé votre flux de données, vous pouvez réceptionner les données dans un jeu de données de destination. Lors d’une transformation de réception, choisissez une définition de jeu de données pour les données de sortie de destination. Vous pouvez utiliser autant de transformations de réception que nécessaire pour votre flux de données.

Pour prendre en compte la modification des données entrantes et la dérive du schéma, réceptionnez les données de sortie dans un dossier sans schéma défini appartenant au jeu de données de sortie. Vous pouvez aussi tenir compte des modifications de colonne dans vos sources en sélectionnant **Autoriser la dérive du schéma** dans la source. Mappez automatiquement tous les champs dans le récepteur.

![Options de l’onglet Récepteur, dont l’option de Mappage automatique](media/data-flow/sink1.png "récepteur 1")

Pour réceptionner tous les champs entrants, activez le **Mappage automatique**. Pour choisir les champs à réceptionner dans la destination, ou pour modifier les noms ou les champs à la destination, désactivez le **Mappage automatique**. Ouvrez ensuite l’onglet **Mappage** pour mapper les champs de sortie.

![Options de l’onglet Mappage](media/data-flow/sink2.png "récepteur 2")

## <a name="output"></a>Output 
Pour les récepteurs Azure Storage Blob et Data Lake Storage, envoyez les données transformées vers un dossier. Spark génère des fichiers de données de sortie partitionnés, selon le schéma de partition utilisé par la transformation de réception. 

Vous pouvez configurer le schéma de partition depuis l’onglet **Optimiser**. Si vous voulez que Data Factory fusionne vos résultats dans un seul fichier, sélectionnez **Partition unique**.

![Options de l’onglet Optimiser](media/data-flow/opt001.png "options du récepteur")

## <a name="field-mapping"></a>Mappages de champs
Sous l’onglet **Mappage** de la transformation de réception, vous pouvez mapper les colonnes entrantes du côté gauche vers la destination à droite. Lorsque des fichiers réceptionnent des flux de données, Data Factory écrit toujours les nouveaux fichiers dans un dossier. Lorsque vous mappez un jeu de données de base de données, vous devez choisir les options d’opération de table de base de données à des fins d'insertion, de mise à jour, d'upsert ou de suppression.

![Onglet Mappage](media/data-flow/sink2.png "Récepteurs")

Vous pouvez utiliser la sélection multiple dans la table de mappage pour lier plusieurs colonnes, pour annuler la liaison de plusieurs colonnes ou pour mapper plusieurs lignes vers le même nom de colonne.

Pour toujours mapper le jeu entrant des champs à une cible en l’état et pour accepter complètement les définitions de schéma flexibles, sélectionnez **Autoriser la dérive du schéma**.

![Onglet Mappage, montrant les champs mappés aux colonnes du jeu de données](media/data-flow/multi1.png "plusieurs options")

Pour réinitialiser vos mappages de colonne, sélectionnez **Remapper**.

![Onglet Récepteur](media/data-flow/sink1.png "Récepteur un")

Sélectionnez **Valider le schéma** pour faire échouer la réception si le schéma est modifié.

Sélectionnez **Clear the folder** (Vider le dossier) pour tronquer le contenu du dossier récepteur avant d’écrire les fichiers de destination dans le dossier cible.

## <a name="rule-based-mapping"></a>Mappage basé sur des règles
Lorsque vous désactivez le mappage automatique, vous avez la possibilité d’ajouter un mappage basé sur des colonnes (mappage fixe) ou un mappage basé sur des règles. Le mappage basé sur des règles vous permet d’écrire des expressions avec des critères spéciaux. 

![Mappage basé sur des règles](media/data-flow/rules4.png "Mappage basé sur des règles")

Lorsque vous choisissez le mappage basé sur des règles, vous demandez à ADF d’évaluer votre expression correspondante pour qu’elle corresponde aux filtres de modèle entrants et de définir les noms de champs sortants. Vous pouvez ajouter n’importe quelle combinaison de mappage de champs et de mappage basé sur des règles. Les noms de champs sont ensuite générés au moment de l’exécution par ADF en fonction des métadonnées entrantes de la source. Vous pouvez afficher les noms des champs générés pendant le débogage et à l’aide du volet d'aperçu des données.

Vous trouverez des informations sur les critères spéciaux [dans la documentation du modèle de colonne](concepts-data-flow-column-pattern.md).

## <a name="file-name-options"></a>Options de nom de fichier

Configurez la dénomination des fichiers : 

   * **Par défaut** : Autorisez Spark à nommer les fichiers en fonction des valeurs par défaut de la partition.
   * **Modèle** : Entrez un modèle pour vos fichiers de sortie. Par exemple, **loans[n]** crée loans1.csv, loans2.csv, et ainsi de suite.
   * **Par partition** : Entrez un nom de fichier pour chaque partition.
   * **Comme les données de la colonne** : Définissez le fichier de sortie sur la valeur d’une colonne.
   * **Sortie d’un seul fichier** : Avec cette option, ADF combine les fichiers de sortie partitionnés en un seul fichier nommé. Pour utiliser cette option, votre jeu de données doit se résoudre à un nom de dossier. En outre, sachez que cette opération de fusion peut échouer en fonction de la taille du nœud.

> [!NOTE]
> Les opérations de fichier ne démarrent que lorsque vous exécuter une activité de flux de données. Ils ne démarrent pas en mode Débogage de flux de données.

## <a name="database-options"></a>Options de la base de données

Choisissez les Paramètres de base de données :

![Onglet Paramètres, montrant les options du récepteur SQL](media/data-flow/alter-row2.png "Options SQL")

* **Mettre à jour la méthode** : Par défaut, elle autorise les insertions. Effacez **Autoriser les insertions** si vous souhaitez arrêter d’insérer des lignes à partir de votre source. Pour mettre à jour, upsert ou supprimer des lignes, ajoutez d’abord une transformation de ligne alternative pour baliser les lignes pour ces actions. 
* **Recréer la table** : Supprimez ou créez la table cible avant que le flux de données se termine.
* **Tronquer la table** : Supprimez toutes les lignes de la table cible avant que le flux de données se termine.
* **Taille de lot** : Entrez le nombre de lots à créer pour diviser les données en plusieurs blocs de données. Utilisez cette option pour des charges de données volumineuses. 
* **Activer le mode de préproduction** : Utilisez PolyBase lors du chargement d’Azure Data Warehouse en tant que jeu de données de réception.
* **Pré et post-scripts SQL** : Entrez des scripts SQL multilignes qui s’exécutent avant (prétraitement) et après (post-traitement) l’écriture de données dans votre base de données de réception.

![Pré et post-scripts de traitement SQL](media/data-flow/prepost1.png "Scripts de traitement SQL")

> [!NOTE]
> Dans Data Flow, vous pouvez diriger Data Factory pour créer une nouvelle définition de table dans votre base de données cible. Pour créer la définition de table, définissez un ensemble dans la transformation de réception qui a un nouveau nom de table. Dans le jeu de données SQL, sous le nom de la table, sélectionnez **Modifier** et entrez un nouveau nom. Dans la transformation de réception, activez **Autoriser la dérive du schéma**. Définissez **Importer un schéma** sur **Aucun**.

![Paramètres du jeu de données SQL, montrant où modifier le nom de la table](media/data-flow/dataset2.png "Schéma SQL")

> [!NOTE]
> Lorsque vous mettez à jour ou supprimez des lignes dans votre récepteur de base de données, vous devez définir la colonne clé. Ce paramètre permet à la transformation de ligne alternative de déterminer la ligne unique dans la bibliothèque de déplacement des données (DML).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez créé votre flux de données, ajoutez une [activité de flux de données à votre pipeline](concepts-data-flow-overview.md).
