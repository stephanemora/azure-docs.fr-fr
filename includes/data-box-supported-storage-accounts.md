---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242129"
---
Voici une liste des comptes de stockage et des types de stockage pris en charge pour l’appareil Data Box. Pour obtenir la liste complète des différents types de comptes de stockage et de leurs fonctionnalités, consultez [Types de comptes de stockage](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Compte de stockage / Type de stockage pris en charge** | **Objet blob de blocs** |**Objet blob de pages*** |**Fichiers Azure** |**Remarques**|
| --- | --- | -- | -- | -- |
| Standard classique | O | O | O |
| Édition Standard v1 à usage général  | O | O | O | Les objets chauds et froids sont pris en charge.|
| Comptes de stockage à usage général v1 Premium  |  | O| | |
| Comptes de stockage à usage général v2 Standard  | O | O | O | Les objets chauds et froids sont pris en charge.|
| Comptes de stockage à usage général v2 Premium  |  |O | | |
| Stockage d'objets blob Standard |O | | |Les objets chauds et froids sont pris en charge. |

\* *-Les données sont téléchargées vers les objets BLOB de pages doivent être alignés tels que des disques durs virtuels de 512 octets.*

>[!NOTE]
> Les comptes Azure Data Lake Storage Gen 2 ne sont pas pris en charge.
