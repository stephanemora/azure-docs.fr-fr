---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: a8b4e3038bfa6a2e937de91804159e340ed13224
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553698"
---
| Ressource | Cible |
|----------|---------------|
| Taille maximale d’une table unique | 500 Tio |
| Taille maximale d’une entité de table | 1 Mio |
| Nombre maximal de propriétés dans une entité de table | 255, ce qui inclut les trois propriétés système : PartitionKey, RowKey et Timestamp |
| Nombre maximal de stratégies d’accès stockées par table | 5. |
| Taux de requête maximal par compte de stockage | 20 000 transactions par seconde, ce qui suppose une taille d’entité de 1-Kio |
| Débit cible pour une partition de table unique (-entités de 1 Kio) | Jusqu'à 2 000 entités par seconde |
