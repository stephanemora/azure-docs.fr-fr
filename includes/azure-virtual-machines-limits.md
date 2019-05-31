---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238349"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| [Machines virtuelles](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) par service cloud<sup>1</sup> |50 |50 |
| Points de terminaison d’entrée par service cloud<sup>2</sup> |150 |150 |

<sup>1</sup>les machines virtuelles créées à l’aide du modèle de déploiement classic au lieu d’Azure Resource Manager sont automatiquement stockés dans un service cloud. Vous pouvez ajouter plus de machines virtuelles à ce service cloud pour l’équilibrage de charge et la disponibilité. 

<sup>2</sup>Les points de terminaison d’entrée autorisent les communications avec une machine virtuelle depuis l’extérieur du service cloud de la machine virtuelle. Les machines virtuelles d’un même service cloud ou réseau virtuel peuvent automatiquement communiquer entre elles. Pour plus d’informations, consultez [Configuration des points de terminaison sur une machine virtuelle](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
