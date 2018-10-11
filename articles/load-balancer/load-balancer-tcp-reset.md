---
title: Réinitialisation TCP de Load Balancer au terme du délai d’inactivité | Microsoft Docs
description: Load Balancer avec des paquets TCP RST bidirectionnels pendant le délai d’inactivité
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/08/2018
ms.author: kumud
ms.openlocfilehash: 9aa3811eb03d38a4c6ab8203512f3e6699098122
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883633"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Load Balancer avec réinitialisation TCP au terme du délai d’inactivité (préversion publique)

Vous pouvez utiliser [Standard Load Balancer](load-balancer-standard-overview.md) pour créer un comportement d’application plus prévisible pour vos scénarios au moyen de réinitialisations TCP (paquet TCP RST) bidirectionnelles à chaque délai d’inactivité configurable.  Le comportement par défaut de Load Balancer consiste à supprimer silencieusement des flux lorsque le délai d’inactivité d’un flux est atteint.

![Réinitialisation TCP de Load Balancer](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>La fonctionnalité Load Balancer avec réinitialisation TCP pendant un délai d’inactivité est pour l’instant disponible en préversion publique dans un ensemble limité de [régions](#regions). Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Vous modifiez ce comportement par défaut et activez l’envoi des réinitialisations TCP pendant un délai d’inactivité, sur des règles NAT entrantes, des règles d’équilibrage de charge et des [règles de trafic sortant](https://aka.ms/lboutboundrules).  Lorsqu’il est activé par règle, Load Balancer envoie une réinitialisation TCP bidirectionnelle (paquets TCP RST) aux points de terminaison client et serveur pendant un délai d’inactivité de tous les flux correspondants.

Les points de terminaison recevant les paquets TCP RST ferment aussitôt le socket correspondant. Une notification immédiate est fournie aux points de terminaison et informe que la libération de la connexion s’est produite et que toute communication ultérieure sur cette même connexion TCP est vouée à l’échec.  Les applications peuvent purger les connexions lorsque le socket se ferme, et les rétablir en fonction des besoins sans attendre que la connexion TCP finisse par expirer.

Pour de nombreux scénarios, le besoin d’envoyer des conservations de connexion active TCP (ou de la couche Application) pour actualiser le délai d’inactivité d’un flux peut s’en trouver réduit. 

Si vos durées d’inactivité sont supérieures à celles autorisées par la configuration, ou si votre application présente un comportement indésirable lorsque les réinitialisations TCP sont activées, vous devrez peut-être continuer à utiliser des conservations de connexion active TCP (ou des conservations de connexion active de la couche Application) pour surveiller l’activité des connexions TCP.  De plus, les conservations de connexion active peuvent également se révéler utiles lorsque la connexion est transmise par proxy à un endroit quelconque sur le chemin, en particulier les conservations de connexion active de la couche Application.  

Examinez attentivement le scénario de bout en bout dans son intégralité pour décider si vous tirez parti de l’activation des réinitialisations TCP, de l’ajustement du délai d’inactivité, et si des étapes supplémentaires peuvent être nécessaires pour garantir le comportement de l’application souhaitée.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Activation de la réinitialisation TCP pendant le délai d’inactivité

À l’aide de la version d’API 01-07-2018, vous pouvez activer, par règle, l’envoi de réinitialisations TCP bidirectionnelles sur un délai d’inactivité :

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="regions"></a> Disponibilité des régions

Ce paramètre est en usage actuellement dans les régions suivantes.  Dans les régions non répertoriées ici, le paramètre n’a aucun effet.

| Région |
|---|
| Asie du Sud-Est |
| Europe occidentale |
| USA Est 2 |
| Nord du Royaume-Uni |
| USA Ouest |

Ce tableau est mis à jour au fur et à mesure que la préversion est étendue à d’autres régions.  

## <a name="limitations"></a>Limites

- [Disponibilité des régions](#regions) limitée.
- Le portail ne peut pas servir à la configuration ou à l’affichage de la réinitialisation TCP.  Utilisez plutôt des modèles, l’API REST, Az CLI 2.0 ou PowerShell.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Standard Load Balancer](load-balancer-standard-overview.md).
- En savoir plus sur les [règles de trafic sortant](load-balancer-outbound-rules-overview.md).
