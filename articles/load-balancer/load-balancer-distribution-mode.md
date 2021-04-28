---
title: Configurer le mode de distribution d’Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Dans cet article, procédez à la configuration du mode de distribution pour Azure Load Balancer afin de prendre en charge l’affinité d’IP source.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 44c56b91a02d53126f00ad64a1404ad4664c6a7f
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107886619"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configuration du mode de distribution pour Azure Load Balancer

Azure Load Balancer prend en charge deux modes de distribution pour distribuer le trafic vers vos applications :

* Basé sur un hachage
* Affinité IP source

Dans cet article, vous allez apprendre à configurer le mode de distribution pour Azure Load Balancer.


## <a name="configure-distribution-mode"></a>Configurer le mode de distribution

---

# <a name="azure-portal"></a>[**Portail Azure**](#tab/azure-portal)

Vous pouvez modifier la configuration du mode de distribution en changeant la règle d’équilibrage de charge dans le portail.

1. Connectez-vous au portail Azure et recherchez le groupe de ressources contenant l’équilibreur de charge que vous souhaitez modifier en cliquant sur **Groupes de ressources**.
2. Dans l’écran Vue d’ensemble de l’équilibreur de charge, sous **Paramètres**, sélectionnez **Règles d’équilibrage de charge**.
3. Dans l’écran Règles d’équilibrage de charge, sélectionnez la règle d’équilibrage de charge dont vous souhaitez modifier le mode de distribution.
4. Sous la règle, modifiez le mode de distribution en changeant la sélection dans la zone de liste déroulante **Persistance de session**. 

Les options suivantes sont disponibles : 

* **Aucun (basé sur le hachage)**  : spécifie que les demandes successives provenant du même client peuvent être gérées par n’importe quelle machine virtuelle.
* **Adresse IP du client (affinité d’IP source à deux tuples)**  : spécifie que les demandes successives provenant de la même adresse IP du client seront traitées par la même machine virtuelle.
* **Adresse IP et protocole du client (affinité d’IP source à trois tuples)**  : spécifie que les demandes successives provenant de la même combinaison d’adresse IP et de protocole du client seront traitées par la même machine virtuelle.

5. Choisissez le mode de distribution, puis sélectionnez **Enregistrer**.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Modifiez la persistance de session sur la règle d’équilibrage de charge." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utilisez PowerShell pour modifier les paramètres de distribution de l’équilibreur de charge sur une règle d’équilibrage de charge existante. La commande suivante met à jour le mode de distribution : 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

Définissez la valeur de l’élément `LoadDistribution` sur le type d’équilibrage de charge requis. 

* Spécifiez **SourceIP** pour l’équilibrage de charge à deux tuples (IP source et IP de destination). 

* Spécifiez **SourceIPProtocol** pour l’équilibrage de charge à trois tuples (IP source, IP de destination et type de protocole). 

* Spécifiez **Default** pour le comportement par défaut de l’équilibrage de charge à cinq tuples.

# <a name="cli"></a>[**INTERFACE DE LIGNE DE COMMANDE**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Utilisez Azure CLI pour modifier les paramètres de distribution de l’équilibreur de charge sur une règle d’équilibrage de charge existante.  La commande suivante met à jour le mode de distribution :

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
Définissez la valeur de `--load-distribution` sur le type d’équilibrage de charge requis.

* Spécifiez **SourceIP** pour l’équilibrage de charge à deux tuples (IP source et IP de destination). 

* Spécifiez **SourceIPProtocol** pour l’équilibrage de charge à trois tuples (IP source, IP de destination et type de protocole). 

* Spécifiez **Default** pour le comportement par défaut de l’équilibrage de charge à cinq tuples.

Pour plus d’informations sur la commande utilisée dans cet article, consultez [az network lb rule update](/cli/azure/network/lb/rule#az_network_lb_rule_update).

---

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble d’Azure Load Balancer](load-balancer-overview.md)
* [Prise en main de la configuration d’un équilibreur de charge sur Internet](quickstart-load-balancer-standard-public-powershell.md)
* [Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
