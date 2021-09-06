---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: f9143f3220ad5f90345436c3c1f97419e4d26fc4
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114594215"
---
Le tableau suivant décrit les limites maximales pour les clusters Azure Data Explorer.

| Ressource | Limite |
| --- | --- |
| Clusters par région et par abonnement | 20 |
| Instances par cluster | 1 000 | 
| Nombre de bases de données dans un cluster | 10 000 |
| Nombre de clusters abonné (consommateurs de partage de données) par cluster leader (producteur de partage de données) | 70 |

Le tableau suivant décrit les limites relatives aux opérations de gestion effectuées sur les clusters Azure Data Explorer.

| Étendue | Opération | Limite |
| --- | --- | --- |
| Cluster | lecture (par exemple, obtenir un cluster) | 500 toutes les 5 minutes |
| Cluster | écriture (par exemple, créer une base de données) | 1 000 par heure |

