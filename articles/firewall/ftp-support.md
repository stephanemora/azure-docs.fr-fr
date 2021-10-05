---
title: Prise en charge du mode FTP actif dans le Pare-feu Azure
description: Par défaut, le mode FTP actif est désactivé dans le Pare-feu Azure. Vous pouvez l’activer à l’aide de PowerShell, de l’interface CLI et d’un modèle ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 09/23/2021
ms.author: victorh
ms.openlocfilehash: 7aeb7c596f1b4ca286cc9fce22965418c7c61c85
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084259"
---
# <a name="azure-firewall-ftp-support"></a>Prise en charge du mode FTP dans le Pare-feu Azure

Pour prendre en charge le mode FTP, un pare-feu doit prendre en compte les aspects clés suivants :
- Mode FTP – actif ou passif
- Emplacement client/serveur-Internet ou intranet
- Direction du flux - entrant ou sortant. 

Le pare-feu Azure prend en charge les scénarios FTP actifs et passifs. Pour plus d’informations sur le mode FTP, consultez [FTP actif et FTP passif, une explication définitive](https://slacksite.com/other/ftp.html). 

Par défaut, le FTP passif est activé et la prise en charge FTP active est désactivée pour protéger contre les attaques FTP bounce à l’aide de la commande de PORT FTP. 

Néanmoins, vous pouvez activer le mode FTP actif quand vous effectuez un déploiement avec Azure PowerShell, Azure CLI ou un modèle ARM Azure. Le pare-feu Azure peut prendre en charge les protocoles FTP actifs et passifs simultanément. *ActiveFTP* est une propriété de pare-feu qui peut être activée pour les références sku Standard et Premium, les pare-feu de concentrateur sécurisé et de réseau virtuel, et lors de l’utilisation de la stratégie de pare-feu et des règles classiques.


## <a name="supported-scenarios"></a>Scénarios pris en charge

Le tableau suivant présente la configuration requise pour prendre en charge différents scénarios FTP :


|Scénario de pare-feu  |Mode FTP actif   |Mode FTP passif  |
|---------|---------|---------|
|VNet-VNet     |Règles de réseau à configurer :<br>-Autoriser du réseau virtuel source au port IP de destination 21<br>-Autoriser du port IP de destination 20 au réseau virtuel source |Règles de réseau à configurer :<br>-Autoriser du réseau virtuel source au port IP de destination 21<br>-Autoriser du réseau virtuel source au port IP de destination \<Range of Data Ports>|
|Réseau virtuel sortant-Internet<br><br>(Client FTP dans VNet, serveur sur Internet)      |Non pris en charge *|**Condition préalable** : configurez votre serveur FTP pour accepter des canaux de données et de contrôle à partir d’adresses IP sources différentes. Vous pouvez également configurer le pare-feu Azure avec une seule adresse IP publique.<br><br>Règles de réseau à configurer :<br>-Autoriser du réseau virtuel source au port IP de destination 21<br>-Autoriser du réseau virtuel source au port IP de destination \<Range of Data Ports> |
|Trafic DNAT entrant<br><br>(Client FTP sur Internet, serveur dans VNet)      |Règle DNAT à configurer :<br>-DNAT de la source Internet au port IP de réseau virtuel 21<br><br>Règle de réseau à configurer :<br>-Autoriser du port IP de réseau virtuel 20 à la source Internet |**Conditions préalables** :<br>Configurez votre serveur FTP pour accepter des canaux de données et de contrôle à partir d’adresses IP sources différentes.<br><br>Conseil : le pare-feu Azure prend en charge un nombre limité de règles DNAT. Il est important de configurer le serveur FTP pour qu’il utilise une petite plage de ports sur le canal de données.<br><br>Règles DNAT à configurer :<br>-DNAT de la source Internet au port IP de réseau virtuel 21<br>-DNAT de la source Internet au port IP de réseau virtuel \<Range of Data Ports> |

\* Le FTP actif ne fonctionnera pas lorsque le client FTP doit atteindre un serveur FTP sur Internet. Le FTP actif utilise une commande de PORT du client FTP qui indique au serveur FTP l’adresse IP et le port à utiliser pour le canal de données. Cette commande de PORT utilise l’adresse IP privée du client qui ne peut pas être modifiée. Le trafic côté client qui traverse le pare-feu Azure est associé à des communications basées sur Internet, de sorte que la commande PORT est considérée comme non valide par le serveur FTP. Il s’agit d’une limitation générale du FTP actif lorsqu’il est utilisé avec une traduction d’adresses réseau (NAT) côté client. 


## <a name="deploy-using-azure-powershell"></a>Déployer avec Azure PowerShell

Pour déployer avec Azure PowerShell, utilisez le paramètre `AllowActiveFTP`. Pour plus d’informations, consultez la section sur la [création d’un pare-feu autorisant le mode FTP actif](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-).

## <a name="deploy-using-azure-cli"></a>Déployer à l’aide d’Azure CLI

Pour déployer avec Azure CLI, utilisez le paramètre `--allow-active-ftp`. Pour plus d’informations, consultez [az network firewall create](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters). 

## <a name="deploy-azure-resource-manager-arm-template"></a>Déployer le modèle Azure Resource Manager (ARM)

Pour déployer avec un modèle ARM, utilisez le champ `AdditionalProperties` :

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Pour plus d’informations, consultez [Microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Étapes suivantes

Pour apprendre à déployer un pare-feu Azure, consultez [Déployer et configurer un pare-feu Azure à l’aide d’Azure PowerShell](deploy-ps.md).