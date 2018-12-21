---
title: Qu’est-ce qu’un pare-feu Azure ?
description: Découvrez les fonctionnalités du service Pare-feu Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 11/28/2018
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: b01e16f85eaccee4d765b4985769df670d4d35ca
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413028"
---
# <a name="what-is-azure-firewall"></a>Qu’est-ce qu’un pare-feu Azure ?

Pare-feu Azure est un service de sécurité réseau informatique géré qui protège vos ressources Réseau virtuel Azure. Il s’agit d’un service de pare-feu entièrement avec état, pourvu d’une haute disponibilité intégrée et de l’extensibilité du cloud sans limites. 

![Présentation du pare-feu](media/overview/firewall-overview.png)

Vous pouvez créer, appliquer et consigner des stratégies de connectivité réseau et d’application de façon centralisée entre les abonnements et les réseaux virtuels. Pare-feu Azure utilise une adresse IP publique statique pour vos ressources de réseau virtuel, ce qui permet aux pare-feu situés à l’extérieur d’identifier le trafic provenant de votre réseau virtuel.  Le service est totalement intégré à Azure Monitor pour la journalisation et les analyses.

## <a name="features"></a>Caractéristiques

Pare-feu Azure offre les fonctionnalités suivantes :

### <a name="built-in-high-availability"></a>Haute disponibilité intégrée
Comme la haute disponibilité est intégrée, aucun équilibreur de charge supplémentaire n’est nécessaire, et vous n’avez rien à configurer.

### <a name="unrestricted-cloud-scalability"></a>Extensibilité du cloud sans limites 
Le service Pare-feu Azure peut évoluer en fonction de vos besoins pour prendre en charge les flux de trafic réseau variables. Vous n’avez donc pas besoin de budgétiser votre trafic de pointe.

### <a name="application-fqdn-filtering-rules"></a>Règles de filtrage des noms de domaine complets de l’application

Vous pouvez limiter le trafic HTTP/S sortant vers une liste spécifiée de noms de domaine complets (FQDN), y compris des caractères génériques. Cette fonctionnalité ne nécessite pas d’arrêt SSL.

### <a name="network-traffic-filtering-rules"></a>Règles de filtrage du trafic réseau

Vous pouvez créer de façon centralisée des règles de filtrage réseau *autoriser* ou *refuser* par protocole, port et adresse IP source et de destination. Le service Pare-feu Azure étant entièrement avec état, il peut distinguer les paquets légitimes pour différents types de connexions. Les règles sont appliquées et consignées entre plusieurs abonnements et réseaux virtuels.

### <a name="fqdn-tags"></a>Balises FQDN

Les balises FQDN vous aident à autoriser le trafic réseau du service Azure via votre pare-feu. Par exemple, supposons que vous souhaitez autoriser le trafic réseau Windows Update via votre pare-feu. Vous créez une règle d’application et incluez la balise Windows Update. Le trafic réseau provenant de Windows Update peut désormais passer par votre pare-feu.

### <a name="outbound-snat-support"></a>Prise en charge du mode SNAT sortant

Toutes les adresses IP du trafic réseau virtuel sortant sont traduites en adresse IP publique de Pare-feu Azure (Source Network Address Translation). Vous pouvez identifier et autoriser le trafic entre votre réseau virtuel et des destinations Internet distantes.

### <a name="inbound-dnat-support"></a>Prise en charge du trafic DNAT entrant

Le trafic réseau entrant vers votre adresse IP publique de pare-feu est traduit (Destination Network Address Translation ou DNAT) et filtré selon les adresses IP privées sur vos réseaux virtuels. 

### <a name="azure-monitor-logging"></a>Journalisation d’Azure Monitor

Tous les événements sont intégrés à Azure Monitor, ce qui vous permet d’archiver les journaux dans un compte de stockage, de transmettre en continu des événements vers votre Event Hub ou de les envoyer vers Log Analytics.

## <a name="known-issues"></a>Problèmes connus

