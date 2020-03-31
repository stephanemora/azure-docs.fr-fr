---
title: Vue d’ensemble des Azure Dedicated Hosts pour machines virtuelles
description: Découvrez-en plus sur la manière dont les hôtes dédiés Azure peuvent être utilisés pour déployer des machines virtuelles.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 39d1c0ddb4961800e889346ec110ca629ae73546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251568"
---
# <a name="azure-dedicated-hosts"></a>Hôtes dédiés Azure

Un hôte dédié Azure est un service qui fournit des serveurs physiques capables d’héberger une ou plusieurs machines virtuelles, dédiés à un abonnement Azure. Les hôtes dédiés sont les mêmes serveurs physiques que ceux utilisés dans nos centres de données, fournis en tant que ressource. Vous pouvez approvisionner des hôtes dédiés au sein d’une région, d’une zone de disponibilité et d’un domaine d’erreur. Ensuite, vous pouvez placer des machines virtuelles directement dans vos hôtes approvisionnés, dans la configuration qui répond le mieux à vos besoins.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez déployer un hôte dédié à l’aide d’[Azure PowerShell](dedicated-hosts-powershell.md), du [portail](dedicated-hosts-portal.md) et d’[Azure CLI](../linux/dedicated-hosts-cli.md).

- Un exemple de modèle, disponible [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), utilise les zones et les domaines d’erreur pour offrir une résilience maximale dans une région.

- Vous pouvez également économiser sur les coûts grâce à une [instance réservée d’ordinateurs Azure Dedicated Host](../prepay-dedicated-hosts-reserved-instances.md).
