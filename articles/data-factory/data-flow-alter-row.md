---
title: Transformation Alter Row du flux de données de mappage
description: Comment mettre à jour la cible de base de données à l’aide de la transformation de Alter Row du flux de données de mappage Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2019
ms.openlocfilehash: 1301b89ef1a6fb02356c6dcd4e568401eb5e9cd2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930474"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Transformation de Alter Row du flux de données de mappage Azure Data Factory

Utiliser la transformation de Alter Row pour définir des stratégies insert, delete, update et upsert sur les lignes. Vous pouvez ajouter des conditions de type un-à-plusieurs en tant qu’expressions. Ces conditions doivent être spécifiées par ordre de priorité, car chaque ligne sera marquée avec la stratégie liée à la première correspondance. Chacune de ces conditions peut entraîner l'insertion, la mise à jour, la suppression ou l'upsert d'une ligne (ou de plusieurs lignes). Alter Row peut produire des actions DDL et DML sur votre base de données.



![Paramètres d’Alter Row](media/data-flow/alter-row1.png "Paramètres d’Alter Row")

> [!NOTE]
> Les transformations Alter Row ne fonctionnent que sur les récepteurs de base de données ou CosmosDB de votre flux de données. Les actions que vous attribuez aux lignes (insert, update, delete, upsert) n’ont pas lieu au cours des sessions de débogage. Vous devez ajouter une tâche Exécuter le flux de données à un pipeline et utiliser le débogage de pipeline ou des déclencheurs pour mettre en œuvre les stratégies Alter Row sur vos tables de base de données.

## <a name="indicate-a-default-row-policy"></a>Indiquer une stratégie de ligne par défaut

Créez une transformation Alter Row et spécifiez une stratégie de ligne associée à la condition `true()`. Les lignes qui ne correspondent à aucune des expressions définies précédemment seront associées à la stratégie de ligne spécifiée. Par défaut, les lignes qui ne correspondent à aucune expression conditionnelle seront associées à `Insert`.

![Une seule stratégie Alter Row](media/data-flow/alter-row4.png "Une seule stratégie Alter Row")

> [!NOTE]
> Pour associer une stratégie à toutes les lignes, vous pouvez créer une condition pour cette stratégie et spécifier la condition comme `true()`.

## <a name="view-policies"></a>Afficher les stratégies

Activez le mode de débogage du flux de données et affichez les résultats de vos stratégies Alter Row dans le volet d'aperçu des données. L’exécution d’une commande Alter Row en mode de débogage du flux de données ne produira pas d’actions DDL ou DML sur votre cible. Pour que ces actions se produisent, exécutez le flux de données au sein d'une activité d'exécution de flux de données au sein d'un pipeline.

![Stratégies Alter Row](media/data-flow/alter-row3.png "Stratégies Alter Row")

Cela permet de vérifier et d’afficher l’état de chaque ligne selon vos conditions. Des icônes représentent chaque action insert, update, delete et upsert survenant dans votre flux de données, et qui indiquent quelle action a lieu lorsque vous exécutez le flux de données au sein d’un pipeline.

## <a name="sink-settings"></a>Paramètres de récepteur

Vous devez disposer d’un type de récepteur de base de données pour que Alter Row fonctionne. Dans le récepteur Paramètres, vous devez définir chaque action correspondant aux conditions Alter Row à autoriser.

![Récepteur Alter Row](media/data-flow/alter-row2.png "Récepteur Alter Row")

Le comportement par défaut dans un flux de données ADF avec des récepteurs de base de données consiste à insérer des lignes. Si vous souhaitez autoriser des mises à jour, des suppressions et des upserts, vous devez également cocher ces zones dans le récepteur pour autoriser les actions.

> [!NOTE]
> Si vos insertions, mises à jour ou upserts modifient le schéma de la table cible du récepteur, le flux de données échoue. Afin de modifier le schéma cible de votre base de données, vous devez choisir l’option « Recréer la table » dans le récepteur. Cela supprime et recrée votre table selon la nouvelle définition de schéma.

## <a name="next-steps"></a>Étapes suivantes

Une fois la transformation Alter Row effectuée, vous souhaiterez peut-être [transférer vos données vers un magasin de données de destination](data-flow-sink.md).
