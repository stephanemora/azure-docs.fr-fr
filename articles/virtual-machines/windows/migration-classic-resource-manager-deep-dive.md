---
title: Étude technique approfondie de la migration de Classic à Azure Resource Manager
description: Étude technique approfondie de la migration prise en charge par la plateforme de ressources du modèle de déploiement Classic vers Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 1ee40d32-a5e8-42a2-97d0-3232fd3cbb98
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 03da35eb0f0a03bd9a9e28b0bd92a94979c6fcf5
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115774"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager
Examinons en détail la migration à partir du modèle de déploiement Azure classique vers le modèle de déploiement Azure Resource Manager. Nous examinons les ressources au niveau des fonctionnalités et des ressources pour vous aider à comprendre comment la plateforme Azure migre les ressources entre les deux modèles de déploiement. Pour plus d’informations, lisez l’article annonçant le service : [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de la migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planification de la migration des ressources IaaS d’Azure Classic vers Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migration de la passerelle VPN du modèle Classic vers le modèle Resource Manager](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [Migrer des circuits ExpressRoute et les réseaux virtuels associés du modèle de déploiement classique au modèle de déploiement Resource Manager](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Outils de la communauté pour aider à la migration de ressources IaaS de Classic vers Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Passer en revue les erreurs courantes de migration](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Passez en revue les questions fréquemment posées sur la migration des ressources IaaS de Classic vers Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
