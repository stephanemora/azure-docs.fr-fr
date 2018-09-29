---
title: Filtres de connexion IP Azure Service Bus | Microsoft Docs
description: Comment utiliser le filtrage IP pour bloquer les connexions à partir d’adresses IP spécifiques à Azure Service Bus.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: clemensv
ms.openlocfilehash: ccb759a9151d734aa99a6f6b9c68e6072874dd84
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394826"
---
# <a name="use-ip-filters"></a>Utiliser des filtres IP

Pour les scénarios où Azure Service Bus est uniquement accessible à partir de certains sites bien connus, la fonctionnalité *Filtre IP* vous permet de configurer des règles pour refuser ou accepter le trafic en provenance d’adresses IPv4 spécifiques. Par exemple, ces adresses peuvent être celles d’une passerelle NAT d’entreprise.

## <a name="when-to-use"></a>Quand utiliser

Il existe deux cas d’utilisation spécifiques pour lesquels il est utile de bloquer les points de terminaison Service Bus pour certaines adresses IP :

- Service Bus doit recevoir le trafic d’une plage d’adresses IP spécifiée uniquement et refuser tout le reste. Par exemple, vous utilisez Service Bus avec [Azure Express Route][express-route] pour créer des connexions privées à votre infrastructure locale.
- Vous devez refuser le trafic provenant d’adresses IP qui ont été identifiées comme suspectes par l’administrateur Service Bus.

## <a name="how-filter-rules-are-applied"></a>Application des règles de filtre

Les règles de filtre IP sont appliquées au niveau de l’espace de noms Service Bus. Par conséquent, les règles s’appliquent à toutes les connexions de clients utilisant un protocole pris en charge.

Toute tentative de connexion à partir d’une adresse IP qui correspond à une règle IP de rejet dans l’espace de noms Service Bus est rejetée comme étant non autorisée. La réponse ne mentionne pas la règle IP.

## <a name="default-setting"></a>Paramètre par défaut

Par défaut, la grille **Filtre IP** dans le portail pour Service Bus est vide. Ce paramètre par défaut signifie que votre espace de noms accepte les connexions de n’importe quelle adresse IP. Ce paramètre par défaut est équivalent à une règle qui accepte la plage d’adresses IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Évaluation de règle de filtre IP

Les règles de filtre IP sont appliquées dans l’ordre et la première règle qui correspond à l’adresse IP détermine l’action d’acceptation ou de rejet.

Par exemple, si vous souhaitez accepter les adresses dans la plage 70.37.104.0/24 et rejeter tout le reste, la première règle de la grille doit accepter la plage d’adresses 70.37.104.0/24. La règle suivante doit rejeter toutes les adresses à l’aide de la plage 0.0.0.0/0.

> [!NOTE]
> Le rejet d’adresses IP est de nature à empêcher d’autres services Azure (comme Azure Stream Analytics, Azure Virtual Machines ou l’Explorateur d’appareils dans le portail) d’interagir avec Service Bus.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Création d’une règle de réseau virtuel avec des modèles Azure Resource Manager

Le modèle Resource Manager suivant permet d’ajouter une règle de réseau virtuel à un espace de noms Service Bus.

Paramètres de modèle :

- **ipFilterRuleName** doit être une chaîne alphanumérique unique qui ne prend pas en compte la casse et qui ne dépasse pas 128 caractères.
- **ipFilterAction** correspond à l’action **Rejeter** ou **Accepter** à appliquer pour la règle de filtre IP.
- **ipMask** est une adresse IPv4 unique ou un bloc d’adresses IP en notation CIDR. Par exemple, dans la notation CIDR, 70.37.104.0/24 représente les 256 adresses IPv4 comprises entre 70.37.104.0 et 70.37.104.255, 24 indiquant le nombre de bits de préfixe significatifs pour la plage.

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
          "ipFilterRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "ipFilterAction":{  
             "type":"string",
             "allowedValues": ["Reject", "Accept"],
             "metadata":{  
                "description":"IP Filter Action"
             }
          },
          "IpMask":{  
             "type":"string",
             "metadata":{  
                "description":"IP Mask"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('ipFilterRuleName'))]",
            "type": "Microsoft.ServiceBus/Namespaces/IPFilterRules",
            "properties": {
                "FilterName":"[parameters('ipFilterRuleName')]",
                "Action":"[parameters('ipFilterAction')]",              
                "IpMask": "[parameters('IpMask')]"
            }
        } 
    ]
}
```

Pour déployer le modèle, suivez les instructions pour [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Étapes suivantes

Pour limiter l’accès à Service Bus aux réseaux virtuels Azure, consultez le lien suivant :

- [Points de terminaison du service de réseau virtuel pour Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services