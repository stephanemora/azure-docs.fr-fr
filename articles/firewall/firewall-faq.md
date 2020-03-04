---
title: FAQ Pare-feu Azure
description: FAQ sur Pare-feu Azure. Service de sécurité réseau cloud managé qui protège vos ressources Réseau virtuel Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 4792c0bce7d9119f5198490d62f49f000e1567d3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621959"
---
# <a name="azure-firewall-faq"></a>FAQ Pare-feu Azure

## <a name="what-is-azure-firewall"></a>Qu’est-ce qu’un pare-feu Azure ?

Pare-feu Azure est un service de sécurité réseau informatique géré qui protège vos ressources Réseau virtuel Azure. Il s’agit d’un service de pare-feu entièrement avec état, pourvu d’une haute disponibilité intégrée et de l’extensibilité sans limites du cloud. Vous pouvez créer, appliquer et consigner des stratégies de connectivité réseau et d’application de façon centralisée entre les abonnements et les réseaux virtuels.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Quelles fonctionnalités sont prises en charge dans Pare-feu Azure ?

* Service de pare-feu avec état
* Haute disponibilité intégrée avec extensibilité sans limites du cloud
* Filtrage des noms de domaine complets
* Balises FQDN
* Règles de filtrage du trafic réseau
* Prise en charge du mode SNAT sortant
* Prise en charge du trafic DNAT entrant
* Création, application et journalisation centralisées de stratégies de connectivité réseau et d’application entre les abonnements et les réseaux virtuels Azure
* Intégration totale avec Azure Monitor pour la journalisation et l’analytique

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qu’est le modèle de déploiement type de Pare-feu Azure ?

Vous pouvez déployer Pare-feu Azure sur n’importe quel réseau virtuel, mais en général, les clients le déploient sur un réseau virtuel central et appairent les autres réseaux virtuels à celui-ci dans un modèle hub-and-spoke. Vous pouvez ensuite définir l’itinéraire par défaut des réseaux virtuels appairés pour qu’il pointe vers ce réseau virtuel de pare-feu central. Global VNET Peering est pris en charge, mais n’est pas recommandé en raison de problèmes potentiels de performances et de latence sur les régions. Pour de meilleures performances, déployez un pare-feu par région.

L'avantage de ce modèle est qu'il permet d'exercer un contrôle centralisé sur plusieurs réseaux virtuels spoke pour différents abonnements. Il permet également de réaliser des économies de coûts puisqu'il n'est pas nécessaire de déployer un pare-feu au sein de chaque réseau virtuel. Les économies doivent être mesurées par rapport aux coûts de peering associés en fonction des modèles de trafic du client.

## <a name="how-can-i-install-the-azure-firewall"></a>Comment installer Pare-feu Azure ?

Vous pouvez installer Pare-feu Azure via le Portail Microsoft Azure, PowerShell, les API REST ou à l’aide de modèles. Voir le [tutoriel : Déployer et configurer Pare-feu Azure avec le portail Azure](tutorial-firewall-deploy-portal.md) pour obtenir des instructions pas à pas.

## <a name="what-are-some-azure-firewall-concepts"></a>Quels sont les concepts de Pare-feu Azure ?

Pare-feu Azure prend en charge les règles et les regroupements de règles. Un regroupement de règles est un ensemble de règles qui partagent le même ordre et la même priorité. Les regroupements de règles sont exécutés dans l’ordre de leur priorité. Ainsi, les regroupements de règles Réseau sont prioritaires pas rapport aux regroupements de règles Application, et toutes les règles se terminent.

Il existe trois types de regroupement de règles :

* *Règles d’application* : Configurez des noms de domaine pleinement qualifiés (FQDN) qui sont accessibles depuis un sous-réseau.
* *Règles de réseau* : Configurez des règles contenant les adresses sources, les protocoles, les ports de destination et les adresses de destination.
* *Règles NAT* : Configurez des règles DNAT pour autoriser les connexions Internet entrantes.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Pare-feu Azure prend-il en charge le filtrage du trafic entrant ?

