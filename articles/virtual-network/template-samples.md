---
title: Exemples de modèles Azure Resource Manager pour un réseau virtuel | Microsoft Docs
description: Apprenez-en davantage sur les différents modèles Azure Resource Manager disponibles pour vous permettre de déployer des réseaux virtuels Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/13/2018
ms.author: jdial
ms.openlocfilehash: af0affc549afd8b63fe0d566fac198de054554c1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>Exemples de modèles Azure Resource Manager pour un réseau virtuel

Le tableau suivant inclut des liens vers des exemples de modèles Azure Resource Manager. Vous pouvez déployer des modèles à l’aide du [portail](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure, d’Azure [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou d’Azure [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pour savoir comment créer vos propres modèles, consultez [Créer et déployer votre premier modèle Azure Resource Manager](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json).


| | |
|----|----|
|[Créer un réseau virtuel avec deux sous-réseaux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| Crée un réseau virtuel avec deux sous-réseaux.|
|[Acheminer le trafic via une appliance virtuelle réseau](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| Crée un réseau virtuel avec trois sous-réseaux. Déploie une machine virtuelle dans chacun de ces sous-réseaux. Crée une table de routage contenant des itinéraires pour diriger le trafic d’un sous-réseau à un autre via la machine virtuelle du troisième sous-réseau. Associe la table de routage à un des sous-réseaux.|
|[Créer un point de terminaison de service de réseau virtuel pour Stockage Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|Crée un réseau virtuel avec deux sous-réseaux, ainsi qu’une interface réseau dans chaque sous-réseau. Active un point de terminaison de service sur Stockage Azure pour l’un des sous-réseaux et protège un nouveau compte de stockage sur ce sous-réseau.|
|[Connecter deux réseaux virtuels](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| Crée deux réseaux virtuels, ainsi qu’une homologation de réseaux virtuels entre eux.|
|[Créer une machine virtuelle avec plusieurs adresses IP](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| Crée une machine virtuelle Windows ou Linux avec plusieurs adresses IP.|
