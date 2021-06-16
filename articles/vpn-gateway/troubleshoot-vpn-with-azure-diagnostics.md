---
title: Résoudre les problèmes de passerelle VPN Azure à l’aide des journaux de diagnostic
description: Découvrez comment résoudre les problèmes de passerelle VPN Azure à l’aide des journaux de diagnostic.
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: cc4165a2636169c34b14795a8c2298a0f9917916
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110652132"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>Résoudre les problèmes de passerelle VPN Azure à l’aide des journaux de diagnostic

Cet article décrit les différents journaux disponibles pour les diagnostics de passerelle VPN et explique comment les utiliser pour résoudre efficacement des problèmes de passerelle VPN.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Les journaux suivants sont disponibles dans Azure :

|***Nom** _ | _ *_Description_** |
|---        | ---               |
|**GatewayDiagnosticLog** | Contient des journaux de diagnostic pour des événements de configuration de passerelle, des modifications principales et des événements de maintenance. |
|**TunnelDiagnosticLog** | Contient des événements de changement d’état de tunnel. Les événements de connexion/déconnexion de tunnel affichent une brève explication du changement d’état, le cas échéant. |
|**RouteDiagnosticLog** | Journalise les modifications apportées aux itinéraires statiques et aux événements BGP qui se produisent sur la passerelle. |
|**IKEDiagnosticLog** | Journalise les messages et événements de contrôle IKE sur la passerelle. |
|**P2SDiagnosticLog** | Consigne les événements et messages de contrôle point à site sur la passerelle. |

Notez que ces tables contiennent plusieurs colonnes. Cet article ne présente que les plus pertinentes pour faciliter l’utilisation du journal.

## <a name="set-up-logging"></a><a name="setup"></a>Configurez la journalisation

