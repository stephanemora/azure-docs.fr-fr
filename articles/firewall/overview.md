---
title: Qu’est-ce qu’un pare-feu Azure ?
description: Pare-feu Azure est un service de sécurité réseau informatique géré qui protège vos ressources Réseau virtuel Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 01/15/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 1507eb4eba88fbf1ef50645390eaa9f17804359a
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293230"
---
# <a name="what-is-azure-firewall"></a>Qu’est-ce qu’un pare-feu Azure ?

Pare-feu Azure est un service de sécurité réseau informatique géré qui protège vos ressources Réseau virtuel Azure. Il s’agit d’un service de pare-feu avec état intégral, doté d’une haute disponibilité intégrée et d’une scalabilité illimitée dans le cloud.

![Présentation du pare-feu](media/overview/firewall-threat.png)

Vous pouvez créer, appliquer et consigner des stratégies de connectivité réseau et d’application de façon centralisée entre les abonnements et les réseaux virtuels. Pare-feu Azure utilise une adresse IP publique statique pour vos ressources de réseau virtuel, ce qui permet aux pare-feu situés à l’extérieur d’identifier le trafic provenant de votre réseau virtuel.  Le service est totalement intégré à Azure Monitor pour la journalisation et les analyses.

Pare-feu Azure offre les fonctionnalités suivantes :

## <a name="built-in-high-availability"></a>Haute disponibilité intégrée

Comme la haute disponibilité est intégrée, aucun équilibreur de charge supplémentaire n’est nécessaire, et vous n’avez rien à configurer.

## <a name="availability-zones"></a>Zones de disponibilité

Le Pare-feu Azure peut être configuré pendant le déploiement pour couvrir plusieurs zones de disponibilité afin de fournir une disponibilité supérieure. Avec les Zones de disponibilité Azure, votre disponibilité s’élève à 99,99 % du temps total. Pour plus d’informations, consultez [Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) du Pare-feu Azure. Un contrat de niveau de service de 99,99 % est offert quand deux Zones de disponibilité ou plus sont sélectionnées.

Vous pouvez également associer le Pare-feu Azure à une zone spécifique uniquement pour des raisons de proximité, en utilisant le contrat de niveau de service standard garantissant un taux de disponibilité de 99,95 %.

