---
title: Utilisation de chaînes dans des requêtes de journal dans Azure Monitor | Microsoft Docs
description: Cet article présente un tutoriel décrivant l’utilisation d’Azure Monitor Log Analytics dans le portail Azure pour interroger et analyser des données de journal dans Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: f792820b7b0dff20e647031410ba87ac26c2495a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80672976"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Utilisation de JSON et de structures de données dans des requêtes de journal dans Azure Monitor

> [!NOTE]
> Avant de suivre ce tutoriel, vous devez avoir suivi les tutoriels [Bien démarrer avec Azure Monitor Log Analytics](get-started-portal.md) et [Bien démarrer avec les requêtes de journal Azure Monitor](get-started-queries.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Les objets imbriqués sont des objets qui contiennent d’autres objets dans un tableau ou un mappage de paires clé-valeur. Ces objets sont représentés sous forme de chaînes JSON. Cet article décrit l’utilisation de JSON pour récupérer des données et analyser les objets imbriqués.

## <a name="working-with-json-strings"></a>Utilisation de chaînes JSON
Utilisez `extractjson` pour accéder à un élément JSON spécifique dans un chemin connu. Cette fonction nécessite une expression de chemin qui utilise les conventions suivantes.

- _$_ pour faire référence au dossier racine
- Utilisez la notation entre crochets ou sous forme de points pour faire référence aux index et aux éléments, comme illustré dans les exemples suivants.


Utilisez des crochets pour les index et des points pour séparer les éléments :

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Voici le même résultat en utilisant uniquement la notation entre crochets :

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

S’il n’existe qu’un seul élément, vous pouvez utiliser uniquement la notation sous forme de points :

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Utilisation d’objets

### <a name="parsejson"></a>parsejson
Pour accéder à plusieurs éléments dans votre structure json, il est plus facile d’y accéder en tant qu’objet dynamique. Utilisez `parsejson` pour caster des données de texte en objet dynamique. Une fois les données converties en un type dynamique, d’autres fonctions peuvent être utilisées pour analyser les données.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Utilisez `arraylength` pour compter le nombre d’éléments dans un tableau :

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Utilisez `mvexpand` pour répartir les propriétés d’un objet en lignes séparées.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Utilisez `buildschema` pour obtenir le schéma qui admet toutes les valeurs d’un objet :

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

La sortie est un schéma au format JSON :
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
Cette sortie décrit les noms des champs d’objets et leurs types de données correspondants. 

Les objets imbriqués peuvent avoir des schémas différents, comme dans l’exemple suivant :

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Générer le schéma](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Étapes suivantes
Voir d’autres leçons pour utiliser des requêtes de journal dans Azure Monitor :

- [Opérations de chaîne](string-operations.md)
- [Opérations de date et d’heure](datetime-operations.md)
- [Fonctions d’agrégation](aggregations.md)
- [Agrégations avancées](advanced-aggregations.md)
- [Écriture de requêtes avancées](advanced-query-writing.md)
- [Joins](joins.md)
- [Graphiques](charts.md)
