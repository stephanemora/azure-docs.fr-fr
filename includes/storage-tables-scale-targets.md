---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/29/2019
ms.author: tamram
ms.openlocfilehash: 6bbd2a421122a05399d35be1b61e65f505253ed2
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420774"
---
| Ressource | Cible |
|----------|---------------|
| Taille maximale d’une table unique | 500 Tio |
| Taille maximale d’une entité de table | 1 Mio |
| Nombre maximal de propriétés dans une entité de table | 255, ce qui inclut les trois propriétés système : PartitionKey, RowKey et Timestamp |
| Taille totale maximale de valeurs de propriété dans une entité | 1 Mio |
| Nombre maximal de stratégies d’accès stockées par table | 5. |
| Taux de requête maximal par compte de stockage | 20 000 transactions par seconde, ce qui suppose une taille d’entité de 1-Kio |
| Débit cible pour une partition de table unique (-entités de 1 Kio) | Jusqu'à 2 000 entités par seconde |
