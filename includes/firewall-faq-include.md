---
title: Fichier Include
description: Fichier Include
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 8/13/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a63a12658bd0a4b4d018d51824af9814691a3cbf
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "40183314"
---
### <a name="what-is-azure-firewall"></a>Qu’est-ce qu’un pare-feu Azure ?

Pare-feu Azure est un service de sécurité réseau informatique géré qui protège vos ressources Réseau virtuel Azure. Il s’agit d’un service de pare-feu entièrement avec état, pourvu d’une haute disponibilité intégrée et de l’extensibilité sans limites du cloud. Vous pouvez créer, appliquer et consigner des stratégies de connectivité réseau et d’application de façon centralisée entre les abonnements et les réseaux virtuels. Pare-feu Azure est actuellement disponible en préversion publique.

### <a name="which-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Quelles fonctionnalités sont prises en charge dans la préversion publique de Pare-feu Azure ?  

* Service de pare-feu avec état
* Haute disponibilité intégrée avec extensibilité sans limites du cloud
* Filtrage des noms de domaine complets 
* Règles de filtrage du trafic réseau
* Prise en charge du mode SNAT sortant
* Possibilité de créer, d’appliquer et de consigner des stratégies de connectivité réseau et d’application de façon centralisée entre les abonnements et les réseaux virtuels
* Intégration totale avec Azure Monitor pour la journalisation et l’analytique 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Comment rejoindre la préversion publique de Pare-feu Azure ?

Pare-feu Azure est actuellement une préversion publique managée que vous pouvez rejoindre en utilisant la commande PowerShell Register-AzureRmProviderFeature. Cette commande est expliquée dans la documentation de la préversion publique de Pare-feu Azure.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Quels sont les tarifs de Pare-feu Azure ?

Pare-feu Azure présente un coût fixe et un coût variable. Les prix sont indiqués ci-dessous ; la préversion publique bénéficie en plus d’une remise supplémentaire de 50 %.

* Frais fixes : 1,25 USD/pare-feu/heure
* Frais variables : 0,03 USD/Go traité par le pare-feu (en entrée ou en sortie)

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qu’est le modèle de déploiement type de Pare-feu Azure ?

Vous pouvez déployer Pare-feu Azure sur n’importe quel réseau virtuel, mais en général, les clients le déploient sur un réseau virtuel central et appairent les autres réseaux virtuels à celui-ci dans un modèle hub-and-spoke. Vous pouvez ensuite définir l’itinéraire par défaut des réseaux virtuels appairés pour qu’il pointe vers ce réseau virtuel de pare-feu central.

### <a name="how-can-i-install-the-azure-firewall"></a>Comment installer Pare-feu Azure ?

Vous pouvez installer Pare-feu Azure via le Portail Microsoft Azure, PowerShell, les API REST ou à l’aide de modèles. Pour obtenir des instructions pas à pas, consultez [Tutoriel : Déployer et configurer Pare-feu Azure avec le portail Azure](../articles/firewall/tutorial-firewall-deploy-portal.md).

### <a name="what-are-some-azure-firewall-concepts"></a>Quels sont les concepts de Pare-feu Azure ?

Pare-feu Azure prend en charge les règles et les regroupements de règles. Un regroupement de règles est un ensemble de règles qui partagent le même ordre et la même priorité. Les regroupements de règles sont exécutés dans l’ordre de leur priorité. Ainsi, les regroupements de règles Réseau sont prioritaires pas rapport aux regroupements de règles Application, et toutes les règles se terminent.

Il existe deux types de regroupement de règles :

* *Règles d’application* : permettent de configurer des noms de domaine complets (FQDN) accessibles depuis un sous-réseau. 
* *Règles réseau* : permettent de configurer des règles contenant les adresses sources, les protocoles, les ports de destination et les adresses de destination. 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Pare-feu Azure prend-il en charge le filtrage du trafic entrant ?

