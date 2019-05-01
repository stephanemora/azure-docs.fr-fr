---
title: Sécuriser l’Architecture de calcul Azure
description: Il s’agit d’une architecture de référence pour une architecture de réseau de périmètre de niveau entreprise, utilisant des Appliances virtuelles réseau et autres outils. Cette architecture a été conçue pour satisfaire Secure Cloud Computing Architecture exigences du ministère de la défense fonctionnelles. Toutefois, elle peut être exploitée pour toute organisation. Cette référence inclut deux options d’automatisation à l’aide d’appliances Citrix ou F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: f2e3d72db3f29dbc6d03b3259acb18daf684fb12
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917585"
---
# <a name="secure-azure-computing-architecture"></a>Sécuriser l’Architecture de calcul Azure

Instructions de configuration des réseaux virtuels sécurisés et la configuration des outils de sécurité et services stipulés par les pratiques et des normes DoD ont demandé un nombre croissant de clients DoD déployant des charges de travail vers Azure. DISA publié le [Document d’exigences fonctionnelles Secure Cloud Computing Architecture (SCCA)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) en 2017. SCCA décrit les objectifs fonctionnels pour la sécurisation de la défense des informations système du réseau (DISN) et de connexion du fournisseur de Cloud Commercial pointe et comment les applications de cloud sécurisée de propriétaires à la limite de connexion. Il est autorisé que chaque entité DoD qui se connecte au cloud commercial suit les instructions stipulées dans le FRD SCCA.
 
Il existe quatre composants de la SCCA. Le Point d’accès Cloud limite (BCAP), la pile de sécurité de centre de données virtuel (VDSS), le centre de données virtuel (VDM) de Services gérés et Trusted Cloud Credential Manager (TCCM). Microsoft a développé une solution répondant aux exigences SCCA pour IL4 et IL5 les charges de travail en cours d’exécution dans Azure. Cette solution Azure spécifique est appelée sécuriser Azure Computing Architecture (SACA). Les clients qui déploient SACA seront conforme à la FRD SCCA et va permettre aux clients DoD déplacer des charges de travail dans Azure une fois connecté. 

Architectures et des conseils de SCCA sont spécifiques aux clients DoD, les dernières révisions de SACA également permettent des clients civils sont conformes aux instructions de connexion (TIC) internet approuvé, ainsi que les clients commerciaux qui souhaitent implémenter un réseau de périmètre sécurisé pour protéger leurs environnements azure.


## <a name="secure-cloud-computing-architecture-components"></a>Sécuriser les composants de l’Architecture de Cloud Computing

**BCAP**

L’objectif de la BCAP consiste à protéger le DISN contre les attaques provenant de l’environnement de cloud. BCAP s’effectuer la détection d’intrusion et la prévention ainsi filtrer le trafic non autorisé. Ce composant peut être colocalisé avec d’autres composants de la SCCA. Il est vivement recommandé que ce composant est déployé à l’aide de matériel physique. Vous trouverez ci-dessous la liste des exigences de sécurité BCAP.

***Exigences de sécurité BCAP***

![Matrice des exigences BCAP](media/bcapreqs.png)


**VDSS**

Le VDSS vise à protéger les applications DoD Mission propriétaire qui sont hébergées dans Azure. VDSS effectue la majeure partie des opérations de sécurité dans le SCCA. Il sera inspection de trafic afin de sécuriser les applications s’exécutant dans Azure. Ce composant peut être fourni au sein de votre environnement Azure.

***Exigences de sécurité VDSS***

![Matrice des exigences VDSS](media/vdssreqs.png)

**VDMS**

L’objectif de VDM consiste à assurer la sécurité de l’hôte ainsi que les services de centre de données partagés. Les fonctions de VDM peuvent exécuter dans le hub de votre SCCA ou le propriétaire de la mission peut déployer des parties de celui-ci dans leur propre abonnement Azure spécifique. Ce composant peut être fourni au sein de votre environnement Azure.

