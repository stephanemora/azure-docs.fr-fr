---
title: Jointures dans des requêtes Azure Log Analytics | Microsoft Docs
description: Cet article inclut une leçon sur l’utilisation de jointures dans le langage de requête Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 39a461a27e8d9d6d1b9712449586bfabf6124d22
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989445"
---
# <a name="joins-in-log-analytics-queries"></a>Jointures dans des requêtes Log Analytics

> [!NOTE]
> Vous devez suivre [Bien démarrer avec le portail Analytics](get-started-analytics-portal.md) et [Bien démarrer avec les requêtes](get-started-queries.md) avant d’effectuer cette leçon.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Les jointures vous permettent d’analyser les données provenant de plusieurs tables, dans la même requête. Elles fusionnent les lignes de deux jeux de données en faisant correspondre les valeurs des colonnes spécifiées.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

Dans cet exemple, le premier jeu de données filtre tous les événements de connexion. Un deuxième jeu de données se joint au premier, qui filtre tous les événements de déconnexion. Les colonnes projetées sont _Computer_, _Account_, _TargetLogonId_ et _TimeGenerated_. Les jeux de données sont corrélés par une colonne partagée, _TargetLogonId_. La sortie est un seul enregistrement par corrélation, avec à la fois l’heure de connexion et de déconnexion.

Si les deux jeux de données ont des colonnes portant le même nom, les colonnes du jeu de données de droite reçoivent un numéro d’index. Ainsi, les résultats dans cet exemple affichent _TargetLogonId_ avec les valeurs de la table de gauche et _TargetLogonId1_ avec les valeurs de la table de droite. Dans ce cas, la deuxième colonne _TargetLogonId1_ a été supprimée à l’aide de l’opérateur `project-away`.

> [!NOTE]
> Pour améliorer les performances, conservez uniquement les colonnes appropriées des jeux de données joints à l’aide de l’opérateur `project`.


Utilisez la syntaxe suivante pour joindre deux jeux de données et la clé jointe porte un nom différent entre les deux tables :
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Tables de recherche
Une utilisation courante des jointures est l’emploi de mappage statique de valeurs à l’aide de `datatable` qui permet de rendre les résultats plus présentables, par exemple pour enrichir les données d’événements de sécurité avec le nom de l’événement pour chaque ID d’événement.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![Joindre avec un datatable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Types de jointures
Spécifiez le type de jointure avec l’argument _kind_. Chaque type effectue une correspondance différente entre les enregistrements des tables données comme décrit dans le tableau suivant.

| Type de jointure | Description |
|:---|:---|
| innerunique | Il s’agit du mode de jointure par défaut. Tout d’abord, les valeurs de la colonne correspondante dans la table de gauche sont recherchées et les valeurs en double sont supprimées.  Ensuite, le jeu de valeurs uniques est comparé à la table de droite. |
| interne | Seuls les enregistrements correspondants dans les deux tables sont inclus dans les résultats. |
| leftouter | Tous les enregistrements dans la table de gauche et les enregistrements correspondants dans la table de droite sont inclus dans les résultats. Les propriétés de sortie sans correspondance contiennent des valeurs null.  |
| leftanti | Les enregistrements du côté gauche n’ayant pas de correspondance du côté droit sont inclus dans les résultats. La table de résultats comporte uniquement les colonnes de la table de gauche. |
| leftsemi | Les enregistrements du côté gauche ayant des correspondance du côté droit sont inclus dans les résultats. La table de résultats comporte uniquement les colonnes de la table de gauche. |


## <a name="best-practices"></a>Meilleures pratiques

Tenez compte des points suivants pour optimiser les performances :

- Utilisez un filtre de temps sur chaque table pour réduire le nombre d’enregistrements qui doivent être évalués pour la jointure.
- Utilisez `where` et `project` pour réduire le nombre de lignes et de colonnes dans les tables d’entrée avant la jointure.
* Si une table est toujours plus petite que l’autre, utilisez-la pour le côté gauche de la jointure.


## <a name="next-steps"></a>Étapes suivantes
Consultez d’autres leçons pour l’utilisation du langage de requête Log Analytics :

- [Opérations de chaîne](string-operations.md)
- [Fonctions d’agrégation](aggregations.md)
- [Agrégations avancées](advanced-aggregations.md)
- [JSON et structures de données](json-data-structures.md)
- [Écriture de requêtes avancées](advanced-query-writing.md)
- [Graphiques](charts.md)