---
title: Règles de pare-feu Azure Service Bus | Microsoft Docs
description: Utilisation des règles de pare-feu pour autoriser les connexions à Azure Service Bus à partir d’adresses IP spécifiques.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 59afdb0e273511f3d8255a9c859b86f93e0b7269
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462414"
---
# <a name="azure-service-bus---use-firewall-rules"></a>Azure Service Bus - utiliser des règles de pare-feu

Pour les scénarios où Azure Service Bus doit uniquement être accessible à partir de certains sites bien connus, les règles de pare-feu vous permettent de configurer des règles pour accepter le trafic en provenance d’adresses IPv4 spécifiques. Par exemple, ces adresses peuvent être celles d’une passerelle NAT d’entreprise.

## <a name="when-to-use"></a>Quand l’utiliser

Si vous cherchez à configurer Service Bus de façon à ce qu’il reçoive le trafic uniquement à partir d’une plage spécifiée d’adresses IP et rejette tout le reste, vous pouvez exploiter un *pare-feu* pour bloquer les points de terminaison Service Bus d’autres adresses IP. Par exemple, vous utilisez Service Bus avec [Azure Express Route][express-route] pour créer des connexions privées à votre infrastructure locale. 

## <a name="how-filter-rules-are-applied"></a>Application des règles de filtre

Les règles de filtre IP sont appliquées au niveau de l’espace de noms Service Bus. Par conséquent, les règles s’appliquent à toutes les connexions de clients utilisant un protocole pris en charge.

Toute tentative de connexion à partir d’une adresse IP qui ne correspond pas à une règle IP autorisée dans l’espace de noms Service Bus est rejetée comme étant non autorisée. La réponse ne mentionne pas la règle IP.

## <a name="default-setting"></a>Paramètre par défaut

Par défaut, la grille **Filtre IP** dans le portail pour Service Bus est vide. Ce paramètre par défaut signifie que votre espace de noms accepte les connexions de n’importe quelle adresse IP. Ce paramètre par défaut est équivalent à une règle qui accepte la plage d’adresses IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Évaluation de règle de filtre IP

Les règles de filtre IP sont appliquées dans l’ordre et la première règle qui correspond à l’adresse IP détermine l’action d’acceptation ou de rejet.

>[!WARNING]
> La mise en place de règles de pare-feu peut empêcher d’autres services Azure d’interagir avec Service Bus.
>
> Les services Microsoft de confiance ne sont pas pris en charge quand le filtrage d’adresse IP (règles de pare-feu) est implémenté. Ils le seront prochainement.
>
> Scénarios courants Azure qui ne fonctionnent pas avec le filtrage d’adresse IP (notez que cette liste **N’EST PAS** exhaustive) :
> - Azure Stream Analytics
> - Intégration à Azure Event Grid
> - Routes Azure IoT Hub
> - Azure IoT Device Explorer
>
> Les services Microsoft suivants doivent être sur un réseau virtuel
> - Azure App Service
> - Azure Functions

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Création d’une règle de pare-feu et de réseau virtuel avec des modèles Azure Resource Manager

> [!IMPORTANT]
> Les pare-feux et les réseaux virtuels sont pris en charge uniquement dans le niveau **Premium** de Service Bus.

Le modèle Resource Manager suivant permet d’ajouter une règle de réseau virtuel à un espace de noms Service Bus.

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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

Pour limiter l’accès à Service Bus aux réseaux virtuels Azure, consultez le lien suivant :

- [Points de terminaison de service de réseau virtuel pour Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
