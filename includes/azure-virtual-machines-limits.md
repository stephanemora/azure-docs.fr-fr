---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335015"
---
| Ressource | Limite |
| --- | --- |
| [Machines virtuelles](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) par service cloud<sup>1</sup> |50 |
| Points de terminaison d’entrée par service cloud<sup>2</sup> |150 |

<sup>1</sup>Les machines virtuelles à l’aide d’un modèle de déploiement classique et non via Azure Resource Manager sont automatiquement stockées dans un service cloud. Vous pouvez ajouter plus de machines virtuelles à ce service cloud pour l’équilibrage de charge et la disponibilité. 

<sup>2</sup>Les points de terminaison d’entrée autorisent les communications avec une machine virtuelle depuis l’extérieur du service cloud de la machine virtuelle. Les machines virtuelles d’un même service cloud ou réseau virtuel peuvent automatiquement communiquer entre elles. Pour plus d’informations, consultez [Configuration des points de terminaison sur une machine virtuelle](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
