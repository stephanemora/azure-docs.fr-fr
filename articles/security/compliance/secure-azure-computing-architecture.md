---
title: Sécuriser l’architecture de calcul Azure
description: Cette architecture de référence pour une architecture de réseau de périmètre au niveau de l’entreprise utilise des appliances virtuelles réseau et autres outils. Cette architecture a été conçue pour satisfaire Secure Cloud Computing Architecture exigences du ministère de la défense fonctionnelles. Il peut également être utilisé pour toute organisation. Cette référence inclut deux options automatisées qui utilisent des appliances Citrix ou F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 017a26d5672f666d4d8eaf629a0f53fe0cfe517f
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963240"
---
# <a name="secure-azure-computing-architecture"></a>Sécuriser l’architecture de calcul Azure

Données Ministère de la défense (DoD) des clients qui déploient des charges de travail vers Azure ont demandé des conseils pour configurer des réseaux virtuels sécurisés et configurer les outils de sécurité et les services qui sont stipulés par les pratiques et des normes DoD. 

L’outil défense informations système Agency (DISA) publié le [Secure Cloud Computing Architecture (SCCA) fonctionnel exigences Document (domaine racine de forêt)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) en 2017. SCCA décrit les objectifs fonctionnels pour la sécurisation des points de connexion fournisseur de la défense des informations système et de réseau (DISN) cloud commercial. SCCA décrit également comment les propriétaires de mission sécurisés aux applications du cloud à la limite de connexion. Chaque entité DoD qui se connecte au cloud commercial doit suivre les instructions stipulées dans le FRD SCCA.
 
Le SCCA possède quatre composants :
 
- Point d’accès Cloud limite (BCAP)
- Pile de sécurité de centre de données virtuel (VDSS)
- Centre de données virtuel Managed Service (VDM)
- Approuvé Cloud Credential Manager (TCCM) 

Microsoft a développé une solution qui répond aux exigences du SCCA IL4 et IL5 les charges de travail qui s’exécutent dans Azure. Cette solution Azure spécifique est appelée le sécuriser Azure Computing Architecture (SACA). Les clients qui déploient SACA sont en conformité avec la FRD SCCA. Ils peuvent activer les clients DoD déplacer des charges de travail dans Azure une fois qu’ils sont connectés.

Architectures et des conseils SCCA concernent les clients DoD, mais les dernières révisions à des clients civils de SACA aide sont conformes aux instructions de connexion (TIC) internet approuvé. Les dernières révisions également aident les clients commerciaux que vous souhaitez implémenter un réseau de périmètre sécurisé pour protéger leurs environnements Azure.


## <a name="secure-cloud-computing-architecture-components"></a>Sécuriser les composants de l’Architecture de Cloud Computing

### <a name="bcap"></a>BCAP

L’objectif de la BCAP consiste à protéger le DISN contre les attaques provenant de l’environnement de cloud. BCAP effectue la prévention et détection d’intrusion. Il filtre également le trafic non autorisé. Ce composant peut être colocalisé avec d’autres composants de la SCCA. Nous vous recommandons de déployer ce composant à l’aide de matériel physique. Exigences de sécurité BCAP sont répertoriées dans le tableau suivant.

#### <a name="bcap-security-requirements"></a>Exigences de sécurité BCAP

