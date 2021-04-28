---
title: Plusieurs branches dans le flux de données de mappage
description: Réplication de flux de données dans le flux de données de mappage avec plusieurs branches
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/16/2021
ms.openlocfilehash: f9f2bf2e2204e6b74bb8a31ac856dbe276a6e983
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588749"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Création d’une nouvelle branche dans le flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ajoutez une nouvelle branche pour effectuer plusieurs ensembles d’opérations et de transformations sur le même flux de données. L’ajout d’une nouvelle branche est utile lorsque vous souhaitez utiliser la même source pour plusieurs récepteurs ou pour effectuer la jointure réflexive des données.

Une nouvelle branche peut être ajoutée à partir de la liste des transformations de la même façon que d’autres transformations. L’action **Nouvelle branche** est disponible uniquement lorsqu’une transformation existante suit la transformation pour laquelle vous tentez de créer une branche.

![Capture d’écran montrant l’option Nouvelle branche dans le menu Plusieurs entrées/sorties.](media/data-flow/new-branch2.png "Ajout d’une nouvelle branche")

Dans l’exemple ci-dessous, le flux de données lit des données de trajet en taxi. La sortie agrégée par jour et par fournisseur est requise. Au lieu de créer deux flux de données distincts qui lisent à partir de la même source, il est possible d’ajouter une nouvelle branche. De cette façon, les deux agrégations peuvent être exécutées dans le cadre du même flux de données. 

![Capture d’écran montrant le flux de données avec deux branches issues de la source.](media/data-flow/new-branch.png "Ajout d’une nouvelle branche")

> [!NOTE]
> Quand vous cliquez sur le signe plus (+) pour ajouter des transformations à votre graphe, l’option Nouvelle branche s’affiche uniquement lorsqu’il existe des blocs de transformation ultérieurs. Cela est dû au fait que Nouvelle branche crée une référence au flux existant, et requiert un traitement en amont supplémentaire sur lequel opérer. Si vous ne voyez pas l’option Nouvelle branche, ajoutez d’abord une transformation Colonne dérivée ou autre, puis revenez au bloc précédent ; vous verrez alors l’option Nouvelle branche.

## <a name="next-steps"></a>Étapes suivantes

Une fois la branche créée, vous pouvez utiliser les [transformations de flux de données](data-flow-transformation-overview.md).
