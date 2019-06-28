---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242129"
---
Voici une liste des comptes de stockage et des types de stockage pris en charge pour l’appareil Data Box. Pour obtenir la liste complète des différents types de comptes de stockage et de leurs fonctionnalités, consultez [Types de comptes de stockage](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Compte de stockage / Type de stockage pris en charge** | **Objet blob de blocs** |**Objet blob de pages*** |**Azure Files** |**Remarques**|
| --- | --- | -- | -- | -- |
| Standard classique | O | O | O |
| Édition Standard v1 à usage général  | O | O | O | Les objets chauds et froids sont pris en charge.|
| Comptes de stockage à usage général v1 Premium  |  | O| | |
| Comptes de stockage à usage général v2 Standard  | O | O | O | Les objets chauds et froids sont pris en charge.|
| Comptes de stockage à usage général v2 Premium  |  |O | | |
| Stockage d'objets blob Standard |O | | |Les objets chauds et froids sont pris en charge. |

\* *- Les données chargées dans les objets blob de pages doivent être de 512 octets alignés (disques durs virtuels, par exemple).*

>[!NOTE]
> Les comptes Azure Data Lake Storage Gen 2 ne sont pas pris en charge.
