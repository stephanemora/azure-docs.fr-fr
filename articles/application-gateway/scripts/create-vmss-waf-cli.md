---
title: Exemple de script Azure CLI - Limiter le trafic web | Microsoft Docs
description: Exemple de script Azure CLI - Créez une passerelle d’application avec un pare-feu d’applications web et un groupe de machines virtuelles identiques qui utilise des règles OWASP pour limiter le trafic.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7b63da2e21735da78d2e6b3c244dc2e80a48aab5
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750253"
---
# <a name="restrict-web-traffic-using-the-azure-cli"></a>Limiter le trafic web avec Azure CLI

Ce script crée une passerelle d’application avec un pare-feu d’applications web qui utilise un groupe de machines virtuelles identiques pour des serveurs principaux. Le pare-feu d’applications web limite le trafic web en fonction de règles OWASP. Après avoir exécuté le script, vous pouvez tester la passerelle d’application à l’aide de son adresse IP publique.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss-waf.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la passerelle d’application et toutes les ressources associées.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer le déploiement. Chaque élément du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_net) | Créer un réseau virtuel. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Crée un sous-réseau dans un réseau virtuel. |
| [az network public-ip create](https://docs.microsoft.com/en-us/cli/azure/network/public-ip?view=azure-cli-latest) | Crée l’adresse IP publique pour la passerelle d’application. |
| [az network application-gateway create](https://docs.microsoft.com/en-us/cli/azure/network/application-gateway?view=azure-cli-latest) | Créer une passerelle d’application |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | Crée un groupe de machines virtuelles identiques. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Crée un compte de stockage. |
| [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) | Crée un compte de stockage. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_show) | Obtient l’adresse IP publique d’une passerelle d’application. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez d’autres exemples de scripts CLI de passerelle d’application dans la [documentation sur Azure Application Gateway](../cli-samples.md).
