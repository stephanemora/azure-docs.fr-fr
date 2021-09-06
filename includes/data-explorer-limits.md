---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: 86b2353109d4005594ee08e27283d6aa9c654120
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078373"
---
Le tableau suivant décrit les limites maximales pour les clusters Azure Data Explorer.

| Ressource | Limite |
| --- | --- |
| Clusters par région et par abonnement | 20 |
| Instances par cluster | 1 000 | 
| Nombre de bases de données dans un cluster | 10 000 |
| Nombre de clusters abonné (consommateurs de partage de données) par cluster leader (producteur de partage de données) | 100 |

Le tableau suivant décrit les limites relatives aux opérations de gestion effectuées sur les clusters Azure Data Explorer.

| Étendue | Opération | Limite |
| --- | --- | --- |
| Cluster | lecture (par exemple, obtenir un cluster) | 500 toutes les 5 minutes |
| Cluster | écriture (par exemple, créer une base de données) | 1 000 par heure |

