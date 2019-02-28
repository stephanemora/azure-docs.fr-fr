---
title: Azure Data Factory - Transformation de réception des flux de données de mappage
description: Azure Data Factory - Transformation de réception des flux de données de mappage
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 381dc2f9f6d3a074af00ba047472719c086f5811
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408406"
---
# <a name="mapping-data-flow-sink-transformation"></a>Transformation de réception des flux de données de mappage

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Options de réception](media/data-flow/windows1.png "Récepteur 1")

À l’issue de la transformation de votre flux de données, vous pouvez réceptionner vos données transformées dans un jeu de données de destination. Lors d’une transformation de réception, vous pouvez choisir la définition de jeu de données que vous souhaitez utiliser pour les données de sortie de destination. Vous pouvez utiliser autant de transformations de réception que nécessaire pour votre flux de données.

Pour prendre en compte la modification des données entrantes et la dérive du schéma, il est courant de réceptionner les données de sortie dans un dossier sans schéma défini appartenant au jeu de données de sortie. Vous pouvez également prendre en compte toutes les modifications des colonnes de vos sources en sélectionnant « Autoriser la dérive de schéma » dans la source, puis en mappant automatiquement tous les champs du récepteur.

Vous pouvez choisir de remplacer, d’ajouter ou de faire échouer le flux de données lors de sa réception par un jeu de données.

Vous pouvez également choisir l’option de mappage automatique pour réceptionner tous les champs entrants. Si vous souhaitez choisir les champs à réceptionner dans la destination, ou si vous souhaitez modifier les noms des champs dans la destination, désactivez le mappage automatique, puis cliquez sur l’onglet Mappage pour mapper les champs de sortie :

![Options de réception](media/data-flow/sink2.png "Récepteur 2")

## <a name="output-to-one-file"></a>Envoyer les résultats vers un seul fichier
Pour les récepteurs Azure Storage Blob et Data Lake, vous allez envoyer les données transformées vers un dossier. Spark génère des fichiers de données de sortie partitionnés, selon le schéma de partition utilisé par la transformation de réception. Vous pouvez configurer le schéma de partition en cliquant sur l’onglet « Optimiser ». Si vous souhaitez qu’ADF fusionne vos résultats dans un seul fichier, cliquez sur la case d’option « Partition unique ».

![Options de réception](media/data-flow/opt001.png "Options de réception")

### <a name="output-settings"></a>Paramètres de sortie

L’option de remplacement tronque la table si celle-ci existe, puis la recrée et charge les données. L’option d’ajout insère de nouvelles lignes. Si la table correspondant au nom de la table du jeu de données n’existe pas dans l’ADW cible, Data Flow crée la table, puis charge les données.

Si vous désactivez l’option « Mappage automatique », vous pouvez mapper manuellement les champs vers votre table de destination.

![Options de réception ADW](media/data-flow/adw2.png "Réception ADW")

#### <a name="field-mapping"></a>Mappages de champs

Sous l’onglet Mappage de la transformation de réception, vous pouvez mapper les colonnes entrantes (côté gauche) vers la destination (à droite). Lorsque des fichiers réceptionnent des flux de données, ADF écrit toujours les nouveaux fichiers dans un dossier. Lorsque vous effectuez un mappage vers un jeu de données de base de données, vous pouvez choisir soit de générer une nouvelle table avec ce schéma (pour cela, définissez Enregistrer la stratégie sur « Remplacer »), soit d’insérer de nouvelles lignes dans une table et de mapper les champs vers le schéma existant.

Vous pouvez utiliser la sélection multiple dans la table de mappage pour lier plusieurs colonnes en un clic, pour annuler la liaison de plusieurs colonnes ou pour mapper plusieurs lignes vers le même nom de colonne.

![Mappages de champs](media/data-flow/multi1.png "Plusieurs options")

Si vous souhaitez réinitialiser vos mappages de colonnes, appuyez sur le bouton « Remapper ».

![Connexions](media/data-flow/maxcon.png "Connexions")

### <a name="updates-to-sink-transformation-for-adf-v2-ga-version"></a>Mises à jour apportées à la transformation de réception pour la version en disponibilité générale d’ADF v2

![Options de réception](media/data-flow/sink1.png "Récepteur 1")

![Options de réception](media/data-flow/sink2.png "Récepteurs")

* Les options Autoriser la dérive de schéma et Valider le schéma sont désormais disponibles dans le récepteur. Ces options vous permettent d’indiquer à ADF de soit accepter entièrement les définitions de schémas flexibles (Autoriser la dérive de schéma), soit de mettre en échec le récepteur si le schéma est modifié (Valider le schéma).

* Effacer le contenu du dossier. ADF va tronquer le contenu du dossier récepteur avant d’écrire les fichiers de destination dans le dossier cible.

* Options de nom de fichier

   * Valeur par défaut : autoriser Spark à nommer les fichiers en fonction des valeurs par défaut de la partition
   * Modèle : entrez un nom pour vos fichiers de sortie.
   * Par partition : entrez un nom de fichier pour chaque partition.
   * Comme les données de la colonne : définissez le fichier de sortie sur la valeur d’une colonne.

> [!NOTE]
> Les opérations de fichier ne seront exécutées que lorsque vous exécuterez l’activité Exécuter le flux de données. Elles ne le seront pas lorsque vous serez en mode de débogage Data Flow.

Avec les types de récepteurs SQL, vous pouvez choisir les options suivantes :

* Tronquer la table
* Recréer la table (supprime puis crée la table)
* Taille du lot (pour faciliter le chargement de données volumineuses). Entrez le nombre de lots à créer pour diviser les données en plusieurs blocs de données.

![Mappage de champs](media/data-flow/sql001.png "Options SQL")

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé votre flux de données, ajoutez une [activité Exécuter un flux de données à votre pipeline](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview).
