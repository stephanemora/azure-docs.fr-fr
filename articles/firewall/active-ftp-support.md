---
title: Prise en charge du mode FTP actif dans le Pare-feu Azure
description: Par défaut, le mode FTP actif est désactivé dans le Pare-feu Azure. Vous pouvez l’activer à l’aide de PowerShell, de l’interface CLI et d’un modèle ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: victorh
ms.openlocfilehash: adbc2a9eb6cd3b054df84911604143ddb711ad20
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499133"
---
# <a name="azure-firewall-active-ftp-support"></a>Prise en charge du mode FTP actif dans le Pare-feu Azure

Avec le mode FTP actif, le serveur FTP lance la connexion de données sur le port de données du client FTP désigné. Normalement, les pare-feu du réseau côté client bloquent une demande de connexion externe sur un port client interne. Pour plus d’informations, consultez [Active FTP vs. Passive FTP, a Definitive Explanation](https://slacksite.com/other/ftp.html) (FTP actif et FTP passif, une explication définitive).

Par défaut, la prise en charge du mode FTP actif est désactivé dans le Pare-feu Azure à des fins de protection contre les attaques par rebond FTP qui utilisent la commande FTP `PORT`. Néanmoins, vous pouvez activer le mode FTP actif quand vous effectuez un déploiement avec Azure PowerShell, Azure CLI ou un modèle ARM Azure.


## <a name="azure-powershell"></a>Azure PowerShell

Pour déployer avec Azure PowerShell, utilisez le paramètre `AllowActiveFTP`. Pour plus d’informations, consultez la section sur la [création d’un pare-feu autorisant le mode FTP actif](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-).

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