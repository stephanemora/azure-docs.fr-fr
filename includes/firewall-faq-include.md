---
title: Fichier Include
description: Fichier Include
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 7/19/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 1db5f0a62a21d040949c9f4e4c42f80c86e76506
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163676"
---
### <a name="what-is-azure-firewall"></a>Qu’est-ce qu’un pare-feu Azure ?

Pare-feu Azure est un service de sécurité réseau informatique géré qui protège vos ressources Réseau virtuel Azure. Il s’agit d’un service de pare-feu entièrement avec état, pourvu d’une haute disponibilité intégrée et de l’extensibilité sans limites du cloud. Vous pouvez créer, appliquer et consigner des stratégies de connectivité réseau et d’application de façon centralisée entre les abonnements et les réseaux virtuels. Pare-feu Azure est actuellement disponible en préversion publique.

### <a name="what-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Quelles fonctionnalités sont prises en charge dans la préversion publique de Pare-feu Azure ?  

* Service de pare-feu avec état
* Haute disponibilité intégrée avec extensibilité sans limites du cloud
* Filtrage des noms de domaine complets 
* Règles de filtrage du trafic réseau
* Prise en charge du mode SNAT sortant
* Création, application et journalisation centralisées de stratégies de connectivité réseau et d’application entre les abonnements et les réseaux virtuels Azure
* Intégration totale avec Azure Monitor pour la journalisation et l’analytique 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Comment rejoindre la préversion publique de Pare-feu Azure ?

Pare-feu Azure est actuellement une préversion publique managée que vous pouvez rejoindre en utilisant la commande PowerShell Register-AzureRmProviderFeature, comme expliqué dans la documentation de la préversion publique de Pare-feu Azure.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Quels sont les tarifs de Pare-feu Azure ?

Pare-feu Azure présente un coût fixe + coût variable. Les prix sont indiqués ci-dessous ; la préversion publique bénéficie en plus d’une remise supplémentaire de 50 %.

* Frais fixes : 1,25 USD/pare-feu/heure
* Frais variables : 0,03 USD/Go traité par le pare-feu (en entrée ou en sortie).

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qu’est le modèle de déploiement type de Pare-feu Azure ?

Bien qu’il soit possible de déployer Pare-feu Azure sur n’importe quel réseau virtuel, les clients déploient généralement Pare-feu Azure dans un réseau virtuel central et appairent les autres réseaux virtuels à celui-ci dans un modèle Hub & Spoke. Vous pouvez ensuite définir l’itinéraire par défaut des réseaux virtuels appairés pour qu’il pointe vers ce réseau virtuel de pare-feu central.

### <a name="how-can-i-install-the-azure-firewall"></a>Comment installer Pare-feu Azure ?

Pare-feu Azure peut être installé via le Portail Microsoft Azure, PowerShell, les API REST ou des modèles. Pour obtenir des instructions pas à pas, consultez [Tutoriel : Déployer et configurer Pare-feu Azure avec le portail Azure](../articles/firewall/tutorial-firewall-deploy-portal.md).

### <a name="what-are-some-azure-firewall-concepts"></a>Quels sont les concepts de Pare-feu Azure ?

Pare-feu Azure prend en charge les règles et les regroupements de règles. Un regroupement de règles est un ensemble de règles qui partagent le même ordre et la même priorité. Les regroupements de règles sont exécutés dans l’ordre de leur priorité. Ainsi, les regroupements de règles Réseau sont prioritaires pas rapport aux regroupements de règles Application. Toutes les règles se terminent.
Il existe deux types de regroupement de règles :

* Règles d’application : permettent de configurer des noms de domaine complets (FQDN) accessibles depuis un sous-réseau. 
* Règles réseau : permettent de configurer des règles contenant l’adresse source, le protocole, le port de destination et l’adresse de destination. 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Pare-feu Azure prend-il en charge le filtrage du trafic entrant ?

