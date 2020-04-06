---
title: Configuration du délai d’inactivité TCP de l’équilibreur de charge dans Azure
titleSuffix: Azure Load Balancer
description: Dans cet article, découvrez comment configurer le délai d’inactivité TCP d’Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d0bb73b58aa23e5f7eb784772acf37b05df463ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456826"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Configuration des paramètres de délai d’inactivité et d’expiration TCP pour Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="tcp-idle-timeout"></a>Délai d’inactivité TCP
Azure Load Balancer a un paramètre de délai d’inactivité de 4 à 30 minutes. Par défaut, il est défini sur 4 minutes. Si une période d’inactivité est supérieure à la valeur de délai d’expiration, il n’est pas garanti que la session TCP ou HTTP est maintenue entre le client et votre service cloud.

Lorsque la connexion est fermée, votre application cliente peut recevoir le message d’erreur suivant : « Le serveur a clos la connexion sous-jacente : une connexion qui devait être tenue active a été fermée par le serveur. »

Une pratique courante consiste à utiliser TCP keep-alive. Cela permet de maintenir la connexion active pendant une période plus longue. Pour plus d’informations, consultez ces [exemples .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). avec keep-alive activé, les paquets sont envoyés au cours des périodes d’inactivité sur la connexion. Les paquets keep-alive garantissent que la valeur de délai d’inactivité n’est pas atteinte et que la connexion est maintenue pendant une longue période.

Le paramètre fonctionne uniquement pour les connexions entrantes. Pour éviter la perte de la connexion, configurez TCP keep-alive sur un intervalle inférieur au paramètre de délai d’inactivité ou augmentez la valeur du délai d’inactivité. Pour prendre en charge ces scénarios, la prise en charge d’un délai d’inactivité configurable a été ajoutée.

TCP keep-alive convient aux scénarios où l’autonomie de la batterie n’est pas une contrainte. Il n’est pas recommandé de l’utiliser pour les applications mobiles. L’utilisation de TCP keep-alive depuis une application mobile peut décharger la batterie de l’appareil plus rapidement.

![Délai d’expiration TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Les sections suivantes décrivent comment modifier les paramètres de délai d’inactivité pour les ressources d’adresses IP publiques et d’équilibreur de charge.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configurer le délai d’expiration TCP pour votre adresse IP publique de niveau instance à 15 minutes

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` est facultatif. S’il n’est pas défini, le délai d’expiration par défaut est de 4 minutes. La plage de délai d’expiration acceptable est comprise entre 4 et 30 minutes.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Affecter la valeur 15 minutes au délai d’expiration TCP sur une règle d’équilibrage de charge

Pour définir le délai d’inactivité d’un équilibreur de charge, la valeur « IdleTimeoutInMinutes » est définie sur la règle d’équilibrage de charge. Par exemple :

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Étapes suivantes

[Présentation de l’équilibrage de charge interne](load-balancer-internal-overview.md)

[Prise en main de la configuration d’un équilibrage de charge accessible sur Internet](quickstart-create-standard-load-balancer-powershell.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)
