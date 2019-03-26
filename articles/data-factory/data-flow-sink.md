---
title: Azure Data Factory - Transformation de réception des flux de données de mappage
description: Azure Data Factory - Transformation de réception des flux de données de mappage
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a39fa0949276b7e86c7fdd0d0861492a9a0b723e
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438630"
---
# <a name="mapping-data-flow-sink-transformation"></a>Transformation de réception des flux de données de mappage

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Options de réception](media/data-flow/sink1.png "Récepteur 1")

À l’issue de la transformation de votre flux de données, vous pouvez réceptionner vos données transformées dans un jeu de données de destination. Lors d’une transformation de réception, vous pouvez choisir la définition de jeu de données que vous souhaitez utiliser pour les données de sortie de destination. Vous pouvez utiliser autant de transformations de réception que nécessaire pour votre flux de données.

Pour prendre en compte la modification des données entrantes et la dérive du schéma, il est courant de réceptionner les données de sortie dans un dossier sans schéma défini appartenant au jeu de données de sortie. Vous pouvez également prendre en compte toutes les modifications des colonnes de vos sources en sélectionnant « Autoriser la dérive de schéma » dans la source, puis en mappant automatiquement tous les champs du récepteur.

Vous pouvez choisir de remplacer, d’ajouter ou de faire échouer le flux de données lors de sa réception par un jeu de données.

Vous pouvez également choisir l’option de mappage automatique pour réceptionner tous les champs entrants. Si vous souhaitez choisir les champs à réceptionner dans la destination, ou si vous souhaitez modifier les noms des champs dans la destination, désactivez le mappage automatique, puis cliquez sur l’onglet Mappage pour mapper les champs de sortie :

![Options de réception](media/data-flow/sink2.png "Récepteur 2")

## <a name="output-to-one-file"></a>Envoyer les résultats vers un seul fichier
Pour les récepteurs Azure Storage Blob et Data Lake, vous allez envoyer les données transformées vers un dossier. Spark génère des fichiers de données de sortie partitionnés, selon le schéma de partition utilisé par la transformation de réception. Vous pouvez configurer le schéma de partition en cliquant sur l’onglet « Optimiser ». Si vous souhaitez qu’ADF fusionne vos résultats dans un seul fichier, cliquez sur la case d’option « Partition unique ».

![Options de réception](media/data-flow/opt001.png "Options de réception")

## <a name="field-mapping"></a>Mappage de champs

Sous l’onglet Mappage de la transformation de réception, vous pouvez mapper les colonnes entrantes (côté gauche) vers la destination (à droite). Lorsque des fichiers réceptionnent des flux de données, ADF écrit toujours les nouveaux fichiers dans un dossier. Lorsque vous effectuez un mappage vers un jeu de données de base de données, vous pouvez choisir soit de générer une nouvelle table avec ce schéma (pour cela, définissez Enregistrer la stratégie sur « Remplacer »), soit d’insérer de nouvelles lignes dans une table et de mapper les champs vers le schéma existant.

Vous pouvez utiliser la sélection multiple dans la table de mappage pour lier plusieurs colonnes avec un seul clic, détachent plusieurs colonnes ou mapper plusieurs lignes pour le même nom de colonne.

Lorsque vous souhaitez toujours prendre l’ensemble entrant de champs et les mapper à une cible en tant que-, définissez le paramètre « Autoriser les dérives de schéma ».

![Mappages de champs](media/data-flow/multi1.png "Plusieurs options")

Si vous souhaitez réinitialiser vos mappages de colonnes, appuyez sur le bouton « Remapper ».

![Options de réception](media/data-flow/sink1.png "Récepteur 1")

![Options de réception](media/data-flow/sink2.png "Récepteurs")

* Les options Autoriser la dérive de schéma et Valider le schéma sont désormais disponibles dans le récepteur. Ces options vous permettent d’indiquer à ADF de soit accepter entièrement les définitions de schémas flexibles (Autoriser la dérive de schéma), soit de mettre en échec le récepteur si le schéma est modifié (Valider le schéma).

* Effacer le contenu du dossier. ADF va tronquer le contenu du dossier récepteur avant d’écrire les fichiers de destination dans le dossier cible.

## <a name="file-name-options"></a>Options de nom de fichier

   * Valeur par défaut : autoriser Spark à nommer les fichiers en fonction des valeurs par défaut de la partition
   * Modèle : Entrer un modèle pour vos fichiers de sortie. Par exemple, « prêts [n] » créera loans1.csv, loans2.csv,...
   * Par partition : entrez un nom de fichier pour chaque partition.
   * Comme les données de la colonne : définissez le fichier de sortie sur la valeur d’une colonne.

> [!NOTE]
> Les opérations de fichier ne seront exécutées que lorsque vous exécuterez l’activité Exécuter le flux de données. Elles ne le seront pas lorsque vous serez en mode de débogage Data Flow.

## <a name="database-options"></a>Options de la base de données

* Autoriser insert, update, delete, upserts. La valeur par défaut est de permettre les insertions. Si vous souhaitez effectuer la mise à jour, d’upsert ou de supprimer des lignes, vous devez d’abord ajouter une transformation de ligne alter aux lignes de balise pour les actions spécifiques. Désactivation de « Autoriser l’insertion » s’arrête ADF à partir de l’insertion de nouvelles lignes à partir de votre source.
* TRUNCATE table (supprime toutes les lignes à partir de la table cible avant la fin du flux de données)
* Recréez la table (effectue suppriment/créent de la table cible avant la fin du flux de données)
* Taille du lot (pour faciliter le chargement de données volumineuses). Entrez le nombre des écritures de compartiment en segments
* Activer la mise en lots : Cela indiquera ADF pour utiliser Polybase lors du chargement de l’entrepôt de données Azure en tant que votre jeu de données récepteur

> [!NOTE]
> Dans le flux de données, vous pouvez demander l’ADF pour créer une nouvelle définition de table dans votre base de données cible en définissant un jeu de données dans la transformation de récepteur qui a un nouveau nom de table. Dans le jeu de données SQL, cliquez sur « Modifier » sous le nom de table et entrez un nouveau nom de table. Puis, dans la Transformation du récepteur, activez « Autoriser les dérives de schéma ». Seth le paramètre « Importer le schéma » None.

![Schéma de Transformation source](media/data-flow/dataset2.png "schéma SQL")

![Options du récepteur SQL](media/data-flow/alter-row2.png "Options SQL")

> [!NOTE]
> Lorsque la mise à jour ou suppression de lignes dans votre récepteur de base de données, vous devez définir la colonne clé. De cette façon, Alter ligne est en mesure de déterminer la ligne unique dans l’instruction DML.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé votre flux de données, ajoutez une [activité Exécuter un flux de données à votre pipeline](concepts-data-flow-overview.md).
