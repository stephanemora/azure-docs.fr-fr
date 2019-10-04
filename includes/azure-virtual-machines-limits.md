---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67177421"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| [Machines virtuelles](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) par service cloud<sup>1</sup> |50 |50 |
| Points de terminaison d’entrée par service cloud<sup>2</sup> |150 |150 |

<sup>1</sup>Les machines virtuelles à l’aide d’un modèle de déploiement classique et non via Azure Resource Manager sont automatiquement stockées dans un service cloud. Vous pouvez ajouter plus de machines virtuelles à ce service cloud pour l’équilibrage de charge et la disponibilité. 

<sup>2</sup>Les points de terminaison d’entrée autorisent les communications avec une machine virtuelle depuis l’extérieur du service cloud de la machine virtuelle. Les machines virtuelles d’un même service cloud ou réseau virtuel peuvent automatiquement communiquer entre elles. Pour plus d’informations, consultez [Configuration des points de terminaison sur une machine virtuelle](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
