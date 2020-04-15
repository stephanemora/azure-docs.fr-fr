---
title: Configurer des règles de pare-feu IP pour Azure Service Bus
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
ms.openlocfilehash: 24591c20ed707d9541eece0698ecd6e6b5ddee35
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878185"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Configurer des règles de pare-feu IP pour Azure Service Bus
Par défaut, les espaces de noms Service Bus sont accessibles à partir d’Internet tant que la demande s’accompagne d’une authentification et d’une autorisation valides. Avec le pare-feu IP, vous pouvez la limiter à un ensemble d’adresses IPv4 ou de plages d’adresses IPv4 dans la notation [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Cette fonctionnalité est utile dans les scénarios où Azure Service Bus ne doit être accessible qu’à partir de certains sites bien connus. Les règles de pare-feu permettent de configurer des règles pour accepter le trafic provenant d’adresses IPv4 spécifiques. Par exemple, si vous utilisez Service Bus avec [Azure ExpressRoute][express-route], vous pouvez créer une **règle de pare-feu** pour autoriser uniquement le trafic provenant d’adresses IP de votre infrastructure locale ou d’adresses d’une passerelle NAT d’entreprise. 

> [!IMPORTANT]
> Les pare-feux et les réseaux virtuels sont pris en charge uniquement dans le niveau **Premium** de Service Bus. Si la mise à niveau vers le niveau **Premier** s'avère impossible, nous vous recommandons de conserver le jeton de signature d'accès partagé (SAP) en lieu sûr et de ne le partager qu'avec des utilisateurs autorisés. Pour plus d'informations sur l'authentification SAP, consultez [Authentification et autorisation](service-bus-authentication-and-authorization.md#shared-access-signature).

## <a name="ip-firewall-rules"></a>Règles de pare-feu IP
Les règles de pare-feu IP sont appliquées au niveau de l’espace de noms Service Bus. Par conséquent, les règles s’appliquent à toutes les connexions de clients utilisant un protocole pris en charge. Toute tentative de connexion à partir d’une adresse IP qui ne correspond pas à une règle IP autorisée dans l’espace de noms Service Bus est rejetée comme étant non autorisée. La réponse ne mentionne pas la règle IP. Les règles de filtre IP sont appliquées dans l’ordre et la première règle qui correspond à l’adresse IP détermine l’action d’acceptation ou de rejet.

## <a name="use-azure-portal"></a>Utiliser le portail Azure
Cette section explique comment utiliser le portail Azure afin de créer des règles de pare-feu IP pour un espace de noms Service Bus. 

1. Accédez à votre **espace de noms Service Bus** dans le [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez l’option **Mise en réseau**. Par défaut, l’option **Tous les réseaux** est sélectionnée. Votre espace de noms Service Bus accepte les connexions à partir de n’importe quelle adresse IP. Ce paramètre par défaut est équivalent à une règle qui accepte la plage d’adresses IP 0.0.0.0/0. 

    ![Option Pare-feu - Tous les réseaux sélectionnée](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Sélectionnez l’option **Réseaux sélectionnés** en haut de la page. Dans la section **Pare-feu**, effectuez les étapes suivantes :
    1. Sélectionnez l’option **Ajouter l’adresse IP de votre client** pour permettre à l’adresse IP de votre client actuel d’accéder à l’espace de noms. 
    2. Dans **Plage d’adresses**, entrez une adresse IPv4 ou une plage d’adresses IPv4 spécifique en notation CIDR. 
    3. Spécifiez si vous voulez **Autoriser les services Microsoft approuvés à contourner ce pare-feu**. 

        ![Pare-feu – Option Tous les réseaux sélectionnée](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer les paramètres. Patientez quelques minutes jusqu’à ce que la confirmation s’affiche dans les notifications du portail.

## <a name="use-resource-manager-template"></a>Utilisation d’un modèle Resource Manager
Cette section présente un exemple de modèle Azure Resource Manager qui crée un réseau virtuel et une règle de pare-feu.


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

Pour limiter l’accès à Service Bus aux réseaux virtuels Azure, consultez le lien suivant :

- [Points de terminaison de service de réseau virtuel pour Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
