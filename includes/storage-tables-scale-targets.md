---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457328"
---
| Ressource | Cible |
|----------|---------------|
| Taille maximale d’une seule table | 500 Tio |
| Taille maximale d'une entité de table | 1 Mio |
| Nombre maximal de propriétés dans une entité de table | 255, ce qui inclut les trois propriétés système : PartitionKey, RowKey et Timestamp |
| Taille totale maximale de valeurs de propriété dans une entité | 1 Mio |
| Taille totale maximale d’une propriété individuelle dans une entité | Varie en fonction du type de propriété. Pour plus d'informations, consultez **Types de propriétés** dans [Présentation du modèle de données du service de Table](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Nombre maximal de stratégies d’accès stockées par table | 5\. |
| Taux de requête maximal par compte de stockage | 20 000 transactions par seconde, en supposant que la taille d’entité est de 1 Kio |
| Débit cible pour une partition de table unique (entités de 1 Kio) | Jusqu'à 2 000 entités par seconde |