---
title: Sécuriser l’architecture de calcul Azure
description: Cette architecture de référence pour une architecture DMZ d'entreprise utilise des appliances virtuelles réseau et d'autres outils. Cette architecture a été conçue pour répondre aux exigences fonctionnelles de l'architecture Secure Cloud Computing du Département de la Défense des États-Unis. Elle peut également être utilisée pour n'importe quelle organisation. Cette référence comprend deux options automatisées qui utilisent les appliances Citrix ou F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: a12f5643c96b855d07bd038fcc96100a87f1252d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001878"
---
# <a name="secure-azure-computing-architecture"></a>Sécuriser l’architecture de calcul Azure

Données Les clients du Département de la Défense des États-Unis (Department of Defense ou DoD) qui déploient des charges de travail chez Azure ont demandé des conseils pour mettre en place des réseaux virtuels sécurisés et configurer les outils et services de sécurité exigés par les normes et pratiques DoD. 

La Defense Information System Agency (DISA) a publié le document [Secure Cloud Computing Architecture (SCCA) Functional Requirements Document (FRD)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/SCCA_FRD_v2-9.pdf) en 2017. SCCA décrit les objectifs fonctionnels de sécurisation des points de connexion du réseau DISN (Defense Information System Network) et des fournisseurs commerciaux de cloud computing. SCCA décrit également comment les chargés de mission sécurisent les applications dans le cloud aux limites de la connexion. Chaque entité DoD qui se connecte au cloud commercial doit suivre les directives énoncées dans le document FRD SCCA.
 
SCCA comporte quatre composants :
 
- Boundary Cloud Access Point (BCAP)
- Virtual Datacenter Security Stack (VDSS)
- Virtual Datacenter Managed Service (VDMS)
- Trusted Cloud Credential Manager (TCCM) 

Microsoft a développé une solution qui répond aux exigences SCCA pour les charges de travail IL4 et IL5 exécutées dans Azure. Cette solution spécifique à Azure est appelée Secure Azure Computing Architecture (SACA). Les clients qui déploient SACA sont en conformité avec le document FRD SCCA. Ils peuvent autoriser les clients DoD connectés à déplacer des charges de travail vers Azure.

Les directives et les architectures SCCA sont spécifiques aux clients DoD, mais les dernières révisions SACA aident les clients civils à se conformer aux directives sur les connexions Internet de confiance (TIC). Les dernières révisions aident également les clients commerciaux qui veulent implémenter une DMZ sécurisée pour protéger leurs environnements Azure.


## <a name="secure-cloud-computing-architecture-components"></a>Composants Secure Cloud Computing Architecture

### <a name="bcap"></a>BCAP

Le but du BCAP est de protéger le DISN contre les attaques provenant de l'environnement dans le cloud. Le BCAP gère la détection et la prévention des intrusions. Il filtre également le trafic non autorisé. Ce composant peut être regroupé avec d'autres composants SCCA. Nous vous recommandons de déployer ce composant en utilisant du matériel physique. Les exigences de sécurité BCAP sont énumérées dans le tableau suivant.

#### <a name="bcap-security-requirements"></a>Conditions de sécurité BCAP

