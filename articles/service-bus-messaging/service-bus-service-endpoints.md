---
title: Configurer des points de terminaison de service de réseau virtuel pour Azure Service Bus
description: Cet article fournit des informations sur l’ajout d’un point de terminaison de service Microsoft.ServiceBus à un réseau virtuel.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 9dbf65522d5c85e1054ed3f1f6ca9f86180e7f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454979"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>Configurer des points de terminaison de service de réseau virtuel pour Azure Service Bus

L’intégration de Service Bus à des [points de terminaison de service de réseau virtuel (VNet)][vnet-sep] permet de sécuriser l’accès aux fonctionnalités de messagerie à partir de charges de travail, notamment celles de machines virtuelles liées à des réseaux virtuels. Le chemin du trafic réseau est sécurisé aux deux extrémités.

Une fois configuré pour être lié à au moins un point de terminaison de service de sous-réseau de réseau virtuel, l’espace de noms Service Bus respectif n’accepte que le trafic provenant de réseaux virtuels autorisés. Du point de vue du réseau virtuel, la liaison d’un espace de noms Service Bus à un point de terminaison de service configure un tunnel de mise en réseau isolé allant du sous-réseau de réseau virtuel au service de messagerie.

Il en résulte une relation privée et isolée entre les charges de travail liées au sous-réseau et l’espace de noms Service Bus respectif, et ce malgré le fait que l’adresse réseau observable du point de terminaison du service de messagerie figure dans une plage d’adresses IP publique.

> [!IMPORTANT]
> Les réseaux virtuels sont pris en charge uniquement dans les espaces de noms du [niveau Premium](service-bus-premium-messaging.md) de Service Bus.
> 
> Quand vous utilisez des points de terminaison de service de réseau virtuel avec Service Bus, vous ne devez pas activer ces points de terminaison dans les applications qui combinent des espaces de noms Service Bus de niveau Standard et Premium. Étant donné que le niveau Standard ne prend pas en charge les réseaux virtuels, le point de terminaison est limité aux espaces de noms du niveau Premium uniquement.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scénarios de sécurité avancés pris en charge par l’intégration à VNet 

En général, les solutions nécessitant une sécurité étroite et compartimentée et dans lesquelles les sous-réseaux de réseau virtuel assurent la segmentation entre les services compartimentés ont toujours besoin de chemins de communication entre les services résidant dans ces compartiments.

Toute route IP immédiate entre les compartiments, notamment celles acheminant le trafic HTTPS sur TCP/IP, comporte un risque d’exploitation des vulnérabilités émanant de la couche réseau. Les services de messagerie fournissent des chemins de communication complètement isolés, les messages étant même écrits sur disque à mesure qu’ils passent d’une partie à une autre. Les charges de travail dans deux réseaux virtuels distincts qui sont tous les deux liés à la même instance de Service Bus peuvent communiquer de manière efficace et fiable au moyen de messages, sans aucun impact sur l’intégrité des limites d’isolement de chaque réseau.
 
Vos solutions cloud sensibles en matière de sécurité accèdent donc aux fonctionnalités de messagerie asynchrones de pointe d’Azure qui allient fiabilité et scalabilité. Vos solutions peuvent désormais utiliser la messagerie pour créer des chemins de communication entre des compartiments de solution sécurisés, ce qui offre intrinsèquement une meilleure sécurité que n’importe quel mode de communication pair à pair (notamment HTTPS et d’autres protocoles de sockets sécurisés par TLS).

## <a name="binding-service-bus-to-virtual-networks"></a>Liaison de Service Bus à des réseaux virtuels

Les *règles de réseau virtuel* sont une fonctionnalité de sécurité de pare-feu. Elles permettent de contrôler si votre serveur Azure Service Bus doit accepter ou non les connexions d’un sous-réseau de réseau virtuel particulier.

