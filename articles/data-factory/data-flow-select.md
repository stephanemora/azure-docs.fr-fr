---
title: Transformation de sélection (Select) de mappage de Data Flow pour Azure Data Factory
description: Transformation de sélection (Select) de mappage de Data Flow pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6b33568354653e2b222dd99d7a933de252646f9e
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271148"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Transformation de sélection (Select) de mappage de Data Flow pour Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilisez cette transformation pour la sélectivité de colonne (en réduisant le nombre de colonnes) ou pour attribuer des alias aux noms de colonnes et de flux.

La transformation de sélection vous permet d’attribuer un alias à l’intégralité d’un flux, ou à des colonnes dans ce flux, d’attribuer des noms différents (alias), puis de référencer ces nouveaux noms ultérieurement dans votre flux de données. Cette transformation est utile pour les scénarios de jointure réflexive. La façon d’implémenter une jointure réflexive dans ADF Data Flow consiste à prendre un flux, d’en créer une nouvelle branche avec « Nouvelle branche », et immédiatement après, d’ajouter une transformation « Select ». Ce flux de données a maintenant un nouveau nom que vous pouvez utiliser pour la jointure au flux d’origine, créant ainsi une jointure réflexive :

![Jointure réflexive](media/data-flow/selfjoin.png "Jointure réflexive")

Dans le diagramme ci-dessus, la transformation de sélection (Select) se trouve en haut. Cela applique l’alias « OrigSourceBatting » au flux d’origine. Dans la transformation de jointure (Join) mise en évidence en dessous, vous pouvez voir que nous utilisons ce flux d’alias de sélection (Select) en tant que jointure de droite, ce qui nous permet de faire référence à la même clé du côté gauche et du côté droit de la jointure interne (Inner).

Le flux Select peut également être utilisé comme un moyen de désélectionner les colonnes à partir de votre flux de données. Par exemple, si vous avez 6 colonnes définies dans votre récepteur, mais que vous souhaitez choisir uniquement 3 colonnes spécifiques à transformer et à transmettre au récepteur, vous pouvez sélectionner uniquement ces 3 colonnes à l’aide de la transformation de sélection.

> [!NOTE]
> Vous devez désactiver « Sélectionner tout » pour sélectionner uniquement des colonnes spécifiques

Options

Le paramètre par défaut pour « Sélectionner » consiste à inclure toutes les colonnes d’entrée et de conserver ces noms d’origine. Vous pouvez appliquer un alias au flux de données en définissant le nom de la transformation de sélection (Select).

Pour appliquer des alias à des colonnes individuelles, désactivez l’option « Sélectionner tout » et utilisez le mappage de colonnes en bas.

![Transformation de sélection (Select)](media/data-flow/select001.png "Alias Select")