Il n’existe aucun coût supplémentaire pour un pare-feu déployé dans une Zone de disponibilité. Toutefois, il existe des coûts supplémentaires pour les transferts de données entrants et sortants associés aux Zones de disponibilité Azure. Pour plus d’informations, consultez [Détails de la tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

Les Zones de disponibilité de Pare-feu Azure sont disponibles dans les régions prenant en charge les Zones de disponibilité. Pour plus d’informations, consultez [Que sont les zones de disponibilité dans Azure ?](../availability-zones/az-overview.md#services-support-by-region).

> [!NOTE]
> Les Zones de disponibilité ne peuvent être configurées que pendant le déploiement. Vous ne pouvez pas configurer un pare-feu existant pour y inclure des Zones de disponibilité.

Pour plus d’informations sur les Zones de disponibilité, consultez [Que sont les zones de disponibilité dans Azure ?](../availability-zones/az-overview.md).

## <a name="unrestricted-cloud-scalability"></a>Extensibilité du cloud sans limites

Le service Pare-feu Azure peut évoluer en fonction de vos besoins pour prendre en charge les flux de trafic réseau variables. Vous n’avez donc pas besoin de budgétiser votre trafic de pointe.

## <a name="application-fqdn-filtering-rules"></a>Règles de filtrage des noms de domaine complets de l’application

Vous pouvez limiter le trafic HTTP/S sortant ou le trafic SQL Azure (préversion) vers une liste spécifiée de noms de domaine complets (FQDN), y compris des caractères génériques. Cette fonctionnalité ne nécessite pas d’arrêt SSL.

## <a name="network-traffic-filtering-rules"></a>Règles de filtrage du trafic réseau

Vous pouvez créer de façon centralisée des règles de filtrage réseau *autoriser* ou *refuser* par protocole, port et adresse IP source et de destination. Le service Pare-feu Azure étant entièrement avec état, il peut distinguer les paquets légitimes pour différents types de connexions. Les règles sont appliquées et consignées entre plusieurs abonnements et réseaux virtuels.

## <a name="fqdn-tags"></a>Balises FQDN

Les balises FQDN vous aident à autoriser le trafic réseau du service Azure via votre pare-feu. Par exemple, supposons que vous souhaitez autoriser le trafic réseau Windows Update via votre pare-feu. Vous créez une règle d’application et incluez la balise Windows Update. Le trafic réseau provenant de Windows Update peut désormais passer par votre pare-feu.

## <a name="service-tags"></a>Balises de service

Une balise de service représente un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité. Vous ne pouvez pas créer votre propre balise de service, ni spécifier les adresses IP incluses dans une balise. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

## <a name="threat-intelligence"></a>Informations sur les menaces

Le filtrage basé sur Threat Intelligence peut être activé pour votre pare-feu pour donner l’alerte et rejeter le trafic depuis ou vers des adresses IP et des domaines malveillants connus. Ces adresses IP et domaines proviennent du flux Microsoft Threat Intelligence.

## <a name="outbound-snat-support"></a>Prise en charge du mode SNAT sortant

Toutes les adresses IP du trafic réseau virtuel sortant sont traduites en adresse IP publique de Pare-feu Azure (Source Network Address Translation). Vous pouvez identifier et autoriser le trafic entre votre réseau virtuel et des destinations Internet distantes. Pare-feu Azure ne traduit pas l’adresse réseau source lorsque l’adresse IP de destination est une plage d’adresses IP privées selon la norme [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Si votre organisation utilise une plage d’adresses IP publiques pour les réseaux privés, Pare-feu Azure traduit l’adresse réseau source du trafic en une des adresses IP privées du pare-feu dans AzureFirewallSubnet.

## <a name="inbound-dnat-support"></a>Prise en charge du trafic DNAT entrant

Le trafic réseau entrant vers votre adresse IP publique de pare-feu est traduit (Destination Network Address Translation ou DNAT) et filtré selon les adresses IP privées sur vos réseaux virtuels.

## <a name="multiple-public-ip-addresses"></a>Adresses IP publiques multiples

Vous pouvez associer plusieurs adresses IP publiques (jusqu’à 100) à votre pare-feu.

Cela donne accès aux scénarios suivants :

- **DNAT** -Vous pouvez traduire plusieurs instances de ports standard vers vos serveurs principaux. Par exemple, si vous avez deux adresses IP publiques, vous pouvez traduire le port TCP 3389 (RDP) pour ces deux adresses IP.
- **SNAT** - Des ports supplémentaires sont disponibles pour les connexions SNAT sortantes, réduisant ainsi le risque de pénurie de ports SNAT. À ce stade, Pare-feu Azure sélectionne aléatoirement l’adresse IP publique source à utiliser pour une connexion. Si votre réseau est doté d’un filtrage en aval, vous devez autoriser toutes les adresses IP publiques associées à votre pare-feu.

## <a name="azure-monitor-logging"></a>Journalisation d’Azure Monitor

Tous les événements sont intégrés à Azure Monitor, ce qui vous permet d’archiver les journaux d’activité dans un compte de stockage, de diffuser en streaming des événements à votre hub d’événements ou de les envoyer à des journaux d’activité Azure Monitor.

## <a name="compliance-certifications"></a>Certifications de conformité

Le service Pare-feu Azure est conforme aux normes PCI (Payment Card Industry), SOC (Service Organization Controls) et ISO (Organisation internationale de normalisation). Pour plus d’informations, consultez [Certifications de conformité du pare-feu Azure](compliance-certifications.md).


## <a name="known-issues"></a>Problèmes connus

Les problèmes connus du service Pare-feu Azure sont les suivants :

|Problème  |Description  |Limitation des risques  |
|---------|---------|---------|
Les règles de filtrage réseau pour les protocoles autres que TCP/UDP (par exemple ICMP) ne fonctionnent pas pour le trafic lié à Internet.|Les règles de filtrage réseau pour les protocoles autres que TCP/UDP ne fonctionnent pas avec SNAT pour votre adresse IP publique. Les protocoles autres que TCP/UDP sont pris en charge entre les sous-réseaux du rayon et les réseaux virtuels.|Le service Pare-feu Azure utilise Standard Load Balancer, [qui ne prend pas en charge SNAT pour les protocoles IP pour le moment](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Nous étudions les possibilités de prendre en charge ce scénario dans une prochaine version.|
|Protocole ICMP non pris en charge par PowerShell et l’interface de ligne de commande|Azure PowerShell et l’interface de ligne de commande ne prennent pas en charge le protocole ICMP en tant que protocole valide dans les règles de réseau.|Il est toujours possible d’utiliser le protocole ICMP par le biais du portail et de l’API REST. Nous travaillons actuellement à ajouter à PowerShell et à l’interface de ligne de commande la prise en charge du protocole ICMP.|
|Les balises FQDN requièrent une définition protocole : port|Les règles d’application avec des balises FQDN requièrent une définition port : protocole.|Vous pouvez utiliser **https** en tant que valeur port : protocole. Nous travaillons actuellement à rendre ce champ facultatif lorsque des balises FQDN sont utilisées.|
|Le déplacement d’un pare-feu vers un autre groupe de ressources ou un autre abonnement n’est pas pris en charge|Le déplacement d’un pare-feu vers un autre groupe de ressources ou un autre abonnement n’est pas pris en charge.|La prise en charge de cette fonctionnalité figure sur notre feuille de route. Pour déplacer un pare-feu vers un autre groupe de ressources ou un autre abonnement, vous devez supprimer l’instance actuelle et la recréer dans le nouveau groupe de ressources ou le nouvel abonnement.|
|Les alertes Threat intelligence peuvent être masquées|Les règles de réseau avec la destination 80/443 pour le filtrage sortant masque les alertes intelligentes de menaces lorsqu’elles sont configurées pour en mode alerte uniquement.|Créez un filtrage sortant pour 80/443 à l’aide de règles d’application. Ou modifiez le mode d’intelligence contre les menaces pour **alerter et rejeter**.|
|Le Pare-feu Azure utilise Azure DNS uniquement pour la résolution de noms|Le Pare-feu Azure résout les noms de domaine complets uniquement à l’aide d’Azure DNS. Un serveur DNS personnalisé n’est pas pris en charge. Il n’y a aucun impact sur la résolution DNS dans les autres sous-réseaux.|Nous travaillons actuellement à l’assouplissement de cette limitation.|
|Le Pare-feu Azure SNAT/DNAT ne fonctionne pas pour les destinations IP privées|La prise en charge du Pare-feu Azure SNAT/DNAT est limitée à la sortie/l’entrée Internet. Actuellement, SNAT/DNAT ne fonctionne pas pour les destinations IP privées. Par exemple, spoke-à-spoke.|Il s’agit d’une limitation actuelle.|
|Impossible de supprimer la première configuration IP publique|Chaque adresse IP publique du Pare-feu Azure est affectée à une *configuration IP*.  La première configuration IP est affectée pendant le déploiement du pare-feu. En général, elle contient une référence au sous-réseau du pare-feu (sauf si elle est configurée autrement de manière explicite via un déploiement de modèle). Vous ne pouvez pas supprimer cette configuration IP, car cela aurait pour effet de désallouer le pare-feu. Vous pouvez toujours changer ou supprimer l’adresse IP publique qui est associée à cette configuration IP si le pare-feu comprend au moins une autre adresse IP publique disponible.|C'est la procédure normale.|
|Les zones de disponibilité ne peuvent être configurées que pendant le déploiement.|Les zones de disponibilité ne peuvent être configurées que pendant le déploiement. Vous ne pouvez pas configurer les Zones de disponibilité après le déploiement d’un pare-feu.|C'est la procédure normale.|
|SNAT sur les connexions entrantes|En plus de DNAT, les connexions via l’adresse IP publique du pare-feu (entrante) sont traduites vers l’une des adresses IP privées du pare-feu. Cette exigence actuelle (également pour les appliances virtuelles réseau Active/Active) permet d’assurer la symétrie du routage.|Pour préserver la source originale pour HTTP/S, pensez à utiliser les en-têtes [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For). Par exemple, utilisez un service tel que [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) ou [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) devant le pare-feu. Vous pouvez également ajouter le pare-feu d’applications web en tant qu’Azure Front Door et l’associer au pare-feu.
|Prise en charge du filtrage FQDN SQL uniquement en mode proxy (port 1433)|Pour Azure SQL Database,Azure SQL Data Warehouse et Azure SQL Managed Instance :<br><br>Dans la préversion, le filtrage FQDN SQL est pris en charge uniquement en mode proxy (port 1433).<br><br>Pour Azure SQL IaaS :<br><br>Si vous utilisez des ports non standard, vous pouvez spécifier ces ports dans les règles de l’application.|Pour SQL en mode de redirection (qui est l’option par défaut si vous vous connectez à partir d’Azure), vous pouvez filtrer l’accès à l’aide de l’étiquette du service SQL, dans le cadre des règles de réseau du Pare-feu Azure.
|Le trafic sortant sur le port TCP 25 n’est pas autorisé| Les connexions SMTP sortantes qui utilisent le port TCP 25 sont bloquées. Le port 25 sert essentiellement à la remise d’e-mails non authentifiés. Il s’agit du comportement par défaut de la plateforme pour les machines virtuelles. Pour plus d’informations consultez [Résoudre les problèmes de connectivité SMTP sortante dans Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Toutefois, contrairement aux machines virtuelles, il n’est actuellement pas possible d’activer cette fonctionnalité sur le Pare-feu Azure.|Suivez la méthode d’envoi d’e-mails recommandée dans l’article traitant de la résolution des problèmes liés à SMTP. Vous pouvez aussi exclure la machine virtuelle qui a besoin d’un accès SMTP sortant entre votre route par défaut et le pare-feu pour configurer à la place un accès sortant directement vers Internet.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure](tutorial-firewall-deploy-portal.md)
- [Déployer Pare-feu Azure à l’aide d’un modèle](deploy-template.md)
- [Créer un environnement de test pour Pare-feu Azure](scripts/sample-create-firewall-test.md)
