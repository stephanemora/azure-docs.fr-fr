---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5b7aa618df63bf813e0ab0d77025cf57cfe55c3a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50964543"
---
| Ressource | Cible |
|----------|---------------|
| Taille maximale de la table unique | 500 Tio |
| Taille maximale d'une entité de table | 1 Mio |
| Nombre maximal de propriétés dans une entité de table | 255 (notamment 3 propriétés système : PartitionKey, RowKey et Timestamp) |
| Nombre maximal de stratégies d’accès stockées par table | 5. |
| Taux de requête maximal par compte de stockage | 20 000 transactions par seconde (en supposant que la taille d’entité soit de 1 Kio) |
| Débit cible pour une partition de table unique (entités de 1 Kio) | Jusqu'à 2 000 entités par seconde |
