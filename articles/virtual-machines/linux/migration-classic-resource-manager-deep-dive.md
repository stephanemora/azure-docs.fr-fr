---
title: Étude technique approfondie de la migration de Classic à Azure Resource Manager
description: Étude technique approfondie de la migration prise en charge par la plateforme de ressources du modèle de déploiement Classic vers Azure Resource Manager
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: a5277e23d92dd026aa19e278532869747709e646
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78944727"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager

> [!IMPORTANT]
> Aujourd’hui, environ 90 % des machines virtuelles IaaS utilisent [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Depuis le 28 février 2020, les machines virtuelles classiques sont dépréciées. Elles seront entièrement mises hors service le 1er mars 2023. [Apprenez-en davantage]( https://aka.ms/classicvmretirement) sur cette dépréciation et [son impact sur vous](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Examinons en détail la migration à partir du modèle de déploiement Azure classique vers le modèle de déploiement Azure Resource Manager. Nous examinons les ressources au niveau des fonctionnalités et des ressources pour vous aider à comprendre comment la plateforme Azure migre les ressources entre les deux modèles de déploiement. Pour en savoir plus, consultez l’article d’annonce de service [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de la migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planification de la migration des ressources IaaS d’Azure Classic vers Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Outils de la communauté pour aider à la migration de ressources IaaS de Classic vers Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Passer en revue les erreurs courantes de migration](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Passez en revue les questions fréquemment posées sur la migration des ressources IaaS de Classic vers Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
