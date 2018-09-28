---
title: Qu’est-ce qu’un pare-feu Azure ?
description: Découvrez les fonctionnalités du service Pare-feu Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 2961f6cc8607ba7ec670b297a1858bf433c3ec89
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960785"
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
|Le modèle Hub-and-Spoke ne fonctionne pas avec Peering mondial|Le modèle Hub-and-Spoke n’est pas pris en charge. Il s’agit du modèle où le hub et le pare-feu sont déployés dans une région Azure, tandis que les rayons le sont dans une autre région Azure et sont connectés au hub via Global VNet Peering.|Pour plus d’informations, consultez [Créer, modifier ou supprimer une homologation de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints).|
Les règles de filtrage réseau pour les protocoles autres que TCP/UDP (par exemple ICMP) ne fonctionnent pas pour le trafic lié à Internet.|Les règles de filtrage réseau pour les protocoles autres que TCP/UDP ne fonctionnent pas avec SNAT pour votre adresse IP publique. Les protocoles autres que TCP/UDP sont pris en charge entre les sous-réseaux du rayon et les réseaux virtuels.|Le service Pare-feu Azure utilise Standard Load Balancer, [qui ne prend pas en charge SNAT pour les protocoles IP pour le moment](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Nous étudions les possibilités de prendre en charge ce scénario dans une prochaine version.|
|La règle NAT de destination (DNAT) ne fonctionne pas pour les ports 80 et 22.|Le champ Port de destination du regroupement de règles NAT ne peut pas inclure le port 80 ou le port 22.|Nous nous efforçons de résoudre ce problème dès que possible. En attendant, utilisez un autre port que le port de destination dans les règles NAT. Le port 80 ou 22 peut toujours être utilisé comme port traduit (par exemple, vous pouvez mapper public ip:81 à private ip:80).|
|

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Déployer et configurer Pare-feu Azure avec le portail Azure](tutorial-firewall-deploy-portal.md)
- [Déployer Pare-feu Azure à l’aide d’un modèle](deploy-template.md)
- [Créer un environnement de test pour Pare-feu Azure](scripts/sample-create-firewall-test.md)

