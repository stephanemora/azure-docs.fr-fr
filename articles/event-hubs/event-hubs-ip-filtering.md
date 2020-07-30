---
title: Règles de pare-feu dans Azure Event Hubs | Microsoft Docs
description: Utilisez les règles de pare-feu pour autoriser les connexions à Azure Event Hubs à partir d’adresses IP spécifiques.
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 2b886aaaf40e5c82d9c7ac3ce5abeda8f54cad3b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288042"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>Configuration des règles de pare-feu IP pour un espace de noms Azure Event Hubs
Par défaut, les espaces de noms Event Hubs sont accessibles sur Internet tant que la demande s’accompagne d’une authentification et d’une autorisation valides. Avec le pare-feu IP, vous pouvez les limiter à un ensemble d’adresses IPv4 ou de plages d’adresses IPv4 dans la notation [CIDR (Classless InterDomain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Cette fonctionnalité est utile dans les scénarios où Azure Event Hubs ne doit être accessible qu’à partir de certains sites bien connus. Les règles de pare-feu permettent de configurer des règles pour accepter le trafic provenant d’adresses IPv4 spécifiques. Par exemple, si vous utilisez Event Hubs avec [Azure ExpressRoute][express-route], vous pouvez créer une **règle de pare-feu** pour autoriser uniquement le trafic provenant des adresses IP de votre infrastructure locale. 

>[!WARNING]
> L’activation du filtrage IP peut empêcher d’autres services Azure d’interagir avec Event Hubs.
>
> Les services Microsoft de confiance ne sont pas pris en charge quand les réseaux virtuels sont implémentés.
>
> Scénarios courants Azure qui ne fonctionnent pas avec les réseaux virtuels (Notez que cette liste **N’EST PAS** exhaustive) :
> - Azure Stream Analytics
> - Routes Azure IoT Hub
> - Azure IoT Device Explorer
>
> Les services Microsoft suivants doivent se trouver sur un réseau virtuel
> - Azure Web Apps
> - Azure Functions


## <a name="ip-firewall-rules"></a>Règles de pare-feu IP
Les règles de pare-feu IP sont appliquées au niveau de l’espace de noms Event Hubs. Par conséquent, les règles s’appliquent à toutes les connexions de clients utilisant un protocole pris en charge. Toute tentative de connexion à partir d’une adresse IP qui ne correspond pas à une règle IP autorisée dans l’espace de noms Event Hubs est rejetée comme étant non autorisée. La réponse ne mentionne pas la règle IP. Les règles de filtre IP sont appliquées dans l’ordre et la première règle qui correspond à l’adresse IP détermine l’action d’acceptation ou de rejet.

## <a name="use-azure-portal"></a>Utiliser le portail Azure
Cette section montre comment utiliser le Portail Azure afin de créer des règles de pare-feu IP pour un espace de noms Event Hubs. 

1. Accédez à votre **espace de noms Event Hubs** sur le [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez l’option **Réseaux**. Si vous sélectionnez l’option **Tous les réseaux**, le hub d’événements accepte les connexions de toutes les adresses IP. Ce paramètre est équivalent à une règle qui accepte la plage d’adresses IP 0.0.0.0/0. 

    ![Pare-feu – Option Tous les réseaux sélectionnée](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Pour restreindre l’accès à des réseaux et des adresses IP spécifiques, sélectionnez l’option **Réseaux sélectionnés**. Dans la section **Pare-feu**, suivez ces étapes :
    1. Sélectionnez l’option **Ajouter l’adresse IP de votre client** pour permettre à l’adresse IP de votre client actuel d’accéder à l’espace de noms. 
    2. Dans **Plage d’adresses**, entrez une adresse IPv4 ou une plage d’adresses IPv4 spécifique en notation CIDR. 
    3. Spécifiez si vous voulez **Autoriser les services Microsoft approuvés à contourner ce pare-feu**. 

        > [!WARNING]
        > Si vous choisissez l’option **Réseaux sélectionnés** et que vous ne spécifiez pas d’adresse IP ni de plage d’adresses, le service autorisera le trafic provenant de tous les réseaux. 

        ![Option Pare-feu – Tous les réseaux sélectionnée](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer les paramètres. Patientez quelques minutes jusqu’à ce que la confirmation s’affiche dans les notifications du portail.


## <a name="use-resource-manager-template"></a>Utilisation d’un modèle Resource Manager

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

Pour limiter l’accès à Event Hubs aux réseaux virtuels Azure, consultez le lien suivant :

- [Points de terminaison de service de réseau virtuel pour Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
