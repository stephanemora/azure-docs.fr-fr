---
title: Configurer une transformation de récepteur dans la fonctionnalité de mappage de flux de données d’Azure Data Factory
description: Découvrez comment configurer une transformation de récepteur dans le mappage de flux de données.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 4341cbb0e24330d535f5211c088f0068eab33af7
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596266"
---
# <a name="sink-transformation-for-a-data-flow"></a>Transformation d’un flux de données du récepteur

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Une fois que vous transformez votre flux de données, vous pouvez recevoir les données dans un jeu de données de destination. Dans la transformation de récepteur, choisissez une définition de dataset pour les données de sortie de destination. Vous pouvez avoir plusieurs transformations du récepteur que requiert votre flux de données.

Au compte pour une dérive du schéma et les modifications apportées aux données entrantes, les données de sortie de récepteur dans un dossier sans un schéma défini dans le jeu de données de sortie. Vous pouvez également tenir compte de colonne est modifiée dans vos sources en sélectionnant **autoriser une dérive du schéma** dans la source. Puis mapper tous les champs dans le récepteur.

![Options de l’onglet récepteur, y compris la possibilité de mappage automatique](media/data-flow/sink1.png "récepteur 1")

Pour recevoir tous les champs entrants, allumez **mappage automatique**. Pour choisir les champs sur le récepteur à la destination, ou pour modifier les noms des champs à la destination, désactiver **mappage automatique**. Ouvrez ensuite le **mappage** onglet pour mapper les champs de sortie.

![Options de l’onglet mappage](media/data-flow/sink2.png "récepteur 2")

## <a name="output"></a>Sortie 
Pour le stockage Blob Azure ou les types de récepteur de Data Lake Storage, les données transformées de sortie dans un dossier. Spark génère des fichiers de données de sortie partitionnée selon le schéma de partitionnement qui utilise la transformation de récepteur. 

Vous pouvez définir le schéma de partitionnement à partir de la **optimiser** onglet. Si vous souhaitez que Data Factory pour fusionner vos résultats dans un seul fichier, sélectionnez **partition unique**.

![Options de l’onglet optimiser](media/data-flow/opt001.png "options du récepteur")

## <a name="field-mapping"></a>Mappage de champs

Sur le **mappage** onglet de transformation de votre récepteur, vous pouvez mapper les colonnes entrants sur la gauche vers les destinations sur la droite. Lorsque le récepteur de flux de données dans des fichiers, Data Factory écrira toujours les nouveaux fichiers dans un dossier. Lorsque vous mappez à un jeu de données de base de données, vous pouvez générer une nouvelle table qui utilise ce schéma en définissant **enregistrer la stratégie** à **remplacer**. Ou insérer de nouvelles lignes dans une table existante et ensuite mapper les champs au schéma existant. 

![L’onglet mappage](media/data-flow/sink2.png "récepteurs")

Dans la table de mappage, vous pouvez la sélection multiple pour lier plusieurs colonnes, détachent plusieurs colonnes ou mapper plusieurs lignes pour le même nom de colonne.

Pour toujours mapper l’ensemble entrant de champs à une cible tels qu’ils sont entièrement accepter les définitions de schéma flexible, sélectionnez **autoriser une dérive du schéma**.

![L’onglet mappage, montrant les champs mappés aux colonnes du jeu de données](media/data-flow/multi1.png "plusieurs options")

Pour réinitialiser vos mappages de colonne, sélectionnez **remapper**.

![L’onglet récepteur](media/data-flow/sink1.png "récepteur un")

Sélectionnez **Validate schéma** échec le récepteur si le schéma est modifié.

Sélectionnez **effacer le dossier** pour tronquer le contenu du dossier récepteur avant d’écrire les fichiers de destination dans le dossier cible.

## <a name="file-name-options"></a>Options de nom de fichier

Vous pouvez configurer la dénomination des fichiers : 

   * **Par défaut** : Spark permettent de nommer les fichiers en fonction des valeurs par défaut de la partie.
   * **Modèle**: Entrer un modèle pour vos fichiers de sortie. Par exemple, **prêts [n]** créera loans1.csv, loans2.csv et ainsi de suite.
   * **Par partition**: Entrez un nom de fichier par partition.
   * **En tant que données de colonne**: Définissez le fichier de sortie à la valeur d’une colonne.
   * **Un seul fichier de sortie**: Avec cette option, ADF combine les fichiers de sortie partitionnée en un seul fichier nommé. Pour utiliser cette option, votre jeu de données doit se résoudre à un nom de dossier. En outre, sachez que cette opération de fusion peut échouer en fonction de la taille de nœud.

> [!NOTE]
> Début des opérations de fichier uniquement lorsque vous exécutez l’activité exécuter le flux de données. Ils ne démarrent pas en mode de données de flux de débogage.

## <a name="database-options"></a>Options de la base de données

Choisissez les paramètres de base de données :

* **Mettre à jour de la méthode**: La valeur par défaut est de permettre les insertions. Effacer **autoriser insert** si vous souhaitez arrêter l’insertion de nouvelles lignes à partir de votre source. Pour mettre à jour, upsert, ou supprimer des lignes, tout d’abord ajouter une transformation de ligne de modification aux lignes de balise pour ces actions. 
* **Recréer la table**: Supprimez ou créez la table cible avant que le flux de données se termine.
* **TRUNCATE table**: Supprimer toutes les lignes à partir de la table cible avant que le flux de données se termine.
* **Taille de lot** : Entrez le nombre de lots à créer pour diviser les données en plusieurs blocs de données. Utilisez cette option pour les chargements de données volumineux. 
* **Activer la mise en lots**: Utiliser PolyBase lors du chargement Azure Data Warehouse en tant que votre jeu de données récepteur.

![L’onglet Paramètres, montrant les options du récepteur SQL](media/data-flow/alter-row2.png "Options SQL")

> [!NOTE]
> Dans le flux de données, vous pouvez diriger Data Factory pour créer une nouvelle définition de table dans votre base de données cible. Pour créer la définition de table, définissez un jeu de données dans la transformation de récepteur qui a un nouveau nom de table. Dans le jeu de données SQL, sous le nom de table, sélectionnez **modifier** et entrez un nouveau nom de table. Puis, dans la transformation de récepteur, allumez **autoriser une dérive du schéma**. Définissez **importer un schéma** à **aucun**.

![Paramètres de jeu de données SQL, montrant où modifier le nom de table](media/data-flow/dataset2.png "schéma SQL")

> [!NOTE]
> Lorsque vous mettez à jour ou supprimez des lignes dans votre récepteur de base de données, vous devez définir la colonne clé. Ce paramètre permet la transformation de ligne alter déterminer la ligne unique dans la bibliothèque de déplacement des données (DML).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé votre flux de données, ajoutez un [activité de flux de données à votre pipeline](concepts-data-flow-overview.md).
