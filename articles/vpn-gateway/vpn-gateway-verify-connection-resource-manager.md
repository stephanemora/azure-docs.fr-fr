---
title: Vérification de la connexion à une passerelle
titleSuffix: Azure VPN Gateway
description: Apprenez à vérifier une connexion de passerelle VPN de réseau virtuel.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/28/2021
ms.author: cherylmc
ms.openlocfilehash: eaeb77e1dafe7b2791537190d0b4d220363280a8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524321"
---
# <a name="verify-a-vpn-gateway-connection"></a>Vérifier une connexion de passerelle VPN

Cet article vous montre comment vérifier une connexion à une passerelle VPN à la fois pour le modèle de déploiement Classic et pour le [modèle de déploiement Resource Manager](../azure-resource-manager/management/deployment-models.md).

## <a name="azure-portal"></a>Portail Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Pour vérifier une connexion de passerelle VPN pour le modèle de déploiement Resource Manager à l’aide de PowerShell, installez la dernière version des [cmdlets PowerShell Azure Resource Manager](/powershell/azure/).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Pour vérifier une connexion de passerelle VPN pour le modèle de déploiement Resource Manager à l’aide d’Azure CLI, installez la dernière version des [commandes CLI](/cli/azure/install-azure-cli) (2.0 ou version ultérieure).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="azure-portal-classic"></a>Portail Azure (Classic)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (Classic)

Pour vérifier votre connexion de passerelle VPN pour le modèle de déploiement classique à l’aide de PowerShell, installez les dernières versions des cmdlets Azure PowerShell. Veillez à télécharger et à installer le module [Gestion des services](/powershell/azure/servicemanagement/install-azure-ps?#azure-service-management-cmdlets). Utilisez « Add-AzureAccount » pour vous connecter au modèle de déploiement classique.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Consultez [Création d’une machine virtuelle](../virtual-machines/windows/quick-create-portal.md) pour connaître les différentes étapes.