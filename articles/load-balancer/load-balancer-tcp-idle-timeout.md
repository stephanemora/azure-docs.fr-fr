---
title: Configurer le délai d’inactivité TCP de l’équilibreur de charge dans Azure
titleSuffix: Azure Load Balancer
description: Dans cet article, découvrez comment configurer le délai d’inactivité TCP d’Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: 26c4c01aaf6abe6b9c9ac6daf6836d7b660ba21e
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649828"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>Configurer un délai d’inactivité TCP pour Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

Azure Load Balancer a un paramètre de délai d’inactivité de 4 à 120 minutes. Par défaut, il est défini sur 4 minutes. Si une période d’inactivité est supérieure à la valeur de délai d’expiration, il n’est pas garanti que la session TCP ou HTTP est maintenue entre le client et votre service cloud. En savoir plus sur le [Délai d’inactivité TCP](load-balancer-tcp-reset.md).

Les sections suivantes décrivent comment modifier les paramètres de délai d’inactivité pour les ressources d’adresses IP publiques et d’équilibreur de charge.


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>Configurer le délai d’inactivité TCP pour votre adresse IP publique

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` est facultatif. S’il n’est pas défini, le délai d’expiration par défaut est de 4 minutes. La plage de délai d’expiration acceptable est comprise entre 4 et 120 minutes.

## <a name="set-the-tcp-idle-timeout-on-rules"></a>Définir le délai d’inactivité TCP sur les règles

Pour définir le délai d’inactivité d’un équilibreur de charge, la valeur « IdleTimeoutInMinutes » est définie sur la règle d’équilibrage de charge. Par exemple :

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>Étapes suivantes

[Présentation de l’équilibrage de charge interne](load-balancer-internal-overview.md)

[Prise en main de la configuration d’un équilibrage de charge accessible sur Internet](quickstart-load-balancer-standard-public-powershell.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)
