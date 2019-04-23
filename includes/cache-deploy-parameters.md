---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60118536"
---
### <a name="cacheskuname"></a>cacheSKUName

Niveau de tarification du nouveau cache Azure pour Redis.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

Le modèle définit les valeurs autorisées pour ce paramètre (Basic, Standard ou Premium) et affecte une valeur par défaut (Basic) si aucune valeur n’est spécifiée. Le niveau De base fournit un nœud unique disponible en plusieurs tailles, jusqu’à 53 Go. Le niveau Standard fournit deux nœuds primaire/réplica disponibles en plusieurs tailles jusqu’à 53 Go et un contrat SLA de 99,9 %.

### <a name="cacheskufamily"></a>cacheSKUFamily

Famille de la référence (SKU).

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

Taille de la nouvelle instance de cache Azure pour Redis.

Pour les familles de base et Standard :

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

La capacité de cache Premium valeur est définie par le même, sauf les valeurs autorisées s’exécutent pendant 1 à 5 au lieu de 0 à 6.

Le modèle définit les valeurs entières autorisées pour ce paramètre (0 à 6 pour les familles de base et Standard ; 1 à 5 pour la famille de Premium). Si aucune valeur n’est spécifiée, le modèle affecte une valeur par défaut de 0 pour la base et Standard, 1 pour le niveau Premium.

Les valeurs correspondent aux tailles de cache suivantes :

| Valeur | Base et Standard<br>Taille du cache | Premium<br>Taille du cache |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 Mo (valeur par défaut)                 | n/a                   |
| 1     | 1 Go                             | 6 Go (valeur par défaut)        |
| 2     | 2,5 Go                           | 13 Go                 |
| 3     | 6 Go                             | 26 Go                 |
| 4     | 13 Go                            | 53 Go                 |
| 5.     | 26 Go                            | 120 Go                |
| 6.     | 53 Go                            | n/a                   |