Pare-feu Azure prend en charge le filtrage du trafic entrant et sortant. La protection du trafic entrant est généralement utilisée pour les protocoles non HTTP/S. Par exemple, les protocoles RDP, SSH et FTP. Pour une protection HTTP/S optimale du trafic entrant, utilisez un pare-feu d’applications web comme le [pare-feu d’applications web Azure (WAF)](../web-application-firewall/overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quels sont les services de journalisation et d’analyse pris en charge par Pare-feu d’Azure ?

Pare-feu Azure est intégré à Azure Monitor pour la consultation et l’analyse des journaux d’activité de Pare-feu. Les journaux d’activité peuvent être envoyés à Log Analytics, Stockage Azure ou Event Hubs. Ils peuvent être analysés dans Log Analytics ou par d’autres outils comme Excel et Power BI. Pour plus d’informations, consultez [Didacticiel : Surveiller les journaux d’activité de pare-feu Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>En quoi Pare-feu Azure fonctionne-t-il différemment des services existants comme les appliances virtuelles réseau sur la Place de marché ?

Pare-feu Azure est un service de pare-feu de base qui peut résoudre les problèmes que rencontrent les clients dans certains cas. Il est probable que vous obteniez un mélange d’appliances virtuelles réseau et de Pare-feu Azure tiers. Mieux travailler ensemble est une priorité.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Quelle est la différence entre Application Gateway WAF et Pare-feu Azure ?

Le pare-feu d’applications web (WAF, Web Application Firewall) est une fonctionnalité d’Application Gateway qui protège en entrée vos applications web de manière centralisée contre les vulnérabilités et les exploits courants. Pare-feu Azure assure une protection en entrée pour les protocoles non-HTTP/S (par exemple, RDP, SSH et FTP), une protection en sortie au niveau du réseau pour tous les ports et protocoles, ainsi qu’une protection au niveau de l’application pour le trafic HTTP/S sortant.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Quelle est la différence entre les groupes de sécurité réseau (NSG) et Pare-feu Azure ?

Le service Pare-feu Azure complète les fonctionnalités de groupe de sécurité réseau. Ensemble, ils fournissent une meilleure sécurité réseau grâce à une défense approfondie. Les groupes de sécurité réseau assurent un filtrage du trafic distribué au niveau de la couche réseau pour limiter le trafic aux ressources au sein de réseaux virtuels de chaque abonnement. Pare-feu Azure est un service de pare-feu entièrement centralisé et avec état qui offre une protection au niveau du réseau et de l’application entre les différents abonnements et réseaux virtuels.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Les groupes de sécurité réseau sont-ils pris en charge dans le sous-réseau de Pare-feu Azure ?

Pare-feu Azure est un service géré avec plusieurs couches de protection, y compris la protection de plateforme avec des groupes de sécurité réseau au niveau de la carte réseau (non visible).  Les groupes de sécurité réseau au niveau du sous-réseau ne sont pas nécessaires sur le sous-réseau de Pare-feu Azure, et sont désactivés pour empêcher toute interruption du service.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Comment faire pour configurer Pare-feu Azure avec mes points de terminaison de service ?

Pour bénéficier d’un accès sécurisé aux services PaaS, nous vous recommandons des points de terminaison de service. Vous pouvez choisir d’activer des points de terminaison de service dans le sous-réseau de Pare-feu Azure et les désactiver dans les réseaux virtuels spoke connectés. De cette façon, vous bénéficiez des deux fonctionnalités : sécurité de point de terminaison de service et journalisation centralisée pour tout le trafic.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Quels sont les tarifs de Pare-feu Azure ?

Consultez [Tarification de Pare-feu Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Comment arrêter et démarrer le Pare-feu Azure ?

Vous pouvez utiliser les méthodes *désallouer* et *allouer* d’Azure PowerShell.

Par exemple :

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Vous devez réaffecter un pare-feu et une adresse IP publique au groupe de ressources et à l’abonnement d’origine.

## <a name="what-are-the-known-service-limits"></a>Quelles sont les limites connues du service ?

Pour connaître les limites du service Pare-feu Azure, voir [Limites, quotas et contraintes applicables aux services et abonnements Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Dans un réseau virtuel de hub, Pare-feu Azure peut-il envoyer et filtrer le trafic réseau entre deux réseaux virtuels spoke ?

Oui, vous pouvez utiliser Pare-feu Azure dans un réseau virtuel de hub pour acheminer et filtrer le trafic entre deux réseaux virtuels spoke. Afin de fonctionner correctement, les sous-réseaux de chaque réseau virtuel spoke doivent disposer d’un UDR pointant vers Pare-feu Azure en tant que passerelle par défaut pour ce scénario.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Pare-feu Azure peut-il envoyer et filtrer le trafic réseau entre des sous-réseaux d’un même réseau virtuel ou réseau virtuel appairé ?

Oui. Toutefois, la configuration des UDR pour rediriger le trafic entre les sous-réseaux d'un même réseau virtuel nécessite une attention supplémentaire. Bien que l'utilisation de la plage d'adresses du réseau virtuel comme préfixe cible pour l'UDR soit suffisante, celle-ci achemine également tout le trafic d'une machine vers une autre au sein du même sous-réseau via l'instance du Pare-feu Azure. Pour éviter cela, incluez l'itinéraire du sous-réseau dans l'UDR, avec **VNET** comme type de tronçon suivant. La gestion de ces itinéraires peut être lourde et sujette à erreur. La méthode recommandée pour la segmentation interne du réseau consiste à utiliser des groupes de sécurité réseau, qui ne requièrent pas d'UDR.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>La sortie de Pare-feu Azure traduit-elle l’adresse réseau source (SNAT) entre réseaux privés ?

Pare-feu Azure ne traduit pas l’adresse réseau source lorsque l’adresse IP de destination est une plage d’adresses IP privées selon la norme [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Si votre organisation utilise une plage d’adresses IP publiques pour les réseaux privés, Pare-feu Azure traduit l’adresse réseau source du trafic en une des adresses IP privées du pare-feu dans AzureFirewallSubnet. Vous pouvez configurer le pare-feu Azure pour ne **pas** traduire l’adresse réseau source (SNAT) de votre plage d’adresses IP publiques. Pour plus d’informations, consultez [Plages d’adresses IP privées SNAT de pare-feu Azure](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Le tunneling/chaînage forcé à une appliance virtuelle réseau est-il pris en charge ?

Le tunneling forcé est pris en charge. Pour plus d’informations, consultez [Tunneling forcé du pare-feu Azure (préversion)](forced-tunneling.md). 

Le Pare-feu Azure doit avoir une connectivité Internet directe. Si votre AzureFirewallSubnet prend connaissance d’un itinéraire par défaut pour votre réseau local via le protocole BGP, vous devez le remplacer par un UDR 0.0.0.0/0 avec la valeur **NextHopType** définie sur **Internet** pour garantir une connectivité Internet directe.

Si votre configuration nécessite un tunneling forcé vers un réseau local et que vous pouvez déterminer les préfixes IP cibles pour vos destinations Internet, vous pouvez configurer ces plages en faisant du réseau local le tronçon suivant avec une route définie par l’utilisateur sur le sous-réseau AzureFirewallSubnet. Vous pouvez aussi utiliser le protocole BGP pour définir ces routes.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Les groupes de ressources de pare-feu font-ils l’objet de restrictions ?

Oui. Le pare-feu, le réseau virtuel et l’IP publique doivent tous être dans le même groupe de ressources.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Lorsque je configure DNAT pour le trafic Internet entrant, dois-je également configurer une règle de réseau correspondante pour autoriser ce trafic ?

Non. Les règles NAT ajoutent implicitement une règle de réseau correspondante pour autoriser le trafic traduit. Vous pouvez remplacer ce comportement en ajoutant explicitement une collection de règles de réseau avec des règles de refus correspondant au trafic traduit. Pour plus d’informations sur la logique de traitement des règles de Pare-feu Azure, consultez l’article [Logique de traitement des règles du service Pare-feu Azure](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Comment fonctionnent les caractères génériques dans un nom de domaine complet cible d’une règle d’application ?

Si vous configurez * **.contoso.com**, cela autorise *anyvalue*.contoso.com, mais pas contoso.com (le domaine apex). Si vous souhaitez autoriser le domaine apex, vous devez explicitement le configurer en tant que nom de domaine complet cible.

## <a name="what-does-provisioning-state-failed-mean"></a>Que signifie l’*état d’approvisionnement : Échec* ?

Chaque fois qu’une modification de configuration est appliquée, Pare-feu Azure tente de mettre à jour toutes ses instances de serveur principal sous-jacentes. Dans de rares cas, il se peut qu’une de ces instances de serveur principal ne soit pas mise à jour avec la nouvelle configuration et le processus de mise à jour s’arrête avec un état d’approvisionnement ayant échoué. Votre Pare-feu Azure est toujours opérationnel, mais la configuration appliquée peut se trouver dans un état incohérent, où certaines instances ont toujours la configuration précédente et d’autres ont l’ensemble de règles mis à jour. Si cela se produit, essayez de mettre à jour votre configuration une fois de plus jusqu’à la réussite de l’opération que votre Pare-feu aie l’état d’approvisionnement *Réussite*.

### <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Comment le pare-feu Azure gère la maintenance planifiée et les défaillances non planifiées ?
Le pare-feu Azure est constitué de plusieurs nœuds back-end dans une configuration active-active.  Pour toute activité de maintenance planifiée, notre logique de drainage des connexions permet de mettre à jour les nœuds de manière appropriée.  Pour limiter le risque d’interruption, les mises à jour sont planifiées en dehors des heures d’ouverture, et ce pour chaque région Azure.  Pour les problèmes non planifiés, nous instancions un nouveau nœud pour remplacer le nœud défaillant.  La connectivité avec le nouveau nœud est généralement rétablie dans les 10 secondes suivant la défaillance.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Une limite de caractères s'applique-t-elle aux noms des pare-feu ?

Oui. Le nom d’un pare-feu est limité à 50 caractères.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Pourquoi le service Pare-feu Azure impose-t-il une taille de sous-réseau de /26 ?

Le Pare-feu Azure doit approvisionner davantage de machines virtuelles au fil de sa mise à l'échelle. Avec un espace d'adressage de /26, le pare-feu dispose de suffisamment d'adresses IP pour prendre en charge la mise à l'échelle.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>La taille du sous-réseau du pare-feu doit-elle changer au fil de la mise à l'échelle du service ?

Non. Un sous-réseau de /26 suffit au Pare-feu Azure.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Comment puis-je augmenter le débit de mon pare-feu ?

La capacité de débit initiale de Pare-feu Azure est de 2,5 à 3 Gbits/s et monte en charge jusqu’à 30 Gbits/s. Elle s’adapte en fonction de l’utilisation de l’UC et du débit. Contactez le support pour augmenter la capacité de débit de votre pare-feu si votre pare-feu ne s’adapte pas à vos besoins et que vous avez besoin d’une capacité de débit plus importante.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Combien de temps le scale-out du Pare-feu Azure prend-il ?

La montée en charge de Pare-feu Azure prend entre cinq et sept minutes. Si vous avez des rafales nécessitant une mise à l’échelle automatique plus rapide, contactez le support afin d’augmenter la capacité de débit initiale de votre pare-feu.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Le Pare-feu Azure autorise-t-il l’accès à Active Directory par défaut ?

Non. Le Pare-feu Azure bloque l’accès à Active Directory par défaut. Pour autoriser l’accès, configurez l’étiquette du service Azure Active Directory. Pour plus d’informations, consultez [Étiquettes du service Pare-feu Azure](service-tags.md).
