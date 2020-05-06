---
title: Vue d’ensemble des Azure Dedicated Hosts pour machines virtuelles
description: Découvrez-en plus sur la manière dont les hôtes dédiés Azure peuvent être utilisés pour déployer des machines virtuelles.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78970724"
---
# <a name="azure-dedicated-hosts"></a>Hôtes dédiés Azure

Un hôte dédié Azure est un service qui fournit des serveurs physiques capables d’héberger une ou plusieurs machines virtuelles, dédiés à un abonnement Azure. Les hôtes dédiés sont les mêmes serveurs physiques que ceux utilisés dans nos centres de données, fournis en tant que ressource. Vous pouvez approvisionner des hôtes dédiés au sein d’une région, d’une zone de disponibilité et d’un domaine d’erreur. Ensuite, vous pouvez placer des machines virtuelles directement dans vos hôtes approvisionnés, dans la configuration qui répond le mieux à vos besoins.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez déployer un hôte dédié à l’aide d'[Azure CLI](dedicated-hosts-cli.md), du [portail](dedicated-hosts-portal.md) et de [PowerShell](../windows/dedicated-hosts-powershell.md).

- Pour en savoir plus, voir la page de présentation [Hôtes dédiés](dedicated-hosts.md).

- Un exemple de modèle, disponible [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), utilise les zones et les domaines d’erreur pour offrir une résilience maximale dans une région.

- Vous pouvez également économiser sur les coûts grâce à une [instance réservée d’ordinateurs Azure Dedicated Host](../prepay-dedicated-hosts-reserved-instances.md).
