---
title: Supprimer des données de l’Explorateur de données Azure
description: Cet article décrit des scénarios de suppression dans Azure Data Explorer, notamment des suppressions basées sur un vidage, un abandon d’étendues ou une rétention.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: dd0f8740d148a7817bcfe2fbad591ceeb1610d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501418"
---
# <a name="delete-data-from-azure-data-explorer"></a>Supprimer des données de l’Explorateur de données Azure

Azure Data Explorer prend en charge différents scénarios de suppression décrits dans cet article. 

## <a name="delete-data-using-the-retention-policy"></a>Supprimer des données en utilisant la stratégie de rétention

Azure Data Explorer supprime automatiquement des données en fonction de la [stratégie de rétention](/azure/kusto/management/retentionpolicy). Cette méthode est la manière la plus efficace et simple de supprimer des données. Définissez la stratégie de rétention au niveau de la base de données ou de la table.

Prenons l’exemple d’une base de données ou d’une table qui est définie pour être conservée 90 jours. Si seuls 60 jours de données sont nécessaires, supprimez les données plus anciennes comme suit :

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Supprimer des données en abandonnant des étendues

Une [étendue (partition de données)](/azure/kusto/management/extents-overview) est la structure interne dans laquelle des données sont stockées. Chaque étendue peut contenir des millions d’enregistrements. Il est possible de supprimer des étendues individuellement ou en tant que groupe à l’aide de [commandes d’abandon d’étendue(s)](/azure/kusto/management/extents-commands#drop-extents). 

### <a name="examples"></a>Exemples

Vous pouvez supprimer toutes les lignes d’une table ou juste une étendue spécifique.

* Supprimer toutes les lignes d’une table :

    ```kusto
    .drop extents from TestTable
    ```

* Supprimer une étendue spécifique :

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Supprimer des lignes individuelles par vidage

Vous pouvez utiliser un [vidage des données](/azure/kusto/concepts/data-purge) pour supprimer des lignes individuelles. La suppression n’est pas immédiate et nécessite des ressources système significatives. Par conséquent, un vidage n’est recommandé qu’à des fins de conformité.  

