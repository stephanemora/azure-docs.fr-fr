---
title: Configurer des points de terminaison sur une machine virtuelle Windows classique | Microsoft Docs
description: Apprenez à configurer des points de terminaison pour une machine virtuelle Windows classique dans le portail Azure pour permettre la communication avec une machine virtuelle Windows dans Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: cynthn
ms.openlocfilehash: 373003eb8be0482ed96d7d11ecd879237f69b47a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957163"
---
# <a name="set-up-endpoints-on-a-windows-virtual-machine-by-using-the-classic-deployment-model"></a>Définir des points de terminaison sur une machine virtuelle Windows suivant le modèle de déploiement Classic
Les machines virtuelles Windows créées sur Azure suivant le modèle de déploiement Classic peuvent automatiquement communiquer sur un canal réseau privé avec d’autres machines virtuelles du même service cloud ou réseau virtuel. Toutefois, dans le cas des ordinateurs connectés sur Internet ou d’autres réseaux virtuels, les points de terminaison doivent diriger le trafic réseau entrant vers une machine virtuelle. 

Il est également possible de configurer des points de terminaison sur des [machines virtuelles Linux](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.  
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Dans le modèle de déploiement **Resource Manager**, les points de terminaison sont configurés à l’aide de **Groupes de sécurité réseau (NSG)**. Pour plus d’informations, voir [Autoriser l’accès externe à une machine virtuelle à l’aide du Portail Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Quand vous créez une machine virtuelle Windows sur le Portail Azure, les points de terminaison courants, notamment ceux du Bureau à distance et de la Communication à distance Windows PowerShell, sont en général créés automatiquement. Si nécessaire, vous pouvez configurer des points de terminaison supplémentaires par la suite.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer un point de terminaison de machine virtuelle à l’aide d’une applet de commande Azure PowerShell, consultez [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Pour gérer une liste de contrôle d’accès sur un point de terminaison à l’aide d’une applet de commande Azure PowerShell, consultez [Gestion des listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Si vous avez créé une machine virtuelle dans le modèle de déploiement Resource Manager, vous pouvez utiliser Azure PowerShell pour [créer des groupes de sécurité réseau](../../../virtual-network/tutorial-filter-network-traffic.md) afin de contrôler le trafic vers la machine virtuelle.
