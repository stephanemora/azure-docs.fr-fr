---
title: Filtres de connexion IP Azure Event Hubs | Microsoft Docs
description: Utilisez le filtrage IP pour bloquer les connexions à Azure Event Hubs à partir d’adresses IP spécifiques.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: spelluru
ms.openlocfilehash: c229a6f84096ecca892b74f7ce65cb831fa50be3
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886175"
---
# <a name="use-ip-filters"></a>Utiliser des filtres IP

Pour les scénarios où Azure Event Hubs est uniquement accessible à partir de certains sites bien connus, la fonctionnalité *Filtre IP* vous permet de configurer des règles pour refuser ou accepter le trafic en provenance d’adresses IPv4 spécifiques. Par exemple, ces adresses peuvent être celles d’une passerelle NAT d’entreprise.

## <a name="when-to-use"></a>Quand utiliser

Les deux cas d’utilisation importants dans lesquels il est utile de bloquer des points de terminaison Event Hubs pour certaines adresses IP sont les suivantes :

- Vos hubs d’événements doivent uniquement recevoir le trafic d’une plage d’adresses IP spécifiée et refuser tout le reste. Par exemple, vous utilisez Event Hubs avec [Azure Express Route][express-route] pour créer des connexions privées à votre infrastructure locale. 
- Vous devez refuser le trafic en provenance d’adresses IP qui ont été identifiées comme suspectes par l’administrateur Event Hubs.

## <a name="how-filter-rules-are-applied"></a>Application des règles de filtre

Les règles de filtre IP sont appliquées au niveau de l’espace de noms Event Hubs. Par conséquent, les règles s’appliquent à toutes les connexions de clients utilisant un protocole pris en charge.

Toute tentative de connexion à partir d’une adresse IP qui correspond à une règle IP de rejet dans l’espace de noms Event Hubs est rejetée comme étant non autorisée. La réponse ne mentionne pas la règle IP.

## <a name="default-setting"></a>Paramètre par défaut

Par défaut, la grille **Filtre IP** du portail pour Event Hubs est vide. Ce paramètre par défaut signifie que votre hub d’événements accepte les connexions de n’importe quelle adresse IP. Ce paramètre par défaut est équivalent à une règle qui accepte la plage d’adresses IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Évaluation de règle de filtre IP

Les règles de filtre IP sont appliquées dans l’ordre et la première règle qui correspond à l’adresse IP détermine l’action d’acceptation ou de rejet.

Par exemple, si vous souhaitez accepter les adresses dans la plage 70.37.104.0/24 et rejeter tout le reste, la première règle de la grille doit accepter la plage d’adresses 70.37.104.0/24. La règle suivante doit rejeter toutes les adresses à l’aide de la plage 0.0.0.0/0.

> [!NOTE]
> Le rejet d’adresses IP est de nature à empêcher d’autres services Azure (comme Azure Stream Analytics, Machines virtuelles Azure ou Device Explorer dans le portail) d’interagir avec Event Hubs.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Création d’une règle de réseau virtuel avec des modèles Azure Resource Manager

> [!IMPORTANT]
> Les réseaux virtuels sont pris en charge dans les niveaux **standard** et **dédié** d’Event Hubs. Il ne sont pas pris en charge dans le niveau de base. 

Le modèle Resource Manager suivant permet d’ajouter une règle de réseau virtuel à un espace de noms Event Hubs.

Paramètres de modèle :

- **ipFilterRuleName** doit être une chaîne alphanumérique unique qui ne prend pas en compte la casse et qui ne dépasse pas 128 caractères.
- **ipFilterAction** correspond à l’action **Rejeter** ou **Accepter** à appliquer pour la règle de filtre IP.
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
            "type": "Microsoft.EventHub/Namespaces/IPFilterRules",
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

Pour limiter l’accès à Event Hubs aux réseaux virtuels Azure, consultez le lien suivant :

- [Points de terminaison du service de réseau virtuel pour Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md