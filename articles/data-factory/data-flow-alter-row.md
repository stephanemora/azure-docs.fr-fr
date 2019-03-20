---
title: Azure Data Factory mappage de Transformation de ligne de modification de flux de données
description: Comment mettre à jour de la cible de base de données à l’aide d’Azure Data Factory mappage flux Alter ligne Transformation des données
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: d842898ca700490ae99b46140be6609622a144df
ms.sourcegitcommit: 5f41e855d415cfa741d8f710792ea486480df5cf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58133159"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Transformation de ligne de Alter Azure Data Factory

Utiliser la transformation de ligne de modification pour définir des stratégies insert, delete, update et upsert sur les lignes. Vous pouvez ajouter des conditions de type un-à-plusieurs en tant qu’expressions. Chacune de ces conditions peut entraîner une (ou plusieurs lignes) inséré, mis à jour, supprimées ou upsert. Ligne de ALTER peut produire des actions DDL et DML sur votre base de données.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Modifier les paramètres de ligne](media/data-flow/alter-row1.png "modifier les paramètres de ligne")

> [!NOTE]
> Transformations de ligne de ALTER fonctionnera uniquement sur les récepteurs de base de données dans votre flux de données. Les actions que vous attribuez aux lignes (insert, update, delete, upsert) ne sont pas lieu au cours de sessions de débogage. Vous devez ajouter une tâche Exécuter le flux de données à un pipeline et utiliser des déclencheurs ou débogage de pipeline pour mettre en œuvre les stratégies de la ligne alter sur vos tables de base de données.

## <a name="view-policies"></a>Afficher les stratégies

Basculer le mode de déboguer des flux de données sur activé et affichez les résultats de vos stratégies de la ligne alter dans le volet de visualisation de données. L’exécution d’une ligne alter en mode de données de flux de débogage ne produira pas les actions DDL ou DML par rapport à votre cible. Pour que ces actions se produise, exécuter le flux de données à l’intérieur d’une activité d’exécution de flux de données à l’intérieur d’un pipeline.

![Modifier les stratégies de ligne](media/data-flow/alter-row3.png "modifier les stratégies de ligne")

Cela vous permettra de vérifier et afficher l’état de chaque ligne en fonction de vos conditions. Icône représente pour chaque instruction insert, update, delete et upsert action qui se produisent dans votre flux de données, qui indique quelle action aura lieu lorsque vous exécutez le flux de données à l’intérieur d’un pipeline.

## <a name="sink-settings"></a>Paramètres du récepteur

Vous devez avoir une base de données récepteur de type de la ligne Alter fonctionne. Dans le récepteur de paramètres, vous devez définir chaque action à autoriser.

![ALTER récepteur de ligne](media/data-flow/alter-row2.png "Alter de récepteur de ligne")

Le comportement par défaut dans le flux de données ADF avec les récepteurs de la base de données consiste à insérer des lignes. Si vous souhaitez autoriser les mises à jour et suppressions ainsi upserts, vous devez également vérifier ces zones dans le récepteur pour autoriser les actions.

> [!NOTE]
> Si votre insertions, mises à jour ou upserts modifier le schéma de la table cible dans le récepteur, votre flux de données échoue. Afin de modifier le schéma cible dans votre base de données, vous devez choisir l’option « Recréez la table » dans le récepteur. Cela sera supprimez et recréez votre table avec la nouvelle définition de schéma.

## <a name="next-steps"></a>Étapes suivantes

Après la transformation de ligne de modification, vous pouvez souhaiter [récepteur vos données dans un magasin de données de destination](data-flow-sink.md).
