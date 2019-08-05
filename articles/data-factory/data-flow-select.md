---
title: Transformation de sélection (Select) de mappage de Data Flow pour Azure Data Factory
description: Transformation de sélection (Select) de mappage de Data Flow pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 15c74637a2dc42ec44f582878b5505d94637cd7b
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314221"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Transformation de sélection (Select) de mappage de Data Flow pour Azure Data Factory
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilisez cette transformation pour la sélectivité de colonne (en réduisant le nombre de colonnes), pour attribuer des alias aux noms de colonnes et de flux et pour réorganiser les colonnes.

## <a name="how-to-use-select-transformation"></a>Guide pratique pour l’utilisation des transformations
La transformation de sélection vous permet d’attribuer un alias à l’intégralité d’un flux, ou à des colonnes dans ce flux, d’attribuer des noms différents (alias), puis de référencer ces nouveaux noms ultérieurement dans votre flux de données. Cette transformation est utile pour les scénarios de jointure réflexive. La façon d’implémenter une jointure réflexive dans ADF Data Flow consiste à prendre un flux, d’en créer une nouvelle branche avec « Nouvelle branche », et immédiatement après, d’ajouter une transformation « Select ». Ce flux de données a maintenant un nouveau nom que vous pouvez utiliser pour la jointure au flux d’origine, créant ainsi une jointure réflexive :

![Jointure réflexive](media/data-flow/selfjoin.png "Jointure réflexive")

Dans le diagramme ci-dessus, la transformation de sélection (Select) se trouve en haut. Cela applique l’alias « OrigSourceBatting » au flux d’origine. Dans la transformation de jointure (Join) mise en évidence en dessous, vous pouvez voir que nous utilisons ce flux d’alias de sélection (Select) en tant que jointure de droite, ce qui nous permet de faire référence à la même clé du côté gauche et du côté droit de la jointure interne.

Le flux Select peut également être utilisé comme un moyen de désélectionner les colonnes à partir de votre flux de données. Par exemple, si vous avez 6 colonnes définies dans votre récepteur, mais que vous souhaitez choisir uniquement 3 colonnes spécifiques à transformer et à transmettre au récepteur, vous pouvez sélectionner uniquement ces 3 colonnes à l’aide de la transformation de sélection.

> [!NOTE]
> Vous devez désactiver « Sélectionner tout » pour sélectionner uniquement des colonnes spécifiques

![Transformation de sélection (Select)](media/data-flow/select001.png "Alias Select")

## <a name="options"></a>Options
* Le paramètre par défaut pour « Sélectionner » consiste à inclure toutes les colonnes d’entrée et de conserver ces noms d’origine. Vous pouvez appliquer un alias au flux de données en définissant le nom de la transformation de sélection (Select).
* Pour appliquer des alias à des colonnes individuelles, désactivez l’option « Sélectionner tout » et utilisez le mappage de colonnes en bas.
* Choisissez Skip Duplicates (Ignorer les doublons) pour éliminer les colonnes dupliquées des métadonnées d’entrée ou de sortie.

![Ignorer les doublons](media/data-flow/select-skip-dup.png "Ignorer les doublons")

## <a name="next-steps"></a>Étapes suivantes
* Après l’utilisation de Select pour renommer, réorganiser et attribuer des alias aux colonnes, utilisez la [transformation de récepteur Sink](data-flow-sink.md) pour placer vos données dans un magasin de données.
