---
title: Points de terminaison de service et règles de réseau virtuel dans Azure Service Bus | Microsoft Docs
description: Ajoutez un point de terminaison de service Microsoft.ServiceBus à un réseau virtuel.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: clemensv
ms.openlocfilehash: f8874a1d9db754485e9624596465560981bd6425
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858043"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Utiliser des points de terminaison de service de réseau virtuel avec Azure Service Bus

L’intégration de Service Bus à des [points de terminaison de service de réseau virtuel (VNet)][vnet-sep] permet de sécuriser l’accès aux fonctionnalités de messagerie à partir de charges de travail, notamment celles de machines virtuelles liées à des réseaux virtuels. Le chemin du trafic réseau est sécurisé aux deux extrémités. 

Une fois configuré pour être lié à au moins un point de terminaison de service de sous-réseau de réseau virtuel, l’espace de noms Service Bus respectif n’accepte que le trafic provenant de réseaux virtuels autorisés. Du point de vue du réseau virtuel, la liaison d’un espace de noms Service Bus à un point de terminaison de service configure un tunnel de mise en réseau isolé allant du sous-réseau de réseau virtuel au service de messagerie.

Il en résulte une relation privée et isolée entre les charges de travail liées au sous-réseau et l’espace de noms Service Bus respectif, et ce malgré le fait que l’adresse réseau observable du point de terminaison du service de messagerie figure dans une plage d’adresses IP publique.

## <a name="enable-service-endpoints-with-service-bus"></a>Activer les points de terminaison de service avec Service Bus

Les réseaux virtuels sont pris en charge uniquement dans les espaces de noms du [niveau Premium](service-bus-premium-messaging.md) de Service Bus. 

Un facteur important lorsque vous utilisez des points de terminaison de service de réseau virtuel avec Service Bus est que vous ne devez pas activer ces points de terminaison dans les applications qui combinent des espaces de noms Service Bus de niveau Standard et Premium. Étant donné que le niveau Standard ne prend pas en charge les réseaux virtuels, le point de terminaison est limité aux espaces de noms du niveau Premium uniquement. Le réseau virtuel bloque le trafic vers l’espace de noms Standard. 

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scénarios de sécurité avancés pris en charge par l’intégration à VNet 

En général, les solutions nécessitant une sécurité étroite et compartimentée et dans lesquelles les sous-réseaux de réseau virtuel assurent la segmentation entre les services compartimentés ont toujours besoin de chemins de communication entre les services résidant dans ces compartiments.

Toute route IP immédiate entre les compartiments, notamment celles acheminant le trafic HTTPS sur TCP/IP, comporte un risque d’exploitation des vulnérabilités émanant de la couche réseau. Les services de messagerie fournissent des chemins de communication complètement isolés, les messages étant même écrits sur disque à mesure qu’ils passent d’une partie à une autre. Les charges de travail dans deux réseaux virtuels distincts qui sont tous les deux liés à la même instance de Service Bus peuvent communiquer de manière efficace et fiable au moyen de messages, sans aucun impact sur l’intégrité des limites d’isolement de chaque réseau.
 
Vos solutions cloud sensibles en matière de sécurité accèdent donc aux fonctionnalités de messagerie asynchrones de pointe d’Azure qui allient fiabilité et scalabilité. Vos solutions peuvent désormais utiliser la messagerie pour créer des chemins de communication entre des compartiments de solution sécurisés, ce qui offre intrinsèquement une meilleure sécurité que n’importe quel mode de communication pair à pair (notamment HTTPS et d’autres protocoles de sockets sécurisés par TLS).

## <a name="binding-service-bus-to-virtual-networks"></a>Liaison de Service Bus à des réseaux virtuels

Les *règles de réseau virtuel* sont une fonctionnalité de sécurité de pare-feu. Elles permettent de contrôler si votre serveur Azure Service Bus doit accepter ou non les connexions d’un sous-réseau de réseau virtuel particulier.

La liaison d’un espace de noms Service Bus à un réseau virtuel est un processus en deux étapes. Vous devez d’abord créer un **point de terminaison de service de réseau virtuel** sur un sous-réseau de réseau virtuel et l’activer pour « Microsoft.ServiceBus », comme expliqué dans la [vue d’ensemble des points de terminaison de service] [vnet-sep]. Après avoir ajouté le point de terminaison de service, liez-le à l’espace de noms Service Bus au moyen d’une *règle de réseau virtuel*.

La règle de réseau virtuel est une association nommée de l’espace de noms Service Bus et d’un sous-réseau de réseau virtuel. Une fois la règle en place, toutes les charges de travail liées au sous-réseau sont autorisées à accéder à l’espace de noms Service Bus. Service Bus n’établit jamais de connexions sortantes, ne nécessite aucun accès et ne se voit donc jamais accorder l’accès à votre sous-réseau quand cette règle est activée.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Création d’une règle de réseau virtuel avec des modèles Azure Resource Manager

Le modèle Resource Manager suivant permet d’ajouter une règle de réseau virtuel à un espace de noms Service Bus.

Paramètres de modèle :

* **namespaceName** : espace de noms Service Bus.
* **vnetRuleName** : nom de la règle de réseau virtuel à créer.
* **virtualNetworkingSubnetId** : chemin complet de Resource Manager pour le sous-réseau de réseau virtuel, par exemple `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pour le sous-réseau par défaut d’un réseau virtuel.

Modèle :

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "vnetRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "virtualNetworkSubnetId":{  
             "type":"string",
             "metadata":{  
                "description":"subnet Azure Resource Manager ID"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('vnetRuleName'))]",
            "type":"Microsoft.ServiceBus/namespaces/VirtualNetworkRules",           
            "properties": {             
                "virtualNetworkSubnetId": "[parameters('virtualNetworkSubnetId')]"  
            }
        } 
    ]
}
```

Pour déployer le modèle, suivez les instructions pour [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réseaux virtuels, consultez les liens suivants :

- [Points de terminaison de service de réseau virtuel Azure][vnet-sep]
- [Filtrage des adresses IP dans Azure Service Bus][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md