![Matrice des exigences BCAP](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

Le but du VDSS est de protéger les applications des propriétaires de mission DoD hébergées dans Azure. Le VDSS gère la majeure partie des opérations de sécurité au sein de SCCA. Il inspecte le trafic pour sécuriser les applications qui s'exécutent dans Azure. Ce composant peut être fourni dans votre environnement Azure.

#### <a name="vdss-security-requirements"></a>Conditions de sécurité VDSS

![Matrice des exigences VDSS](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

Le but du VDMS est d'assurer la sécurité de l'hôte et de fournir des services de centre de données partagés. Les fonctions du VDMS peuvent être exécutées dans le hub de votre architecture SCCA, ou le propriétaire de la mission peut en déployer certaines parties dans son propre abonnement Azure spécifique. Ce composant peut être fourni dans votre environnement Azure.

#### <a name="vdms-security-requirements"></a>Conditions de sécurité VDMS

![Matrice des exigences VDMS](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM est un rôle métier. Cette personne est responsable de la gestion de l’architecture SCCA. Sa mission consiste à : 

- Établir des plans et des stratégies pour l'accès des comptes à l'environnement dans le cloud. 
- S'assurer que la gestion des identités et des accès fonctionne correctement. 
- Maintenir le plan de gestion des informations d'identification dans le cloud. 

Cette personne est nommée par le responsable autorisé. Le BCAP, le VDSS et le VDMS fournissent les capacités dont le TCCM a besoin pour effectuer son travail.

#### <a name="tccm-security-requirements"></a>Conditions de sécurité TCCM

![Matrice des exigences TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Considérations sur les composants SACA et la planification 

L'architecture de référence SACA est conçue pour déployer les composants VDSS et VDMS sur Azure et pour activer le TCCM. Cette architecture est modulaire. Tous les éléments du VDSS et du VDMS peuvent coexister dans un hub centralisé. Certains des contrôles peuvent être effectués dans l'espace du propriétaire de la mission ou même sur place. Microsoft vous recommande de regrouper les composants VDSS et VDMS dans un réseau virtuel central auquel tous les responsables de mission peuvent se connecter. Le diagramme qui suit montre cette architecture : 


![Diagramme de l'architecture de référence SACA](media/sacav2generic.png)

Lorsque vous planifiez votre stratégie de conformité SCCA et votre architecture technique, tenez compte d’emblée des points suivants car ils affectent chaque client. Les problèmes suivants sont apparus chez les clients DoD et ont tendance à ralentir la planification et l'exécution. 

#### <a name="which-bcap-will-your-organization-use"></a>Quel BCAP votre organisation utilisera-t-elle ?
   - BCAP DISA :
        - DISA dispose de deux BCAP opérationnels au Pentagone et à Camp Roberts en Californie. Un troisième devrait être disponible prochainement. 
        - Les BCAP de DISA ont tous des circuits Azure ExpressRoute vers Azure, qui peuvent être utilisés par les clients DoD à des fins de connectivité. 
        - DISA dispose d'une session d’homologation Microsoft de niveau professionnel pour les clients DoD qui souhaitent s'abonner aux outils Microsoft Software as a Service (SaaS) comme Office 365. En utilisant le BCAP DISA, vous pouvez activer la connectivité et l’homologation avec votre instance SACA. 
    - Créez votre propre BCAP :
        - Cette option vous oblige à louer de l'espace dans un centre de données colocalisé et à installer un circuit ExpressRoute vers Azure. 
        - Cette option nécessite une approbation supplémentaire. 
        - En raison de l'approbation supplémentaire et d'une construction physique, cette option prend le plus de temps. 
    - Nous vous recommandons d’utiliser le BCAP DISA. Cette option est facilement disponible, offre une redondance intégrée, et des clients l'utilisent dès aujourd'hui en production.
- Espace d’adressage IP routable DoD :
    - Vous devez utiliser l'espace d’adressage IP routable DoD à votre périphérie. Vous pouvez également utiliser NAT pour connecter ces espaces à un espace d’adressage IP privé dans Azure.
    - Contactez le centre d'information du réseau DoD (NIC) pour obtenir un espace d’adressage IP. Vous en avez besoin dans le cadre de votre demande d'approbation du système/réseau (SNAP) auprès de DISA. 
    - Si vous comptez utiliser NAT pour connecter l'espace d'adressage privé dans Azure, vous avez besoin au minimum d'un sous-réseau /24 d'espace d'adressage attribué par le NIC pour chaque région où vous prévoyez de déployer SACA.
- Redondance :
    - Déployez une instance SACA dans au moins deux régions. Dans le nuage DoD, vous la déployez dans les deux régions DoD disponibles.
    - Connectez-vous à au moins deux BCAP via des circuits ExpressRoute distincts. Les deux connexions ExpressRoute peuvent alors être associées à l'instance SACA de chaque région. 
- Exigences spécifiques aux composants DoD :
    - Votre organisation a-t-elle des exigences particulières autres que celles de SCCA ? Certaines organisations ont des exigences IPS spécifiques.
- SACA est une architecture modulaire :
    - N'utilisez que les composants dont vous avez besoin pour votre environnement. 
        - Déployez des appliances virtuelles réseau sur un ou plusieurs niveaux.
        - Utilisez l'IPS intégré ou fournissez votre propre IPS.
- Niveau d'impact DoD de vos applications et données :
    - S'il y a une possibilité que les applications s’exécutent dans des régions Microsoft IL5, construisez votre instance SACA dans IL5. L'instance peut être utilisée devant des applications IL4 et IL5. Une instance SACA IL4 devant une application IL5 ne recevra probablement pas d'accréditation.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Quel fournisseur d'appliance virtuelle réseau allez-vous utiliser pour VDSS ?
Comme mentionné précédemment, vous pouvez construire cette référence SACA en utilisant toute une gamme d’appliances et de services Azure. Microsoft a automatisé des modèles de solution pour déployer l'architecture SACA avec F5 et Citrix. Ces solutions sont traitées dans la section suivante.

#### <a name="which-azure-services-will-you-use"></a>Quels services Azure allez-vous utiliser ?
- Il existe des services Azure capables de répondre aux exigences en matière d'analyse des journaux, de protection basée sur l'hôte et de fonctionnalité IDS. Il est possible que certains services ne soient généralement pas disponibles dans les régions Microsoft IL5. Dans ce cas, vous pourriez avoir besoin d'utiliser des outils tiers si ces services Azure ne répondent à vos besoins. Examinez les outils avec lesquels vous êtes à l'aise et la possibilité d'utiliser les outils natifs Azure.
- Nous vous recommandons d'utiliser autant d'outils natifs Azure que possible. Ces outils ont été conçus en tenant compte de la sécurité dans le cloud et s'intègrent de façon transparente au reste de la plateforme Azure. Utilisez les outils natifs Azure de la liste suivante pour répondre aux différentes exigences SCCA :

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Centre de sécurité Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Application Gateway Azure](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Pare-feu Azure](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure Front Door](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Groupes de sécurité Azure](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Dimensionnement
    - Un exercice de dimensionnement doit être effectué. Examinez le nombre de connexions simultanées possibles via l'instance SACA ainsi que les exigences de débit du réseau. 
    - Cette étape est essentielle. Elle vous aide à dimensionner les machines virtuelles et à identifier les licences requises des différents fournisseurs que vous utilisez dans votre instance SACA. 
    - Cet exercice de dimensionnement est indispensable pour obtenir une bonne analyse des coûts. Un bon dimensionnement permet également d'optimiser les performances. 


## <a name="most-common-deployment-scenario"></a>Scénario de déploiement le plus courant

 Plusieurs clients Microsoft sont passés par le déploiement complet ou du moins par les étapes de planification de leurs environnements SACA. À partir de leurs expériences, nous avons pu définir le scénario de déploiement le plus courant. Le diagramme suivant montre l'architecture la plus courante : 


![Diagramme de l'architecture de référence SACA](media/sacav2commonscenario.png) 


Comme vous pouvez le voir sur le diagramme, les clients DoD s'abonnent généralement à deux des BCAP DISA. L'un d'eux vit sur la côte ouest et l'autre sur la côte est des États-Unis. Un homologue privé ExpressRoute est activé pour Azure sur chaque site BCAP DISA. Ces homologues ExpressRoute sont ensuite associés à la passerelle réseau virtuelle dans les régions Azure DoD Est et DoD Centre. Une instance SACA est déployée dans les régions Azure DoD Est et DoD Centre. Tout le trafic d'entrée et de sortie est acheminé vers et depuis la connexion ExpressRoute vers le BCAP DISA.

Les applications du propriétaire de la mission choisissent ensuite les régions Azure dans lesquelles ils prévoient de déployer leurs applications. Ils utilisent l’homologation de réseau virtuel pour connecter le réseau virtuel de leur application au réseau virtuel SACA. Puis ils tunnélisent de force tout leur trafic via l'instance VDSS.

Nous recommandons cette architecture car elle répond aux exigences SCCA. Elle est hautement disponible et facilement évolutive. Elle simplifie également le déploiement et la gestion.

## <a name="automated-saca-deployment-options"></a>Options de déploiement SACA automatisé

 Comme mentionné précédemment, Microsoft s'est associé à deux fournisseurs pour créer un modèle automatisé d'infrastructure SACA. Ces deux modèles déploient les composants Azure suivants : 

- Réseau virtuel SACA
    - Sous-réseau de gestion
        - Ce sous-réseau est l'endroit où les machines virtuelles et les services de gestion sont déployés, également connu sous le nom de « jump box ».
        - Sous-réseau VDMS
            - Ce sous-réseau est l'endroit où les machines virtuelles et les services utilisés pour VDMS sont déployés.
        - Sous-réseaux fiables et non fiables
            - Ces sous-réseaux sont les endroits où les appliances virtuelles sont déployées.
        - Sous-réseau de passerelle
            - Ce sous-réseau est l'endroit où la passerelle ExpressRoute est déployée.
- Machines virtuelles Jump Box de gestion
    - Elles sont utilisées pour la gestion hors-bande de l'environnement.
- Appliances virtuelles réseau
    - Vous utilisez Citrix ou F5 selon le modèle que vous déployez.
- Adresses IP publiques
    - Elles sont utilisées pour le serveur frontal jusqu'à ce que ExpressRoute soit mis en ligne. Ces adresses IP sont traduites vers l'espace d'adressage privé back-end Azure.
- Tables de routage 
    - Appliquées lors de l'automatisation, ces tables de routage forcent la tunnélisation de tout le trafic via l'appliance virtuelle.
- Équilibreurs de charge Azure - Référence SKU standard
    - Ils sont utilisés pour équilibrer le trafic entre les appliances.
- Groupes de sécurité réseau
    - Ils sont utilisés pour identifier les types de trafic capable d’atteindre certains points de terminaison.


### <a name="citrix-saca-deployment"></a>Déploiement SACA Citrix

Un modèle de déploiement Citrix déploie deux couches d'appliances Citrix ADC hautement disponibles. Cette architecture répond aux exigences VDSS. 

![Diagramme SACA Citrix](media/citrixsaca.png)


Pour la documentation Citrix et le script de déploiement, voir [ce lien GitHub](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>Déploiement SACA F5

Deux modèles de déploiement F5 distincts couvrent deux architectures différentes. Le premier modèle ne comporte qu'une seule couche d'appliances F5 dans une configuration active-active hautement disponible. Cette architecture répond aux exigences VDSS. Le deuxième modèle ajoute une deuxième couche d’appliances F5 actives-actives hautement disponibles. Cette deuxième couche permet aux clients d'ajouter leur propre IPS séparément de l’appliance F5 entre les couches F5. Tous les composants DoD n'ont pas d’IPS spécifique dont l'utilisation est prescrite. Si c'est le cas, la couche unique des appliances F5 fonctionne pour la plupart de ces composants car cette architecture inclut IPS sur les appareils F5.

![Diagramme SACA F5](media/f5saca.png)

Pour la documentation F5 et le script de déploiement, voir [ce lien GitHub](https://github.com/f5devcentral/f5-azure-saca).












