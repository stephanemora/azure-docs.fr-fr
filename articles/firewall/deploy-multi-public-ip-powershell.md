---
title: Déployer un pare-feu Azure avec plusieurs adresses IP publiques à l’aide d’Azure PowerShell
description: Dans cet article, vous allez apprendre à déployer un pare-feu Azure avec plusieurs adresses IP publiques à l’aide d’Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/2/2019
ms.author: victorh
ms.openlocfilehash: a5a53766df3338bb36913b589ebda970de55ec94
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491937"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Déployer un pare-feu Azure avec plusieurs adresses IP publiques à l’aide d’Azure PowerShell

> [!IMPORTANT]
> Le pare-feu Azure avec plusieurs adresses IP publiques est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez déployer un pare-feu Azure avec jusqu’à 100 adresses IP publiques.

Cette fonctionnalité donne accès aux scénarios suivants :

- **DNAT** -Vous pouvez traduire plusieurs instances de ports standard vers vos serveurs principaux. Par exemple, si vous avez deux adresses IP publiques, vous pouvez traduire le port TCP 3389 (RDP) pour ces deux adresses IP.
- **SNAT** - Des ports supplémentaires sont disponibles pour les connexions SNAT sortantes, réduisant ainsi le risque de pénurie de ports SNAT. À ce stade, Pare-feu Azure sélectionne aléatoirement l’adresse IP publique source à utiliser pour une connexion. Si votre réseau est doté d’un filtrage en aval, vous devez autoriser toutes les adresses IP publiques associées à votre pare-feu.

Les exemples de Azure PowerShell suivants montrent comment vous pouvez ajouter, supprimer et configurer des adresses IP publiques pour le pare-feu Azure.

> [!NOTE]
> Durant la préversion publique, si vous ajoutez ou supprimez une adresse IP publique à un pare-feu en cours d’exécution, la connectivité entrante existante utilisant les règles DNAT peut ne pas fonctionner pendant 40 à 120 secondes. Vous ne pouvez pas supprimer la première adresse IP publique affectée au pare-feu sauf si celui-ci est libéré ou supprimé.

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Ajouter une adresse IP publique à un pare-feu existant

Dans cet exemple, l’adresse IP publique *azFwPublicIp1* est associée au pare-feu.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Supprimer une adresse IP publique d’un pare-feu existant

Dans cet exemple, l’adresse IP publique *azFwPublicIp1* est dissociée du pare-feu.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Créer un pare-feu avec deux ou plusieurs adresses IP publiques

Cet exemple crée un pare-feu associé au réseau virtuel *vnet* avec deux adresses IP publiques.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Superviser les journaux d’activité de Pare-feu Azure](./tutorial-diagnostics.md)
