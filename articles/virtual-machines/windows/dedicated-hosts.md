---
title: Vue d’ensemble des hôtes dédiés Azure pour machines virtuelles | Microsoft Docs
description: Découvrez-en plus sur la manière dont les hôtes dédiés Azure peuvent être utilisés pour déployer des machines virtuelles.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: 011bfeb337f3c04b2d9041abedac50affe1f86b0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977259"
---
# <a name="preview-azure-dedicated-hosts"></a>Aperçu : Hôtes dédiés Azure

Un hôte dédié Azure est un service qui fournit des serveurs physiques capables d’héberger une ou plusieurs machines virtuelles, dédiés à un abonnement Azure. Les hôtes dédiés sont les mêmes serveurs physiques que ceux utilisés dans nos centres de données, fournis en tant que ressource. Vous pouvez approvisionner des hôtes dédiés au sein d’une région, d’une zone de disponibilité et d’un domaine d’erreur. Ensuite, vous pouvez placer des machines virtuelles directement dans vos hôtes approvisionnés, dans la configuration qui répond le mieux à vos besoins.

[!INCLUDE [virtual-machines-common-dedicated-hosts-preview](../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]

[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez déployer un hôte dédié à l’aide d’[Azure PowerShell](dedicated-hosts-powershell.md), du [portail](dedicated-hosts-portal.md) et d’[Azure CLI](../linux/dedicated-hosts-cli.md).

- Un exemple de modèle, disponible [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), utilise les zones et les domaines d’erreur pour offrir une résilience maximale dans une région.
