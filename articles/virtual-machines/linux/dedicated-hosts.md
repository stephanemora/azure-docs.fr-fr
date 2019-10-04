---
title: Vue d’ensemble des hôtes dédiés Azure pour machines virtuelles | Microsoft Docs
description: Découvrez-en plus sur la manière dont les hôtes dédiés Azure peuvent être utilisés pour déployer des machines virtuelles.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: 5f2b34b3acb559d74414ea622fba2769ede7f0a7
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976640"
---
# <a name="preview-azure-dedicated-hosts"></a>Aperçu : Hôtes dédiés Azure

Un hôte dédié Azure est un service qui fournit des serveurs physiques capables d’héberger une ou plusieurs machines virtuelles, dédiés à un abonnement Azure. Les hôtes dédiés sont les mêmes serveurs physiques que ceux utilisés dans nos centres de données, fournis en tant que ressource. Vous pouvez approvisionner des hôtes dédiés au sein d’une région, d’une zone de disponibilité et d’un domaine d’erreur. Ensuite, vous pouvez placer des machines virtuelles directement dans vos hôtes provisionnés, dans la configuration qui répond le mieux à vos besoins.

[!INCLUDE [virtual-machines-common-dedicated-hosts-preview](../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]

[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


virtual-machines-common-dedicated-hosts-preview.md

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez déployer un hôte dédié à l’aide d'[Azure CLI](dedicated-hosts-cli.md), du [portail](dedicated-hosts-portal.md) et de [PowerShell](../windows/dedicated-hosts-powershell.md).

- Pour en savoir plus, consultez la page de présentation [Hôtes dédiés](dedicated-hosts.md).

- Un exemple de modèle, disponible [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), utilise les zones et les domaines d’erreur pour offrir une résilience maximale dans une région.