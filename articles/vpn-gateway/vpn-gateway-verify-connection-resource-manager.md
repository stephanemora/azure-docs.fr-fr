---
title: 'Passerelle VPN Azure : Vérification de la connexion à une passerelle'
description: Cet article vous montre comment vérifier une connexion de passerelle VPN de réseau virtuel.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: fef9eb49b10008c86ee044a199ae69a43585f4f3
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217937"
---
# <a name="verify-a-vpn-gateway-connection"></a>Vérifier une connexion de passerelle VPN

Cet article vous montre comment vérifier une connexion à une passerelle VPN à la fois pour le modèle de déploiement Classic et pour le modèle de déploiement Resource Manager.

## <a name="azure-portal"></a>Portail Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Pour vérifier une connexion de passerelle VPN pour le modèle de déploiement Resource Manager à l’aide de PowerShell, installez la dernière version des [cmdlets PowerShell Azure Resource Manager](/powershell/azure/).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Pour vérifier une connexion de passerelle VPN pour le modèle de déploiement Resource Manager à l’aide d’Azure CLI, installez la dernière version des [commandes CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 ou version ultérieure).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="azure-portal-classic"></a>Portail Azure (Classic)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (Classic)

Pour vérifier votre connexion de passerelle VPN pour le modèle de déploiement classique à l’aide de PowerShell, installez les dernières versions des cmdlets Azure PowerShell. Veillez à télécharger et à installer le module [Gestion des services](/powershell/azure/servicemanagement/install-azure-ps?#azure-service-management-cmdlets). Utilisez « Add-AzureAccount » pour vous connecter au modèle de déploiement classique.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Consultez [Création d’une machine virtuelle](../virtual-machines/windows/quick-create-portal.md) pour connaître les différentes étapes.
