---
title: Prise en charge du mode FTP actif dans le Pare-feu Azure
description: Par défaut, le mode FTP actif est désactivé dans le Pare-feu Azure. Vous pouvez l’activer à l’aide de PowerShell, de l’interface CLI et d’un modèle ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 47bced3c3c4f01212af6919d51adc5dfc2e6e1b5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200057"
---
# <a name="azure-firewall-active-ftp-support"></a>Prise en charge du mode FTP actif dans le Pare-feu Azure

Avec le mode FTP actif, le serveur FTP lance la connexion de données sur le port de données du client FTP désigné. Normalement, les pare-feu du réseau côté client bloquent une demande de connexion externe sur un port client interne. Pour plus d’informations, consultez [Active FTP vs. Passive FTP, a Definitive Explanation](https://slacksite.com/other/ftp.html) (FTP actif et FTP passif, une explication définitive).

Par défaut, la prise en charge du mode FTP actif est désactivé dans le Pare-feu Azure à des fins de protection contre les attaques par rebond FTP qui utilisent la commande FTP `PORT`. Néanmoins, vous pouvez activer le mode FTP actif quand vous effectuez un déploiement avec Azure PowerShell, Azure CLI ou un modèle ARM Azure.

> [!NOTE]
> Actuellement, le mode FTP actif est pris en charge uniquement pour les pare-feu déployés dans un réseau virtuel. La prise en charge des réseaux étendus virtuels sera ajoutée ultérieurement.

## <a name="azure-powershell"></a>Azure PowerShell

Pour déployer avec Azure PowerShell, utilisez le paramètre `AllowActiveFTP`. Pour plus d’informations, consultez la section sur la [création d’un pare-feu autorisant le mode FTP actif](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>Azure CLI

Pour déployer avec Azure CLI, utilisez le paramètre `--allow-active-ftp`. Pour plus d’informations, consultez [az network firewall create](/cli/azure/ext/azure-firewall/network/firewall#ext_azure_firewall_az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Modèle Azure Resource Manager (ARM)

Pour déployer avec un modèle ARM, utilisez le champ `AdditionalProperties` :

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Pour plus d’informations, consultez [Microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Étapes suivantes

Pour apprendre à déployer un pare-feu Azure, consultez [Déployer et configurer un pare-feu Azure à l’aide d’Azure PowerShell](deploy-ps.md).