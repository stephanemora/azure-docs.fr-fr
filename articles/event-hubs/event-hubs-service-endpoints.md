---
title: Points de terminaison de service de réseau virtuel - Azure Event Hubs | Microsoft Docs
description: Cet article fournit des informations sur l’ajout d’un point de terminaison de service Microsoft.EventHub à un réseau virtuel.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 9b8b3600acc33e177e65002ba69dcf98a20c2253
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555331"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Utiliser des points de terminaison de service de réseau virtuel avec Azure Event Hubs

L’intégration d’Event Hubs à des [points de terminaison de service de réseau virtuel][vnet-sep] permet de sécuriser l’accès aux fonctionnalités de messagerie à partir de charges de travail, notamment celles de machines virtuelles liées à des réseaux virtuels. Le chemin du trafic réseau est sécurisé aux deux extrémités.

Une fois configuré pour être lié à au moins un point de terminaison de service de sous-réseau de réseau virtuel, l’espace de noms Event Hubs respectif n’accepte que le trafic provenant de sous-réseaux autorisés dans les réseaux virtuels. Du point de vue du réseau virtuel, la liaison d’un espace de noms Event Hubs à un point de terminaison de service configure un tunnel de mise en réseau isolé allant du sous-réseau de réseau virtuel au service de messagerie. 

Il en résulte une relation privée et isolée entre les charges de travail liées au sous-réseau et l’espace de noms Event Hubs respectif, et ce malgré le fait que l’adresse réseau observable du point de terminaison du service de messagerie figure dans une plage d’adresses IP publique. Toutefois, il existe une exception à ce comportement. Par défaut, l’activation d’un point de terminaison de service permet à la règle denyall du pare-feu IP associée au réseau virtuel. Vous pouvez ajouter des adresses IP spécifiques dans le pare-feu IP pour permettre l’accès au point de terminaison public Event Hub. 


>[!WARNING]
> L’implémentation de l’intégration de réseaux virtuels peut empêcher d’autres services Azure d’interagir avec Event Hubs.
>
> Les services Microsoft de confiance ne sont pas pris en charge quand les réseaux virtuels sont implémentés.
>
> Scénarios courants Azure qui ne fonctionnent pas avec les réseaux virtuels (Notez que cette liste **N’EST PAS** exhaustive) :
> - Intégration à Azure Monitor. Vous ne pouvez pas envoyer en streaming des journaux de diagnostic à partir d’**autres** services Azure dans Event Hubs. Toutefois, vous pouvez activer les journaux de diagnostic Azure sur le hub d’événements lui-même. Il en est de même quand le pare-feu (filtrage IP) est activé.
> - Azure Stream Analytics
> - Intégration à Azure Event Grid
> - Routes Azure IoT Hub
> - Azure IoT Device Explorer
>
> Les services Microsoft suivants doivent être sur un réseau virtuel
> - Azure Web Apps
> - Azure Functions

> [!IMPORTANT]
> Les réseaux virtuels sont pris en charge dans les niveaux **standard** et **dédié** d’Event Hubs. Il ne sont pas pris en charge dans le niveau de base.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scénarios de sécurité avancés pris en charge par l’intégration à VNet 

En général, les solutions nécessitant une sécurité étroite et compartimentée et dans lesquelles les sous-réseaux de réseau virtuel assurent la segmentation entre les services compartimentés ont encore besoin de chemins de communication entre les services résidant dans ces compartiments.

Toute route IP immédiate entre les compartiments, notamment celles acheminant le trafic HTTPS sur TCP/IP, comporte un risque d’exploitation des vulnérabilités émanant de la couche réseau. Les services de messagerie fournissent des chemins de communication complètement isolés, les messages étant même écrits sur disque à mesure qu’ils passent d’une partie à une autre. Les charges de travail dans deux réseaux virtuels distincts qui sont tous les deux liés à la même instance d’Event Hubs peuvent communiquer de manière efficace et fiable au moyen de messages, sans aucun impact sur l’intégrité des limites d’isolement de chaque réseau.
 
Vos solutions cloud sensibles en matière de sécurité accèdent donc aux fonctionnalités de messagerie asynchrones de pointe d’Azure qui allient fiabilité et scalabilité. Vos solutions peuvent désormais utiliser la messagerie pour créer des chemins de communication entre des compartiments de solution sécurisés, ce qui offre intrinsèquement une meilleure sécurité que n’importe quel mode de communication pair à pair (notamment HTTPS et d’autres protocoles de sockets sécurisés par TLS).

## <a name="bind-event-hubs-to-virtual-networks"></a>Lier Event Hubs à des réseaux virtuels

Les *règles de réseau virtuel* sont une fonctionnalité de sécurité de pare-feu. Elles permettent de contrôler si votre espace de noms Azure Event Hubs doit accepter ou non les connexions d’un sous-réseau de réseau virtuel particulier.

La liaison d’un espace de noms Event Hubs à un réseau virtuel est un processus en deux étapes. Vous devez d’abord créer un **point de terminaison de service de réseau virtuel** sur un sous-réseau de réseau virtuel, puis l’activer pour « Microsoft.EventHub », comme expliqué dans la [vue d’ensemble des points de terminaison de service][vnet-sep]. Après avoir ajouté le point de terminaison de service, liez-le à l’espace de noms Event Hubs au moyen d’une *règle de réseau virtuel*.

La règle de réseau virtuel est une association de l’espace de noms Event Hubs et d’un sous-réseau de réseau virtuel. Une fois la règle en place, toutes les charges de travail liées au sous-réseau sont autorisées à accéder à l’espace de noms Event Hubs. Event Hubs n’établit jamais de connexions sortantes, ne nécessite aucun accès et ne se voit donc jamais accorder l’accès à votre sous-réseau quand cette règle est activée.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Créer une règle de réseau virtuel avec des modèles Azure Resource Manager

Le modèle Resource Manager suivant permet d’ajouter une règle de réseau virtuel à un espace de noms Event Hubs.

Paramètres du modèle :

* **namespaceName** : espace de noms Event Hubs.
* **vnetRuleName** : nom de la règle de réseau virtuel à créer.
* **virtualNetworkingSubnetId** : chemin complet de Resource Manager pour le sous-réseau de réseau virtuel, par exemple `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pour le sous-réseau par défaut d’un réseau virtuel.

> [!NOTE]
> Bien qu’il n’existe aucune règle de refus possible, l’action par défaut du modèle Azure Resource Manager est **Autoriser**, ce qui ne restreint pas les connexions.
> Lorsque vous élaborez des règles de réseau virtuel ou de pare-feu, vous devez modifier ***defaultAction***
> 
> from
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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Pour déployer le modèle, suivez les instructions pour [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réseaux virtuels, consultez les liens suivants :

- [Points de terminaison de service de réseau virtuel Azure][vnet-sep]
- [Filtrage IP Azure Event Hubs][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
