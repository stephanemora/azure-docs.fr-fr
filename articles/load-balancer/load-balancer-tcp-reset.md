---
title: Réinitialisation TCP de Load Balancer au terme du délai d’inactivité dans Azure
titlesuffix: Azure Load Balancer
description: Load Balancer avec des paquets TCP RST bidirectionnels pendant le délai d’inactivité
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: kumud
ms.openlocfilehash: 4a09492fcb8a7985fa27b6daae89aa5dec0fa6e0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413856"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Load Balancer avec réinitialisation TCP au terme du délai d’inactivité (préversion publique)

Vous pouvez utiliser [Standard Load Balancer](load-balancer-standard-overview.md) pour créer un comportement d’application plus prévisible pour vos scénarios en activant Réinitialisation TCP pendant le délai d'inactivité pour une règle donnée. Le comportement par défaut de Load Balancer consiste à supprimer silencieusement des flux lorsque le délai d’inactivité d’un flux est atteint.  Lorsque cette fonctionnalité est activée, Load Balancer envoie des réinitialisations TCP bidirectionnelles (paquet TCP RST) sur un délai d'inactivité.  Les points de terminaison de votre application sont ainsi informés que la connexion a expiré et n'est plus utilisable.  Les points de terminaison peuvent immédiatement établir une nouvelle connexion, si besoin.

![Réinitialisation TCP de Load Balancer](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>La fonctionnalité Load Balancer avec réinitialisation TCP pendant un délai d’inactivité est pour l’instant disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
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

Disponible dans toutes les régions.

## <a name="limitations"></a>Limites

- Le portail ne peut pas servir à la configuration ou à l’affichage de la réinitialisation TCP.  Utilisez plutôt des modèles, l’API REST, Az CLI 2.0 ou PowerShell.
- TCP RST est envoyé uniquement au cours d’une connexion TCP dont l’état est ESTABLISHED.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Standard Load Balancer](load-balancer-standard-overview.md).
- En savoir plus sur les [règles de trafic sortant](load-balancer-outbound-rules-overview.md).