La liaison d’un espace de noms Service Bus à un réseau virtuel est un processus en deux étapes. Vous devez d’abord créer un **point de terminaison de service de réseau virtuel** sur un sous-réseau de réseau virtuel et l’activer pour **Microsoft.ServiceBus**, comme expliqué dans la [vue d’ensemble des points de terminaison de service][vnet-sep]. Après avoir ajouté le point de terminaison de service, liez-le à l’espace de noms Service Bus au moyen d’une **règle de réseau virtuel**.

La règle de réseau virtuel est une association de l’espace de noms Service Bus et d’un sous-réseau de réseau virtuel. Une fois la règle en place, toutes les charges de travail liées au sous-réseau sont autorisées à accéder à l’espace de noms Service Bus. Service Bus n’établit jamais de connexions sortantes, ne nécessite aucun accès et ne se voit donc jamais accorder l’accès à votre sous-réseau quand cette règle est activée.

## <a name="use-azure-portal"></a>Utiliser le portail Azure
Cette section montre comment utiliser le portail Azure pour ajouter un point de terminaison de service de réseau virtuel. Pour limiter l’accès, vous devez intégrer le point de terminaison de service de réseau virtuel pour cet espace de noms Event Hubs.

1. Accédez à votre **espace de noms Service Bus** dans le [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez l’option **Mise en réseau**. Par défaut, l’option **Tous les réseaux** est sélectionnée. Votre espace de noms accepte les connexions à partir de n’importe quelle adresse IP. Ce paramètre par défaut est équivalent à une règle qui accepte la plage d’adresses IP 0.0.0.0/0. 

    ![Option Pare-feu – Tous les réseaux sélectionnée](./media/service-endpoints/firewall-all-networks-selected.png)
1. Sélectionnez l’option **Réseaux sélectionnés** en haut de la page.
2. Dans la section **Réseau virtuel** de la page, sélectionnez **+Ajouter un réseau virtuel existant**. 

    ![ajouter un réseau virtuel existant](./media/service-endpoints/add-vnet-menu.png)
3. Sélectionnez le réseau virtuel dans la liste des réseaux virtuels, puis choisissez le **sous-réseau**. Vous devez activer le point de terminaison de service avant d’ajouter le réseau virtuel à la liste. Si le point de terminaison de service n’est pas activé, le portail vous invite à l’activer.
   
   ![sélectionner un sous-réseau](./media/service-endpoints/select-subnet.png)

4. Une fois le point de terminaison de service pour le sous-réseau activé pour **Microsoft.ServiceBus**, le message suivant doit s’afficher. Sélectionnez **Ajouter** au bas de la page pour ajouter le réseau. 

    ![sélectionner le sous-réseau et activer le point de terminaison](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Si vous ne pouvez pas activer le point de terminaison de service, vous pouvez ignorer le point de terminaison de service de réseau virtuel manquant en utilisant le modèle Resource Manager. Cette fonctionnalité n’est pas disponible dans le portail.
6. Sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer les paramètres. Patientez quelques minutes jusqu’à ce que la confirmation s’affiche dans les notifications du portail. Le bouton **Enregistrer** doit être désactivé. 

    ![Enregistrer un réseau](./media/service-endpoints/save-vnet.png)

## <a name="use-resource-manager-template"></a>Utilisation d’un modèle Resource Manager
Le modèle Resource Manager suivant permet d’ajouter une règle de réseau virtuel à un espace de noms Service Bus.

Paramètres du modèle :

* **namespaceName** : espace de noms Service Bus.
* **virtualNetworkingSubnetId** : chemin complet de Resource Manager pour le sous-réseau de réseau virtuel, par exemple `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pour le sous-réseau par défaut d’un réseau virtuel.

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

Modèle :

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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
                    "service": "Microsoft.ServiceBus"
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
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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
          "trustedServiceAccessEnabled": false,          
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
- [Filtrage des adresses IP dans Azure Service Bus][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
