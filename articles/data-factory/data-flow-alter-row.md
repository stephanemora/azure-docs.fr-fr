---
title: Transformation de Alter Row du flux de données de mappage Azure Data Factory
description: Comment mettre à jour la cible de base de données à l’aide de la transformation de Alter Row du flux de données de mappage Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: f0ac5bb36079983b10e4d86cc776bd4e5ee6817d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65520144"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Transformation de Alter Row du flux de données de mappage Azure Data Factory

Utiliser la transformation de Alter Row pour définir des stratégies insert, delete, update et upsert sur les lignes. Vous pouvez ajouter des conditions de type un-à-plusieurs en tant qu’expressions. Chacune de ces conditions peut entraîner l’insertion, la mise à jour, la suppression ou l’upsert d’une ligne (ou de lignes). Alter Row peut produire des actions DDL et DML sur votre base de données.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Paramètres Alter row](media/data-flow/alter-row1.png "Paramètres Alter row")

> [!NOTE]
> Les transformations Alter Row ne fonctionnent que sur les récepteurs de base de données de votre flux de données. Les actions que vous attribuez aux lignes (insert, update, delete, upsert) n’ont pas lieu au cours des sessions de débogage. Vous devez ajouter une tâche Exécuter le flux de données à un pipeline et utiliser le débogage de pipeline ou des déclencheurs pour mettre en œuvre les stratégies Alter Row sur vos tables de base de données.

## <a name="view-policies"></a>Afficher les stratégies

Activez le mode de débogage du flux de données et affichez les résultats de vos stratégies Alter row dans le volet d’aperçu des données. L’exécution d’une commande Alter Row en mode de débogage du flux de données ne produira pas d’actions DDL ou DML sur votre cible. Pour que ces actions se produisent, exécutez le flux de données au sein d’une activité d’exécution de flux de données à l’intérieur d’un pipeline.

![Stratégies Alter Row](media/data-flow/alter-row3.png "Stratégies Alter Row")

Cela permet de vérifier et d’afficher l’état de chaque ligne selon vos conditions. Des icônes représentent chaque action insert, update, delete et upsert survenant dans votre flux de données, et qui indiquent quelle action a lieu lorsque vous exécutez le flux de données au sein d’un pipeline.

## <a name="sink-settings"></a>Paramètres de récepteur

Vous devez disposer d’un type de récepteur de base de données pour que Alter Row fonctionne. Dans les paramètres de récepteur, vous devez définir chaque action à autoriser.

![Récepteur Alter Row](media/data-flow/alter-row2.png "Récepteur Alter Row")

Le comportement par défaut dans un flux de données ADF avec des récepteurs de base de données consiste à insérer des lignes. Si vous souhaitez autoriser des mises à jour, des suppressions et des upserts, vous devez également cocher ces zones dans le récepteur pour autoriser les actions.

> [!NOTE]
> Si vos insertions, mises à jour ou upserts modifient le schéma de la table cible du récepteur, le flux de données échoue. Afin de modifier le schéma cible de votre base de données, vous devez choisir l’option « Recréer la table » dans le récepteur. Cela supprime et recrée votre table selon la nouvelle définition de schéma.

## <a name="next-steps"></a>Étapes suivantes

Une fois la transformation Alter Row effectuée, vous souhaiterez peut-être [transférer vos données vers un magasin de données de destination](data-flow-sink.md).
