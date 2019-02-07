---
title: Exemple de script Azure PowerShell - Télécharger le modèle de configuration de périphérique | Microsoft Docs
description: Téléchargez le modèle de configuration de périphérique.
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/17/2018
ms.author: alzam
ms.openlocfilehash: 1f89d6a33346abc8a84f5bc5c0e46e5431fb80ec
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510934"
---
# <a name="download-vpn-device-template-using-powershell"></a>Télécharger le modèle de périphérique VPN à l’aide de PowerShell

Ce script télécharge le modèle de périphérique VPN pour une connexion.


```azurepowershell-interactive
# Declare variables
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration template for the connection
Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG `
-DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’avez plus besoin des ressources créées, utilisez la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources. Ce faisant, vous supprimez le groupe de ressources et l’ensemble des ressources qu’il contient.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer le déploiement. Chaque élément du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice](/powershell/module/azurerm.network/Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice) | Répertorie l’ensemble des modèles et versions de périphérique VPN disponibles. |
| [Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript](/powershell/module/azurerm.network/Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript) | Télécharge le modèle de configuration pour la connexion. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).
