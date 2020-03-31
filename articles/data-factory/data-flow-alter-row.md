---
title: Transformation de modification de ligne dans le flux de données de mappage
description: Guide pratique pour mettre à jour la cible de base de données à l’aide de la transformation de modification de ligne dans le flux de données de mappage
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 0798a3f9ab45ce68086681e7aea96deeb9639f94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834539"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Transformation de modification de ligne dans le flux de données de mappage

Utiliser la transformation de Alter Row pour définir des stratégies insert, delete, update et upsert sur les lignes. Vous pouvez ajouter des conditions de type un-à-plusieurs en tant qu’expressions. Ces conditions doivent être spécifiées par ordre de priorité, car chaque ligne sera marquée avec la stratégie liée à la première correspondance. Chacune de ces conditions peut entraîner l'insertion, la mise à jour, la suppression ou l'upsert d'une ligne (ou de plusieurs lignes). Alter Row peut produire des actions DDL et DML sur votre base de données.

![Paramètres d’Alter Row](media/data-flow/alter-row1.png "Paramètres d’Alter Row")

Les transformations Alter Row ne fonctionnent que sur les récepteurs de base de données ou CosmosDB de votre flux de données. Les actions que vous affectez aux lignes (insert, update, delete, upsert) n’ont pas lieu au cours des sessions de débogage. Exécutez une activité Exécuter un flux de données dans un pipeline pour activer les stratégies de modification de ligne sur vos tables de base de données.

## <a name="specify-a-default-row-policy"></a>Spécifier une stratégie de ligne par défaut

Créez une transformation Alter Row et spécifiez une stratégie de ligne associée à la condition `true()`. Chaque ligne qui ne correspond à aucune des expressions définies précédemment sera marquée pour la stratégie de ligne spécifiée. Par défaut, chaque ligne qui ne correspond à aucune expression conditionnelle sera marquée pour `Insert`.

![Stratégie de modification de ligne](media/data-flow/alter-row4.png "Stratégie de modification de ligne")

> [!NOTE]
> Pour associer une stratégie à toutes les lignes, vous pouvez créer une condition pour cette stratégie et spécifier la condition comme `true()`.

## <a name="view-policies-in-data-preview"></a>Afficher les stratégies dans l’aperçu des données

Utilisez le [mode de débogage](concepts-data-flow-debug-mode.md) pour afficher les résultats de vos stratégies de modification de ligne dans le volet d’aperçu des données. Un aperçu des données d’une transformation de modification de ligne ne génèrera pas d’actions DDL ni DML sur votre cible.

![Stratégies Alter Row](media/data-flow/alter-row3.png "Stratégies Alter Row")

Chaque stratégie de modification de ligne est représentée par une icône qui indique si une action d’insertion, de mise à jour, upsert ou de suppression se produira. L’en-tête supérieur indique le nombre de lignes affectées par chaque stratégie dans l’aperçu.

## <a name="allow-alter-row-policies-in-sink"></a>Autoriser les stratégies de modification de ligne dans le récepteur

Pour que les stratégies de modification de ligne fonctionnent, le flux de données doit écrire dans une base de données ou un récepteur Cosmos. Dans l’onglet **Paramètres** de votre récepteur, activez les stratégies de modification de ligne autorisées pour ce récepteur.

![Récepteur Alter Row](media/data-flow/alter-row2.png "Récepteur Alter Row")

 Le comportement par défaut est d’autoriser uniquement les insertions. Pour autoriser les mises à jour, les opérations upsert ou les suppressions, cochez la case dans le récepteur correspondant à cette condition. Si les mises à jour, les opérations upsert ou les suppressions sont activées, vous devez spécifier les colonnes clés du récepteur sur lesquelles effectuer la correspondance.

> [!NOTE]
> Si vos insertions, mises à jour ou opérations upsert modifient le schéma de la table cible du récepteur, le flux de données échoue. Pour modifier le schéma cible dans votre base de données, choisissez **Recréer la table** en tant qu’action de table. Cela supprime et recrée votre table selon la nouvelle définition de schéma.

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Exemple

L’exemple ci-dessous est une transformation de modification de ligne nommée `CleanData` qui prend un flux entrant `SpecifyUpsertConditions` et crée trois conditions de modification de ligne. Dans la transformation précédente, une colonne nommée `alterRowCondition` est calculée pour déterminer si une ligne est ou n’est pas insérée, mise à jour ou supprimée dans la base de données. Si la valeur de la colonne a une valeur de chaîne qui correspond à la règle de modification de ligne, cette stratégie lui est affectée.

Dans l’expérience utilisateur Data Factory, cette transformation se présente comme dans l’image ci-dessous :

![Exemple de modification de ligne](media/data-flow/alter-row4.png "Exemple de modification de ligne")

Le script de flux de données pour cette transformation se trouve dans l’extrait de code ci-dessous :

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Étapes suivantes

Une fois la transformation Alter Row effectuée, vous souhaiterez peut-être [transférer vos données vers un magasin de données de destination](data-flow-sink.md).