La préversion publique de Pare-feu Azure prend en charge le filtrage en sortie uniquement. La protection en entrée pour les protocoles autres que HTTP/S (ex : RDP, SSH, FTP) est provisoirement prévue pour la version généralement disponible de Pare-feu Azure.  
 
### <a name="what-logginganalytics-is-supported-by-the-azure-firewall"></a>Quelles fonctionnalités de journalisation/analytiques Pare-feu Azure prend-il en charge ?

Pare-feu Azure est intégré à Azure Monitor pour la consultation et l’analyse des journaux de Pare-feu. Les journaux peuvent être envoyés à Log Analytics, Stockage Azure ou Event Hub. Ils peuvent être analysés dans Log Analytics ou par d’autres outils comme Excel et Power BI. Pour plus d’informations, consultez [Didacticiel : Surveiller les journaux de Pare-feu Azure](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-relative-to-existing-like-nvas-in-the-marketplace"></a>Comment Pare-feu Azure fonctionne-t-il par rapport à l’existant, notamment les appliances virtuelles réseau rencontrées dans la place de marché ?

Pare-feu Azure est un service de pare-feu de base qui peut résoudre les problèmes que rencontrent les clients dans certains cas. Selon nous, les clients qui utilisent Pare-feu Azure disposent aussi d’appliances virtuelles réseau tierces. À cet égard, nous œuvrons avec nos partenaires pour proposer de meilleures alternatives. 
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Quelle est la différence entre Application Gateway WAF et Pare-feu Azure ?

Le pare-feu d’applications web (WAF, Web Application Firewall) est une fonctionnalité d’Application Gateway qui protège en entrée vos applications web de manière centralisée contre les vulnérabilités et les exploits courants. Pare-feu Azure assure une protection en sortie au niveau du réseau pour tous les ports et les protocoles, ainsi qu’une protection au niveau de l’application pour le trafic HTTP/S sortant. La protection en entrée pour les protocoles autres que HTTP/S (par exemple, RDP, SSH, FTP) est provisoirement prévue pour la version généralement disponible de Pare-feu Azure.

### <a name="what-is-the-difference-between-network-security-groups-nsg-and-azure-firewall"></a>Quelle est la différence entre les groupes de sécurité réseau (NSG) et Pare-feu Azure ?

Le service de pare-feu Azure vient en complément de nos fonctionnalités de groupe de sécurité réseau existantes pour assurer une sécurité réseau plus efficace avec une défense en profondeur. Les groupes de sécurité réseau assurent un filtrage du trafic distribué au niveau de la couche réseau pour limiter le trafic aux ressources au sein de réseaux virtuels de chaque abonnement.  Pare-feu Azure est un service de pare-feu entièrement centralisé et avec état qui offre une protection au niveau du réseau et de l’application entre les différents abonnements et réseaux virtuels (VNet). 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Comment faire pour configurer Pare-feu Azure avec mes points de terminaison de service ?

Pour bénéficier d’un accès sécurisé aux services PaaS, nous vous recommandons des points de terminaison de service. Les clients Pare-feu Azure peuvent choisir d’activer les points de terminaison de service dans le sous-réseau Pare-feu Azure et de les désactiver sur les réseaux virtuels spoke connectés pour bénéficier des deux fonctionnalités (sécurité des points de terminaison de service et journalisation centralisée pour l’ensemble du trafic).

### <a name="what-are-the-known-service-limits"></a>Quelles sont les limites connues du service ?

* Pare-feu Azure a une limite logicielle de 1 000 To/pare-feu/mois. 
* Une instance Pare-feu Azure qui s’exécute dans un réseau virtuel central est soumise aux limitations du peering des réseaux virtuels : 50 réseaux virtuels spoke, au maximum.  
* Pare-feu Azure n’étant pas compatible avec le Peering mondial, les clients doivent disposer d’au moins un déploiement du pare-feu par région.