***Exigences de sécurité VDM***

![Matrice des exigences de VDM](media/vdmsreqs.png)


**TCCM**

TCCM est un rôle d’entreprise. Cette personne est responsable de la gestion de la SCCA. Son travail implique l’établissement des plans et les stratégies d’accès de compte à l’environnement de cloud, garantissant l’identité et la gestion des accès fonctionne correctement et de gérer le Plan de gestion des informations d’identification Cloud. Cette personne est nommée par l’autorisation officielle. Le BCAP, VDSS et VDM fournit les fonctionnalités nécessaires pour le TCCM effectuer leur fonction.

***Exigences de sécurité TCCM***

![Matrice des exigences TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Composants SACA et considérations de planification 

L’architecture de référence SACA est conçu pour déployer les composants VDSS et VDM dans azure, mais aussi activer le TCCM. Cette architecture est modulaire, ce qui signifie que tous les éléments de VDSS et VDM peuvent se trouver dans un hub centralisé ou certains contrôles peuvent être remplies dans l’espace du propriétaire de la mission ou même localement. Il est recommandé de notre équipe de Microsoft colocaliser les composants VDSS et VDM dans une centrale virtuel Net que tous les propriétaires de Mission peuvent se connecter via. Le diagramme ci-dessous illustre notre architecture recommandée. 


![Diagramme d’Architecture de référence SACA](media/sacav2generic.png)

Lorsque vous planifiez votre stratégie de conformité SCCA et l’architecture technique, il existe de nombreux éléments à prendre en compte. Il est important que les rubriques suivantes sont prises en compte dès le début, comme chaque client va devoir aborder ces raisons. Les rubriques ci-dessous ont été les problèmes qui ont proposer aux clients DoD réels et ont tendance à ralentir les la planification et l’exécution. 

- Le BCAP utiliser par votre organisation ?
    - DISA BCAP
        - DISA a deux BCAPs opérationnelles au pentagone et une autorité de certification Camp Roberts, avec un tiers en ligne sera bientôt disponible. 
        - BCAPs du DISA qu'ont tous les circuits ExpressRoute à Azure, qui peuvent être exploitées par les clients DoD pour la connectivité. 
        - DISA a déjà une session de Microsoft Peering de niveau entreprise pour les clients DoD qui souhaitent s’abonner à des outils Microsoft SaaS, telles qu’Office 365. À l’aide de DISA BCAP, vous pouvez activer la connectivité et l’homologation à votre instance SACA. 
    - Créer votre propre BCAP
        - Cela vous oblige à louer de l’espace dans un centre de données colocalisée et configurer un circuit ExpressRoute vers Azure. 
        - Cette option nécessite une approbation supplémentaire 
        - En raison d’une approbation supplémentaire et une build physique out, cette option utilise le plus de temps. 
    - Recommandation de Microsoft consiste à utiliser le BCAP DISA. Cette option est disponible en redondance intégrée et a déjà des clients qui exploitent dessus dès aujourd'hui en production.
- Espace d’adressage IP routable DoD
    - Vous devez utiliser l’espace d’adressage IP routable DoD sur votre périphérie. L’option NAT pour celles utilisées pour l’espace IP privé dans Azure est disponible.  
    - Contactez DoD carte d’interface réseau pour obtenir l’espace d’adressage IP, il est nécessaire dans le cadre de votre soumission du composant logiciel enfichable avec DISA. 
    - Si vous envisagez NAT à l’espace d’adressage privé dans Azure, vous devez au minimum une taille/24 sous-réseau de l’espace d’adressage attribué à partir de la carte réseau pour chaque région que vous envisagez de déployer de SACA. 
- Redondance 
    - Microsoft suggère que vous déployez une instance SACA sur au moins deux régions. Dans le cloud DoD, cela signifierait que vous le déployez dans les deux régions DoD disponibles. 
    - Il est également conseillé de vous connecter au moins deux BCAPs par le biais des circuits ExpressRoute distincts. Les deux itinéraires Express peut ensuite être liés à instance SACA de chaque région. 
- Exigences spécifiques au composant DoD
    - Votre entreprise utilise-t-elle des exigences spécifiques en dehors de la configuration requise SCCA ? (Certaines organisations ont des exigences d’adresses IP spécifiques)
- SACA est une architecture modulaire  
    - Utiliser uniquement les composants que vous avez besoin pour votre environnement. 
        - Déployer des appliances virtuelles réseau dans un seul ou plusieurs niveaux
        - Intégré des adresses IP, ou apportez vos propres adresses IP
- Niveau d’impact DoD de vos applications et données
    - S’il est possible d’applications s’exécutant dans les autres régions IL5, il est recommandé que vous générez votre instance SACA dans IL5. L’instance peut être utilisée devant IL4 applications ainsi que IL5. Toutefois, une instance IL4 SACA devant une application IL5 recevra probablement pas accréditation. 
- Le fournisseur d’Appliance virtuelle réseau utiliserez-vous pour VDSS ?
    - Comme mentionné précédemment, cette référence SACA peut être créée à l’aide d’une variété d’appareils et services Azure. Toutefois, nous ont des modèles de solution automatisée pour déployer l’architecture SACA avec F5 et Citrix. Ces solutions seront abordées plus en détail ci-dessous. 
- Quels services Azure allez-vous utiliser ?
    - Il existe des services Azure capable de répondre aux exigences de journal analytique, la protection basée sur l’hôte et fonctionnalités des ID. Toutefois, il est possible que certains services ne sont pas à la disposition générale dans les autres régions IL5. Cela peut conduire à la nécessité d’utiliser certains outils tiers 3e si ces services Azure ne peut pas répondre à vos besoins. Vous devrez examiner quels outils sont à l’aise avec et la possibilité d’utiliser les outils Azure natifs. 
    - Il s’agit de recommandation de Microsoft que vous utilisez des outils natifs Azure autant que possibles, car ils sont générés avec la sécurité de cloud à l’esprit et intégrant en toute transparence avec le reste de la plateforme Azure. Voici une liste d’outils Azure natifs qui peuvent être exploités pour répondre aux différentes exigences en matière de SCCA. 
        - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
        - [Centre de sécurité Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) 
        - [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
        - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
        - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
        - [Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
        - [Pare-feu Azure](https://docs.microsoft.com/azure/firewall/overview) 
        - [Porte d’entrée Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
        - [Groupes de sécurité](https://docs.microsoft.com/azure/virtual-network/security-overview)
        - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
        - [Sentinel Azure](https://docs.microsoft.com/azure/sentinel/overview)
- Dimensionnement
    - Un exercice de dimensionnement sera doivent être effectuées. Vous devez examiner le nombre de connexions simultanées, que vous devrez peut-être par l’intermédiaire de l’instance SACA, ainsi que les exigences de débit de réseau. 
    - Il s’agit d’une étape essentielle car il sera aider à dimensionner les machines virtuelles, mais aussi vous aider à identifier les licences requises par les différents fournisseurs que vous utiliserez dans votre instance SACA. 
    - Une analyse des coûts bon ne peut pas être effectuée sans cet exercice de dimensionnement, il est également important de vous assurer que tout est dimensionné correctement pour permettre des performances optimales. 


## <a name="most-common-deployment-scenario"></a>Scénario de déploiement courante

Microsoft a plusieurs clients qui sont déjà passées par la version complète de déploiement ou de planification au moins les étapes de leurs environnements de SACA. Cela nous a permis d’obtenir des informations sur le scénario de déploiement le plus courant. Le diagramme ci-dessous illustre l’architecture courant. 


![Diagramme d’Architecture de référence SACA](media/sacav2commonscenario.png) 


Comme vous pouvez le voir dans le diagramme, les clients DoD s’abonner généralement de deux de la DISA BCAPs, un de ces se trouve sur la côte ouest et l’autre se trouve sur la côte est. Un homologue privé ExpressRoute est activé pour Azure à chaque emplacement DISA BCAP. Ces homologues ExpressRoute sont ensuite liés à la passerelle de réseau virtuel dans le DoD est et les régions Azure centrale DoD. Une instance SACA est déployée dans les régions DoD est et Azure centrale DoD et tous les trafics entrant et sortant du trafic circule à travers il vers et à partir de la connexion Express Route pour la DISA BCAP. 

Mission propriétaire des applications, puis choisissez les ou les régions Azure qu’ils comptent déployer leurs applications dans Utilisation de l’homologation de réseau virtuel pour se connecter à leur application de messagerie du réseau virtuel au réseau virtuel SACA. Forcer le mode Tunneling de tout le trafic via l’instance VDSS. 

Cette architecture est fortement recommandée par Microsoft, comme il répond aux exigences de SCCA, il est hautement disponible et évolutif facilement, et il simplifie le déploiement et la gestion.

## <a name="automated-saca-deployment-options"></a>Options de déploiement automatisées SACA

 Nous avons mentionné précédemment que Microsoft a conclu un partenariat avec deux fournisseurs pour créer un modèle d’infrastructure SACA automatisé. Les deux modèles déploie les composants Azure suivants. 

- Réseau virtuel de SACA
    - Sous-réseau de gestion
        - Où les machines virtuelles de gestion et de services sont déployés (passer des zones)
        - Sous-réseau de VDM
            - Où sont déployées les machines virtuelles et services utilisés pour les VDM
        - Sous-réseaux et non approuvées 
            - Dans lequel les appliances virtuelles sont déployées
        - Sous-réseau de la passerelle
            - Sur lequel la passerelle ExpressRoute doit être déployée
- Ordinateurs virtuels de gestion reroutage boîte
    - Utilisé pour la gestion hors bande de l’environnement.
- Appliances virtuelles réseau
    - Citrix ou F5, selon le modèle que vous déployez.
- Adresses IP publiques
    - Utilisé pour la partie front-end jusqu'à ce que ExpressRoute est mis en ligne. Ces adresses IP est traduit au backend de l’espace d’adressage privé Azure
- Tables de routage 
    - Appliqué lors de l’automatisation, ces tables de routage force-tunnel tout le trafic via l’appliance virtuelle
- Équilibreurs de charge Azure - référence (SKU) Standard
    - Ceux-ci sont utilisés pour équilibrer le trafic entre les appareils
- Network Security Group
    - Utilisé pour contrôler les types de trafic peuvent traverser à certains points de terminaison


**Déploiement de Citrix SACA**

Citrix a créé un modèle de déploiement qui déploie les deux couches d’appliances de Citrix ADC hautement disponibles. Cette architecture répond aux exigences de VDSS. 

![Diagramme de SACA de Citrix](media/citrixsaca.png)


Script de déploiement et de la Documentation Citrix trouverez [ici.](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)


 **Déploiement de SACA de F5**

F5 a créé deux modèles de déploiement distinct couvrant les deux architectures différentes. Le premier n’a qu’une seule couche d’appliances de F5 dans une configuration hautement disponible actif-actif. Cette architecture répond aux exigences du VDSS. La deuxième ajoute une deuxième couche de F5s haut actif-actif. L’objectif de cette deuxième couche consiste à permettre aux clients d’ajouter leur propres adresses IP distincte de F5 entre les couches de F5. Pas tous les composants de DoD ont prescrite pour l’utilisation des adresses IP spécifiques. Si tel est le cas, la seule couche de F5 appliances fonctionnera pour plus depuis qu’architecture inclut les adresses IP sur les appareils F5.  

![Diagramme de SACA de Citrix](media/f5saca.png)

Script de déploiement et de la Documentation de F5 trouverez [ici.](https://github.com/f5devcentral/f5-azure-saca) 