Les problèmes connus du service Pare-feu Azure sont les suivants :


|Problème  |Description  |Atténuation  |
|---------|---------|---------|
|Conflit avec la fonctionnalité JIT d’Azure Security Center (ASC)|Si une machine virtuelle est accessible à l’aide de la fonctionnalité juste-à-temps (JIT) et se trouve dans un sous-réseau dont l’itinéraire défini par l’utilisateur pointe vers le service Pare-feu d’Azure en tant que passerelle par défaut, la fonctionnalité JIT d’ASC ne fonctionne pas. Il s’agit du résultat d’un routage asymétrique : un paquet entre via l’adresse IP publique de la machine virtuelle (JIT a ouvert l’accès), mais le chemin d’accès de retour passe via le pare-feu, ce qui supprime le paquet, car aucune session n’est établie sur le pare-feu.|Pour contourner ce problème, placez les machines virtuelles JIT sur un sous-réseau distinct dépourvu d’itinéraire défini par l’utilisateur vers le pare-feu.|
|Le modèle Hub-and-Spoke avec un Peering mondial n’est pas pris en charge|En utilisant le modèle Hub-and-Spoke, le hub et le pare-feu sont déployés dans une région Azure, tandis que les rayons sont eux déployés dans une autre région Azure. Les connexions au hub via Global VNet Peering ne sont pas prises en charge.|C’est normal. Pour plus d’informations, consultez [Abonnement Azure et limites, quotas et contraintes de service](../azure-subscription-service-limits.md#azure-firewall-limits)|
Les règles de filtrage réseau pour les protocoles autres que TCP/UDP (par exemple ICMP) ne fonctionnent pas pour le trafic lié à Internet.|Les règles de filtrage réseau pour les protocoles autres que TCP/UDP ne fonctionnent pas avec SNAT pour votre adresse IP publique. Les protocoles autres que TCP/UDP sont pris en charge entre les sous-réseaux du rayon et les réseaux virtuels.|Le service Pare-feu Azure utilise Standard Load Balancer, [qui ne prend pas en charge SNAT pour les protocoles IP pour le moment](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Nous étudions les possibilités de prendre en charge ce scénario dans une prochaine version.|
|Protocole ICMP non pris en charge par PowerShell et l’interface de ligne de commande|Azure PowerShell et l’interface de ligne de commande ne prennent pas en charge le protocole ICMP en tant que protocole valide dans les règles de réseau.|Il est toujours possible d’utiliser le protocole ICMP par le biais du portail et de l’API REST. Nous travaillons actuellement à ajouter à PowerShell et à l’interface de ligne de commande la prise en charge du protocole ICMP.|
|Les balises FQDN requièrent une définition protocole : port|Les règles d’application avec des balises FQDN requièrent une définition port : protocole.|Vous pouvez utiliser **https** en tant que valeur port : protocole. Nous travaillons actuellement à rendre ce champ facultatif lorsque des balises FQDN sont utilisées.|
|Le déplacement d’un pare-feu vers un autre groupe de ressources ou un autre abonnement n’est pas pris en charge|Le déplacement d’un pare-feu vers un autre groupe de ressources ou un autre abonnement n’est pas pris en charge.|La prise en charge de cette fonctionnalité figure sur notre feuille de route. Pour déplacer un pare-feu vers un autre groupe de ressources ou un autre abonnement, vous devez supprimer l’instance actuelle et la recréer dans le nouveau groupe de ressources ou le nouvel abonnement.|
|Plage de ports dans les règles de réseau et d’application|Les ports sont limités à 64 000, car les ports élevés sont réservés aux sondes d’intégrité et à la gestion. |Nous travaillons actuellement à l’assouplissement de cette limitation.|
|

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure](tutorial-firewall-deploy-portal.md)
- [Déployer Pare-feu Azure à l’aide d’un modèle](deploy-template.md)
- [Créer un environnement de test pour Pare-feu Azure](scripts/sample-create-firewall-test.md)