![Matrice des exigences BCAP](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

Le VDSS vise à protéger les applications de propriétaire de la mission DoD qui sont hébergées dans Azure. VDSS effectue la majeure partie des opérations de sécurité dans le SCCA. Il effectue le contrôle du trafic pour sécuriser les applications qui s’exécutent dans Azure. Ce composant peut être fourni au sein de votre environnement Azure.

#### <a name="vdss-security-requirements"></a>Exigences de sécurité VDSS

![Matrice des exigences VDSS](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

L’objectif de VDM consiste à assurer la sécurité de l’hôte et services de centre de données partagés. Les fonctions de VDM peuvent exécuter dans le hub de votre SCCA ou le propriétaire de la mission peut déployer des parties de celui-ci dans leur propre abonnement Azure spécifique. Ce composant peut être fourni au sein de votre environnement Azure.

#### <a name="vdms-security-requirements"></a>Exigences de sécurité VDM

![Matrice des exigences de VDM](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM est un rôle d’entreprise. Cette personne est responsable de la gestion de la SCCA. Leurs fonctions sont les suivants : 

- Établissez des plans et les stratégies d’accès de compte à l’environnement de cloud. 
- Assurez-vous que la gestion des identités et des accès fonctionne correctement. 
- Mettre à jour le Plan de gestion des informations d’identification de Cloud. 

Cette personne est désignée par l’agent d’autorisation. Les BCAP, VDSS et VDM fournissent les fonctionnalités dont la TCCM a besoin pour effectuer leur travail.

#### <a name="tccm-security-requirements"></a>Exigences de sécurité TCCM

![Matrice des exigences TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Composants SACA et considérations de planification 

L’architecture de référence SACA est conçu pour déployer les composants VDSS et VDM dans Azure et pour activer la TCCM. Cette architecture est modulaire. Tous les éléments de VDSS et VDM peuvent résider dans un hub centralisé. Certains contrôles peuvent être satisfaits dans l’espace du propriétaire de la mission ou même localement. Microsoft recommande que vous colocalisez les composants VDSS et VDM dans un réseau virtuel central que tous les propriétaires de mission peuvent se connecter via. Le diagramme suivant illustre cette architecture : 


![Diagramme d’architecture de référence SACA](media/sacav2generic.png)

Lorsque vous planifiez votre stratégie de conformité SCCA et l’architecture technique, envisagez les rubriques suivantes à partir du début, car elles affectent tous les clients. Les problèmes suivants ont développé avec les clients DoD et ont tendance à ralentir la planification et l’exécution. 

#### <a name="which-bcap-will-your-organization-use"></a>Le BCAP utiliser par votre organisation ?
   - DISA BCAP :
        - DISA a deux BCAPs opérationnelles à la pentagone et à Camp Roberts, autorité de certification. Une troisième est prévue seront bientôt disponibles en ligne. 
        - BCAPs du DISA qu'ont tous les circuits Azure ExpressRoute vers Azure, ce qui peut être utilisé par les clients DoD pour la connectivité. 
        - DISA a une session d’homologation Microsoft de niveau entreprise pour les clients DoD qui souhaitent s’abonner aux logiciels Microsoft en tant qu’un outils de service (SaaS), telles qu’Office 365. À l’aide de la DISA BCAP, vous pouvez activer la connectivité et l’homologation à votre instance SACA. 
    - Créer votre propre BCAP :
        - Cette option vous oblige à louer de l’espace dans un centre de données colocalisée et configurer un circuit ExpressRoute vers Azure. 
        - Cette option nécessite une approbation supplémentaire. 
        - En raison de l’approbation supplémentaire et une build physique-out, cette option utilise le plus de temps. 
    - Nous vous recommandons d’utiliser le BCAP DISA. Cette option est immédiatement disponible, intègre la redondance et a des clients qui opèrent sur celui-ci dès aujourd'hui en production.
- Espace d’adresse IP routable DoD :
    - Vous devez utiliser l’espace d’adressage IP routable DoD sur votre périphérie. La possibilité d’utiliser NAT pour connecter ces espaces pour espace IP privé dans Azure est disponible.
    - Contactez le centre d’informations de réseau (NIC) DoD pour obtenir l’espace d’adressage IP. Vous en avez besoin dans le cadre de votre envoi réseau ou système d’approbation processus SNAP () avec DISA. 
    - Si vous envisagez d’utiliser NAT pour connecter l’espace d’adressage privé dans Azure, vous devez au minimum une taille/24 sous-réseau de l’espace d’adressage attribué à partir de la carte réseau pour chaque région où vous envisagez de déployer de SACA.
- Redondance :
    - Déployer une instance SACA sur au moins deux régions. Dans le cloud DoD, déployez-la sur les deux régions DoD disponibles.
    - Se connecter au moins deux BCAPs par le biais des circuits ExpressRoute distincts. Les deux connexions ExpressRoute peuvent ensuite être liées à instance SACA de chaque région. 
- Configuration requise spécifique au composant de DoD :
    - Votre entreprise utilise-t-elle des exigences spécifiques en dehors de la configuration requise SCCA ? Certaines organisations ont des exigences d’adresses IP spécifiques.
- Il s’agit d’une architecture modulaire de SACA :
    - Utiliser uniquement les composants que vous avez besoin pour votre environnement. 
        - Déployer des appliances virtuelles réseau dans un seul ou plusieurs niveaux.
        - Utiliser des adresses IP intégrée ou apportez votre propre des adresses IP.
- Niveau d’impact DoD de vos applications et les données :
    - S’il est possible d’applications s’exécutant dans Microsoft IL5 régions, créez votre instance SACA dans IL5. L’instance peut être utilisé devant les applications IL4 et IL5. Une instance IL4 SACA devant une application IL5 probablement ne recevront pas accréditation.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Le fournisseur d’appliance virtuelle réseau utiliserez-vous pour VDSS ?
Comme mentionné précédemment, vous pouvez créer cette référence SACA à l’aide d’une variété d’appareils et services Azure. Microsoft a automatisé des modèles de solution pour déployer l’architecture SACA avec F5 et Citrix. Ces solutions sont traitées dans la section suivante.

#### <a name="which-azure-services-will-you-use"></a>Quels services Azure allez-vous utiliser ?
- Il existe des services Azure capable de répondre aux conditions requises pour l’analytique de journal, la protection basée sur l’hôte et fonctionnalités des ID. Il est possible que certains services ne sont pas à la disposition générale dans les régions Microsoft IL5. Dans ce cas, vous devrez peut-être utiliser des outils tiers si ces services Azure ne peut pas répondre à vos besoins. Examinons les outils que vous êtes familiarisé avec et la possibilité d’utiliser les outils Azure natifs.
- Nous vous recommandons d’utiliser des outils natifs Azure autant que possible. Ils sont générés avec la sécurité du cloud à l’esprit et intégrant en toute transparence avec le reste de la plateforme Azure. Pour répondre aux différentes exigences en matière de SCCA, utilisez les outils natifs Azure dans la liste suivante :

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Centre de sécurité Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Application Gateway Azure](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Pare-feu Azure](https://docs.microsoft.com/azure/firewall/overview) 
    - [Porte d’entrée Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Groupes de sécurité Azure](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Sentinel Azure](https://docs.microsoft.com/azure/sentinel/overview)
- Dimensionnement
    - Un exercice de dimensionnement doit être effectué. Regardez le nombre de connexions simultanées, que vous devrez peut-être par l’intermédiaire de l’instance SACA et les exigences de débit du réseau. 
    - Cette étape est essentielle. Cela permet de dimensionner les machines virtuelles et identifier les licences qui sont nécessaires sur les différents fournisseurs que vous utilisez dans votre instance SACA. 
    - Une analyse des coûts bon ne peut pas être effectuée sans cet exercice de dimensionnement. Dimensionnement correct permet également de meilleures performances. 


## <a name="most-common-deployment-scenario"></a>Scénario de déploiement courante

 Plusieurs clients de Microsoft sont passées via le déploiement complet ou au moins les étapes de planification de leurs environnements de SACA. Leurs expériences a révélé un aperçu dans le scénario de déploiement le plus courant. Le diagramme suivant illustre l’architecture courant : 


![Diagramme d’architecture de référence SACA](media/sacav2commonscenario.png) 


Comme vous pouvez le voir dans le diagramme, les clients DoD s’abonner généralement de deux de la BCAPs DISA. Un d’eux se trouve sur la côte ouest des États-Unis et d’autres la vie sur la côte est. Un homologue privé ExpressRoute est activé pour Azure à chaque emplacement DISA BCAP. Ces homologues ExpressRoute sont ensuite liés à la passerelle de réseau virtuel dans les régions DoD est et Azure centrale DoD. Une instance SACA est déployée dans les régions DoD est et Azure centrale DoD. Tous les trafics entrant et sortant flux de trafic par son intermédiaire vers et à partir de la connexion ExpressRoute pour la DISA BCAP.

Propriétaire de la mission applications puis choisissez les régions Azure dans lequel ils envisagent de déployer leurs applications. Ils utilisent d’homologation de réseaux virtuels pour connecter le réseau virtuel de l’application sur le réseau virtuel de SACA. Il force tout le trafic via l’instance VDSS du tunnel.

Nous recommandons cette architecture, car il répond aux exigences de SCCA. Il est hautement disponible et très évolutives. Elle simplifie également le déploiement et la gestion.

## <a name="automated-saca-deployment-options"></a>Options de déploiement automatisées SACA

 Comme mentionné précédemment, Microsoft a conclu un partenariat avec deux fournisseurs pour créer un modèle d’infrastructure SACA automatisé. Les deux modèles déploient les composants Azure suivants : 

- Réseau virtuel de SACA
    - Sous-réseau de gestion
        - Ce sous-réseau est où les machines virtuelles de gestion et de services sont déployés, également appelé un saut de zones.
        - Sous-réseau de VDM
            - Ce sous-réseau est où les machines virtuelles et services utilisés pour les VDM sont déployés.
        - Sous-réseaux et non approuvées
            - Ces sous-réseaux est où les appliances virtuelles sont déployées.
        - Sous-réseau de passerelle
            - Ce sous-réseau est où la passerelle ExpressRoute est déployée.
- Ordinateurs virtuels de gestion reroutage boîte
    - Ils sont utilisés pour la gestion hors bande de l’environnement.
- Appliances virtuelles réseau
    - Vous utilisez soit Citrix ou F5 basé sur le modèle que vous déployez.
- Adresses IP publiques
    - Ils sont utilisés pour le front-end jusqu'à ce que ExpressRoute est mis en ligne. Ces adresses IP se traduire par le back-end espace d’adressage privé Azure.
- Tables d'itinéraires 
    - Appliqué lors de l’automatisation, qui achemine tables forcer le tunnel tout le trafic via l’appliance virtuelle.
- Équilibreurs de charge Azure - référence (SKU) Standard
    - Elles sont utilisées pour équilibrer le trafic entre les appareils.
- Groupes de sécurité réseau
    - Elles sont utilisées pour contrôler les types de trafic peuvent traverser à certains points de terminaison.


### <a name="citrix-saca-deployment"></a>Déploiement de Citrix SACA

Un modèle de déploiement de Citrix déploie deux couches d’appliances de Citrix ADC hautement disponibles. Cette architecture répond aux exigences de VDSS. 

![Diagramme de Citrix SACA](media/citrixsaca.png)


Pour la documentation Citrix et le script de déploiement, consultez [ce lien GitHub](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>Déploiement de SACA de F5

Deux modèles de déploiement F5 distincts couvrent deux architectures différentes. Le premier modèle ne possède qu’une seule couche d’appliances de F5 dans une configuration hautement disponible actif-actif. Cette architecture répond aux exigences du VDSS. Le deuxième modèle ajoute une deuxième couche de F5s haut actif-actif. Cette deuxième couche permet aux clients d’ajouter leur propres adresses IP distincte de F5 entre les couches de F5. Pas tous les composants de DoD ont prescrite pour l’utilisation des adresses IP spécifiques. Si tel est le cas, la seule couche de F5 appliances fonctionne pour la plupart étant donné que cette architecture inclut les adresses IP sur les appareils F5.

![Diagramme de SACA de F5](media/f5saca.png)

Pour la documentation de F5 et le script de déploiement, consultez [ce lien GitHub](https://github.com/f5devcentral/f5-azure-saca).












