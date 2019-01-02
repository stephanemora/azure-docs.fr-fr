---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/21/2018
ms.author: wesmc
ms.openlocfilehash: dd9700c9472e07daf294eca12b766e3dc4832955
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111966"
---
### <a name="cacheskuname"></a>cacheSKUName
Niveau de tarification du nouveau cache Azure pour Redis.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },

Le modèle définit les valeurs autorisées pour ce paramètre (De base ou Standard) et affecte une valeur par défaut (De base) si aucune valeur n’est spécifiée. Le niveau De base fournit un nœud unique disponible en plusieurs tailles, jusqu’à 53 Go.
Le niveau Standard fournit deux nœuds primaire/réplica disponibles en plusieurs tailles jusqu’à 53 Go et un contrat SLA de 99,9 %.

### <a name="cacheskufamily"></a>cacheSKUFamily
Famille de la référence (SKU).

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
Taille de la nouvelle instance de cache Azure pour Redis. 

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


Le modèle définit les valeurs autorisées pour ce paramètre (0, 1, 2, 3, 4, 5 ou 6) et affecte une valeur par défaut (0) si aucune valeur n’est spécifiée. Ces chiffres correspondent aux tailles de cache suivantes : 0 = 250 Mo, 1 = 1 Go, 2 = 2,5 Go, 3 = 6 Go, 4 = 13 Go, 5 = 26 Go, 6 = 53 Go