La préversion publique de Pare-feu Azure prend en charge le filtrage en sortie uniquement. La protection en entrée pour les protocoles autres que HTTP/S (par exemple, RDP, SSH ou FTP) est provisoirement prévue pour la version généralement disponible de Pare-feu Azure.  
 
### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quels sont les services de journalisation et d’analyse pris en charge par Pare-feu d’Azure ?

Pare-feu Azure est intégré à Azure Monitor pour la consultation et l’analyse des journaux de Pare-feu. Les journaux peuvent être envoyés à Log Analytics, Stockage Azure ou Event Hubs. Ils peuvent être analysés dans Log Analytics ou par d’autres outils comme Excel et Power BI. Pour plus d’informations, consultez [Tutoriel : surveiller les journaux de Pare-feu Azure](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>En quoi Pare-feu Azure fonctionne-t-il différemment des services existants comme les appliances virtuelles réseau sur la Place de marché ?

Pare-feu Azure est un service de pare-feu de base qui peut résoudre les problèmes que rencontrent les clients dans certains cas. Il est probable que vous obteniez un mélange d’appliances virtuelles réseau et de Pare-feu Azure tiers. Mieux travailler ensemble est une priorité.
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Quelle est la différence entre Application Gateway WAF et Pare-feu Azure ?

Le pare-feu d’applications web (WAF, Web Application Firewall) est une fonctionnalité d’Application Gateway qui protège en entrée vos applications web de manière centralisée contre les vulnérabilités et les exploits courants. Pare-feu Azure assure une protection en sortie au niveau du réseau pour tous les ports et les protocoles, ainsi qu’une protection au niveau de l’application pour le trafic HTTP/S sortant. La protection en entrée pour les protocoles autres que HTTP/S (par exemple, RDP, SSH, FTP) est provisoirement prévue pour la version généralement disponible de Pare-feu Azure.

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Quelle est la différence entre les groupes de sécurité réseau (NSG) et Pare-feu Azure ?

Le service Pare-feu Azure complète les fonctionnalités de groupe de sécurité réseau. Ensemble, ils fournissent une meilleure sécurité réseau grâce à une défense approfondie. Les groupes de sécurité réseau assurent un filtrage du trafic distribué au niveau de la couche réseau pour limiter le trafic aux ressources au sein de réseaux virtuels de chaque abonnement. Pare-feu Azure est un service de pare-feu entièrement centralisé et avec état qui offre une protection au niveau du réseau et de l’application entre les différents abonnements et réseaux virtuels. 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Comment faire pour configurer Pare-feu Azure avec mes points de terminaison de service ?

Pour bénéficier d’un accès sécurisé aux services PaaS, nous vous recommandons des points de terminaison de service. Vous pouvez choisir d’activer des points de terminaison de service dans le sous-réseau de Pare-feu Azure et les désactiver dans les réseaux virtuels spoke connectés. De cette façon, vous bénéficiez des deux fonctionnalités : sécurité de point de terminaison de service et journalisation centralisée pour tout le trafic.

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Comment arrêter et démarrer le Pare-feu Azure ?

Vous pouvez utiliser les méthodes *désallouer* et *allouer* d’Azure PowerShell.

Par exemple : 

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzureRmFirewall -Name "FW Name” -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzureRmFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzureRmPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzureRmFirewall -AzureFirewall $azfw
```

### <a name="what-are-the-known-service-limits"></a>Quelles sont les limites connues du service ?

* Pare-feu Azure a une limite logicielle de 1000 To par pare-feu et par mois. 
* Une instance Pare-feu Azure qui s’exécute dans un réseau virtuel central est soumise aux limitations du peering des réseaux virtuels, avec un maximum de 50 réseaux virtuels spoke.  
* Le pare-feu Azure n’étant pas compatible avec le Peering mondial, les clients doivent disposer d’au moins un déploiement de pare-feu par région.
* Pare-feu Azure prend en charge 10 000 règles d’application et 10 000 règles de réseau.