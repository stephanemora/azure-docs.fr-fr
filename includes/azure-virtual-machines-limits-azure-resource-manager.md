---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 54ca93cb61de993bed4dec4588e439955e628ea9
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279606"
---
| Ressource | Limite par défaut |
| --- | --- |
| Machines virtuelles par groupe à haute disponibilité | 200 |
| Certificats par abonnement |Illimité<sup>1</sup> |

<sup>1</sup>Avec Azure Resource Manager, les certificats sont stockés dans Azure Key Vault. Bien que le nombre de certificats soit illimité pour un abonnement, il existe toujours une limite de 1 Mo de certificats par déploiement (qui se compose d’une seule machine virtuelle ou d’un groupe à haute disponibilité).

