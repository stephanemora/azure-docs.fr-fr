---
title: Règles de pare-feu dans Azure Event Hubs | Microsoft Docs
description: Utilisez les règles de pare-feu pour autoriser les connexions à Azure Event Hubs à partir d’adresses IP spécifiques.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 769a70cee4f5a1d5d5f77cdd4e55108e3ba40fa1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978695"
---
# <a name="azure-event-hubs---use-firewall-rules"></a>Azure Event Hubs – utiliser des règles de pare-feu

Pour les scénarios où Azure Event Hubs doit uniquement être accessible à partir de certains sites bien connus, les règles de pare-feu vous permettent de configurer des règles pour accepter le trafic en provenance d’adresses IPv4 spécifiques. Par exemple, ces adresses peuvent être celles d’une passerelle NAT d’entreprise.

## <a name="when-to-use"></a>Quand l’utiliser

Si vous cherchez à configurer votre espace de noms Event Hubs de façon à ce qu’il reçoive le trafic uniquement à partir d’une plage spécifiée d’adresses IP et rejette tout le reste, vous pouvez exploiter une *règle de pare-feu* pour bloquer les points de terminaison Event Hub d’autres adresses IP. Par exemple, si vous utilisez Event Hubs avec [Azure Express Route][express-route], vous pouvez créer une *règle de pare-feu* pour restreindre le trafic des adresses IP de votre infrastructure sur site.

## <a name="how-filter-rules-are-applied"></a>Application des règles de filtre

Les règles de filtre IP sont appliquées au niveau de l’espace de noms Event Hubs. Par conséquent, les règles s’appliquent à toutes les connexions de clients utilisant un protocole pris en charge.

Toute tentative de connexion à partir d’une adresse IP qui ne correspond pas à une règle IP autorisée dans l’espace de noms Event Hubs est rejetée comme étant non autorisée. La réponse ne mentionne pas la règle IP.

## <a name="default-setting"></a>Paramètre par défaut

Par défaut, la grille **Filtre IP** du portail pour Event Hubs est vide. Ce paramètre par défaut signifie que votre hub d’événements accepte les connexions de n’importe quelle adresse IP. Ce paramètre par défaut est équivalent à une règle qui accepte la plage d’adresses IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Évaluation de règle de filtre IP

Les règles de filtre IP sont appliquées dans l’ordre et la première règle qui correspond à l’adresse IP détermine l’action d’acceptation ou de rejet.

>[!WARNING]
> La mise en place de pare-feu peut empêcher d’autres services Azure d’interagir avec Event Hubs.
>
> Les services Microsoft de confiance ne sont pas pris en charge quand le filtrage d’adresse IP (pare-feu) est implémenté. Ils le seront prochainement.
>
> Scénarios courants Azure qui ne fonctionnent pas avec le filtrage d’adresse IP (notez que cette liste **N’EST PAS** exhaustive) :
> - Azure Stream Analytics
> - Intégration à Azure Event Grid
> - Routes Azure IoT Hub
> - Azure IoT Device Explorer
>
> Les services Microsoft suivants doivent être sur un réseau virtuel
> - Azure Web Apps
> - Azure Functions

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Création d’une règle de pare-feu avec des modèles Azure Resource Manager

> [!IMPORTANT]
> Les règles de pare-feu sont prises en charge dans les niveaux **standard** et **dédié** d’Event Hubs. Il ne sont pas pris en charge dans le niveau de base.

Le modèle Resource Manager suivant permet d’ajouter une règle de filtre IP à un espace de noms Event Hubs.

Paramètres du modèle :

- **ipMask** est une adresse IPv4 unique ou un bloc d’adresses IP en notation CIDR. Par exemple, dans la notation CIDR, 70.37.104.0/24 représente les 256 adresses IPv4 comprises entre 70.37.104.0 et 70.37.104.255, 24 indiquant le nombre de bits de préfixe significatifs pour la plage.

> [!NOTE]
> Bien qu’il n’existe aucune règle de refus possible, l’action par défaut du modèle Azure Resource Manager est **Autoriser**, ce qui ne restreint pas les connexions.
> Lorsque vous élaborez des règles de réseau virtuel ou de pare-feu, vous devez modifier ***defaultAction***
> 
> de
> ```json
> "defaultAction": "Allow"
> ```
> to
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Pour déployer le modèle, suivez les instructions pour [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Étapes suivantes

Pour limiter l’accès à Event Hubs aux réseaux virtuels Azure, consultez le lien suivant :

- [Points de terminaison de service de réseau virtuel pour Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