Pour savoir comment configurer les événements de journal de diagnostic à partir d’une passerelle VPN Azure à l’aide d’Azure Log Analytics, consultez [Configurer des alertes sur les événements de journal de diagnostic à partir d’une passerelle VPN](./vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

Les changements de configuration sont audités dans la table **GatewayDiagnosticLog**. Plusieurs minutes peuvent être nécessaires avant que les modifications que vous exécutez soient reflétées dans les journaux.

Ici, vous disposez d’un exemple de requête pour référence.

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

Cette requête sur **GatewayDiagnosticLog** affiche plusieurs colonnes.

|***Nom** _ | _ *_Description_** |
|---        | ---               |
|**TimeGenerated** | Horodateur de chaque événement dans le fuseau horaire UTC.|
|**OperationName** |Événement qui s’est produit. Il peut s’agir de *SetGatewayConfiguration, SetConnectionConfiguration, HostMaintenanceEvent, GatewayTenantPrimaryChanged, MigrateCustomerSubscription, GatewayResourceMove, ValidateGatewayConfiguration*.|
|**Message** | Détaille l’opération qui se produit et répertorie les résultats en termes de réussite ou d’échec.|

L’exemple ci-dessous montre l’activité journalisée lors de l’application d’une nouvelle configuration :

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="Exemple de d’opération de définition de passerelle visible dans GatewayDiagnosticLog.":::


Notez qu’une opération SetGatewayConfiguration est journalisée chaque fois qu’une configuration est modifiée sur une passerelle VPN ou de réseau local.
Le croisement des résultats des tables **GatewayDiagnosticLog** et **TunnelDiagnosticLog** nous permet de déterminer si une défaillance de connectivité de tunnel a commencé au même moment qu’un changement configuration ou qu’une opération de maintenance. Si c’est le cas, nous disposons d’un excellent pointeur vers la cause racine possible.

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TunnelDiagnosticLog

La table **TunnelDiagnosticLog** est très utile pour inspecter les états de connectivité historiques du tunnel.

Ici, vous disposez d’un exemple de requête pour référence.

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

Cette requête sur **TunnelDiagnosticLog** affiche plusieurs colonnes.


|***Nom** _ | _ *_Description_** |
|---        | ---               |
|**TimeGenerated** | Horodateur de chaque événement dans le fuseau horaire UTC.|
|**OperationName** | Événement qui s’est produit. Il peut s’agir de *TunnelConnected* ou de *TunnelDisconnected*.|
| **Instance\_s** | instance de rôle de passerelle ayant déclenché l’événement. Il peut s’agir de GatewayTenantWorker\_IN\_0 ou de GatewayTenantWorker\_IN\_1, qui sont les noms des deux instances de la passerelle.|
| **Ressource** | Indique le nom de la passerelle VPN. |
| **ResourceGroup** | Indique le groupe de ressources dans lequel se trouve la passerelle.|


Exemple de sortie :

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="Exemple d’événement de tunnel connecté observé dans TunnelDiagnosticLog.":::


La table **TunnelDiagnosticLog** est très utile pour résoudre des problèmes d’événements passés relatifs à des déconnexions de VPN inattendues. Sa nature légère offre la possibilité d’analyser des intervalles de temps importants sur plusieurs jours avec un minimum d’effort.
Ce n’est qu’après avoir identifié l’horodateur d’une déconnexion, vous pouvez basculer vers l’analyse plus détaillée de la table **IKEdiagnosticLog** pour approfondir le raisonnement visant à déterminer si les déconnexions sont liées à IPsec.


Conseils de dépannage :
- Si vous voyez un événement de déconnexion sur une instance de passerelle, suivi d’un événement de connexion sur une instance de passerelle **différente** en quelques secondes, vous examinez un basculement de passerelle. Il s’agit généralement d’un comportement attendu résultant d’une maintenance sur une instance de passerelle. Pour en savoir plus sur ce comportement, consultez [À propos de la redondance de passerelle VPN Azure](./vpn-gateway-highlyavailable.md#activestandby).
- Le même comportement se produit si vous exécutez intentionnellement une réinitialisation de passerelle côté Azure, ce qui entraîne un redémarrage de l’instance de la passerelle active. Pour en savoir plus sur ce comportement, consultez [Réinitialiser une passerelle VPN](./reset-gateway.md).
- Si vous voyez un événement de déconnexion sur une instance de passerelle, suivi d’un événement de connexion sur la **même** instance de passerelle en quelques secondes, vous examinez peut-être un problème de réseau provoquant une expiration de détection d’homologue mort (DPD) ou une déconnexion envoyée par erreur par l’appareil local.

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

La table **RouteDiagnosticLog** trace l’activité pour les itinéraires modifiés de manière statique ou reçus via BGP.

Ici, vous disposez d’un exemple de requête pour référence.

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

Cette requête sur la table **RouteDiagnosticLog** affiche plusieurs colonnes.

|***Nom** _ | _ *_Description_** |
|---        | ---               |
|**TimeGenerated** | Horodateur de chaque événement dans le fuseau horaire UTC.|
|**OperationName** | Événement qui s’est produit. Il peut s’agir de *StaticRouteUpdate, BgpRouteUpdate, BgpConnectedEvent, BgpDisconnectedEvent*.|
| **Message** | Détails de l’opération qui se produit.|

La sortie affiche des informations utiles sur les homologues BGP connectés/déconnectés et les itinéraires échangés.

Exemple :


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="Exemple d’activité d’échange d’itinéraires BGP visible dans la table RouteDiagnosticLog.":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

La table **IKEDiagnosticLog** propose une journalisation de débogage détaillée pour IKE/IPSec. Celle-ci est très utile pour la résolution des problèmes de déconnexion, ou les scénarios d’échec de connexion au VPN.

Ici, vous disposez d’un exemple de requête pour référence.

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

Cette requête sur la table **IKEDiagnosticLog** affiche plusieurs colonnes.


|***Nom** _ | _ *_Description_** |
|---        | ---               |
|**TimeGenerated** | Horodateur de chaque événement dans le fuseau horaire UTC.|
| **RemoteIP** | Adresse IP de l’appareil VPN local. Dans des scénarios réels, il est utile de filtrer sur l’adresse IP de l’appareil local concerné s’il y en a plus d’un. |
|**LocalIP** | Adresse IP de la passerelle VPN que nous résolvez les problèmes. Dans des scénarios réels, il est utile de filtrer sur l’adresse IP de la passerelle VPN concernée s’il y en a plus d’une dans votre abonnement. |
|**Event** | Contient un message de diagnostic utile pour la résolution des problèmes. Il commence généralement par un mot clé et font référence aux actions effectuées par la passerelle Azure. **\[SEND\]** indique un événement provoqué par un paquet IPSec envoyé par la passerelle Azure.  **\[RECEIVED\]** indique un événement résultant de la réception d’un paquet envoyé par un appareil local.  **\[LOCAL\]** indique une action effectuée localement par la passerelle Azure. |


Notez que les colonnes RemoteIP, LocalIP et Event ne sont pas présentes dans la liste de colonnes d’origine de la base de données AzureDiagnostics, mais ajoutées à la requête en analysant la sortie de la colonne « Message » pour simplifier l’analyse.

Conseils de dépannage :

- Pour identifier le début d’une négociation IPSec, vous devez rechercher le message SA\_INIT initial. Ce message peut provenir de chaque côté du tunnel. Le côté envoyant le premier paquet est appelé « initiator » dans la terminologie IPsec, tandis que l’autre côté devient « responder ». Le premier message SA\_INIT d’initialisation est toujours celui où rCookie = 0.

- Si le tunnel IPsec ne parvient pas à établir la connexion, Azure effectue une nouvelle tentative toutes les quelques secondes. C’est pourquoi le dépannage des problèmes de « VPN down » est très pratique sur IKEdiagnosticLog, car vous n’avez pas besoin d’attendre un moment spécifique pour reproduire le problème. En outre, l’échec étant en principe toujours le même à chaque tentative, il suffit d’effectuer un zoom sur un « exemple » de négociation en échec à tout moment.

- Le message SA\_INIT contient les paramètres IPSec que l’homologue souhaite utiliser pour cette négociation IPSec. Le document officiel   
Les [paramètres IPSec/IKE par défaut](./vpn-gateway-about-vpn-devices.md#ipsec) sont les paramètres IPSec que la passerelle Azure prend en charge avec les paramètres par défaut.


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

Le dernier tableau disponible pour les diagnostics VPN est **P2SDiagnosticLog**. Ce tableau effectue le suivi de l’activité de point à site.

Ici, vous disposez d’un exemple de requête pour référence.

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

Cette requête sur **P2SDiagnosticLog** affiche plusieurs colonnes.

|***Nom** _ | _ *_Description_** |
|---        | ---               |
|**TimeGenerated** | Horodateur de chaque événement dans le fuseau horaire UTC.|
|**OperationName** | Événement qui s’est produit. Ce sera *P2SLogEvent*.|
| **Message** | Détails de l’opération qui se produit.|

La sortie affiche tous les paramètres point à site que la passerelle a appliqués, ainsi que les stratégies IPsec en place.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="Exemple de connexion point à site visible dans P2SDiagnosticLog.":::

En outre, chaque fois qu’un client se connecte via IKEv2 ou OpenVPN point à site, la table journalise l’activité des paquets, les conversations EAP/RADIUS et les résultats de réussite ou d’échec par utilisateur.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="Exemple d’authentification EAP visible dans P2SDiagnosticLog.":::

## <a name="next-steps"></a>Étapes suivantes

Pour configurer des alertes sur les journaux de ressources de tunnel, consultez [Configurer des alertes sur les journaux de ressources de la passerelle VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).