---
title: FAQ Pare-feu Azure
description: FAQ concernant le Pare-feu Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 5/30/2019
ms.author: victorh
ms.openlocfilehash: 75b1131f2853cb444481b9c7a6c96e28f8537538
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384670"
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

Vous pouvez déployer Pare-feu Azure sur n’importe quel réseau virtuel, mais en général, les clients le déploient sur un réseau virtuel central et appairent les autres réseaux virtuels à celui-ci dans un modèle hub-and-spoke. Vous pouvez ensuite définir l’itinéraire par défaut des réseaux virtuels appairés pour qu’il pointe vers ce réseau virtuel de pare-feu central. Global VNet peering est pris en charge, mais il n’est pas recommandé en raison des performances potentielles et problèmes de latence entre les régions. Pour de meilleures performances, déployez un pare-feu par région.

L'avantage de ce modèle est qu'il permet d'exercer un contrôle centralisé sur plusieurs réseaux virtuels spoke pour différents abonnements. Il permet également de réaliser des économies de coûts puisqu'il n'est pas nécessaire de déployer un pare-feu au sein de chaque réseau virtuel. Les économies doivent être mesurées par rapport aux coûts de peering associés en fonction des modèles de trafic du client.

## <a name="how-can-i-install-the-azure-firewall"></a>Comment installer Pare-feu Azure ?

Vous pouvez installer Pare-feu Azure via le Portail Microsoft Azure, PowerShell, les API REST ou à l’aide de modèles. Consultez le [tutoriel : Déployer et configurer Pare-feu Azure avec le portail Azure](tutorial-firewall-deploy-portal.md) pour obtenir des instructions pas à pas.

## <a name="what-are-some-azure-firewall-concepts"></a>Quels sont les concepts de Pare-feu Azure ?

Pare-feu Azure prend en charge les règles et les regroupements de règles. Un regroupement de règles est un ensemble de règles qui partagent le même ordre et la même priorité. Les regroupements de règles sont exécutés dans l’ordre de leur priorité. Ainsi, les regroupements de règles Réseau sont prioritaires pas rapport aux regroupements de règles Application, et toutes les règles se terminent.

Il existe trois types de regroupements de règles :

* *Règles d’application* : Configurer des noms de domaine complet (FQDN) qui est accessible à partir d’un sous-réseau.
* *Règles de réseau* : Configurer des règles qui contiennent des adresses source, les protocoles, les ports de destination et les adresses de destination.
* *Règles NAT*: Configurer des règles DNAT pour autoriser les connexions entrantes.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Pare-feu Azure prend-il en charge le filtrage du trafic entrant ?

Pare-feu Azure prend en charge le filtrage du trafic entrant et sortant. La protection du trafic entrant concerne les protocoles non HTTP/S. Par exemple, les protocoles RDP, SSH et FTP.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quels sont les services de journalisation et d’analyse pris en charge par Pare-feu d’Azure ?

Pare-feu Azure est intégré à Azure Monitor pour la consultation et l’analyse des journaux d’activité de Pare-feu. Les journaux d’activité peuvent être envoyés à Log Analytics, Stockage Azure ou Event Hubs. Ils peuvent être analysés dans Log Analytics ou par d’autres outils comme Excel et Power BI. Pour plus d'informations, consultez le [tutoriel : Surveiller les journaux d’activité de pare-feu Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>En quoi Pare-feu Azure fonctionne-t-il différemment des services existants comme les appliances virtuelles réseau sur la Place de marché ?

Pare-feu Azure est un service de pare-feu de base qui peut résoudre les problèmes que rencontrent les clients dans certains cas. Il est probable que vous disposiez d’un mélange d’appliances virtuelles réseau et les pare-feu d’Azure par des tiers. Mieux travailler ensemble est une priorité.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Quelle est la différence entre Application Gateway WAF et Pare-feu Azure ?

Le pare-feu d’applications web (WAF, Web Application Firewall) est une fonctionnalité d’Application Gateway qui protège en entrée vos applications web de manière centralisée contre les vulnérabilités et les exploits courants. Pare-feu Azure assure une protection en entrée pour les protocoles non-HTTP/S (par exemple, RDP, SSH et FTP), une protection en sortie au niveau du réseau pour tous les ports et protocoles, ainsi qu’une protection au niveau de l’application pour le trafic HTTP/S sortant.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Quelle est la différence entre les groupes de sécurité réseau (NSG) et Pare-feu Azure ?

Le service Pare-feu Azure complète les fonctionnalités de groupe de sécurité réseau. Ensemble, ils fournissent une meilleure sécurité réseau grâce à une défense approfondie. Les groupes de sécurité réseau assurent un filtrage du trafic distribué au niveau de la couche réseau pour limiter le trafic aux ressources au sein de réseaux virtuels de chaque abonnement. Pare-feu Azure est un service de pare-feu entièrement centralisé et avec état qui offre une protection au niveau du réseau et de l’application entre les différents abonnements et réseaux virtuels.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Groupes de sécurité réseau (NSG) sont pris en charge sur le sous-réseau de pare-feu Azure ?

Pare-feu Azure est un service géré avec plusieurs couches de protection, y compris la protection de plateforme avec des NSG au niveau de carte réseau (non visible).  Niveau de sous-réseau ne sont pas requis sur le sous-réseau de pare-feu Azure et sont désactivées pour éviter les interruptions de service.


## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Comment faire pour configurer Pare-feu Azure avec mes points de terminaison de service ?

Pour bénéficier d’un accès sécurisé aux services PaaS, nous vous recommandons des points de terminaison de service. Vous pouvez choisir d’activer des points de terminaison de service dans le sous-réseau de Pare-feu Azure et les désactiver dans les réseaux virtuels spoke connectés. De cette façon, vous bénéficiez des deux fonctionnalités : sécurité de point de terminaison de service et journalisation centralisée pour tout le trafic.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Quels sont les tarifs de Pare-feu Azure ?

Consultez [pare-feu Azure tarification](https://azure.microsoft.com/pricing/details/azure-firewall/).

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

Pour les limites du service de pare-feu Azure, consultez [abonnement Azure et limites de service, quotas et contraintes](../azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Dans un réseau virtuel de hub, Pare-feu Azure peut-il envoyer et filtrer le trafic réseau entre deux réseaux virtuels spoke ?

Oui, vous pouvez utiliser Pare-feu Azure dans un réseau virtuel de hub pour acheminer et filtrer le trafic entre deux réseaux virtuels spoke. Afin de fonctionner, les sous-réseaux dans chaque réseau virtuel spoke doivent disposer de routes définies par l’utilisateur pointant vers le Pare-feu Azure en tant que passerelle par défaut pour ce scénario.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Pare-feu Azure peut-il envoyer et filtrer le trafic réseau entre des sous-réseaux d’un même réseau virtuel ou réseau virtuel appairé ?

Oui. Toutefois, la configuration de l’UDR pour rediriger le trafic entre sous-réseaux dans le même réseau virtuel requiert une attention supplémentaire. Bien que l'utilisation de la plage d'adresses du réseau virtuel comme préfixe cible pour l'UDR soit suffisante, celle-ci achemine également tout le trafic d'une machine vers une autre au sein du même sous-réseau via l'instance du Pare-feu Azure. Pour éviter cela, incluez l'itinéraire du sous-réseau dans l'UDR, avec **VNET** comme type de tronçon suivant. La gestion de ces itinéraires peut être lourde et sujette à erreur. La méthode recommandée pour la segmentation interne du réseau consiste à utiliser des groupes de sécurité réseau, qui ne requièrent pas d'UDR.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Est obligé de tunneling/chaînage une Appliance virtuelle réseau pris en charge ?

Le tunneling forcé n’est pas pris en charge par défaut, mais il peut être activé à l’aide de la prise en charge.

Le Pare-feu Azure doit avoir une connectivité Internet directe. Si votre AzureFirewallSubnet prend connaissance d’un itinéraire par défaut pour votre réseau local via le protocole BGP, vous devez le remplacer par un UDR 0.0.0.0/0 avec la valeur **NextHopType** définie sur **Internet** pour garantir une connectivité Internet directe. Par défaut, Pare-feu Azure ne prend en charge le tunneling forcé vers un réseau local.

Toutefois, si votre configuration nécessite un tunneling forcé vers un réseau local, Microsoft le prendra en charge au cas par cas. Contactez le support technique afin qu’il puisse étudier votre cas. Si accepté, nous permettre de votre abonnement et garantir que la conservation de la connectivité Internet de pare-feu nécessaires.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Les groupes de ressources de pare-feu font-ils l’objet de restrictions ?

Oui. Le pare-feu, le sous-réseau, le réseau virtuel et l’adresse IP publique doivent tous être dans le même groupe de ressources.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Lorsque je configure DNAT pour le trafic réseau entrant, dois-je également configurer une règle de réseau correspondante pour autoriser ce trafic ?

Non. Les règles NAT ajoutent implicitement une règle de réseau correspondante pour autoriser le trafic traduit. Vous pouvez remplacer ce comportement en ajoutant explicitement une collection de règles de réseau avec des règles de refus correspondant au trafic traduit. Pour plus d’informations sur la logique de traitement des règles de Pare-feu Azure, consultez l’article [Logique de traitement des règles du service Pare-feu Azure](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Comment les caractères génériques ne fonctionnent pas dans une cible de la règle application FQDN ?

Si vous configurez * **. contoso.com**, il permet de *anyvalue*. contoso.com, mais pas contoso.com (l’extrémité de domaine). Si vous souhaitez autoriser l’extrémité de domaine, vous devez le configurer explicitement comme un nom de domaine complet de la cible.

## <a name="what-does-provisioning-state-failed-mean"></a>Ce que fait *état d’approvisionnement : Échec de* signifie ?

Chaque fois qu’une modification de configuration est appliquée, pare-feu Azure tente de mettre à jour de toutes ses instances de serveur principal sous-jacent. Dans de rares cas, une de ces instances de serveur principal risquent de ne pas mettre à jour avec la nouvelle configuration et le processus de mise à jour s’arrête avec un état d’approvisionnement ayant échoué. Votre pare-feu d’Azure est toujours opérationnel, mais la configuration appliquée peut être dans un état incohérent, où certaines instances ont la configuration précédente où d’autres que la règle de mise à jour définie. Si cela se produit, essayez de mettre à jour votre configuration une nouvelle fois jusqu'à ce que l’opération réussit et que votre pare-feu est en cours un *Succeeded* état d’approvisionnement.
